
## Questions 1
1. Which assets are involve in this adapter? 
	- Inverters
		- Photovoltaics Panels (PV’s)
		- Batteries, anything else?
	- To create this asset in Concerto we must create and `Inverter` asset first and then assign the connected `batteries` and `PV’s`**.**
	
 >NOTE: Concerto target is just the INVERTERS, the sysMode determines how the batteries and PVC balance their load will supply that Event

2. Which is the role of each asset on the field?
	- PV’s provide DC energy to Inverter
	- PV’s store energy in Batteries
	- Inverter transform DC to AC either to export energy to the Grid or provide energy to the asset owner place.

3.  Which Goal Types does this adapter “performs”?
	- DR events only, that can be one of both:
		- `All-Called`: Give me everything you have (discharge 100%) 
		- `Capacity DR`: is a fix amount of energy requested by the grid, regardless if the battery doesn’t discharge 100%.

4. I found on the code something called `sysMode` that can have these values: `CLEAN_BACKUP` and `real_power`. Do you know what does it mean?
	- `CLEAN_BACKUP`: Recharge battery before an event starts
	- `real_power`:  Still some doubts about this.
	- Are the more?

5. What is a site?
	- A site is the container of the batteries, PV’s and Inverters of a location.

6. What is a fleet?
	- A fleet is the container of all sites within a company

7. What is the `channelType` on a `energyRecord`?

8. Why is this adapter a multi tenant adapter?
	- Because that’s how CES team is sending telemetry. They adds all fleets into one company

9. Is there any documentation on how to retrieve a username and password (credentials) to access CES API?
	- It is a manual step for now

10. How do CES create assets and associate them with a fleet_id? And how do they associated later with a concerto instance?
	- UUID that is bind to every company
	- We can retrieve the fleet_id by GET request using the company_Id

11. What is the information of a test asset (real asset (ggs office)) that we can use?
	- There is a ggs.qa company (test account shared by Nima) with only one asset:
		- asset_name: Inverter-X7602-03061
		- remote_id: f4574585-185d-41a8-b385-4860fd76c890
		- fleet_id: 5b7fa063-ae74-40eb-9a9e-17293783f5cc
	- Work with infra team to understand where the adapter is deployed (which aws account) - concerto02.qa
	- Make sure no-one is testing this asset, talk to @Umair.

12. How can we get telemetry for the fleet that the asset is associated with
	- Talk to @Fariz_Hajiyev at vpp-integrations-0 slack channel to connect CES telemetry to the adapter kinesis stream.

13. The `{{root_url}}/sites/v1/{{site_id}}/devices/metadata?inverterSetting=true` only returns only one system, despite the sites has multiple systems.
14. How is the registration process carry on?
15. There are currently two ways of identifying the system status by using the `InverterState` or the `StatusName` - why both? - Why the need to check if `InverterState` is greater than or equal to 0x7000 and less than 0x8000 then Inverter and siteMeter remoteStates gets mapped to “ERROR”?
16. Which are the possible values for a `systemStatus` UNKNOWN, NEVER_CONNECTED, DISCONNECTED, ERROR, SUCCESS?
17. What happens when there is miss-configuration from Concerto regarding batteries, site or pvs? should we log a warning or should we handle the differences?


## Questions 2
1. Can a system handle `real_power` and and `sell` mode at the same time?


## PWRCELL EPICS

Single Tenant Epic
[https://enbala.atlassian.net/wiki/spaces/PM/pages/2569109542/PWRcell+Adapter+Enhancement+-+support+multiple+Kinesis+streams+for+incoming+telemetry](https://enbala.atlassian.net/wiki/spaces/PM/pages/2569109542/PWRcell+Adapter+Enhancement+-+support+multiple+Kinesis+streams+for+incoming+telemetry)

AC/DC Epic
[https://enbala.atlassian.net/wiki/spaces/PM/pages/2504196108/PWRcell+Adapter+Control+and+Commissioning+Enhancements](https://enbala.atlassian.net/wiki/spaces/PM/pages/2504196108/PWRcell+Adapter+Control+and+Commissioning+Enhancements)

Pwrcell Epic v3.0
https://enbala.app.box.com/file/810193910556

CES API Docs
https://generac-ces-api.neur.io/doc

CES API Control messages workflow
![[CES API Controls behaviour.pdf]]