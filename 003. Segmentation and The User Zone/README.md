# Lab3: Segmentation & The User Zone

**Objectives**
Deploy a Windows Machine in on subnet 192.168.20.0/24. Test InterVlan connectivity between Subnet 20.0 and the rest of the Subnets.

---

## 1. ​Network Achitecture
The heavy lifting was done in the last lab so that we could smoothly carry out the task in this lab.
Here is the same table again:
| Interface | Zone | Subnet | VirtualBox Network | Description |
| :--- | :--- | :--- | :--- | :--- |
| **WAN** | WAN | *Home DHCP* | Bridged Adapter | Physical Wi-Fi/Ethernet access. |
| **LAN** | **IDENTITY** | 192.168.10.0/24 | Vlan10 | Domain Controller & Auth. |
| **OPT1** | **USER** | 192.168.20.0/24 | Vlan20 | Client Workstations. |
| **OPT2** | **SERVER** | 192.168.30.0/24 | Vlan30 | App & Web Servers. |

In the last lab, I configured pfSense to implement InterVlan Routing on Virtualbox (not real InterVlan Routing, but for the sake of making this work), We configured Interfaces on pfSense which are the Gateways for machines in our Vlans.
So far, we have placed our Windows Server 2019 on subnet 192.168.10.0/24

## Our Topology and Why it matters.
In the last lab, I mentioned we have moved the Server behind the Firewall. Well technically that is correct, as no-one can access the Server/DC without going through the firewall. Our actuall topology looks more like a Hub and Spoke or Star Topology.
Each Vlan is connected on their own separated spoke to the Firewall/Router. All traffic is routed though pfSense before it leaves each Vlan.
Network Segmentation was the FIRST lesson in CyberSecurity that was taught to me during my Networking class.

---

## ​2. Infrastructure Deployment

### ​Step 2.1: Deploy Windows 10 Client
​I created a new VM for Windows.

> **Network Configuration**
1. Right-click the new Windows 10 VM > Settings > Network.
2. **Adapter 1:**
   * Enable Network Adapter.
   * Attached to: Internal Network
   * Name: Vlan20 (Selected from the dropdown;that existed from Lab 1).
   * Promiscuous Mode: Allow all

> **OS Installation & Naming**
1. Install Windows 10 Education (Home edition cannot join domains). Luckily I get access to these OS images via University.
2. Once installed, log in as a local user
3. Rename the PC:
  Settings > System > About > "Rename this PC".
  Name it anything you want.
4. Restart.

---

## ​3. IP Configuration (The "Cross-Subnet" Setup)
​We do not have a DHCP Relay yet. So I used Static IP for now.

​This PC is in 192.168.20.0/24 subnet. It needs to ask the Server in 192.168.10.0/24 subnet "Where is the Domain?",

Therefore, the DNS Server IP must point to the Remote Domain Controller.

I opened Network Connections (`ncpa.cpl`) Properties > IPv4 and I set the below Config.

* **IP Address:** 192.168.20.10
* **Subnet Mask:** 255.255.255.0
* **Default Gateway:** 192.168.20.1 (The pfSense interface for this zone).
* **Preferred DNS:** 192.168.10.10 (Crucial: This is your DC's IP in the other zone. Since we have done the routing in Lab 1, it all will be connected.).

---

## ​4. Connectivity Validation (Before Domain Join)
Befor jumping straight into joining the Domain, I tested connectivity.
**Test: Can we ping Gateway, DC, and does our DNS work?**
Answer: Yes, Yes and YES.
So i Pinged 192.168.20.1 and 192.168.10.10 (DC) and I recieved all packets that were sent.
I again used Resolve-DNSname shab.rmit.com   <---- this is the domain i am going with
The Reslove-DNSname returned bakc my DC Ip - 192.168.10.10
Perfect!

## 5. Joining the Domain
Now lets get our Windows User VM into our Domain.
This can be easily done by going into Access work or School settings, click connect and select "Join this device to a local Active Directory Domain".
Then I simply typed my Domain Name in the box and moved forward.
I was promped for Credentials where I used Admin credentials for the server and we were IN after a quick restart.

I then used some command line tools to double-check connectivity.
![Screenshot](2.png)

