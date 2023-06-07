This document is intended to explain and compare the current (inherit) against the new (proposed) asset commissioning process for the Pwrcell Adapter.

## Inherit asset commissioning process

![[Inherit Pwrcell asset commissioning.svg]]

The following steps are explained in a sequential-logical order for better understanding even though thats doesn't represent the reality of the adapter because these processes happen concurrently.

### 1. Telemetry Processor

![[Inherit Pwrcell asset commissioning - Telemetry processor.svg]]

The Telemetry Processor Lambda parses the Protobuf records received from Kinesis Stream every 5 minutes. Then the Lambda search the system from Kinesis record in the `device_table`; If the device exists then the `last_heard_at` will be updated and telemetry will be sent to Concerto; If the device does not exists then it will be added to the `device_table`.

**Flaws of this approach:**
- This Lambda is creating new devices in the `device_table`. By creating new devices in the `device_table` the Lambda is assuming CES Telemetry as source of truth.
- This Lambda is reading and writing from `device_table`, and sending telemetry to Concerto. This Lambda its doing more tasks than expected.

### 2. Registrar

![[Inherit Pwrcell asset commissioning - Registrar.svg]]

The Registrar Poller sends every minute a message to Concerto Gateway Queue to ask for all assets paginated in a specific concerto instance. The Concerto Gateway Lambda process the message and invoke the Registrar Lambda.
The Registrar Lambda filters the devices from the response of the all assets paginated query and select the devices that are under the Pwrcell Adapter name. 
For each device in concerto the Registrar will: 
- Search for it in the `device_table` and determines the `remote_state` based on `system_state` gathered from CES API and the `last_heard_at` property based on a 15 minutes timeout. If the `remote_state` was different from the last record the Registrar will update it on concerto as well as the site `remote_state` by sending a message to the Concerto Gateway Queue that will be later processed by the Concerto Gateway Lambda.
- If the device is a simulated device the Registrar will add them into the `device_table` and the `simulator_table`.
- If the device is not found on the `device_table` the Registrar will send this errors to Concerto.

**Flaws of this approach:**
- Using two table to determine if an asset is simulated or not.
- Does not register new devices because the logic of adding devices to the `device_table` lives in the Telemetry Processor Lambda.
- Only sets expiry for devices in the `device_table` and not to the devices added on the `simulator_table`.

### 3.  Metadata Fetcher

![[Inherit Pwrcell asset commissioning - Metadata Fetcher.svg]]
The Metadata Fetcher Lambda will scan the `device_table` and evaluate if the `fleet_id` of each device is in the `ALLOWED_AUTO_CREATION_FLEETS` then the Lambda will send every 3 minutes a message to the CES queue to request metadata for each device on the `device_table`. If the device is simulated the Lambda will invoke the Fleet Builder Lambda.

The Pwrcell Gateway invokes the Fleet Builder Lambda then it will create the device and the site in Concerto.

**Flaws of this approach:**
- `ALLOWED_AUTO_CREATION_FLEETS` is totally deprecated since the single-tenant adapter. So, the Metadata Fetcher functionality is completely broken.
- The fleet builder creates devices and sites in Concerto, this logic should live in the Registrar Lambda.
- This Lambdas are not doing the expected behaviour of fetching the metadata from CES API, nowhere in the code is using the data fetched.

### 4. CES State Processor

![[Inherit Pwrcell asset commissioning - CES State.svg]]

The CES State Poller will scan the `device_table` and check for devices with `last_heard_at` less than 10 minutes and send a message to CES Queue requesting for system status. Then the CES State Processor is invoke by the Pwrcell Gateway with the response payload, this Lambda will update the `system_status` for each system on the response.

**Flaws of this approach:**
- If the asset has `status_called=True` then it will never update the system status again.
- Inconsistency on `last_heard_at` timeout 10 minutes here and 15 minutes in Fleet Builder Lambda.


# Proposed asset registration process


## High level implementation

Concerto contains information about the assets registered in Pwrfleet, these assets are added manually to Concerto by the NOC team. Concerto is the source of truth for existing devices, so, the adapter must maintain persistent information about these assets and populate them with the necessary information to allow the adapter to function properly.

![[Proposed Asset Registration - High level diagram.svg]]

Concerto provides information about the devices but not all the information required is provided. Thus, the Adapter must gather the remaining information from other resources, like: CES API, and Kinesis Stream records.

![[Proposed Asset Registration - Extra information gathering diagram.svg]]


## Implementation details


### Required asset information

- `remote_id`
	- `type`: String.
	- `description`: Unique identifier for an asset. 
	- `gathered from`: Concerto.

- `remote_state`
	- `type`: String.
	- `description`: remote state of an asset - possible values: [`ONLINE`, `OFFLINE`, `ERROR`]
	- `gathered from`: This is a dependant variable that can be determine as follow:
		**From Kinesis Stream record:**
		- If adapter has not received an `EnergyRecordSet` message from Kinesis stream for the past configurable interval (15 minutes) -> Mark remote_state as `OFFLINE`.
		- If the `StatusName` from the system equal `SUCCESS` **AND** the `InverterState` field in the `EnergyRecordSet` message is in between 0x7000 and 0x8000 then Inverter and Site Meter remote states gets mapped to `ONLINE`.
		- For any other StatusName (UNKNOWN, NEVER_CONNECTED, DISCONNECTED, or ERROR) mark the remote_state as `ERROR`.
		**From CES API:**
		- Since a device can stop sending telemetry for whatever reason the adapter needs to poll for system status continuously by querying CES API `{{root_url}}/sites/v1/{{site_id}}/devices/metadata`.

- `fleet_id`
	- `type`: String
	- `description`: Unique identifier for a fleet in Pwrfleet.
	- `gathered from`: Config parameter that can be passed as environment variable.

- `site_id`
	- `type`: String
	- `description`: Unique identifier for a site.
	- `gathered from`: Concerto.

- `site`
	- `type`: Dict.
	- `description`: Site information.
	- `gathered from`: Concerto.

- `rated_power`
	- `type`: Int.
	- `description`: Rated power of the asset.
	- `gathered from`: Concerto.

- `ac_coupled`
	- `type`: Bool.
	- `description`: Determines whether the asset is AC or DC coupled.
	- `gathered from`: CES API by querying `{{root_url}}/sites/v1/{{site_id}}/devices/metadata?inverterSetting=true` and check for `acPVPowerRating` within the response.
		- If **all** of the `acPVPowerRating` values are zero, mark the asset as DC coupled  
		- If **any** of the `acPVPowerRating` values are non-zero, mark the asset as AC coupled
		- If the device has inverters with both zero and non-zero values, log an error including the systemId with enough context for an operator to determine the underlying issue

- `is_simulated`
	- `type`: Bool.
	- `description`: Determine if an asset is simulated.
	- `gathered from`: Concerto.

- `connected_batteries`
	- `type`: List[Dict].
	- `description`: Connected batteries information.
	- `gathered from`: Concerto.

- `connected_photovoltaics`
	- `type`: List[Dict]
	- `description`: Connected photovoltaics information.
	- `gathered from`: Concerto.

- `last_heard_at`
	- `type`: Datetime.
	- `description`: Last time telemetry was received for an asset.
	- `gathered from`: Kinesis Stream record.

- `registered_at`
	- `type`: Datetime.
	- `description`: Date of the asset being register on the `DataTable`.
	- `gathered from`: Auto calculated.


### Registration Narrative



1. 

