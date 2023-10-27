## To Do
- (LATER) List subscriptions issues 
- Complete team documentation on JIRA


## To Ask
- (LATER) Talk about graphql transaction for create site, create inverter, associate inverter with site


**Team retros**
- Create toy-example projects for common use utilities like: gql subscription, caching data (elixir)
- Not knowing which are the actual deployments for some adapters. i.e. PWRCell
- Which are the "oficial" confluence pages for releases and deployments?



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
- **VS Code Formatting**
```json
{
	"[python]": {
		"editor.formatOnSave": true,
		"editor.codeActionsOnSave": {
			"source.organizeImports": true
		}
	},
	"python.formatting.provider": "black",

	"[elixir]": {
		"editor.defaultFormatter": "JakeBecker.elixir-ls", %%ElixirLS extension%%
		"editor.formatOnSave": true,
		"editor.formatOnType": true,
		"editor.insertSpaces": true,
		"editor.tabSize": 2,
		"editor.trimAutoWhitespace": false,
		"editor.wordBasedSuggestions": false,
		"files.insertFinalNewline": true,
		"files.trimFinalNewlines": true,
		"files.trimTrailingWhitespace": true
	}
}
```

- **Transform unix datetime in excel**
	- =H3/86400+DATE(1970,1,1)
