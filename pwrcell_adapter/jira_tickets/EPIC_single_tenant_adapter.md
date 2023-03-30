
## Files related with instance_table

- template.yml
- concerto_gateway.py
- concerto_gateway/queue_worker.py
- registrar_poller_lambda_function.py
- lambda_handler_test.py
- registrar_lambda_function.py
- registrar_lambda_function_test.py
- concerto_api.py
- test_helpers.py
- telemetry_processor_lambda.py
	- remove `fleet_id` message attribute.
- Add deployment `<concerto_url>/graphql` param on `deployment_example.toml` file.

> 9 Files to be `modify`
> 4 Files to be `delete` - WIP


## PR's ordered

### PR-set-concerto-root-url-as-deployment-parameter
By including this parameter the behavior of the adapter will be influenced in `concerto_api.py`  and `concerto_gateway.py` files. 

The `concerto_api.py` is in charge of creating an object that can hanlde authentication and send post requests to the Concerto Api. This parameter should be pass as `env_variable` for every lambda that will instanciate this class (`concerto_gateway.py`).

Steps to consider:
- Include `ConcertoRootUrl` in `deployment.toml` and `template.yml` files, pass `ConcertoRootUrl` as `env_variables` to `concert_gateway` lambda.
- Test that asset is still created on the `device_table`.

### PR-use-concerto-credentials-secrets-to-instanciate-concertoApi
The `concerto_gateway.py` lambda is currently retreiving concerto credentials from `instance_table`, this behaviour must change before deleting the table. Now, the lambda should grab this values from `ConcertoCredentials` secrets and pass them to the `ConcertoApi` class every time we instaciate the class.

Also, the current logic retreive the concerto `token` and update it on the `instance_table`. This behaviour must change in order to implement a full secret rotation to retreive and store the `token` on `ConcertoCredentials` secrets.

Steps to consider:
- Create `ConcertoSecrets` class to manage secrets retreiving and rotation.
- Retrieve `ConcertoCredentials` from secrets manager, pass `SecretId` as `env_variables` to `concert_gateway` lambda to grab secrets values and successfully instanciate `ConcertoApi` class to make proper requests and auth process.
- Assert username and password values on `CocnertoApi` constructor.
- Test that asset is still created on the `device_table`.
- Implement secret rotation on ConcertoApi `auth()` method.
- Delete `update_token()` file and method from `concerto_api.py`.
- Test that asset is still created on the `device_table`.

> NOTE: Now every item on `instancetable` does not requiered username, password, and token.

### PR-remove-instance-table-dependency-from-concerto_gateway
Right now the `concerto_gateway.py` is using a mechanism to identify and map concerto instances to send messages to the corresponding concerto intstance, this should be remove because now the adapter only handle one concerto instance.

Also, the messages send to the concerto sqs queue are populated with `fleet_id` and `concerto_url` as message attribute, this attributes are no longer needed.

Steps to consider:
- Delete `instance_map` dict from `concerto_gateway.py`.
- Use only one `ConcertoApi` object instance instead of looping through `instance_map` dict on `concerto_gateway.py`.
- Refactor how we stop the threads for every concerto instance on `concerto_gateway.py`.
- Remove loop for `instance_map` dict and send message to the unique `fleet_id` from `concerto_gateway/queue_worker.py`.
- Test that asset is still created on the `device_table`.

### PR-remove-concerto-instances-functionality-from-code
Now that we will only use one concerto intansce there is no need to loop over all the concerto instances. This behavour should be change on every file that loops concerto instances.

The `instance_table` is used on couple of files to perform the expected behaviour. However, this is no longer needed so we should remove the logic and change the lambda behavour to incorporate a single concerto instance without the need of querying the `instance_table`.

Steps to consider:
- Remove all functionality regarding `instance_table` variable from `registrar_lambda_function.py`.
- Remove `get_concerto_instance_by_url()` method from `registrar_lambda_function.py`.
- Remove all functionality regarding `instance_table` variable from `update_device_in_device_table()` method in `registrar_lambda_function.py`.
- Remove concerto instances looping from:
	- concerto_gateway.py
	- concerto_gateway/queue_worker.py
	- registrar_poller_lambda_function.py
- Test that asset is still created on the `device_table`.

### PR-remove-fleet-id-and-concerto-url-from-messages-attributes-sent-concerto-sqs
Messages sent to concerto sqs queue are oftenly attached with `fleet_id` as message attribute for the `concerto_gateway/queue_worker.py` to map if the `fleet_id` is present on each concerto instance. The need of this is no longer useful since we are moving to a single `fleet_id` for every asset.

Steps to consider:
- Remove `fleet_id` message attribute from:
	- telemetry_processor_lambda.py
	- registrar_poller_lambda_function.py
	- registrar_lambda_function.py
	- concerto_gateway/queue_worker.py
	- pwrcell_metadata_fetcher/pwrcell_metadata_fetcher_lib/utility.py
	- fleet_builder_lambda_function.py
		- remove `fleet_id` message attribute from `create_device_on_concerto()` 
		- remove `fleet_id` message attribute from `create_site_on_concerto()` 
		- remove `fleet_id` message attribute from `update_site_on_concerto()` 
		- remove `get_fleet_id_attribute()` method from `fleet_builder/fleet_builder_lib/utility.py
- Delete `get_message_attribute` and `test_get_message_attribute` file.
- **CLEANUP TESTS** to incorporate new messages without `fleet_id` message attribute.
- Test that asset is still created on the `device_table`.

### PR-cleanup-code
The Dynamodb `instance_table` must be removed by the end of this epic. So that the depency from this table is completely decouple from the adapter.

Steps to consider:
- Remove `instance_table` resource form `template.yml`
- Remove all `env_variables` relates to `INSTANCE_TABLE` from aws resources.
- Test that asset is still created on the `device_table`.
- Remove `create_instance_table()` method from `test_helpers.py`.
- Successfully run tests.

## Asset comissioning process - WIP
Asset comissioning is a multi-step process that involves:
1. step 1
2. step 2
3. step 3