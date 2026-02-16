# Wireshark Lab

## Objective:
Analyze network traffic and detect suspicious activity using Wireshark and Kali Linux in a virtualized environment.

## Environment:
    Kali Linux VM (attacker/malicious traffic generator)
    Windows host with Wireshark (traffic capture & analysis)
    VMware networking (NAT/Bridged)

## Activity:
    Captured network traffic between Kali Linux VM and Windows host using Wireshark
    Generated traffic scenarios including:
    ICMP ping requests/replies
    TCP SYN port scans with Nmap
    HTTP requests to local web server
    Applied display filters to isolate ICMP, TCP, and HTTP packets
    Analyzed packet details to identify patterns of normal and suspicious activity
    Documented findings, including potential indicators of compromise (IOCs)

## Skills Demonstrated:
    Packet capture and analysis (Wireshark)
    Network protocol understanding (TCP, ICMP, HTTP)
    Detection of reconnaissance and scanning activity
    Traffic filtering and interpretation
    Reporting and documenting network security observations
