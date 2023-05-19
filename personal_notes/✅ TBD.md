## Overall

### Soon
- Conifgure postman environment.


### Pwrcell Adapter
- 👷🏼‍♂️ Hoist IncommingTelemtryRole ARN on kinesis.yml outputs.
- 👷🏼‍♂️ Hoist api gateway url on template.yml outputs
- 👷🏼‍♂️ Embed vpc.yml into template.yml
	- 👷🏼‍♂️ Stablish subnets and securitygroup id as !Ref outputs from vpc deployment.
	- 👷🏼‍♂️ Delete subnets and securitygroup values from .toml file and template.yml
- 👷🏼‍♂️ Remove version number from samconfig.toml and deployments.toml files
- 👷🏼‍♂️ Fix HttpAPI StageName should it be Prod or Dev?
- 👷🏼‍♂️ Remove “”warning: block quote” from adapter deployment on deployment.md file
- 👷🏼‍♂️ Remove stack_name s3_prefix and other params values from deployment.toml
- 👷🏼‍♂️ Rename all Pwrcell mentions to CES (template.yml, and env vars)
- 👷🏼‍♂️ Rename ces_state_poller to ces_registrar_poller from lambdas/ and template.yml
- 👷🏼‍♂️ Rename ces_state_processor to ces_registrar from lambdas/ and template.yml
- 👷🏼‍♂️ Change file name lambda_handler_test to registrar_poller_lambda_function_test.py
- 👷🏼‍♂️ **Verify that fleet ids receive on telemetry processor is the same of the adapter fleet_id parameter**
- 👷🏼‍♂️ **Remove sort key from device_table and simulator_table**
- 👷🏼‍♂️ Implement more detail on pydantic datamodel for CESRequest class
```text
class CESControlMessage(CustomBaseModel):
	    controlMessageId: str
	    startTime: datetime
	    duration: int
	    systemMode: str
	    cancelIfOvershadowed: bool = False
	    cancelOnExternalChange: bool = False
	
	
	class CESControlMessages(CustomBaseModel):
	    control_messages: List[CESControlMessage]
	
	
	class CESCancelMessages(CustomBaseModel):
	    controlMessageIds: List[str]
```
- 👷🏼‍♂️ Improve Concerto auth process by creating a new lambda to retreive token (as we do with CES auth)