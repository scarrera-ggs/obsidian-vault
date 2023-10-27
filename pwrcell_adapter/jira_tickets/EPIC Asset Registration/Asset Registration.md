## Changes on inherit process

### Telemetry processor
- Remove the ability to create new devices.
- Update `last_heard_at`, `system_status`, `sys_mode` for devices that exists in DynamoDB.

### ❌ Registrar poller
- Add the ability to query for all assets.
- Add the ability to query for remaining assets.
- Send only adapter related devices to Registrar Lambda.

### Registrar
- Add the ability to create new devices according to Concerto information.
- Remove the ability to create devices in simulator table.
- ❌ Remove the ability to query for next paginated assets.
- Remove ability to create new devices in device table for simulated assets.
- Improve logging description for errors, warnings and info actions.

### CES state poller
- Add the ability to query system status for all devices in DynamoDB, not only for offline assets.
- Add the ability to query system status multiple times for devices in DynamoDB, not only once.
- Remove `status_called` attribute from code source for devices.

### CES state processor
- Update system status if the gather value from CES response is different from what it is on DynamoDB and send updated device to synchronizer.

### CES Coupling processor
- Send device to synchronizer