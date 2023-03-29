
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
- Fleet builder lambda - WIP, CORROBRATIONS TO BE DONE FOR DELETING THIS FILE.
	- We dont use the `instance_table` here. There is no need to map `fleet_ids` once we move towards single-tenant adapter. This lambda should be removed.

> ⚠️ Add deployment `<concerto_url>/graphql` param on `deployment_example.toml` file.

> 9 Files to be `modify`
> 4 Files to be `delete` - WIP


## PR's to be created
1. PR-remove-instance-table-from-template.yml
	- Remove Dynamodb `instance_table` from AWS architecture.
	- Remove all `environment_variables` related to `INSTANCE_TABLE` from template.yml resources.

2. PR-remove-concerto-instance-logic-from-concerto_gateway.py
	- Delete `instance_map` dict from code.
	- Use only one `ConcertoApi` object instance instead of looping through `instance_map` dict.
	- Refactor how we stop the threads for every concerto instance.

3. PR-remove-concerto-instance-logic-from-concerto_gateway/queue_worker.py
	- Remove loop for `instance_map` dict and send message to the unique `fleet_id`.

4. PR-remove-concerto-instance-logic-from-registrar_poller_lambda_function.py
	- Remove `concerto_instance` loop.

5. PR-remove-concerto-instance-logic-from-lambda_handler_test.py
	- Remove all functionality regarding `instance_table` variable from the tests.

6. PR-remove-concerto-instance-logic-from-registrar_lambda_function.py
	- Remove all functionality regarding `instance_table` variable from `lambda_handler`.
	- Remove `get_concerto_instance_by_url()` method from code.
	- Remove all functionality regarding `instance_table` variable from `update_device_in_device_table()` method.

7. PR-remove-concerto-instance-logic-from-registrar_lambda_function_test.py
	- Remove all functionality regarding `instance_table` variable from the tests.

8. PR-remove-concerto-instance-logic-from-concerto_api.py
	- Remove `instance_table` variable from `__init__()` constructor.
	- Refactor auth() logic.
	- Implement secret rotation for concerto token.
	- Delete `update_token.py` file.

9. PR-remove-concerto-instance-logic-from-test_helpers.py
	- Remove `create_instance_table` method from code.

10. PR-delete-fleet-builder-lambda -> WIP

11. PR-add-concerto_graphql_url-to-deployment.toml-and-template.yml
	- Add new param to deployment.toml file.
	- Add new param and env variables to template.yml