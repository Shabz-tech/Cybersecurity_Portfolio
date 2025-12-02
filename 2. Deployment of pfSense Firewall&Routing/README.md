# Lab2: Goodbye linux firewall/router - Hello, pfSense!

**Objective:** 
Deploy the pfSense firewall and establish the secure network zones using a 192.168.x.x addressing standard.

Move the Windows Server 2019 to the "Identity" subnet (192.168.10.0) right behind a firewall. Start implementing of Network Segmentation. Route traffic across subnet 192.168.10.0, 192.168.20.0, 192.168.30.0 using pfSense.

Unfortunately due to lack of money and resources, this lab is all virtual and Virtualbox does not support true Vlans and Dot1Q tagging of packets. So we have to use multiple network adaptors with pfSense that operate on the LAN side which will simulate Vlans. 

---

## 1. Network Architecture

On Virtualbox, in pfSense adaptor settings, I enabled Network Adaptors to resemble Vlans. WAN adaptor is set as a bridge adaptor but the Vlan10, 20, 30 are all Internal.
I used network 192.168.10.0/24 as Domain Controller. Behind this network, I placed my Windows Server 2019.

| Interface | Zone | Subnet | VirtualBox Network | Description |
| :--- | :--- | :--- | :--- | :--- |
| **WAN** | WAN | *Home DHCP* | Bridged Adapter | Physical Wi-Fi/Ethernet access. |
| **LAN** | **IDENTITY** | 192.168.10.0/24 | Vlan10 | Domain Controller & Auth. |
| **OPT1** | **USER** | 192.168.20.0/24 | Vlan20 | Client Workstations. |
| **OPT2** | **SERVER** | 192.168.30.0/24 | Vlan30 | App & Web Servers. |

---

## 2. Infrastructure Setup (VirtualBox)

### Step 2.1: Configure the "Hub" (pfSense VM)

**Action:** Right-click pfSense VM > **Settings** > **Network**. Ensure 4 Adapters are enabled.

* **Adapter 1 (WAN):**
    * Attached to: `Bridged Adapter`
   
* **Adapter 2 (LAN - Identity):**
    * Attached to: Internal Network
    * Name: Vlan10
    * *Promiscuous Mode: Allow All*
* **Adapter 3 (OPT1 - User):**
    * Attached to: Internal Network
    * Name: Vlan20
    * *Promiscuous Mode: Allow All*
* **Adapter 4 (OPT2 - Server):**
    * Attached to: Internal Network
    * Name: Vlan30
    * *Promiscuous Mode: Allow All*

---

## 3. pfSense Console Configuration

I then installed and fired up pfSense. I sent WAN interface to the relevant bridged network adaptor and LAN interface to the relevant Internal adaptor. I used MAC address to identify the correct adaptors.

### Set IP Addresses
Now it was time to Configure the (subnet 10.0) interface to enable login.

1. Select **Option 2** (Set interface IP).
2. Select Interface: **2** (LAN).
3. **Enter new LAN IPv4 address:** 192.168.10.1
4. **Enter new LAN subnet bit count:** 24
5. **Gateway/IPv6:** N/A
6. **Enable DHCP server on LAN?** no because I want to use Windows Server for DHCP later.
7. **Revert to HTTP?** y

---

## 4. The Migration

I moved the Windows Server 2019 "behind" the firewall by "moving" the VM Cable

1. VirtualBox Settings > Windows Server 2019 > Network.
2. **Adapter 1:** Change "Attached to" to Internal Network.
3. **Name:** selected Vlan10 from drop down menu.

### Step 4.2: IP Re-Addressing
Log into Windows Server 2019 and open ncpa.cpl. Set the IPv4 properties:

| Setting | Value |
| :--- | :--- |
| **IP Address** | 192.168.10.10 |
| **Subnet Mask** | 255.255.255.0 |
| **Default Gateway** | 192.168.10.1 |
| **DNS** | 127.0.0.1 |

---

## 5. pfSense Web Configuration

Now it was time to configure pfSense. My first ever time I used a proper Firewall. I opened the browser on Windows Server and typed http://192.168.10.1

I enter default Credentials which I have since changed.

### The Setup Wizard
* **Hostname:** firewall / **Domain:** shab.rmit.com
* **WAN Interface:** **Uncheck** "Block private networks".

### Interface Assignments (Interfaces > Assignments)
1. **Vlan10:** Rename to IDENTITYVlan10.
2. **OPT1:** Enable. Rename to USERVlan20.
    * IPv4: 192.168.20.1/24
3. **OPT2:** Enable. Rename to SERVERVlan30.
    * IPv4: 192.168.30.1/24

### Firewall Rules
I navigated to **Firewall > Rules** and Created a "Pass Any" rule for **IDENTITY**, **USER**, and **SERVER**.

This will be hardened as I continue with more labs but for the initial set-up and test connectivity - All protocols from above subnets are set to pass.

---

## 6. Validation

From the Windows Server 2019 Powershell, I then pinged Vlan10, 20 and 30 Gateways. I checked DNS Resolver and all pings were successful!!

## 7. Learning Outcome

I am really getting to understand Virtualisation and Hypervisors. I researched and learned about different types of Network Adaptors and their usecase inside Hypervisors for the purpose of this lab. 
I have also just now started to dip my toes in the world of pfSense and got my hands on an actual Firewall tool. 

I am excited to explore pfSense further and learn more about different policies/rules I can implement to control traffic going in and out of the network.

![Screenshot](1.png)
