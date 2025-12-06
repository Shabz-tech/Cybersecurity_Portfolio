# Lab4: Linux Server Deployment (Vlan30)

**Objectives**
Deploy a Linux Machine which will be our server on subnet 192.168.30.0/24 and test InterVlan connectivity. Join the Domain Controller and install SSH services on the Linux VM for admin remote access. 

---

I have fallen in love with Linux and also the Command Line in general since I found my love for Linux. I was told that a Security Engineer who only knows Windows is half blind and this is my way to showcase my familiarity with Linux. 

## 1. ​Network Achitecture

| Interface | Zone | Subnet | VirtualBox Network | Description |
| :--- | :--- | :--- | :--- | :--- |
| **WAN** | WAN | *Home DHCP* | Bridged Adapter | Physical Wi-Fi/Ethernet access. |
| **LAN** | **IDENTITY** | 192.168.10.0/24 | Vlan10 | Domain Controller & Auth. |
| **OPT1** | **USER** | 192.168.20.0/24 | Vlan20 | Client Workstations. |
| **OPT2** | **SERVER** | 192.168.30.0/24 | Vlan30 | App & Web Servers. |

So above is the table I created to give an idea of what our topology is. In this Lab, I installed and configured a Linux Mint VM to attach it to our LAN. 

## ​2. Infrastructure Deployment

### ​Step 2.1: Deploy Linux Mint
Install Linux Mint.
> **Network Configuration**
1. Inside Virtualbox, Right-click the new VM > Settings > Network.
2. **Adapter 1:**
   * Enable Network Adapter.
   * Attached to: Internal Network
   * Name: Vlan30 (Selected from the dropdown;that existed from Lab 1).
   * Promiscuous Mode: Allow all

> **OS Installation & IP Configuration**
1. Boot up the Linux VM.
2. After our OS is ubdated - Click on Networks Settings.
3. Click on geared icon to edit the Connection.
4. Click on ipv4 settings.
* **Change the Method from DHCP to Manual in the drop down menu.**
* **Enter Address:** 192.168.30.10
* **Enter Subnet:** 24
* **Enter Gateway:** 192.168.30.1
In the DNS and Domain Fields:
* **DNS:** 192.168.10.10
* **Search Domains:** shab.rmit.com
5. Restart


## ​3. Connectivity Validation (Before Domain Join)
After following the above step, I tested connectivity before joining our domain.
I did this by pinging our Gateway, DC and or domain.

I pinged 192.168.30.1 and 192.168.10.10 (DC) and I recieved all packets that were sent.

---

## 4. Joining the Domain
Before joining the domain, I installed the lasted OS updates.
1. Open terminal
2. Type sudo apt update && sudo apt upgrade -y
3. Press enter
4. Wait

I then installed SSH server after OS had finished updating.
1. sudo apt install openssh-server

Now the machine is ready to connect to our Domain. 

On Linux, there is no Active Directory (AD) so I downloaded the dependencies which makes it simpler for Linux users to join AD.
1. In terminal run the below command in that order.
2. sudo apt install realmd sssd sssd-tools libnss-sss libpam-sss adcli samba-common-bin oddjob oddjob-mkhomedir packagekit -y
3. sudo realm discover shab.rmit.com
Once discovered - it means our DNS settings are correct.
4. sudo realm join -U Administrator shab.rmit.com
And finally to confirm that I had joined, I ran the below command.
5. id administrator@shab.rmit.com

I will take more about these commands in my learning outcome section.

## 5. SSH Server
Now it was time to test our Linux VM's SSH services.
1. Open Windows Client VM on 192.168.20.0/24 subnet.
2. Logged into our domain using Administrator credentials.
3. Opened CMD prompt.
4. Typed ssh administrator@shab.rmit.com@192.168.30.10
5. Press enter.
6. Enter password.

I was in!! I was able to go around the file systems on my Linux machine while I was on my Windows VM.

---

## 6. Learning Outcomes
I want to mention again how much I love Linux and I absolutely love the terminal since I have been getting into Linux.
I am currently doing a few courses for Linux on Udemy to get more and more familiar with the system.
First of all, I realise that I cannot possibly memorise all these commands and that is OKAY.
I just need to learn the patterns and a few basic commands to navigate my way around the system.
So far, I have learnt that you have your categories and then commands for those categories. 

A few examples are:
- For file navigation I use ls, cd, pwd. 
- For editing, it is nano or vim. 
- For file operators, use commands such as cp, mv, rm, mkdir. 
- chmod for permissions. 
- ps for processes. 
- Networking commands such as ip, ping iptables, netstat. 
The list goes on.

Now all these commands are followed by an option argument.

I learnt to navigate the man page of different commands to learn about the options and what they do.

Linux also has online resources which I used a lot to learn about Active Directory dependancies and also routing when I was working on Lab 1.

Mastery Linux is knowing what you want to do and how to describe it in Google or man pages. All jokes aside, I learnt that I need to use the correct resources and build muscle memory inside the terminal by repeating tasks.

![Screenshot](3.png)
![Screenshot](4.png)
