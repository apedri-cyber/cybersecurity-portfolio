# Lab: Explore signatures and logs with Suricata

## Overview
In this lab, I configured and tested custom detection rules using Suricata, an open-source Intrusion Detection and Prevention System (IDS/IPS). The goal was to understand how Suricata processes traffic, applies rule logic, and generates alerts in different log formats. Instead of simply running prebuilt rules, I analyzed the rule structure, triggered alerts using packet capture data, and examined the resulting logs to validate rule behavior.

## Lab Environment & Files
The lab included the following files:

    sample.pcap – A packet capture file containing HTTP traffic

    custom.rules – A file containing Suricata detection rules

    /var/log/suricata/fast.log – Quick alert output

    /var/log/suricata/eve.json – Detailed structured event log (JSON format)

## Activity
I began by reviewing the rule inside custom.rules:

        alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
This rule consists of 3 main components: the action, the header, and the rule options. The action in this rule is "alert", which tells Suricata to generate an alert when the rule conditions are met. The header,

        http $HOME_NET any -> $EXTERNAL_NET any
defines the protocol, source address and port, destination address and port, and the traffic flow. This signature triggers an alert when it detects any HTTP traffic leaving the home network and going to the external network. The final component of this rule is the rule options:

        msg → The alert message displayed in logs
    
        flow:established,to_server → Only inspect traffic in an established TCP session going to the server

        content:"GET" + http_method → Match HTTP packets where the method is GET

        sid → Unique signature ID

        rev → Revision number of the rule
This rule signature triggers an alert whenever Suricata observes the text GET as the HTTP method in an HTTP packet from the home network going to the external network.


Before running Suricata, I verified that the log directory was empty:

    ls -l /var/log/suricata


Then I executed Suricata in offline mode:

    sudo suricata -r sample.pcap -S custom.rules -k none

The "-r sample.pcap" reads traffic from a packet capture file. The "-S custom.rules" loads only the specified custom rules. The "-k none" disables checksum validation. Suricata processed the packets and applied the detection logic from the rule.

After execution, I checked:

    cat /var/log/suricata/fast.log


And got something like this:

    [1:12345:3] GET on wire {TCP} 172.21.224.2:49652 -> 142.250.1.139:80
This Tells Me the rule with SID 12345, revision 3, was triggered on TCP traffic from internal IP 172.21.224.2 to external IP 142.250.1.139 on port 80 (HTTP). Multiple entries indicate that multiple HTTP GET requests were detected. While fast.log is useful for quick verification, it does not contain deep metadata. For detailed analysis, eve.json is preferred.

The eve.json file is the primary Suricata log. It contains structured JSON events, including alerts, flow metadata, protocol information, timestamps, and severity levels To make it readable:

    jq . /var/log/suricata/eve.json | less



The first alert showed: "severity": 3, indicating a low-to-medium priority alert based on Suricata’s classification system. To extract only important fields:

    jq -c "[.timestamp,.flow_id,.alert.signature,.proto,.dest_ip]" /var/log/suricata/eve.json

This gives us an output similar to this:

    ["2022-11-23T12:38:34.624866+0000",14500150016149,"GET on wire","TCP","142.250.1.139"]


This shows the exact timestamp, Unique flow_id, alert signature, protocol, destination IP, and flow Correlation. Each network conversation is assigned a unique flow_id. To view all logs related to a specific flow:

    jq "select(.flow_id==X)" /var/log/suricata/eve.json

This is useful for incident response because it allows full visibility into all events within a single network session, and it helps correlate alerts with metadata such as source/destination, protocol, and behavior

## Conclusion
This lab provided hands-on experience with configuring and validating Suricata detection rules in a controlled environment. I analyzed rule structure, executed Suricata against packet capture data, and verified alert generation through both fast.log and the structured eve.json output. By extracting specific fields with jq and correlating events using flow_id, I demonstrated the ability to move beyond basic alert validation and into structured log analysis and traffic correlation. I now have a stronger understanding of how signature-based detection works, how rule conditions influence alert behavior, and how to interpret IDS output in a meaningful way. These skills directly translate to real-world SOC responsibilities, where analysts must validate detection logic, investigate alerts efficiently, and understand exactly why a rule triggered. This lab strengthened my ability to analyze network traffic, tune detection rules, and work confidently with IDS logging formats — all essential competencies for a cybersecurity analyst.
