# Simple Application Whitelisting Host/Network

1. Start with an empty whitelist

2. Apply a policy to log everything not in whitelist

3. Use logs to generate a whitelist/Update Whitelist

4. Modify policy to block everything not in whitelist

5. Review new logs

## Host Side
Policy could be in an endpoint tool like:
Microsoft AppLocker
Windows Defender Application Control
Carbon Black

   - Investigate blocked files
   - Update Whitelist as needed

## Network Side
Could be configured as:
Firewall Rules (PAN-OS, Cisco, etc.)
IDS/IPS rules (Suricata)
Detection Scripts/policies (zeek)

   - Investigate new ports/hosts
   - Update whitelist as needed

Will generate a lot of logs but with data processing, we will see we may be only using 500 ports
These 500 ports may belong to 10 different services

## Network Baseline

Minimum or starting point used for comparisons

### How to create and use one?
- Make a quantitative observations that describe host behavior, host interactions, protocol usage etc.
  
- Record those observations in a standard, easily consumed format
  
- Analyze the data, look for patterns and deviations


### Why we need to baseline:

+ Network Performance Baseline provides a fundamental reference point for what normal network operations look like.

+ Enables quick identification and troubleshooting of actual performance issues when they arise, differentiating real problems from expected fluctuations.

+ Network metrics like bandwidth utilization, latency, and packet loss constantly fluctuate, requiring context for proper interpretation.
-Without baseline, cannot make sense if current values are good or bad

+ A baseline captures normal ranges for bandwidth, latency, and packet loss.

+ Creates a clear picutre of what constitutes expected network health.

+ To establish effective baseline, need to systematically collect data from network over an extended period

Involves monitoring key performance indicatiors using specialized netowkr management tools

Essential to capture data during different times of day, days of the week and periods of peak and low usage to account for natural variations, regularly reviewing and updating this data ensures baseline remains relevant as network evolves.

This proactive approach will drastically reduce Mean Time To Repair (MTTR) by enabling proactive identificaiton of issues before they escalate.

Able to spot early warning signs and address them.
Also aids in capacity planning, allow data-drivne decisions about when and where to upgrade infrastructure, ensuring network can meet future demands.


### Baselining a Network with Zeek
Network defenders need to be able to understand what hosts/devices exist in the environment, what OS they run
Client applications/services, other clients and services they interact with internally and externally

Most protocol metadata in qualitative
- IP address
- User Agent
- URL
- Domain
- Port Numbers

Byte and Packet Counters are quantitative
Other quantative measures:
Duration - also can be found in conn log stream
Interval
Rate

Still need additional context to gain an understanding of "normal"

Other data measures in log streams:
Byte 
Packet Counters

## Dashboards for baselines

### Creating Dashboards
To create dashboards like the ones I mentioned (for network baselining in Security Onion / Kibana), you build them in three steps:

Create a visualization
Save it
Add it to a dashboard

Step 1 — Create a Dashboard
You’re already in the right place.
Go to Analytics → Dashboards
Click Create dashboard (top right)

Click Create visualization
This opens the visualization builder (Lens).

Step 2 — Select the Data
Choose the Security Onion data index.

Usually something like:

logs-*
so-*
securityonion-*

If you are using Security Onion default dashboards, the correct data view is usually:

so-*
Step 3 — Build Visualizations (Baseline Metrics)
.
#### Top Talkers (Source & Destination IPs)
-shows wich hosts generate the most traffic

ex. top Source IPs by connection count
    top Destination IPs by connection count

Why: Quickly shows which machines are the most active
     Helps detect compromised hosts doing scanning or exfiltration

typical chart types: 
- bar chart
- table of top 10 IPs

Visualization: Bar Chart

Fields:

Setting	Value
X-axis	source.ip
Y-axis	Count

Result:
Shows top communicating hosts.


#### Protocol Distribution
Shows what protocols are used on the network

Why: Establishes normal protocol usage, anything unusual stands out

Visualization type: Pie chart or Donut chart

Fields:

Setting	      Field

Slice by	      network.protocol
Metric	      Count

#### Network Ports Usage
Shows the most common destination ports
Why: A baseline helps detect things like: Workstation -> external IP -> 4444 ...could be a reverse shell or RAT

