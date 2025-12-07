# Labs 5 to 10 - The Summary

The focus in these labs shifted to Network Segmentation (Layer 3), Identity and Access Management (IAM), and establishing a baseline for Security Monitoring.

---

## 1. Network Segmentation and Gateway Hardening (Lab 5)

The foundational step involved applying network segmentation using Virtual Local Area Networks (VLANs) managed by a pfSense Firewall.

| **Zone**            | **VLAN ID** | **Subnet**      | **Purpose**                         |
| ------------------- | ----------- | --------------- | ----------------------------------- |
| **Identity/Server** | 10          | 192.168.10.0/24 | Domain Controller, Central Services |
| **User/Client**     | 20          | 192.168.20.0/24 | Standard Employee Workstations      |
| **Log Server**      | 30          | 192.168.30.0/24 | Syslog Aggregation Point (Isolated) |
| **DMZ**             | 40          | 192.168.40.0/24 | Public-Facing Web Services          |

This established a Trust Boundary, forcing all inter-VLAN traffic through the firewall's rule set to enforce the Principle of Least Permissiveness.

---

## 2. Centralized DHCP and Broadcast Relay

To eliminate manual IP addressing and simulate enterprise management, Dynamic Host Configuration Protocol (DHCP) was centralized on the Windows Server 2019 Domain Controller (VLAN 10).

- **The Constraint:** DHCP relies on the DORA process, initiating with a DHCP Discover broadcast packet (255.255.255.255). By design, routers (pfSense) do not forward broadcast traffic across Layer 3 boundaries, preventing Clients in VLAN 20 from reaching the Server in VLAN 10.

- **The Solution:** A DHCP Relay Agent was configured on the pfSense firewall.
  
  - **Function:** The Relay listens for broadcasts on UDP Port 67 (Server side) and UDP Port 68 (Client side) on the client interfaces.
  
  - **Mechanism:** It intercepts the broadcast "Discover" packet, encapsulates it as a Unicast packet, and routes it directly to the Domain Controller IP (192.168.10.10).

- **Result:** Scalable address management where a single server manages IP Scopes for multiple isolated subnets.

---

## 3. File Services and Data Access Control (Lab 6)

Data security was implemented by configuring a network file share on the Windows Domain Controller, focusing on Defense in Depth through permissions layering.

- **Protocol:** Server Message Block (SMB) operating primarily over TCP Port 445.

- **Permissions Distinction:** Access was controlled using two distinct mechanisms:
  
  1. **Share Permissions:** Control remote access to the share (Network level).
  
  2. **NTFS Permissions:** Control local file system access on the host drive (Storage level).

- **Principle of Least Privilege (PoLP):** Permissions were explicitly denied via NTFS at the file level for standard Domain Users, granting them only Read & Execute access, thereby ensuring Data Integrity and Confidentiality.

---

## 4. Intrusion Detection System (IDS) Deployment (Lab 8)

Visibility into network traffic payloads was achieved by deploying Suricata as a dedicated IDS sensor.

- **Monitoring Mode:** The IDS was configured to operate in Promiscuous Mode, enabling the system to inspect a copy of all packets traversing the local network interface, regardless of the intended destination MAC address.

- **Detection Mechanism:** Suricata performs Deep Packet Inspection (DPI) at Layer 7 (Application Layer), checking traffic against published signature sets, specifically the Emerging Threats (ET) Open rule set.

- **Validation:** Detection logic was validated by triggering a known signature (testmyids.com request), verifying the IDS successfully identified the malicious payload content within the TCP stream.

---

## 5. Architectural Enhancement: Demilitarized Zone (DMZ) (Lab 9)

The network architecture was expanded to safely host vulnerable, internet-accessible services.

- **DMZ Purpose:** VLAN 40 was designated as the DMZ, providing a segregated buffer zone between the public WAN and the internal, sensitive Identity/User VLANs.

- **Deployment:** A vulnerable test application (OWASP Juice Shop) was deployed using Docker within an Ubuntu Server instance on the DMZ subnet.

- **Hypervisor Adaptation:** The pfSense VM was manually configured using the command-line utility VBoxManage to bypass the graphical interface's limit of four Network Interface Cards (NICs), allowing the critical fifth interface for the DMZ to be provisioned.

---

## 6. Centralized Log Aggregation (Lab 10)

A dedicated, isolated server was deployed to function as the central repository for all network and host events, establishing the precursor to a Security Information and Event Management (SIEM) system.

- **Log Server Host:** Rsyslog on Ubuntu Server (VLAN 30).

- **Protocol Used:** Syslog over UDP Port 514.

- **Rationale for UDP:** UDP is preferred for high-volume, performance-critical logging due to its low overhead and speed, prioritizing throughput over guaranteed delivery.

- **Configuration:** The pfSense firewall was configured to forward all system and security logs to the central Rsyslog server, creating a single Chain of Custody point for all network events.

---

## Conclusion

The successful completion of these labs resulted in a Network architecture defined by: enforced segregation, monitored traffic at the application layer, controlled data access, and centralized event logging, preparing the environment for sophisticated threat simulation and incident response scenarios.

The next step is utilising the Kali Linux client to launch simulated attacks against the deployed services and logging infrastructure.
