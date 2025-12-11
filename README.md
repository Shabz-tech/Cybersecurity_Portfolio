# Hey there — Welcome to My Networking and Cybersecurity Portfolio

Thanks for stopping by!

This repository is my personal, hands-on journey into the world of Networking and Cybersecurity. It serves as a documentation portfolio. Each "Lab" folder contains screenshots, configuration snippets, and my personal notes on the implementation process. The labs will showcase my ability to learn new concepts and tools, because I believe that skill combined with critical thinking is essential to be successful in this career.

I love taking complex concepts and translating them into something approachable, practical, and beginner-friendly. I work full time and I am a current Bachelor of IT (CyberSecurity) student at RMIT - Melbourne. I started these labs as my own structured study plan to supplement my University studies by putting theory into practice through learning and using different tools.

---

## What Is This Repo?

I simulated a real-world environment where I could act as the Network Engineer, the System Administrator, the Attacker (Red Team), and the Defender (Blue Team).

### The Architecture

I didn't just plug everything into a home router. I designed a Zone-Based Architecture to simulate a corporate environment:

- **Management Zone (VLAN 10):** For admin traffic and switch management.

- **Server Zone (VLAN 30):** Hosting critical infrastructure (Logs, SIEM).

- **DMZ (VLAN 40):** Public-facing services, segregated from the internal network.

- **User/Test Zones (VLAN 20/66):** Simulated endpoints and attack boxes.

## Tech Stack & Tools Used

This project exposed me to a wide variety of technologies:

| **Category**              | **Technologies**                                                     
| ------------------------- | -------------------------------------------------------------------- 
| **Networking**            | Network segmentation via VLANs, moditifed Trunking (virtualbox), Routing, pfSense (Firewall)
| **Operating Systems**     | Linux, Kali Linux (Basic Offensive Ops), Windows Server 2019 and Windows 11
| **Security & Monitoring** | Suricata (IDS), Wazuh (SIEM), Syslog-ng (Log Management), Firewall Rules and policy            
| **Services**              | Apache (Web Server), SSH (Secure Access), DHCP, DNS, NTP             
| **Tools**                 | Virtualbox, Docker, Nmap, Hydra                                    

---

## The Journey

This project was broken down into distinct phases. Click the links below to read the detailed breakdown of what I did and what I learned in each phase.

### Phase 1: The Foundation (Networking & Infrastructure)

*Before securing the network, I had to build it.*

- **Layer 2 Switching & VLANs** - Configuring the physical Cisco switch, setting up VLANs, and trunking.

- **Layer 3 Routing** - Enabling inter-VLAN routing using "Router on a Stick."

- **DHCP Services** - Automating IP address assignment for different subnets.

### Phase 2: Security & Services (The Blue Team)

*Moving from connectivity to security and application hosting.*

- **The Firewall (pfSense)** - replacing the router with a dedicated firewall and defining strict rules.

- **DMZ & Server Hardening** - Setting up a web server in the DMZ and hardening SSH access.

- **Centralized Logging** - Creating a "Single Source of Truth" for logs using Syslog.

### Phase 3: The Attack & Analysis (Red vs. Blue)

*Testing the defenses with real-world attacks and analyzing the fallout.*

- **The Attack (Red Team Ops)** - Using Kali Linux, Nmap, and Hydra to generate attack traffic.

- **SIEM Deployment (Wazuh)** - Deploying a SIEM to ingest logs, detect threats, and visualize the attacks from Lab 11.

## Who This Repo Is For

Everyone is welcome — but this Repo will mainly be my portfolio to showcase my knowledge to future employers/recruitors.

So if that is you (future employer/recruitor) - Hello, please hire me haha.

---

## A Note From Me

I built this Repo to show my passion for Networking and Cybersecurity. I study fulltime and work fulltime (shift work). My life gets intense due to my study and work load, but I dedicate my freetime to learn and strengthen the foundational skills of Networking and Cybersecurity.

## What's Next?

Now that the baseline environment is built, I plan to:

- Experiment with writing custom Suricata rules.

- Deploy a Honeypot to catch bad actors.

- Automate some of the server configurations using Ansible.

- Implement SOAR (Security Orchestration, Automation, and Response).

---
