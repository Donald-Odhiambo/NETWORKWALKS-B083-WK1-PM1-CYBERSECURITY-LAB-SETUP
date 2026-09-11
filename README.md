# NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP

🔐 Cybersecurity Lab Environment Setup

Design and Implementation of an Isolated Virtual Laboratory for Authorized Penetration Testing and Ethical Hacking

📌 Executive Summary

This project documents the design, deployment, and validation of a secure, isolated virtual cybersecurity laboratory using Oracle VM VirtualBox 7.2 and Kali Linux 2026.2. The lab provides a reproducible, controlled environment for practicing authorized security-testing activities—including network reconnaissance, vulnerability assessment, exploitation, and tool experimentation—while maintaining strict isolation from production networks and unauthorized systems.

The architecture leverages a NAT Network to enable inter-VM communication and controlled internet access, supporting future expansion with additional target machines (e.g., Metasploitable, DVWA, Windows clients, Android Target  etc). Comprehensive documentation, snapshot management, and ethical governance ensure the lab meets academic, professional, and legal standards for responsible cybersecurity practice.

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

⚠️ Disclaimer: This laboratory must only be used against systems you own, control, or have explicit written permission to test. Never direct lab tools or techniques toward unauthorized networks, devices, or services. All activities must comply with applicable laws, institutional policies, and professional ethical standards (e.g., EC-Council Code of Ethics, (ISC)² Canons).

🏗️ The Lab Architecture

Logical Topology
<img width="1582" height="721" alt="Lab_Structure" src="https://github.com/user-attachments/assets/86e0deea-a653-40bc-a980-555e534d296d" />


Network Design Rationale

NAT Network Selection: Chosen over standard NAT or Host-Only to enable multi-VM communication while providing controlled internet access for tool updates and package installation.

Subnet Selection: 10.0.0.0/24 provides a clean, memorable address space with ample room for future target VMs (10.0.0.3–10.0.0.99).

Static IP for Kali: Ensures consistent referencing in documentation, scripts, and future lab exercises.

Shared Folder: Facilitates secure file transfer between host and guest without exposing the lab to external networks.

⚙️ Lab Configuration Specifications

![Uploading Lab_Specification.png…]()


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

Path: File → Tools → Network  → NAT Networks → Create

<img width="1912" height="1039" alt="Network_Config" src="https://github.com/user-attachments/assets/70aa814c-b0bf-4cc8-802a-7b0e67b7cc72" />

Configuration:

Name: NatNetwork

IPv4 Prefix: 10.0.0.0/24

DHCP: Enabled (for future target VMs)

IPv6: Disabled (to reduce complexity)

Rationale: NAT Network allows inter-VM communication + outbound internet, unlike standard NAT.

Step 4: Import Kali Linux VM
Source: Kali Linux Official Pre-Built VMs

Import Path: File → Open → Select .ova → Review → Open  or Right Click the .over file then select "Open with Virtualbox"
<img width="919" height="691" alt="Import Kali  Machine" src="https://github.com/user-attachments/assets/eff9db16-45a0-463e-8bf0-f815dd562219" />

Network Adapter:

Adapter 1: Attached to NAT Network, Name: NatNetwork

Adapter Type: Intel PRO/1000 MT Desktop

Resources: 4096 MB RAM, 2 CPU cores (if available)

<img width="941" height="556" alt="Kali_Nat Network Config" src="https://github.com/user-attachments/assets/e28b2374-2053-4755-a454-31a8723856b8" />


Step 5: Configure Static IP on Kali
Method: GUI

<img width="709" height="553" alt="Static_IP Config" src="https://github.com/user-attachments/assets/2d00dd8f-a92c-4e30-99e7-437009db3e1b" />

Step 6: Create Baseline Snapshot
Name: Clean Kali – Network Setup

<img width="1482" height="933" alt="Kali_Snapshot" src="https://github.com/user-attachments/assets/b44b83c8-08d0-4164-b3a1-5ff6d4e4549c" />

Description: "Baseline configuration with static IP, shared folder, and NAT Network. Safe to restore before risky exercises."

Path: Machine → Take Snapshot

<img width="1482" height="933" alt="Kali_Snapshot" src="https://github.com/user-attachments/assets/f416f594-e831-4dc5-80a6-96ca214e7b8f" />

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

<img width="1010" height="429" alt="Diagnosting_Internet Connection Problem" src="https://github.com/user-attachments/assets/01103b1c-de13-4e6b-8d38-cc5cc4122218" />

Lesson: Always verify connection name with nmcli connection show before modifying.


💡 Key Learning Outcomes

1. NAT vs. NAT Network: NAT Network enables multi-VM communication + internet; standard NAT isolates VMs from each other.

2. VirtualBox Networking Modes: Understood trade-offs between NAT, NAT Network, Host-Only, and Bridged for security labs.

3. Static IP Configuration: Mastered nmcli for persistent IPv4 settings in Kali Linux.

4. Snapshot Management: Learned to create, restore, and document VM snapshots for lab reproducibility.

5. Professional Documentation: Recognized the importance of structured reports, troubleshooting logs, and version control in cybersecurity projects.

🔐 Ethical and Legal Compliance

1. Authorization: All testing confined to owned or explicitly authorized systems.

2. Isolation: Lab network segregated from host LAN and internet via NAT Network boundaries.

3. Accountability: All activities logged and documented for academic and professional review.

4. Compliance: Adheres to Kenyan Computer Misuse and Cybercrimes Act (2018) and international ethical hacking standards.

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

LinkedIn:https://www.linkedin.com/in/oketch-donald-odhiambo-0a6823429

GitHub: https://github.com/Donald-Odhiambo

📌 Project Metadata
Field	Value
Program	Cybersecurity Internship, NetworkWalks Academy
Week	01
Project Title	Cybersecurity & Penetration Testing Lab Setup
Repository	GitHub (public)
Version	1.0
Last Updated	September 11, 2026
