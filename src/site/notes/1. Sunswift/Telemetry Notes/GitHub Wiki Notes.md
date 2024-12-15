---
{"dg-publish":true,"permalink":"/1-sunswift/telemetry-notes/git-hub-wiki-notes/","created":"2024-06-30T17:23:48.830+10:00","updated":"2024-12-15T22:50:02.455+11:00"}
---

### Sunswift Telemetry Overview
[GitHub wiki link](https://github.com/UNSW-Sunswift/SR-Telemetry/wiki/Telemetry:-An-Overview#telemetry)
- Telemetry is about data on the car: the collection, flow and visualisation of the data
- It provides a fun, challenging and unique structure and framework to learn with a mix of:
	- Interaction with low-level CAN data from sensors on the car
	- Requires a good high-level understanding of cars, our car, how it works and what anomalies to spot (i.e. high motor temps)
	- Writing client connection code in a large project and framework (EMBD-HighLevel, ROS2)
	- Handling networks and persistent connections (both internal, external, TCP, HTTPS, MQTT, etc.)
	- Using a variety of cloud tools (AWS, IoTCore, Timestream, EC2, Location Services, CloudWatch, Lambda, Cognito, etc.)
	- System administration (on a small scale) for Grafana
	- Large, complicated SQL queries from Grafana -> Timestream
	- Authentication, application and network security (certificates, roles, rules, SSL/TLS, x.509, SSO, SAML, OIDC, etc.)
	- UI/UX in Grafana, how to take a mammoth of data and make it "useful" 
- Questions:
	- What is CAN data? CANBus?
	- What is client connection code EMBD-HighLevel, ROS2? 
	- How to work with network connections (TCP, HTTPS, MQTT)? 
	- What are the cloud tools used (AWS, IoTCore, Timestream, EC2, Location Services, CloudWatch, Lambda, Cognito, etc.)?
	- SQL queries for Grafana -> Timestream
	- What about authentication, application and network security (AWS, IoTCore, Timestream, EC2, Location Services, CloudWatch, Lambda, Cognito, etc.)?

![architecture.png|overview of the entire telemetry system](/img/user/z.%20Images%20&%20Attachments/architecture.png)

### Telemetry EMBD 'HighLevel' Project
[GitHub wiki link](https://github.com/UNSW-Sunswift/EMBD-v2-HighLevel/wiki/Telemetry)
![telem-embd-flow.png|overview of the local telemetry on the car](/img/user/z.%20Images%20&%20Attachments/telem-embd-flow.png)
- [Diagram explanation](https://github.com/UNSW-Sunswift/EMBD-v2-HighLevel/wiki/Telemetry#the-iotcore--greengrass-connection):
	- Direct connection to IoTCore is the easiest way to write to IoTCore -> doesn't depend on the rear escort and can use any 4G connection the local router has
	- Rear Escort has a solid PtP connection to the Solar Car -> it provides external (Starlink) network to the Solar Car, however it is not guaranteed to be up
		- Local writing of the data to the rear escort means that people can still view telemetry data even if Starlink is down, without hindering the ROSCube performance
	- Writing locally to an InfluxDB and a local Grafana instance means that someone in the Solar Car can login and view/monitor telemetry data if the Rear Escort set up goes down
- Basically, there are three Grafana set ups to ensure that telemetry data is always able to be accessed and viewed

There are several aspects to the local telemetry:
-  The ROS2 telemetry node
- Interfaces with other ROS2 nodes
- Interfaces with AWS Greengrass
- Locally hosted Grafana and InfluxDB
- Network configurations within Sunswift7

**ROS2 Node**
- Subscribes to all (useful) events that get pushed around ROS2, manages the IoTCore connection, writes to InfluxDB and manages external parameters.
	- It ignores nodes that are not `/car`, `/gps`, `/cruise`, `/sensors` or `/limit`
- GPS Latitude data is handled separately -> [AWS documentation](https://docs.aws.amazon.com/location/latest/developerguide/tracking-using-mqtt.html)
- Odometer data is also handled separately -> [repo link](https://github.com/UNSW-Sunswift/EMBD-v2-HighLevel/blob/cfdce9eb0da08c1db9e946e2b777965d03cd0d20/src/vehicle_telemetry/iotcore_bridge/iotcore_bridge/iotcore_bridge.py#L213C24-L213C24)
	- Bug with the odometer where it constantly goes up when the car is stationary -> possibly worth looking into

### [[1. Sunswift/Telemetry Notes/IOTCore ⁄ Greengrass Connection\|IOTCore ⁄ Greengrass Connection]]
- [Wiki link](https://github.com/UNSW-Sunswift/EMBD-v2-HighLevel/wiki/Telemetry#the-iotcore--greengrass-connection)
- Need to research later

**psutil**
- Python library used to report system telemetry
- [Github repo link where the library was used](https://github.com/UNSW-Sunswift/EMBD-v2-HighLevel/blob/cfdce9eb0da08c1db9e946e2b777965d03cd0d20/src/vehicle_telemetry/iotcore_bridge/iotcore_bridge/iotcore_bridge.py#L424)

### Potential Issues and Improvements
- AWS Improvements: [Section Link](https://github.com/UNSW-Sunswift/EMBD-v2-HighLevel/wiki/Telemetry#issues-and-ideas)
	- Credential management for AWS IoTCore. This should be automated, without having to create and store certificate files. Similar to how Greengrass works.
	- Rewriting this node in cpp. Big project, huge potential for performance boosts.
	- InfluxDB connection management - this should also be "token-less", or at least not a token in plain sight.
	- Persistent odometer isn't working
	- Ability for this node to run on a different machine if we were to break off into "drive-control" and "other" machines.
	- Greengrass connection logic is a bit scrappy and can be tidied up.
	- Parameter fetching and setting spins up "new nodes" every time it is run. Research an alternative service call that doesn't have the overhead of extra nodes getting made each time.
	- Split telemetry "reporting" from the parameter setting and more telemetry "logic" to split up what is becoming a larger node.
	- Reporting logs through telemetry?
	- Reduce the initialisation time (5 second sleep, etc.)
	- Less hard-coding of what topics to ignore
	- More back-and-forth interaction with AWS Cloud
	- Remove bulky AWS IoT connection libraries for lighter-weight MQTT library
	- Use Asynchrnous InfluxDB client
	- `asyncio` in Python is a wonderful albeit complicated library and syntax to get your head around at first. If this node remains in python, using that could be nice [https://docs.python.org/3/library/asyncio.html](https://docs.python.org/3/library/asyncio.html)
	- [Some random link from the T2 project planning page]([Building Connected Vehicle Solutions on the AWS Cloud | The Internet of Things on AWS – Official Blog (amazon.com)](https://aws.amazon.com/blogs/iot/building-connected-vehicle-solutions-on-the-aws-cloud/))
- Other considerations:
	- James was talking about how embedded was able to ssh into the sunswift vehicle 

### Extracting Data from Timestream
- https://github.com/UNSW-Sunswift/SR-Telemetry/wiki/Extracting-Data-From-Timestream
	- Also contains notes on challenges faced during BWSC and what we could improve