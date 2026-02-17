# Network Traffic Analysis and Threat Detection

In this lab, network traffic was captured and analyzed between a Kali Linux virtual machine and a Windows host using Wireshark. The goal was to observe normal network behavior and identify activity that could indicate a potential security threat. The lab focused on ICMP traffic, TCP SYN scanning, and HTTP communication.

ICMP traffic was generated using the ping command. This allowed Echo Request and Echo Reply packets to be observed in Wireshark. These packets confirmed connectivity between the two systems and showed how normal communication appears in a packet capture. By reviewing the packet details, the source and destination IP addresses and TTL values were verified. This established a baseline for normal network behavior.

Next, a TCP SYN scan was performed using Nmap. A SYN scan is commonly used during reconnaissance to identify open ports on a system. In the packet capture, multiple SYN packets were sent to different destination ports without completing the full TCP three-way handshake. This pattern clearly indicated scanning activity. Recognizing this behavior is important because port scanning is often one of the first steps an attacker takes before attempting further exploitation.

HTTP traffic was also analyzed by hosting a web server on the Kali machine and accessing it from the Windows host. The capture showed HTTP GET requests and corresponding 200 OK responses. Using the “Follow TCP Stream” feature, the full HTTP conversation was reconstructed. The request headers, including the Host and User-Agent fields, were visible in plaintext. This demonstrated the security risk of transmitting data over HTTP without encryption, as sensitive information could potentially be intercepted.

Overall, this lab demonstrated how packet analysis can be used to distinguish normal traffic from suspicious activity. It reinforced the importance of monitoring network behavior, detecting reconnaissance attempts, and understanding the risks of unencrypted communication. These are essential skills for identifying and responding to network-based threats.
