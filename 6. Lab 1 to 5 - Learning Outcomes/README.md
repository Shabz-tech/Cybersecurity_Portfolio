# Theoretical Foundations: From Concepts to Configuration

## The journey so far...

So far, my journey has been from "getting it connected" to "engineering it securely using resources and guidance I have access to right now. I have built a continuous, production-grade enterprise simulation. Rather than building disposable labs, I have architected a permanent infrastructure where every new lab relies on the stability and security of the previous one. Eventhough it is at a much smaller scale, this is something I am really proud of. 

Below are the core Networking and Cybersecurity theories I have practically applied across the first five labs of this roadmap.

---

## 1. Foundational Network Models (OSI & TCP/IP)

**Labs Covered:** 1, 2 & 3

Before deciding how I was going to start these Home-Labs and deploying the VMs, I had to think about and engineer the network stack from the bottom up. I utilised the OSI (Open Systems Interconnection) Model as my blueprint for troubleshooting and design.

- **Layer 1 (Physical):** I learned that in a virtual environment, "cables" are software definitions. I configured "VirtualBox Network Adapters" to act as physical links.
  
  - Application: Using "Bridged" mode on Firewall to piggyback on my physical hardware for WAN access, while keeping LAN traffic strictly contained on separate adaptors using Internal mode.
    It is a shame I couldn't configure true DOT1q vlan tagging inside Virtualbox.

- **Layer 2 (Data Link) & Broadcast Domains:** I manually segmented the network into isolated Broadcast Domains using VirtualBox "Internal Networks."
  
  - Theory: This proved that VLANs are Layer 2 constructs. A device in the Vlan20 cannot ARP for a device in the Vlan10 because they separated even if they are physically connected to the same switch.

- **Layer 3 (Network) & Routing:** To connect these isolated Layer 2 islands, I deployed a Layer 3 Gateway (pfSense).
  
  - Theory: This demonstrated the role of the Router: moving packets between different subnets (192.168.10.0/24 and 192.168.20.0/24) that physically cannot talk to each other.

- **TCP/IP Stack Validation:** I validated the encapsulation process by troubleshooting connectivity. I observed that even if the physical link (Layer 1) and IP routing (Layer 3) were correct, a firewall blocking ICMP (Protocol) or a specific Port (Layer 4) would still cause the connection to fail.

Now finally, instead of a simple "Flat Network" (where all devices can talk to each other directly), I implemented a Segmented Network Topology. Unlike a simple packet filter (our amazing Linux Gateway), I used pfSense as the central "Hub" (Router/Firewall) and created distinct "Spokes" (Vlans) for Identity, Users, and Servers.

---

## 2. Applied Networking & Security Protocols

**Labs Covered:** 4 & 5

Once the layers were established, I manually configured and hardened the specific protocols required for enterprise operations. This required a deep understanding of their ports and functions to create the AD_Services firewall alias.

- **Kerberos (TCP/UDP 88):** The primary authentication protocol. I witnessed this in action when joining the Linux server to the domain; unlike legacy NTLM, Kerberos relies on time-stamped tickets, requiring synchronized clocks and proper DNS.

- **DNS (UDP 53):** The navigation system. I learned that blocking this port breaks the "Identity Vlan10" entirely, as clients cannot locate the Domain Controller (via SRV records) to request login.

- **LDAP/LDAPS (TCP 389/636):** The query language. This was utilized when I ran the id command on Linux to query the Windows database for user group memberships.

- **SMB (TCP 445):** The delivery mechanism. Crucial for delivering Group Policy Objects (GPOs) from the SYSVOL share to client machines. I restricted this heavily to prevent lateral movement exploits (like EternalBlue).

- **ICMP (Internet Control Message Protocol):** The diagnostic tool. I discovered that ICMP is a Layer 3 protocol distinct from TCP/UDP. My initial firewall rules failed to pass ping traffic because I had only permitted Layer 4 (TCP/UDP) traffic, teaching me to treat diagnostics separately from data transport.

- **SSH (TCP 22):** The management channel. I secured this by modifying the sshd_config to disable Root Login, enforcing non-repudiation by forcing administrators to log in as named users first.

---

## 3. Distributed Systems & Identity Management

**Labs Covered:** 3 & 4

### The Single Source of Truth (SSOT)

At the end of Lab 2, our VMs were still on the same Network after we deployed pfSense.

In Lab 3, Our Windows Server (DC) remained to VLAN10 while I migrated our Windows Client to VLAN20.

As a result of appropriate configuration in Lab 2, this was a smooth transition to initiate Intervlan routing demonstrating network segmentation and keeping Centralized Identity Management.

### Diverse Environment Integration

In Lab 4, I integratied a Linux VM into the domain that will be used as a Server in later Labs.

- **Interoperability Protocols:** I utilized SSSD (System Security Services Daemon) and Realmd to translate Linux authentication requests into Kerberos tickets that the Windows DC could understand.

- **Theory in Action:** This proved that identity is platform-agnostic. I successfully logged into a Linux server using Windows credentials, demonstrating cross-platform Authentication vs. Authorization flows.

---

## 4. Security Engineering: The Principle of Least Privilege

**Labs Covered:** 5

### Reducing the Attack Surface

In Lab 5, I moved from "Connectivity First" to "Security First." I applied the Principle of Least Privilege (PoLP) as we prepare the environment for "Zero Trust" policy". I started the process by defining only the ports necessary for business functions.

- **Default Deny:** I verified that by creating specific "Pass" rules for these services, the firewall's implicit "Block All" rule successfully dropped unauthorized traffic (like ICMP Ping) that I had not explicitly permitted.

###### Non-Repudiation & Auditing

I implemented Defense in Depth by hardening the endpoints themselves.

- **Host-Based Security:** I configured Group Policy Objects (GPO) to enable "Audit Process Creation," ensuring that every program execution generates a log entry. This is the foundation for the future SIEM ingestion.

- **Accountability:** On the Linux server, I disabled PermitRootLogin. This enforces Non-Repudiation, meaning, administrators must log in as their named user first, creating an audit trail in /var/log/auth.log before elevating privileges (i.e using sudo or logging staight as root user).

---

## 5. Troubleshooting as a Science

Throughout these labs, I encountered real-world theoretical conflicts:

- **Protocol Mismatches:** I learned that allowing TCP/UDP is not the same as allowing Any. My ping 8.8.8.8 failed because ICMP is its own protocol layer, separate from the transport layer. I also learned Firewall policies have to be in certain order.

- **Tamper Protection:** I discovered that modern OS defenses (Windows Defender Tamper Protection) actively fight against centralized management (GPO). At the end of every lab, I would validate to see if the changes I made actually applied and that is when I discovered this feature.

---

### Finishing thoughts...

I have successfully built the "User," "Identity," and "Server" Segments. Only thing I am missing is that I do not have a method to demonstrate network architecture in the traditional sense. Traditionally you will have a core layer, distribution layer and then finally access layer. There is also spine and leaf architecture. I will try to get my hands on some real equiment to demonstrate my understanding of scalability and redundancy.
Anyhow, the infrastructure is now segmented, integrated, and hardened. I am ready to implement Data Governance in Lab 6.
