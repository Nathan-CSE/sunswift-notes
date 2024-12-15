---
{"dg-publish":true,"permalink":"/1-sunswift/telemetry-notes/sr-8-telemetry/sr-8-telemetry-reference-document/","created":"2024-12-08T10:42:56.082+11:00","updated":"2024-12-15T22:50:00.047+11:00"}
---

# Project Scope
---
The SR8 telemetry system is designed to monitor and report the Sunswift vehicle’s real-time performance. Its scope encompasses data collection from multiple vehicle sensors, cloud-based data transmission, processing, storage, video data streaming, and insights via visual dashboards and historical data logs/reports.

While SR7’s telemetry focused primarily on ensuring reliable, 100% data availability, SR8’s telemetry system expands this foundation. The SR8 telemetry aims to enable bi-directional data flow, incorporate video data capabilities, streamline historical data access, and implement systematic updates—all while maintaining the reliability standard set by SR7.

In essence, SR8's telemetry builds upon the foundational work of SR7, advancing towards a more robust, scalable system with CI/CD pipelines and systematic data updates. This allows for a broader data spectrum and a stronger reporting framework.

### Proposed Timeline
- Operational Setup: Configure separate instances and accounts.
- Basic Data Reading: Enable data reading from essential sensors (e.g., vehicle speed, battery temperature).
- Efficiency Improvements: Streamline data handling processes.
- Stretch Goals: Implement advanced features, such as video data integration.

### External Requirements
- Latency: Ensure minimal delay in data transmission, targeting a maximum of 30 seconds from sensor data capture to its display on main telemetry and visualisation systems.
- Scalability: Design for high data volume handling, particularly for video data.
- Data Accuracy: High precision in data collection and reporting.
  

### Telemetry Components
There are two main aspects to the telemetry system:
1. Data Interfacing: This component manages the direct exchange of data with the vehicle, including receiving and sending data, syncing data, maintaining local storage, and logging data for later analysis.
2. Telemetry Operational Architecture: This component defines the integration of all telemetry subsystems into a unified framework, supporting seamless interaction between components. It also establishes robust, systematic processes for deploying updates and monitoring diagnostics.


# Data Interfacing
---
### Data Information
- Data Types
	- Sensors: This encompasses the various sensors installed on the vehicle that capture data essential to vehicle performance monitoring. For a detailed overview of the sensor types, refer to the Sensors Conceptual Design Document.
	- Video data: The SR8 telemetry system aims to integrate video data from vehicle-mounted cameras. While the specific camera models are still being determined, the system is designed to store video feeds to enhance real-time monitoring and analysis.
- Data Management: All sensor and video data will be transmitted to the cloud and stored in an external database via AWS S3. Local storage (such as a 2TB hard disk) would serve as interim storage until daily or sleep-state sync with AWS/cloud, enduring data integrity.
- Redundancy Setup: A double redundancy setup is planned to provide reliable data storage through both local and cloud backups. Data syncs at the end of each day or during the vehicle’s sleep state, supporting data availability even if connectivity issues arise.
- This setup draws inspiration from SR7’s triple redundancy system (pictured below), though it excludes the rear escort component.
  
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcwC2dGb0T5zTF39dYHBOgNO99n3mwS5h9JFet5HDeQJeBhJ6GZjOpfNF1DlKnzUalONTI1B0OwLSTJqKSVTISULMH1p3PobvA_V2De9gX7XG6BFsOXBLvEVoywquHAjgByg89uHw?key=Q2Y_RPBgfBC5WxLxHs-jHV1F)

### Bi-Directional Data Flow
- The SR8 telemetry system supports sending commands to the vehicle (e.g., remotely locking/unlocking doors) in addition to receiving data. This functionality enables real-time control adjustments and operational modifications as needed.
- This aspect has been trialled to a degree within SR7 using a router provided by one of our partners, Cradlepoint, however for SR8, this aspect aims to be fully implemented as part of the overall telemetry.

