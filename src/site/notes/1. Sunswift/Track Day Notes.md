---
dg-publish: true
---
### Grafana Fixes
- Solar current drops in/out
- Need to double check odometer -> was changing units strangely on track day
- Fix GPS maps to prevent data point bloat -> limits were an issue
- Fastest speed per lap doesn't really work
- Perms got removed

### Personal
- Need to become more familiar with Grafana boards + the whole telemetry set up for Sunswift
- Getting data is really important -> need to ensure that data is consistently appearing on Grafana at all times, reliably
- There are a lot of refinements/refactoring to the code that would be more beneficial to SR7 and telemetry as opposed to working on a mobile app 
	- [[1. Sunswift/Telemetry Notes/GitHub Wiki Notes#Potential Issues and Improvements\|Link to Improvements]]


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/1-sunswift/sunswift-fastest-lap-speed/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==

# Excalidraw Data
## Text Elements
Problem: Adding a top speed to each lap on Grafana

To do:
* Find where the lap time information is stored -> what determines a lap?
* Find the relevant data speed entries within that time range
* How am I going to link the data information between both databases and then process the 
  information to present the fastest speeds per lap 
Relevant Query Headings:
* GPS Speed & time (this is in the standard query db)
* Lap times db and tables -> probably a separate db, need to find where this data is stored 
Questions to ask:
* How are lap times calculated/configured? Would finding the top speed for each lap be done in retrospective or live?
* Also where is the data for the lap times stored? 
If databases are separate:
* For each lap -> define the upper and lower bounds, as entries in the table, then extract the GPS data from that data range and get the fastest speed
* Something like:

WITH merged_data AS (
    SELECT 
        q.gps_speed,
        q.time,
        l.lap_time
    FROM 
        queries q
    LEFT JOIN 
        lap_times l 
    ON 
        q.time <= l.time
    ORDER BY 
        q.time DESC
)
SELECT 
    lap_time,
    MAX(gps_speed) AS max_gps_speed
FROM 
    merged_data
GROUP BY 
    lap_time;

 
This makes it so that we associate each query entry from queries 'q' to the earlier lap timestamp -> each row in
the 'queries' table, is mapped to a particular lap time
from 'lap_times' 
From the combined table, we group the results by lap_time and get the maximum GPS speed from each group 
Grafana will let me query two separate databases, but I'm not sure
how I would go about joining both of them together
* to join, I need to use the grafana transformations
 
Query 1:
SELECT 
    avg(case when measure_name = '/car/speed/gps' then measure_value::double end) as "GPS Speed",
    bin(time, parse_duration('$__interval_ms')) as time 
FROM 
    $__database.$__table 
WHERE 
    measure_name IN ('/car/speed/gps') 
    AND $__timeFilter
ORDER BY 
    time DESC

^ This query returns the "gps speed" of the car in the first column, followed by the time corresponding to each recorded gps speed in the second time, I need to figure out:
* What the functions do, what is bin(), $_timeFilter?
    * bin(): This part groups the 'time' field into bins of size determined by the Grafana variable `$__interval_ms`
    * $__timeFilter: This is a Grafana variable that dynamically applies a time filter to the query based on the time selected in the Grafana dashboard 
Query 2:
SELECT 
  to_milliseconds(lag(time, 1) over (order by time desc) - time) as "Lap Time", 
  time as "starting time"
FROM $__database.$__table
ORDER BY 
  time DESC


^ This query returns the lap time for each lap in the first column in milliseconds(?), followed by the starting time in the second column, with time in descending order (last recorded lap time at the top) 
I think that what I need to do, is somehow map lap times in dashboard 1 to GPS Speed times in dashboard 2. So:
    * For each entry in the GPS Speed database, check which timeframe/time period it's between then assign it to a certain lap time based on that time period
    * E.g. for GPS speed 57.9 km/hr at 15:20:20, it would be assigned to lap time 195584 at 15:17:37 since it's greater than that time, but less than the start of the next lap which is at 15:20:53


UPDATE:
* The issue is defining the intervals between table entries -> these intervals determine how the two queries are joined, but it seems like they are dependant on grafana so it's hard to get a reliable join on them
* For instance there might be an interval that goes for 1 minute, or 20 seconds, both of which are unreliable measures to get the fastest speed per lap
* I can still probably make rough fastest speed per lap, but it'll probably very inaccurate 
Adjust this to change the interval measured
(higher max data points = lower interval = more accurate readings for fastest GPS speed) 
To find the fastest time:
* Set the max data points to a high value for accurate readings
* Need to figure out a way to find the fastest speed in the interval, remove the rest 
## Embedded Files
5e6bb2c42c01d57ec6ecc1d6379cd701efb23c6f: [[Pasted Image 20240612174153_931.png]]
ec9d852de0ff502dd1b1df5b2f319d38e60fbb57: [[Pasted Image 20240624092036_841.png]]



</div></div>


