# NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP

🔐 Cybersecurity Lab Environment Setup

Design and Implementation of an Isolated Virtual Laboratory for Authorized Penetration Testing and Ethical Hacking

📌 Executive Summary

This project documents the design, deployment, and validation of a secure, isolated virtual cybersecurity laboratory using Oracle VM VirtualBox 7.2 and Kali Linux 2026.2. The lab provides a reproducible, controlled environment for practicing authorized security-testing activities—including network reconnaissance, vulnerability assessment, exploitation, and tool experimentation—while maintaining strict isolation from production networks and unauthorized systems.

The architecture leverages a NAT Network to enable inter-VM communication and controlled internet access, supporting future expansion with additional target machines (e.g., Metasploitable, DVWA, Windows clients). Comprehensive documentation, snapshot management, and ethical governance ensure the lab meets academic, professional, and legal standards for responsible cybersecurity practice.

🎯 Project Objectives

Core Technical Objectives

1. Install and configure Oracle VM VirtualBox 7.2 with appropriate system resources and extension packs.

2. Deploy Kali Linux 2026.2 as a pre-configured virtual machine optimized for security testing.

3. Design and implement a private NAT Network (NatNetwork) to facilitate secure inter-VM communication with controlled outbound connectivity.

4. Configure dual-stack network adapters on the Kali VM to balance isolation and tool-update capabilities.

5. Assign a static IPv4 address (10.0.0.2/24) to ensure consistent addressing across lab sessions.

6. Validate end-to-end connectivity, including gateway reachability, internet access, and DNS resolution.

7. Create a baseline VM snapshot (Clean Kali – Network Setup) to enable rapid restoration and repeatable testing scenarios.

8. Produce comprehensive technical documentation covering architecture, configuration, verification, and troubleshooting.

9. Establish a scalable foundation for future cybersecurity projects, including multi-target labs, Active Directory simulations, and web application testing environments.

10. Align lab design with industry best practices for isolation, segmentation, and safe tool usage.


🛡️ Purpose and Scope

This laboratory serves as a dedicated, ethical, and legally compliant environment for cybersecurity education and authorized security testing. It supports hands-on practice in the following domains:

1. Network reconnaissance and enumeration (e.g., nmap, netdiscover)

2. Port scanning and service fingerprinting

3. Vulnerability discovery and assessment (e.g., nikto, openvas)

4. Packet capture and protocol analysis (e.g., Wireshark, tcpdump)

5. Web application security testing (e.g., Burp Suite)

6. Controlled exploitation and privilege escalation (e.g., Metasploit, searchsploit)

7. Security tool evaluation and workflow automation

8. Defensive monitoring and incident response simulation

⚠️ Ethical and Legal Notice: This laboratory must only be used against systems you own, control, or have explicit written permission to test. Never direct lab tools or techniques toward unauthorized networks, devices, or services. All activities must comply with applicable laws, institutional policies, and professional ethical standards (e.g., EC-Council Code of Ethics, (ISC)² Canons).

🏗️ The Lab Architecture

Logical Topology
<img width="1582" height="721" alt="Lab_Structure" src="https://github.com/user-attachments/assets/86e0deea-a653-40bc-a980-555e534d296d" />


Network Design Rationale

NAT Network Selection: Chosen over standard NAT or Host-Only to enable multi-VM communication while providing controlled internet access for tool updates and package installation.

Subnet Selection: 10.0.0.0/24 provides a clean, memorable address space with ample room for future target VMs (10.0.0.3–10.0.0.99).

Static IP for Kali: Ensures consistent referencing in documentation, scripts, and future lab exercises.

Shared Folder: Facilitates secure file transfer between host and guest without exposing the lab to external networks.

⚙️ Lab Configuration Specifications
Component	Specification
Host OS	Windows 10 (64-bit)
Host RAM	8 GB
Processor	Intel Core i7 (with VT-x enabled)
Hypervisor	Oracle VM VirtualBox 7.2
Security OS	Kali Linux 2026.2 (Official OVA)
Kali RAM Allocation	2048 MB
Virtual Network	NAT Network (NatNetwork)
Network Address	10.0.0.0/24
Kali IP Address	10.0.0.2/24 (static)
Default Gateway	10.0.0.1
DNS Server	8.8.8.8 (Google Public DNS)
Future VM Range	10.0.0.3–10.0.0.99 (DHCP or static)
🪜 Implementation Procedure
Step 1: Install 7-Zip
Purpose: Extract .7z archives containing Kali Linux OVA or ISO files.

Tool: 7-Zip 23.01

Verification: Right-click .7z file → "7-Zip" → "Extract Here".

Step 2: Install VirtualBox
Purpose: Deploy hypervisor for VM management.

Tool: VirtualBox 7.2 + Extension Pack

Post-Install: Enable USB 3.0, VirtualBox Bridged Networking, and Host-Only Ethernet Adapter.

Step 3: Create NAT Network
Path: File → Tools → Network Manager → NAT Networks → Create

Configuration:

Name: NatNetwork

IPv4 Prefix: 10.0.0.0/24

