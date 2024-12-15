---
{"dg-publish":true,"permalink":"/1-sunswift/telemetry-notes/iot-core-greengrass-connection/","created":"2024-07-02T20:22:29.508+10:00","updated":"2024-12-15T22:50:04.828+11:00"}
---

### [What is AWS IoT?](https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html)
- Provides device software to integrate IoT devices into AWS IoT cloud services

![what-is-aws-iot.png](/img/user/z.%20Images%20&%20Attachments/what-is-aws-iot.png)

### How devices and apps access AWS IoT
- **[AWS IoT Device SDKs](https://github.com/aws/aws-iot-device-sdk-python-v2)**: Build applications on devices that send messages to and receive messages from AWS IoT
- **[AWS Command Line Interface CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)**: Run commands for AWS IoT on Windows that allow users to create and manage thing objects, certificates, rules, jobs, and policies
	- It enables users to interact with AWS services using commands in the command-line shell
- **AWS IoT API:** Allows users to build IoT applications using HTTP or HTTPS requests -> these actions allow users to programmatically create and manage thing objects, certificates, rules and policies

### [MQTT](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html)
- MQTT (Message Queueing Telemetry Transport) is a lightweight and widely adopted messaging protocol
- AWS IoT Core support uses the [MQTT v5.0 specification](http://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)
	- MQTT v5.0 introduces scalability enhancements, improved error reporting with reason corde responses, message and session expiry timers, and custom user message headers
- The [AWS IoT Device SDKs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-connect-devices.html#iot-connect-device-sdks) supports the MQTT protocol and is the recommended way to connect devices to AWS IoT Core
	- [Connecting with MQTT using the AWS IoT Device SDKs](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html#mqtt-sdk)
	- [Protocols, port mappings and authentication](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html#protocol-port-mapping)

### [How AWS IoT Greengrass Works](https://docs.aws.amazon.com/greengrass/v2/developerguide/how-it-works.html)
- [AWS IoT Greengrass v2 interactive wiki](https://catalog.us-east-1.prod.workshops.aws/workshops/5ecc2416-f956-4273-b729-d0d30556013f/en-US)
- Allows users to program devices to act locally on the data they generate, run predictions based on machine learning models, and filter and aggregate device data
- It provides pre-built software modules called 'components' that allows users to easily extend edge device functionality
	- Components enable users to connect to AWS services and third-party applications at the edge
- After developing IoT applications, Greengrass allows users to remotely deploy, configure and manage those applications 

![how-it-works.png|not really sure what this diagram means](/img/user/z.%20Images%20&%20Attachments/how-it-works.png)

#### [Key Concepts for AWS IoT Greengrass](https://docs.aws.amazon.com/greengrass/v2/developerguide/how-it-works.html#concept-overview)
##### AWS IoT Thing
- A representation of a specific device or logical entity. Information about a thing is stored in the AWS IoT registry
	- A Greengrass core device is an AWS IoT Thing

##### Greengrass core device
- A device that runs the AWS IoT Greengrass Core software. Users can add multiple core devices to AWS IoT thing groups to create and manage groups of Greengrass core devices
	- The core device can process, filter, and aggregate data from client devices that connect to it
		- The core device can be configured to relay MQTT messages between client devices, the AWS IoT Core cloud service and Greengrass components
	- [Setting up AWS IoT Greengrass core devices](https://docs.aws.amazon.com/greengrass/v2/developerguide/setting-up.html)
	- When custom components are deployed to a core device, the device downloads the following resources to run the component:
		- *Recipe:* A JSON/YAML file that describes the software module by defining component details, configuration and parameters
		- *Artifact:* The source code, binaries or scripts that define the software that will run on the device - these can be made from scratch or made using a Lambda function, a Docker container or custom runtime
		- *Dependency:* Relationship between components enables the enforcement of automatic updates or restarts of dependent components

##### Greengrass client device
- A device that connects to and communicates with a Greengrass core device over MQTT 
	- The client device is also an AWS IoT thing
	- Client devices can use the AWS IoT Device SDK to get information about core devices to which they can connect

##### Greengrass component
- A software module that is deployed to and runs of a Greengrass core device -> all software that is developed/deployed with AWS IoT Greengrass is modeled as a component
	- Greengrass provides *pre-built public components* that provide features and functionality that you can use in your applications
		- Custom components can also be developed on a local device or in the cloud
	- After a custom component is developed, users can use the AWS IoT Greengrass cloud service to deploy it to single or multiple core devices

##### Deployment
- Refers to the process to send components and apply the desired component configuration to a destination target device, which can be a single Greengrass core device or a group of Greengrass core devices
	- They automatically apply any updated component configurations to the target and include any other components that are defined as dependencies
- [Deploy AWS IoT Greengrass components to devices](https://docs.aws.amazon.com/greengrass/v2/developerguide/manage-deployments.html)

##### AWS IoT Greengrass Core Software
- This refers to the software installed on a core device, it comprises the following:
	- **[Nucleus](https://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-nucleus-component.html):** Provides the minimum functionality of the AWS IoT Greengrass Core software. It manages deployments, orchestration and lifecycle management of other component
		- It also facilitates communication between AWS IoT components locally on an individual device
	- **[Optional components](https://docs.aws.amazon.com/greengrass/v2/developerguide/public-components.html)**: Enable additional features on edge devices. These include data streaming, local machine learning inference, or a local command line interface.

### [Features of AWS IoT Greengrass](https://docs.aws.amazon.com/greengrass/v2/developerguide/how-it-works.html#components-and-features)
- Software distributions:
	- The *Greengrass nucleus component*, containing the minimum installation of the AWS IoT Greengrass Core software -> manages deployments, orchestration, and lifecycle management of Greengrass components
	- Greengrass development tools, used to create, test, build, publish and deploy custom Greengrass components
- AWS IoT Greengrass Core software features:
	- Process data streams on the local device with automatic exports to the AWS Cloud
	- Support MQTT messaging between AWS IoT and components
	- Interact with local devices that connect and communicate over MQTT
	- Support local publish and subscribe messaging between components
	- Deploy and invoke components and Lambda functions
	- Manage component lifecycles, such as with support for install and run scripts
	- Perform secure, over-the-air software updates of the AWS IoT Greengrass Core software and custom components
	- Secure connections between devices and the AWS Cloud with device authentication and authorisation