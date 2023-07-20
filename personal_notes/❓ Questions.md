## To Do
- Concerto maintenance could take up to 2 hours.
- Pwrcell v2.0 Release version notes
- list subscriptions issues


## To Ask
- talk about graphql transaction for create site, create inverter, associate inverter with site


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
	- Standard Operational Procedure


## Other

### Steps To Deploy Adapter And Test It
- Put new record on instance table
- Create assets on concerto instance for Pwrcell Adapter
- Create asset on device table with the information that the telemetry_processor will use.
- Create VPP and add asset to VPP
- Create event
- Check device table for the asset created on concerto.


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

=H3/86400+DATE(1970,1,1)




Registration overview
