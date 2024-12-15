---
{"dg-publish":true,"permalink":"/1-sunswift/telemetry-notes/sr-8-telemetry/personal-notes/","created":"2024-12-08T10:42:56.081+11:00","updated":"2024-12-15T22:49:57.684+11:00"}
---

## 3/12
- Going to read over the stuff that Jane put together (i.e. the presentation) and then figure out what AWS stuff would be relevant for us
	- Also going to just recall the important decisions that Jane and stuff told us about

*AWS Meeting*
- Data visualisation: They said that the AWS-managed instance doesn't seem to impose issues or constraints, and the AWS managed instance syncs up better with timestream
	- We (Sunswift) don't really know what custom stuff we would want for SR8, and we don't know the limits of the AWS-managed instance
- Systematic data logging: We can look into querying the influxDB layer instead of accessing data from Grafana 
	- I think Tharun made a script that's meant to query influxDB directly, but others seem to have issues with the script
- I remember we asked them about our approach, and they recommended setting up the CI/CD pipelines first and also sorting out the instances (figuring out the degree of separation between SR7 and SR8)

### SR8's Future Goals/Actionables
1. **CI/CD Pipeline**:
	- Begin with setting up basic pipelines for tasks such as telemetry component updates and data visualisation (e.g., deploying changes to Grafana).
	- Use AWS-provided resources and examples to streamline the process.
2. **Instance Management**:
	- Evaluate separating SR8 and SR7 telemetry instances for modularity and maintainability.
	- Document the current SR7 architecture and decide on elements to retain or discard.
3. **System Design Decisions**:
	- Prioritise areas needing clarity, like video streaming and bi-directional commands. Research AWS IoT Core and Kinesis Video Streams to understand implementation possibilities.
		- What do we want out of these features/components and 
4. **Collaborative Review**:
	- Organise team discussions around specific challenges (e.g., sensor integration, data storage, and retrieval systems).
	- Assign responsibilities for researching and presenting options (e.g., one member focuses on video data while another explores telemetry diagnostics).

### SR7 Maintenance
> To prepare SR7 for BWSC '25, we need to test that the triple redundancy setup still works and clean up technical debt (unused instances etc.) that was left over from the last BWSC.
- To help with the above, we can also reach out to Josh Bramley who his input and what could be improved 
- I think the best approach here is:
	- Conduct an initial assessment of SR7 telemetry:
		- Does triple redundancy still work? Is each instance able to be accessed by the team?
		- Where is technical debt? What can be removed? What can be documented better?
	- Create a detailed report outlining what works, what needs fixing and what can be streamlined
- After we conduct that initial assessment, we can ask how we can implement fixes and stuff, and once we have a better understanding of how SR7 works, we can ask for input for SR8 stuff

### Combining both approaches
- Ideally, I would like to split both problems into separate workflows, but we don't have the manpower to do that, so I'll have to make do and see how I can combine both into one workflow

# Timeline
---
#### SR7 Telemetry Review/Fixes
- Conduct initial assessment of SR7 telemetry (assess thoroughness of documentation, note technical debt etc.)
- Get Josh's input/help on assessment, and understanding how to properly diagnose and test the separate instances

#### Implement some SR8 telemetry features into SR7 for streamlining
- After we fix/verify triple redundancy etc. then we can look into implementing some Github actions CI/CD stuff to systematically deploy updates or changes
	- This will help us figure out how to do these changes for SR8
- Figure out the degree of separation between SR7 and SR8 telemetry -> do we want some data to be accessible by both? or are both completely isolated instances?

#### Full SR8 telemetry development
*Note: I figured it would be best to fully dive into SR8 development after we've streamlined SR7's telem, since that would give us a better understanding of how it all works*
- System design decisions -> looking into provided AWS services and resources, and figure out what best to go from here

*Note: Some of these stages can be done out of order as well, e.g. whilst Josh is helping us diagnose/fix SR7's telemetry, we can also ask for his input on what we have so far for SR8's telem for future reference.*
