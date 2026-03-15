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























































































































































