# Windows Server 2019: DHCP, DNS, IIS, and FTP Server Configuration & Testing - also Routing

## Overview

This lab documents my practical experience configuring a Windows Server 2019 environment to provide core network and web services. This was something I built as part of one of my projects at RMIT. 

It is a basic client server model with a Linux VM acting as a Firewall and Router. It is a simple network that is part of 192.168.10.0/24 subnet. There are no Vlans and it is definetly not hardened. I will build the rest of the homelab virtual network on top of this and implement industry standard firewall/router, implement Vlans, use IDS/IPS, SEIM tools etc.

Back to the current lab!

I found Windows Server 2019 functions straight forward. The challenge started when I wanted to route all traffic via Linux Firewall. However, we persevered and finished our tasks.

The tasks included:

- Installing Active Directory DS.
- Joining a Windows 10 client to the server's domain
- Installing and configuring DHCP Server and DNS.
- Installing and testing IIS with ASP.NET support
- Installing and testing an FTP Server within IIS
- A Linux VM that acts as a router/firewall with 1x NIC for NAT (internet) and 1x NIC for internal LAN
- Route all traffic through Linux VM and back. Server and Client will not be connecting to the internet directly.
- UFW rules set to protect server from icmp floods.

The lab is something I decided to do to expand my knowledge on real world networking. I had the option to use tools such as packet tracer but I love playing with real tools too much.

## Environment

- Server OS: Windows Server 2019 (Domain Controller)
- Client OS: Windows 10 (Domain-joined)
- Firewall/Router: Linux Mint
- Roles & Features Installed:
  - AD
  - DNS
  - DHCP Server
  - Web Server (IIS) with ASP/ASP.NET
  - FTP Server within IIS

---

## Tasks Performed

### 1. Domain Join & Network Configuration
- Configured static IP for server (e.g., 192.168.10.10) with server as DNS
- Verified connectivity using ping between client and server
- Joined Windows 10 client to the server's domain
- Logged into client using a domain user account

---

### 2. DHCP Server Installation & Scope Configuration
- Installed DHCP Server role via Server Manager
- Created a new scope:
  - IP range: 192.168.1.50 – 192.168.1.150
  - Lease Duration: 8 days
  - No exclusions or WINS configuration
- Activated scope and authorized the DHCP server
- Verified lease assignment by setting client to DHCP mode:
  - Confirmed via `ipconfig` on client
  - Verified lease in DHCP Management Console

---

### 3. IIS Installation & ASP.NET Support
- Installed IIS Web Server role via Server Manager
- Included required services:
  - ASP
  - ASP.NET
  - Static and dynamic content delivery
- Verified DNS configuration (Forward Lookup Zones)
- Tested IIS using:
  - http://shabtech.com
  - http://192.168.10.10
  - http://127.0.0.1

---

### 4. **FTP Server Installation & Testing**
- Installed **FTP Server** as part of IIS role
- Created FTP site in IIS:
  - Root folder: `C:\inetpub\wwwroot`
  - Configured authentication and permissions
- Tested FTP connection from client browser
- Logged in with domain user credentials
- Confirm file transfer capability

### 5. **Linux Mint (Router/Firewall)** 
This was a big learning step for me on my journey to becoming proficient at linux.
Prior to this, I did not know that linux VM can be used as a firewall and router.

I learnt about using linux tools to create firewall rules and packet forwarding via iptables. I researched and learnt to use sysctl to enable Packet Forwarding to enable Linux to forward packets from LAN to NAT adaptor.

I used a lightweight DNS forwarder (dnsmasq) as Windows Server DNS was not working. 

I learnt the hard way about how to save and reload the iptable rules on reboot. By using using iptables-persistant and netfilter-persistent save command!.

The tools used for testing were - ping, ip a, nslookup. I also used iptables command to check if packet forwarding from LAN interfaces to WAN inside virtualbox were configured on the correct interfaces. Finally tcpdump command to check packets recieved on interfaces to troubleshoot issues I caused by not saving iptables.

It was also really cool to see how Virtualbox network adaptors operate and connect VMs together.

--------
[](1.png)
[](2.png)
[](3.png)
[](4.png)
[](5.png)
[](6.png)
[](7.png)
[](8.png)
[](9.png)
[](10.png)
[](11.png)
[](12.png)
[](13.png)
