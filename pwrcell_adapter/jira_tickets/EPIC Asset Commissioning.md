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


## Proposed Asset Commissioning

![[Proposed Pwrcell asset commissioning.svg]]
- Protobuf records are received by Telemetry Processor Lambda; If the device exists in the `device_table` telemetry will be sent to concerto for batteries, photovoltaics, site meter, and inverter.
- Registrar Poller will poll for devices registered in Concerto Instance every 1 minute, then Concerto Gateway will invoke Registrar Lambda with the all asset paginated response.
- Registrar Lambda will: 
	- Filter devices that belongs to the Pwrcell Adapter and writes them into the `data_table`.
	- Sends message to CES Queue requesting metadata for each device stored on the table, then the Pwrcell Gateway Lambda will invoke the `device_metadata_processor` to identify if it is a AC/DC coupled system.
	- If `AUTO_COMMISSIONING` is enabled the Registrar will create the inverter and the site in Concerto, else, avoid this step.
- CES State Poller will request `system_status` every 15 minutes, later the Pwrcell Gateway will invoke CES State Processor with the response payload.
- CES State Processor will update `remote_state` of each device that exists in the `device_table` based on the `system_status` received from the response payload.

**Advantages of this approach**
- Complete isolation of processes between lambdas
- One source of truth: Concerto.
- Removed extra logic and unused lambdas.
- Removed multiple sources of truth: Concerto, and CES API.


### Questions to ask

- Which should be the timeout limit for setting an asset as `OFFLINE` 10 or 15 minutes?
- How frequent the `system_status` should be polled? (right now is every 15 minutes).
- 