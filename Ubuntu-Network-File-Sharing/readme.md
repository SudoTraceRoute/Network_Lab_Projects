
## ✅ Project Overview

This lab simulates a small home network environment using:

- **Ubuntu Server** running in **VirtualBox**
- **Samba** for file sharing
- **Android phone** as client
- Bridged networking for LAN access
- Troubleshooting and configuration steps included

---

## 📂 Goal

Set up a Samba file share on a local Ubuntu Server VM and access it from an Android phone connected to the same LAN. Upload a photo from the Android phone to the server, then download a photo from the Ubuntu Server using Lubuntu VM. Restricted access to the Ubuntu Server will be configured via OPNsense firewall in the next projects (optional phase 2).

---

## 🔧 Tools Used

| Component       | Details                            |
|----------------|-------------------------------------|
| Host OS         | Linux or Windows                   |
| VirtualBox      | VM platform                        |
| Ubuntu Server   | Guest OS (no GUI)                  |
| Android Phone   | Used for accessing SMB share       |
| Cx File Explorer| Android app for SMB                |
| OPNsense (optional) | Firewall VM for LAN segmentation |

---

## 🛠️ Setup Summary

### Ubuntu Server Network

- Interface: `enp0s3`
- Bridged Mode (connected to physical LAN)
- Static IP: `192.168.1.150`

### Samba Share

- Folder path: `/srv/samba/shared`
- Access: Guest (no password)
- Share name: `Shared`

---

## ✅ Samba Installation and Configuration

```
Step 1: Install Samba

- sudo apt update
- sudo apt install samba -y


Step 2: Create Share Folder

- sudo mkdir -p /srv/samba/shared
- sudo chmod 0777 /srv/samba/shared


Step 3: Configure Samba
Edit the config:

- sudo nano /etc/samba/smb.conf
Add at the bottom:

[Shared]
  path = /srv/samba/shared
  browseable = yes
  read only = no
  guest ok = yes

Restart the Samba service:
- sudo systemctl restart smbd

---
🔍 Troubleshooting SMB Access from Android Problem
Could not connect from Android phone using Cx File Explorer. Connection timed out or showed “empty folder”.


Diagnostic Steps
✅ 1. Confirmed Samba was Running

sudo systemctl status smbd


✅ 2. Verified Samba was Listening
sudo ss -tulnp | grep smbd
Expected output:

LISTEN 0 50 0.0.0.0:139
LISTEN 0 50 0.0.0.0:445


✅ 3. Checked Server IP Address
ip a
Found IP: 192.168.1.5



⚠️ 4. Discovered IP Conflict

- Phone was also assigned 192.168.1.5 via DHCP → IP conflict!

✅ Resolution: Set Static IP for Server
- systemd-networkd config:

- sudo nano /etc/systemd/network/10-enp0s3.network

- Paste:

[Match]
Name=enp0s3

[Network]
Address=192.168.1.150/24
Gateway=192.168.1.1
DNS=1.1.1.1 8.8.8.8

Apply:

- sudo systemctl restart systemd-networkd


🔁 Verified Fix
Server now at 192.168.1.150

Phone can now connect with Cx File Explorer or X-plore

Share appears as expected

🔐 (Optional) Firewall Lockdown with OPNsense
Coming soon: restrict Samba access so only your phone can connect, and block all other LAN devices using OPNsense firewall rules.

📸 Screenshots of:

Ubuntu IP config

Samba config file

Cx File Explorer showing share

OPNsense rule (if added)

```
---

**Network Diagram**

Network Diagram Description

The network diagram illustrates the current lab setup:

Physical LAN (192.168.1.0/24):

Includes the physical router (192.168.1.1), host PC, Android phone, and the Ubuntu Server VM.

Ubuntu Server: Connected via a bridged adapter with static IP 192.168.1.150, making it accessible on the LAN.
    
Lubuntu VM: Has two adapters — one NAT adapter (10.0.2.15) for internet access and one internal network adapter (not assigned an IP or used in this setup).
    
OPNsense VM: Present but turned off; intended for future segmentation and firewalling.
    
Connectivity: All active devices communicate via the physical router without network segmentation, meaning there is currently one flat LAN.
    
The diagram helps visualize device roles, IP addressing, and physical vs virtual network boundaries.

---


🧠 Lessons Learned
IP conflicts silently break LAN access — always check both ends

Bridged VM networking allows full LAN participation

Samba is fast to set up for testing, but care is needed with permissions

Real-world troubleshooting is more valuable than perfect tutorials

---

📚 References
https://wiki.samba.org

https://ubuntu.com/server/docs/samba-file-server

https://docs.opnsense.org/manual/firewall.html







## 📘 Lessons Learned

This project wasn't just about setting things up — it was about learning how to troubleshoot and adapt when things don’t work the first time. Here are some of the key takeaways:

### 🧠 1. IP Conflicts Are The Real Thing
My server and phone both got assigned the same IP (`192.168.1.5`), which broke SMB access with no clear error message. I learned how to detect and resolve IP conflicts by manually assigning a static IP.

### 🧠 2. There’s More Than One Way to Configure Networking in Ubuntu  
I initially expected Ubuntu to use `netplan` or `/etc/network/interfaces`, but neither existed. That forced me to research and learn about `systemd-networkd`, which is used in minimal or headless Ubuntu Server installations. I manually created a `.network` config file and applied it successfully.

### 🧠 3. Don’t Assume It’s a Samba Problem  
Even though the file share appeared "empty," it wasn’t Samba’s fault — it was a network-level issue (IP conflict). This helped me build better troubleshooting habits by checking IPs, services, and routes before changing configs blindly.


> 🚀 This project taught me practical Linux networking, service configuration, and troubleshooting — all things a person would encounter in real IT and sysadmin roles.
