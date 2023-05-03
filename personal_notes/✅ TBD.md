### Overall
- 👷🏼‍♂️ Implement pydantic models
- 👷🏼‍♂️ Prepare meeting for Friday (see email from nick)
- ✅ Prepare questions for ids review on may 2
- ✅ Read ids
- ✅ Fix bug for simulated asset (wait for umber to update the ticket)
- ✅ Fix 401 unauthorized for Pwrcell gateway
- ✅ Read tickets for next sprint.
	- ✅ AP-604
	- ✅ AP-304
	- ✅ AP-305
- ✅ Address comment on AP-790

### Pwrcell Adapter
- 👷🏼‍♂️ Hoist IncommingTelemtryRole ARN on kinesis.yml outputs.
- 👷🏼‍♂️ Hoist api gateway url on template.yml outputs
- 👷🏼‍♂️ Embed vpc.yml into template.yml
	- 👷🏼‍♂️ Stablish subnets and securitygroup id as !Ref outputs from vpc deployment.
	- 👷🏼‍♂️ Delete subnets and securitygroup values from .toml file and template.yml
- 👷🏼‍♂️ Hoist deployed version, what if the source code is v2.0 but for an specific instance the deployment is v2.1?
- 👷🏼‍♂️ Remove version number from samconfig.toml and deployments.toml files
- 👷🏼‍♂️ Fix HttpAPI StageName should it be Prod or Dev?
- 👷🏼‍♂️ Remove “”warning: block quote” from adapter deployment on deployment.md file
- 👷🏼‍♂️ Remove stack_name s3_prefix and other params values from deployment.toml
- 👷🏼‍♂️ Rename all Pwrcell mentions to CES (template.yml, and env vars)
- 👷🏼‍♂️ Delete release_notes.md docs?
- 👷🏼‍♂️ Update secret_rotation.md docs
- 👷🏼‍♂️ Rename ces_state_poller to ces_registrar_poller from lambdas/ and template.yml
- 👷🏼‍♂️ Rename ces_state_processor to ces_registrar from lambdas/ and template.yml
- 👷🏼‍♂️ Change file name lambda_handler_test to registrar_poller_lambda_function_test.py
- 👷🏼‍♂️ Include capabilities on deploy.sh for kinesis stream and delete samconfig.toml file
- 👷🏼‍♂️ **Verify that fleet ids receive on telemetry processor is the same of the adapter fleet_id parameter**
- 👷🏼‍♂️ **Remove sort key from device_table and simulator_table**