DHCP: Enabled (for future target VMs)

IPv6: Disabled (to reduce complexity)

Rationale: NAT Network allows inter-VM communication + outbound internet, unlike standard NAT.

Step 4: Import Kali Linux VM
Source: Kali Linux Official Pre-Built VMs

Import Path: File → Import Appliance → Select .ova → Review → Import

Network Adapter:

Adapter 1: Attached to NAT Network, Name: NatNetwork

Adapter Type: Intel PRO/1000 MT Desktop

Resources: 2048 MB RAM, 2 CPU cores (if available)

Shared Folder:

Path: C:\Users\<YourUser>\LabFiles

Mount Point: /media/sf_LabFiles

Options: Auto-mount, Make Permanent

Step 5: Configure Static IP on Kali
Method: NetworkManager CLI (nmcli)

Commands:

bash
nmcli connection modify "Wired connection 1" \
  ipv4.addresses 10.0.0.2/24 \
  ipv4.gateway 10.0.0.1 \
  ipv4.dns "8.8.8.8" \
  ipv4.method manual
nmcli connection up "Wired connection 1"
Verification:

bash
ip a show eth0
ping -c 4 10.0.0.1
ping -c 4 8.8.8.8
nslookup kali.org
Step 6: Create Baseline Snapshot
Name: Clean Kali – Network Setup

Description: "Baseline configuration with static IP, shared folder, and NAT Network. Safe to restore before risky exercises."

Path: Machine → Take Snapshot

Best Practice: Snapshot before every major tool installation or configuration change.

🔎 Lab Verification Results
Test	Command	Expected Output	Actual Result
IP Address	ip a show eth0	10.0.0.2/24 displayed	✅ Pass
Gateway Reachability	ping -c 4 10.0.0.1	4/4 packets received	✅ Pass
Internet Connectivity	ping -c 4 8.8.8.8	4/4 packets received	✅ Pass
DNS Resolution	nslookup networkwalks.com	Non-authoritative answer: IP shown	✅ Pass
Nmap Installation	nmap --version	Nmap 7.94+ displayed	✅ Pass
Snapshot Restoration	Restore → ip a	IP reverts to 10.0.0.2	✅ Pass

🐞 Troubleshooting Log

Problem 1: Loss of Internet After Static IP Configuration

Symptom: No outbound connectivity after applying static IP.

Root Cause: NetworkManager duplicate address detection (DAD) timeout conflict.

Solution:

bash
nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0

nmcli connection down "Wired connection 1"

nmcli connection up "Wired connection 1"

Lesson: Always verify connection name with nmcli connection show before modifying.

Problem 2: VT-x / AMD-V Hardware Virtualization Disabled

Symptom: VM fails to start with error: VT-x is disabled in BIOS.

Root Cause: BIOS/UEFI virtualization extensions not enabled.

Resolution:

Reboot → Enter BIOS/UEFI (F2/Del).

Navigate to Advanced → CPU Configuration.

Enable Intel VT-x or AMD-V.

Save & Exit → Boot Windows → Start VM.

Verification: System Information → Hyper-V Requirements → Virtualization Enabled: Yes

Problem 3: Shared Folder Not Mounting

Symptom: /media/sf_LabFiles missing or permission denied.

Solution:

bash
sudo usermod -aG vboxsf $USER
sudo reboot
Verification: ls /media/sf_LabFiles

💡 Key Learning Outcomes
NAT vs. NAT Network: NAT Network enables multi-VM communication + internet; standard NAT isolates VMs from each other.

VirtualBox Networking Modes: Understood trade-offs between NAT, NAT Network, Host-Only, and Bridged for security labs.

Static IP Configuration: Mastered nmcli for persistent IPv4 settings in Kali Linux.

Snapshot Management: Learned to create, restore, and document VM snapshots for lab reproducibility.

Professional Documentation: Recognized the importance of structured reports, troubleshooting logs, and version control in cybersecurity projects.

🔐 Ethical and Legal Compliance

Authorization: All testing confined to owned or explicitly authorized systems.

Isolation: Lab network segregated from host LAN and internet via NAT Network boundaries.

Accountability: All activities logged and documented for academic and professional review.

Compliance: Adheres to Kenyan Computer Misuse and Cybercrimes Act (2018) and international ethical hacking standards.

🔗 Tools and Resources

7-Zip: https://7-zip.org/download.html

VirtualBox: https://virtualbox.org/wiki/Downloads

Kali Linux: https://kali.org/get-kali

NetworkManager CLI Guide: https://wiki.archlinux.org/title/NetworkManager

VirtualBox Networking: https://www.virtualbox.org/manual/ch06.html

👤 Author Information
Donald Oketch
Cybersecurity Intern B083
NetworkWalks Academy | Week 01
LinkedIn: 
GitHub: https://github.com/Donald-Odhiambo

📌 Project Metadata
Field	Value
Program	Cybersecurity Diploma, NetworkWalks Academy
Week	01
Project Title	Cybersecurity & Penetration Testing Lab Setup
Repository	GitHub (private/public)
Version	1.0
Last Updated	September 10, 2026
