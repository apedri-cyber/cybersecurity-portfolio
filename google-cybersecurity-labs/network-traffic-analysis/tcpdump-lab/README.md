# Lab: Capture and filter network traffic in a Linux environment using tcpdump

## Overview
In this lab, network traffic was captured and analyzed using tcpdump in a Linux environment. The purpose of this exercise was to understand how to identify network interfaces, capture live traffic, apply filters, and analyze packet data from a saved capture file. This lab demonstrates foundational skills in packet analysis and command-line network monitoring.

## Lab Environment
    Linux Virtual Machine
    User account: analyst
    Tool used: tcpdump
    Sample packet capture file provided in home directory

## Task 1: Identify Network Interfaces
The first step was identifying available network interfaces that could be used for packet capture.
The following command was used: 

    sudo ifconfig. 
This displayed available interfaces such as: 

    eth0 (Ethernet interface)
    lo (Loopback interface)

The eth0 interface was selected for packet capture because it handles external network traffic. To confirm available interfaces, the following command was also used:

    sudo tcpdump -D

This command listed all capture-capable interfaces.

## Task 2: Inspect Live Network Traffic
Live traffic was inspected using tcpdump with the following command:

    sudo tcpdump -i eth0 -v -c5

### Command Breakdown:

    -i eth0 → Capture traffic from the eth0 interface

    -v → Display verbose packet details

    -c5 → Capture only 5 packets

### The output included:

    Timestamp of packet capture
    
    Protocol type (IP)
    
    TTL, flags, packet length
    
    Source and destination systems
    
    Port numbers
    
    TCP flags such as SYN, ACK, and PUSH

This step helped reinforce how to interpret packet header information and understand communication between systems.

### Understanding Packet Details

Several important packet properties were observed:

    Timestamp – Shows when the packet was captured
    
    Protocol – Indicates IP and TCP information
    
    TTL (Time To Live) – Shows hop limit
    
    Flags – Identifies packet type (SYN, ACK, PUSH, etc.)
    
    Source/Destination Ports – Identifies communication endpoints
    
    The direction arrow (>) in the output indicates the direction of traffic flow.

## Task 3: Capture Traffic to a File

Next, traffic was filtered and saved to a packet capture file.

    sudo tcpdump -i eth0 -nn -c9 port 80 -w capture.pcap &

### Command Breakdown:

    -i eth0 → Capture from eth0
    
    -nn → Disable IP and port name resolution
    
    -c9 → Capture 9 packets
    
    port 80 → Filter for HTTP traffic
    
    -w capture.pcap → Save to file
    
    & → Run in background

HTTP traffic was generated using:

    curl opensource.google.com


The capture file was verified using:

    ls -l capture.pcap


This confirmed that HTTP traffic was successfully recorded.

## Task 4: Filter Captured Packet Data

The saved capture file was analyzed using:

    sudo tcpdump -nn -r capture.pcap -v

Options Used:

    -nn → Disable name resolution
    
    -r → Read from capture file
    
    -v → Display verbose details

This displayed:

    Source and destination IP addresses
    
    TCP flags
    
    Sequence and acknowledgment numbers
    
    Window size
    
    Packet length

To analyze raw packet contents in more detail, the following command was used:

    sudo tcpdump -nn -r capture.pcap -X


The -X option displayed packet data in both hexadecimal and ASCII format. This is useful during forensic analysis or malware investigations when deeper inspection of payload data is required.


## Conclusion

This lab demonstrated how tcpdump can be used to capture, filter, and analyze network traffic from the command line. Understanding how to interpret packet headers and filter specific traffic types is essential for network monitoring, incident response, and threat detection. Command-line packet analysis tools such as tcpdump provide valuable visibility into network activity and are commonly used in real-world security investigations.