### Data Visualisation
- Grafana remains the primary visualisation tool for its familiarity with the team and robust functionality. The system will report errors (e.g., battery faults, abnormal temperature readings) and provide flexibility through a self-hosted setup, allowing custom configurations and tailored diagnostic features.
- The team aims to create a self-hosted Grafana instance as opposed to an AWS-managed instance as this would provide flexibility within our implementation as SR8 continues production.
- Extends to the telemetry infrastructure
- Revise discussions -> open to both implementation
  

### Systematic Data Logging
- To streamline data access for various departments, a centralised data logging solution is proposed. Departments could access historical data through Grafana accounts, potentially exporting data to a report format with visual graphs for ease of use. This solution may also involve a web application to automate CSV data exports from Grafana into easily interpretable reports.
- A key challenge with SR7's telemetry system is the slow retrieval of historical data, especially when accessing records older than one month on Grafana, which can often be unresponsive.
- To address this, a script was developed to directly query data from the S3 databases. However, this solution has been inconsistent and unreliable, falling short of providing the streamlined and efficient access we require. We are actively seeking a more robust and elegant solution to improve data retrieval performance for long-term records.
  

# Telemetry Operational Architecture
---
The operational architecture integrates various telemetry components with vehicle sensors, creating a cohesive system supporting real-time operations. This DevOps-like structure enables robust interfacing, systematic deployment, and consistent data handling.

### Instance Management
- There is ongoing deliberation regarding the approach to building the telemetry system. The team is evaluating whether to develop an entirely new system independent of the existing SR7 telemetry framework or to enhance the current SR7 structure. 
- If the team proceeds with the latter, significant cleanup would be necessary to remove obsolete data and unused elements within the existing framework.
- These discussions have introduced the possibility of establishing separate instances for SR7 and SR8. This approach would facilitate a cleaner system design, enable modular updates, and promote maintainability across both platforms.
- For example, within the below image there would be two separate accounts/instances, one for SR7 and another for SR8.
- Clean up Sr7 telemetry to get a better understanding, figure out the degree of separation between SR7 and SR8    

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfbHAFUW7Ay1YnJi4ZNPwNIpTIsHxOwrJZyyX6EqQmwp-3Fatcz8YUkfZEwsQGeOYcSrljmnpDafPcc3m4Hg7VNWZovAqL6GM6rCDn3el5pZwqVdHEPJnTkA6_ZFaKQVrZ23sFaQw?key=Q2Y_RPBgfBC5WxLxHs-jHV1F)

  
### Authentication/Encryption Service
- Maintain current AWS identity management and leverage encrypted MQTT packets through IoTCore for secure data handling.


### CI/CD Practices
- The SR8 system aims to utilise GitHub Actions integrated with AWS Terraform for continuous deployment. This setup automates updates to Grafana and AWS services, enabling a seamless modification process without manual navigation through AWS dashboards.

### Backup System
- We would also like to investigate a backup solution for the telemetry system in case of an accidental action -> similar to a time machine or something that backs up every x amount of days    
- There may be a solution already implemented but there is a lack of documentation for such an item

### Cybersecurity
- Potentially explore an additional security layer to protect sensitive information

### Telemetry Diagnostics
- To facilitate network monitoring and diagnostics, SR8’s telemetry will integrate CloudWatch to allow proactive network packet monitoring, supporting rapid issue resolution and seamless data continuity.
- Monitor packet loss between car telemetry node + data sync
- Although CloudWatch is part of SR7’s current setup, there is minimal documentation on accessing or interpreting its logging outputs (as illustrated below). Clearer procedural documentation would greatly benefit the team, improving efficiency in diagnosing and addressing potential network issues.

  
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeUZkFoYrWkd3kt60We1CllUI7n4vSoq3xrOwAzY8MAQ2EMCprkACHiAf_imbIEQIZPnSrtUt-4Mv4SfGqQs490bbjPCnFtM9jtbF9yWQzp-F4cAogVrrPoeEaEad5O12ojIxAL?key=Q2Y_RPBgfBC5WxLxHs-jHV1F)