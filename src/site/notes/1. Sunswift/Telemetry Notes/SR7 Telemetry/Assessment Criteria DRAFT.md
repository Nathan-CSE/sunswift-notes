---
{"dg-publish":true,"permalink":"/1-sunswift/telemetry-notes/sr-7-telemetry/assessment-criteria-draft/","created":"2024-12-08T10:42:56.058+11:00","updated":"2024-12-15T22:49:41.351+11:00"}
---

# Functionality and Performance
**Triple Redundancy Setup**
- Are all three Grafana instances (local, rear-escort, cloud-based) functioning as expected?
	- Can each Grafana instance be accessed individually?
	- Test proximity accuracy? (e.g. on track day, have someone at the workshop and verify if the cloud instance and rear escort instance are synchronised)
- Verify data syncing and redundancy mechanisms:
	- With all three instances, check the following:
		- Starlink outage (i.e. cloud instance goes down): Are both the rear escort and local instance still accessible and synchronised?
		- Worst-case scenario: If the rear escort and cloud instance fail/unsynchronised, does the local instance still report the latest car performance metrics?
		- *Note: Local instance needs to ALWAYS be accurate and report the latest data*
	- Data syncing: Verify how data is synchronised between all instances -> is it at the end of the day? how is it set up?
		- See if you can force instances to go out of sync, and see if the telemetry self-corrects itself

**Data Collection and Flow**
- Verify that all critical CAN data (e.g. `/car`, `/gps`, `/cruise`, `/sensors`, `/limit`) is accurately collected and processed
	- Is this even possible to verify? Since telemetry is the only thing reporting these, unless we can read the raw data that's being passed to the telemetry node?
	- Assess latency in data transmission across the network (see if we can use CloudWatch or something to measure packet times)

**System Responsiveness**
- Is it possible to simulate a stress test? (test responsiveness under typical and peak loads)
- Are there scenarios where an instance has to be rebooted?
- Review the impact of known bugs (e.g. odometer issue when the car is stationary) and their resolution status
	- Make a spreadsheet or something for issue tracking
- Error handling: Verify robustness of error detection, logging and recovery mechanisms (i.e. cloudwatch)

# Code and Architecture Quality
**Technical Debt with AWS Services**
- Identify areas of hardcoding (e.g. ignored topics, parameter fetching logic)
- Assess the feasibility of refactoring the telemetry node into smaller, more focused components
- *Note: I don't think the telemetry node has any major issues, but I think cleanup needs to be done with AWS services, especially in regards to instance-management (like sunswift-prod, sunswift-dev etc. - I don't think any of them have been touched since BWSC '23)

**Documentation**
- Review existing documentation for clarity and completeness, ensuring team members can understand the system without prior knowledge
	- What parts are lacking documentation?