  
## To Ask
- The `{{root_url}}/sites/v1/{{site_id}}/devices/metadata?inverterSetting=true` only returns only one system, despite the sites has multiple systems.
- How is the registration process carry on?
- There are currently two ways of identifying the system status by using the `InverterState` or the `StatusName` - why both? - Why the need to check if `InverterState` is greater than or equal to 0x7000 and less than 0x8000 then Inverter and siteMeter remoteStates gets mapped to “ERROR”?
- Which are the possible values for a `systemStatus` UNKNOWN, NEVER_CONNECTED, DISCONNECTED, ERROR, SUCCESS?
- What happens when there is miss-configuration from Concerto regarding batteries, site or pvs? should we log a warning or should we handle the differences?


- look at vpp docs to run concerto locally (https://github.com/Enbala/vpp).


## For Later
- Why AWS costs are increasing?


## To Remember
- 🧠 Update onboarding process proposal?
- 👷🏼‍♂️ What’s the difference between DR and PR goal types? What kind of assets are in those goal types?
- ✅ How can I SSH to concerto and check mini adapters?
	- SSH concerto-adapters.qa
- 👨🏻‍💻 git clean -d -x -f src
- 👨🏻‍💻 IEEE-754 floating number standards

- **Acronyms**
	- Emergency Solar Management
	- Scope of Work
	- Commercial Exporter 
	- Master Service Agreements
	- Embedded Metric Format
	- State Of Charge
	- Outbound Control Adapter


## Other

### Steps To Deploy Adapter And Test It
- Put new record on instance table
- Create assets on concerto instance for Pwrcell Adapter
- Create asset on device table with the information that the telemetry_processor will use.
- Create VPP and add asset to VPP
- Create event
- Check device table for the asset created on concerto.

### To Do
- Create documentation for migrating existing deployments.
	- Deploy adapter (point to new adapter deployments).
	- Steps to delete concerto instance from instance table.
	- Check that assets are updated successfully.
-   Create documentation for testing the adapter.

### VS Code Formatting
```json
{
	"[python]": {
		"editor.formatOnSave": true,
		"editor.codeActionsOnSave": {
			"source.organizeImports": true
		}
	},
	"python.formatting.provider": "black",
}
```
