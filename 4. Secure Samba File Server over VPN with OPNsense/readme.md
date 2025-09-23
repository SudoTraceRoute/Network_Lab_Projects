## Secure Samba File Server over VPN with OPNsense

This Project creates a secure, subnet-isolated file server environment using Samba and OPNsense, with access gated through a VPN tunnel.

While this implementation is confined to the local network due to CG-NAT ISP limitations, it mirrors real-world use cases like office network segmentation, guest access isolation, and internal-only services accessed via VPN.


--- My original idea was to enable remote access to the Ubuntu server on the subnet, by using OPNsense and WireGuard, BUT... My router is 5G sim card router, therefore it doesn't have a static public IPv4 address, instead the ISP auto assignes CG-NAT IP, which is not routable from the public internet, therefore I can't use my non-existent public IP to set port forwarding for WireGuard on the router. CG-NAT IP is translated into a public IP on the ISP's router, through which I can access the internet. We will talk more about this setup and how it works in some of the next projects. ---

---

## Features of this setup:

- 🔒 Secure server access via VPN (WireGuard)
- 🛡️ Network segmentation and isolation using OPNsense
- 📁 Samba guest share with upload capability (anonymous access)
- 📱 Access tested via Android (CX File Explorer over VPN)
- 🔧 Firewall rules a bit looseley scoped to allow SMB traffic over VPN. Strict Firewall rules lead to frequent connection disturbances and access unstability.

---

## 🧰 Tech Stack

| Component     | Technology                     |
|---------------|--------------------------------|
| VPN Server    | OPNsense (WireGuard) |
| Firewall      | OPNsense                       |
| File Sharing  | Samba on Ubuntu Server         |
| Mobile Client | CX File Explorer (Android)     |
| Protocols     | SMBv1 (temp), SMBv2+, TCP 139/445 |


---

## 🛠️ Setup Overview

### 1. Samba Server Configuration

- OS: Ubuntu Server (20.04/22.04)
- Shared path: `/srv/samba/public`
- Permissions: Guest write access enabled
- Samba config:
  
  [Public]
  
     path = /srv/samba/public
  
     browsable = yes
  
     writable = yes
  
     guest ok = yes
  
     guest only = yes
  
     force user = nobody

  
2. VPN Configuration (OPNsense)
3. 
    • VPN type: WireGuard
   
    • Client: Android (WireGuard app)
   
    • Routing: VPN subnet to Samba subnet
   
    • DNS: Internal DNS

   
5. Firewall Rules

    • Allow from VPN interface:
   
        ◦ TCP 139 (NetBIOS Session)
   
        ◦ TCP 445 (SMB)

   
7. Android Client Test
   
    • App: CX File Explorer
   
    • Method: Connect via SMB to 192.168.x.x/Public
   
    • Auth: Guest  (no password)
   
    • Test: Upload image to Samba share
   

🔐 Security Notes

    • Guest access allowed for convenience (can be hardened later)
    
    • Temporarily enabled SMBv1 (server min protocol = NT1) to support CX File Explorer

      

📷 Screenshots

1. ✅ Successful VPN Connection (on phone)
e.g. WireGuard app "Connected"

2. 📁 CX File Explorer Connected to Share
Showing the Public folder contents

3. 📤 Upload Confirmation / Images in Share
A photo uploaded and visible in /srv/samba/public

4. 🔧 OPNsense Firewall Rules
Screenshot of rule allowing VPN → Samba ports (redact IPs)

6. 💻 Server-side Confirmation
Terminal screenshot: ls /srv/samba/public showing uploaded file



📚 Learning Outcomes

    • VPN tunneling
    
    • Subnet design and inter-network routing
    
    • Secure Samba deployment over isolated networks
    
    • Firewall rule planning and testing
    
    • Cross-platform service access (Android ↔ Linux)
    

📦 License
MIT License
