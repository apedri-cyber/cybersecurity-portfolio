# SSH Brute Force Detection Lab (Using Splunk)
In this lab, I simulated a real-world SSH brute force attack and detected it using the Splunk SIEM. In this project, you will see the attack simulation, log ingestion, SPL querying, dashboards, and alert creation.

## Objective
I simulated an SSH brute force attack using Hydra on a Kali Linux VM. The target was an Ubuntu Server VM, and the generated auth logs were ingested into Splunk to create detections, dashboards, and alerts. 

## Lab Architecture
          Attacker                                      Target
    +------------------+       SSH Attack        +------------------+
    |   Kali Linux VM  | ----------------------> | Ubuntu Server VM |
    |   (Attacker)     |                         | (Target)         |
    |   Hydra          |                         | OpenSSH Server   |
    +------------------+                         | Splunk UF        |
                                                 +--------+---------+
                                                          |
                                                   Log Forwarding
                                                   (port 9997)
                                                          |
                                                 +--------+---------+
                                                 | Windows Host     |
                                                 | Splunk Enterprise|
                                                 | (SIEM)           |
                                                 +------------------+

## Tools Used
          Splunk Enterprise: SIEM for log ingestion, searching events, and creating dashboards and alerts
          Splunk Universal Forwarder: Collect and forward auth logs from Ubuntu to Splunk
          Hydra: A Kali Linux tool used to simulate an SSH brute force attack
          Ubuntu Server: Target machine generating auth logs
          Kali Linux: Attacker machine conducting brute force attacks on the server
          VMware: Virtualization software

## Setup & Configuration
1. Ubuntu Server VM (Target)

Installed OpenSSH Server:

    bash  sudo apt install openssh-server

Installed and configured Splunk Universal Forwarder to monitor /var/log/auth.log


Configured inputs.conf to forward logs to Splunk index ssh_lab:

    ini  [monitor:///var/log/auth.log]
      disabled = false
      index = ssh_lab
      sourcetype = linux_secure

Configured outputs.conf to point at Splunk Enterprise on Windows host:

    ini  [tcpout]
      defaultGroup = default-autolb-group
    
      [tcpout:default-autolb-group]
      server = <splunk-ip>:9997
2. Splunk Enterprise (Windows Host)

Created index ssh_lab to receive forwarded logs,
Enabled receiving on port 9997,
Confirmed Universal Forwarder connection showing Active.

## Attack Simulation
Ran Hydra from Kali Linux targeting the Ubuntu SSH server using the rockyou.txt wordlist:

    hydra -l root -P /usr/share/wordlists/rockyou.txt -t 4 ssh://<ubuntu-ip>
To simulate a worst-case scenario (successful brute force), a successful SSH login was performed from Kali after generating failures:

    ssh <username>@<ubuntu-ip>

## Splunk Detection
### SPL Queries
Failed SSH Attempts by Source IP:

    index=ssh_lab "Failed password"
    | rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
    | stats count by src_ip
    | sort -count

Failed Attempts Over Time:

    index=ssh_lab "Failed password"
    | timechart span=1m count as "Failed Attempts"
    
Most Targeted Usernames:

    index=ssh_lab "Failed password"
    | rex "Failed password for (?<username>\S+) from"
    | stats count by username
    | sort -count
    | head 10
    
Correlation — Brute Force with Successful Login:

    index=ssh_lab ("Failed password" OR "Accepted password")
    | rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
    | stats count(eval(match(_raw,"Failed"))) as failures,
      count(eval(match(_raw,"Accepted"))) as successes by src_ip
    | where failures > 5 AND successes > 0

## Dashboard
Built a Splunk Classic Dashboard — SSH Brute Force Detection — with 4 panels:

    Panel                      | Visualization | Purpose
    _______________________________________________________________________________
    Total Failed SSH Attempts  | Single Value  | High-level count of all failures
    Failed Attempts Over Time  | Line Chart    | Visualizes the attack spike
    Top 10 Attacking IPs       | Bar Chart     | Identifies most active attackers
    Most Targeted Usernames    | Bar Chart     | Shows which accounts were targeted

## Alerts
  ### Alert 1: SSH Brute Force Detected
Fires when a single IP exceeds 10 failed SSH attempts within 5 minutes

  ### Alert 2: Brute Force Success Detected (Critical)
Fires when an IP has more than 5 failed attempts and at least 1 successful login — indicating a likely compromised account.

## Key Findings
* Generated hundreds of failed SSH attempts in just a few minutes using Hydra—all attempts, whether successful or unsuccessful, were captured by Splunk and displayed  on a SIEM dashboard. This shows how easy it can be for an attacker to gain unauthorized access to an account with weak or default credentials. Implementing MFA and the principle of least privilege can help prevent an attacker from gaining access or limit what they can do if an account is compromised.
* The brute force detection alert fired multiple times, demonstrating the importance of having reliable alerts. Whenever the number of login attempts reaches a specified threshold, the alert will fire.
* The successful brute force alert fired when the simulated attacker gained access to the server. This alert is more important than the brute force detection act because we can figure out where the attacker logged in from and take measures to mitigate and eradicate the threat.
* The dashboard I created showed multiple key findings, such as attack spikes and where most of the attacks come from. This is crucial to incident response. We can block traffic coming from IPs that are on the top 10 IP list. We can also find out if the failed logins occurred outside of normal work hours, which could indicate an attacker was trying to access the server.
* It's important to configure your alerts to balance between sensitivity and noise. You don't want too many alerts where you are overwhelmed and potentially miss a real security threat. However, you also want to ensure you capture real attacks happening and don't miss them because no alert was made.

## NOTE
Check the screenshots folder to see pictures of the dashboard, alert configuration, and SPL queries

