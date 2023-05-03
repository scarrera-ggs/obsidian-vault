  
## To Ask
- Should we use a async/await on requests?

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

## For Later
- Why AWS costs are increasing?

## To Remember
- 🧠 Update onboarding process proposal?
- 👷🏼‍♂️ What’s the difference between DR and PR goal types? What kind of assets are in those goal types?
- ✅ How can I SSH to concerto and check mini adapters?
	- SSH concerto-adapters.qa
- 👨🏻‍💻 git clean -d -x -f src

- **Acronyms**
	- Emergency Solar Management
	- Scope of Work
	- Commercial Exporter 
	- Master Service Agreements
	- Embedded Metric Format
	- State Of Charge