Type: Bar chart

Fields:

Setting	   Field
X-axis	   destination.port
Y-axis	   Count
Top values	10 or 20

Optional filters

You can filter to only network traffic:

(In KQL search bar)
event.category : network

### Network Traffic Volume Over Time:
Shows normal bandwidth patterns

Why: 
- Detects data exfiltration
- Detects malware beaconing patterns

  usually in a line graph

Purpose: Identify spikes or beaconing.

Visualization: Line Chart

Fields:

Setting	Value
X-axis	@timestamp
Y-axis	Count

#### DNS Query Dashboard

ex. Top DNS Query Domains
    Top DNS Query Sources
    Rare Domains

Why: Malware often generates weird DNS traffic

#### A. DNS Volume Over Time 
Visualization: Line chart

Filter: (In KQL search bar)
network.protocol : dns

Fields:

Setting	Field
X-axis	@timestamp
Y-axis	Count

This shows DNS activity patterns.

Malware beaconing often appears as regular spikes.

Save as:

DNS Query Volume

#### B. Top DNS Domains

Visualization:
Data table

Filter:

network.protocol : dns

Fields:

Setting	Field
Rows	dns.query.name
Metric	Count
Top values	20

This shows the most requested domains.


#### External Connections
Shows internal hosts talking to the internet

Ex. Top External IP's
Countries communicating with the network
External traffic volume

Helps detect: 
Command and Control Servers
Data Exfiltration

#### Connection Graph
Shows who talks to who

ex.
workstation -> Domain Controller
workstation -> DNS server
workstation -> internet

printer -> internet -> port 8000 (suspicious)

Visualization type:

Horizontal bar chart
(or Sankey graph if available)

Fields:

Setting	Field
X-axis	source.ip
Breakdown by	destination.ip
Metric	Count

Limit to:

Top 10

### Top Destination Ports
Purpose: Baseline common services.

Visualization: Bar Chart

Fields:

Setting	Value
X-axis	destination.port
Y-axis	Count

### Baselining a Network with Kibana and Security Onion
Kibana - Visualization of data stored in elasticsearch and navigate the elastic stack

We categorize the data inside Security Onion based on module and dataset
Kibana allows you to quickly analyze all of the several datatypes produced by Sec Onion through a single pane of glass

Pulls event and log data together which includes syslog events into a single pane

Includes Network Intrusion Detection System (NIDS)/Host Intrusion Detection System(HIDS) logs
Zeek Logs
System Logs

From Kibana we can pivot to Stenographers full packet capture via Security Onion Console

If we filter for the zeek module, we can see zeek datasets within zeek conn logs, http logs, file logs

Major way that we categorize data by module and then by dataset

Datasets - subsets of logs inside a particular module for suricata
If you filter for it you'll see we just have alert data

Other major way we categorize is through event.category

Four Major Classifications:
Alert data:
- Four different alerting engines: playbook, suricata, wazzuh and zeek
-- playbook allows generation of alerts based on sigma alerts with network or host data
-- suricata is network intrusion detection, rules generalte alerts base don network data
-- wazzuh generates alerts based on primarily host data and logs from host and zeek
-- we get zeek alerts from network data

File data:
Zeek extracts out files form the network, runs analysis and generates metadata
Strelka also runs file analysis by running signatures

Host data:
Spans a range of different logs and datatypes in the endpoint device
We have os query, host data, sysmon, wazzuh, syslog, windows event logs and many other data types

Network data:
MAny datasets we can go through
If we click connections, we see lots of ways to visualize data using Kibana

ex. source ip, destination ip, destination port

Combinining sources gives ability to visualize data with dashboards
Shows us how we categorize data by module data set and event.category

### Configurations with Kibana

Configuration files - /opt/so/conf/kibana

Most configuration managed with Salt
Configurations written to /opt/so/conf/kibana will be overwritten in next salt update

Salt - set of scripts formed to managed multiple security onion sensors

Kibana dashboard will display timestamps of timezone of local browser used to deploy security onion console

To change timestamps:

Management -> Advanced Settings and set dateFormat:tz to UTC









































































































































