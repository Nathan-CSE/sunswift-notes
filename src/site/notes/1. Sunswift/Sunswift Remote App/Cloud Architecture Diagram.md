---
{"dg-publish":true,"permalink":"/1-sunswift/sunswift-remote-app/cloud-architecture-diagram/","created":"2024-07-22T22:35:27.507+10:00","updated":"2024-07-23T23:33:37.493+10:00"}
---

- [T2 Planning](https://docs.google.com/document/d/1RJXRiCVt_27EhIYQAgpv9bbyQgl6lvOZI0yVGBZEDr4/edit)
	- Diagram link: https://aws.amazon.com/blogs/iot/building-connected-vehicle-solutions-on-the-aws-cloud/
![connected_vehicle_architecture.png|Create something similar to this?](/img/user/Images%20&%20Attachments/connected_vehicle_architecture.png)
- Related links:
	- [[1. Sunswift/Telemetry Notes/GitHub Wiki Notes\|GitHub Wiki Notes]]: General high level notes from the [GitHub wiki](https://github.com/UNSW-Sunswift/SR-Telemetry/wiki/Telemetry:-An-Overview#telemetry)
		- EMBD 'HighLevel' project -> HighLevel is the name of the actual project not literally "high level"
			- Basically how the embedded sensors get data from the car and send it to a db/grafana
		- Potential issues/improvements to the Sunswift telemetry
	- [[1. Sunswift/Telemetry Notes/IOTCore ⁄ Greengrass Connection\|IOTCore ⁄ Greengrass Connection]]: Notes from the Amazon whitepapers about what IOTCore/Greengrass is
		- What is AWS IoT?
		- MQTT network/messaging protocol
		- Greengrass and its concepts (IoT Thing, core/client device, component etc.)
- Questions:
	- What are Amazon services (S3, Lambda, Timestream, EC2, Cognito, CloudWatch)?
	- What is CAN data? CANBus?
	- What is ROS2/ROSCube?
	- What even is IoT?

### What is IoT?
>The Internet of Things (IoT) describes the network of physical objects ("things") that are embedded with sensors, software and other technologies for the purpose of connecting and exchanging data with other devices and systems over the internet.
- [^ from oracle.com](https://www.oracle.com/au/internet-of-things/)

### Amazon Services
- This seems like a good starting place: [AWS services by category](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html)

#### [AWS S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)
- Amazon Simple Storage Service, is an object storage service that offers industry-leading scalability, data availability, security and performance.
	- It can be used to store and protect any amount of data for a range of use cases
	- An *object* is a file and any metadata that describes the file.
	- A *bucket* is a container for objects.
	- Storing data in Amazon S3 involves creating a bucket and specifying its name and AWS region. Data is then uploaded to that bucket as objects in Amazon S3
- It also provides management features to optimise, organise and configure access to data to meet user-specific needs and requirements
	- It has A LOT of features and custom objects/classes
	- E.g. Types of storage classes, storage management features, access management & security features, data processing features, storage logging & monitoring tools, analytics & insight features etc.
- Accessing Amazon S3 can be done via the Management Console, Command Line Interface (CLI), SDKs or REST APIs

#### [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- AWS Lambda is a serverless computing service ->  it runs code on a high-availability compute infrastructure
	- The code just simply needs to be supplied in one of the language runtimes that is supported by AWS Lambda
- There are also Lambda functions, which can be run by the Lambda service only when needed (e.g. in response to events)
	- Lambda functions can perform any kind of computing task and these functions are able to be uploaded to AWS Lambda
- [Serverless.com](https://www.serverless.com/aws-lambda#toc1)
- [Another AWS link](https://aws.amazon.com/lambda/features/)

#### [AWS Timestream](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/database.html#amazon-timestream)
- It is a managed time series database service for IoT and operational applications
	- Time-series data has specific characteristics, such as arriving in time order form, data is append-only and queries are always over a time interval
- Timestream efficiently stores and processes data by time intervals
	- It keeps recent data in its memory to quickly access it 
- It also provides the user with built-in analytics functions which allows users to identify the trends and patterns in their data in real-time
	- [GeeksForGeeks](https://www.geeksforgeeks.org/what-is-aws-time-series-databases-setup-amazon-timestream/)

#### [AWS EC2](https://aws.amazon.com/lambda/features/)
- Amazon Elastic Compute Cloud provides on-demand, scalable computing capacity in the AWS cloud -> it can be used to launch as many or as few virtual servers as needed, configure security and networking, and manage storage.
	- Users can configure their instances as needed (e.g. allocate the RAM, ROM and storage as required)
- An EC2 instance is a virtual server in the AWS Cloud and such instances are able to be created and managed by the following interfaces:
	- Amazon EC2 console
	- AWS Command Line Interface
	- AWS CloudFormation
	- AWS SDKs
	- Query API

#### [AWS Cognito](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html)
- It is an identify platform for web and mobile apps, allowing authentication and authorising users from the built-in user directory
- There are various ways to configure these authentication/authorisation protocols, through features such as user pools and identity pools

#### [AWS CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)
- CloudWatch monitors AWS resources and applications run on AWS in real time -> it can be used to collect and track metrics