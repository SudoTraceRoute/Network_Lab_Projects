## Project Overview
This project demonstrates how to remotely access an Ubuntu server (running as a VM behind a CG-NAT router and OPNsense firewall) using Tailscale. 
It enables secure SSH and Samba file access from an Android phone — no public IP, port forwarding, or dynamic DNS required.
Due to the limitations of my 5G SIM router, which operates behind carrier-grade NAT (CG-NAT) and lacks a publicly routable IPv4 address, I was unable to set up port forwarding. To work around this, I implemented Tailscale, which creates a secure, peer-to-peer encrypted tunnel without requiring a public IP. Tailscale attempts to establish a direct encrypted peer-to-peer connection between the phone and the server. When direct connectivity is blocked due to CG-NAT, Tailscale routes encrypted traffic through its nearest DERP relay server as a fallback — ensuring end-to-end secure communication even without port forwarding or public IPs



---

## Architecture Diagram 

![Screenshot](https://github.com/SudoTraceRoute/Network_Lab_Projects/blob/main/5.%20Remote%20access%20to%20the%20Ubuntu%20server%20via%20Tailscale/Network_diagram_Tailscale_tunnel.drawio.png)

---

## System Setup

- Virtualization: VirtualBox
- Network:
  - 5G SIM Router (CG-NAT, no port forwarding)
  - OPNsense VM
    - WAN: Bridged Adapter (gets IP from router)
    - LAN: Internal Network ("intnet")
  - Ubuntu Server VM, running Samba
    - Adapter: Internal Network ("intnet")
    - Gateway: OPNsense LAN IP

---

## Installation Steps

- Install & Configure Tailscale:
  
curl -fsSL https://tailscale.com/install.sh | sh

sudo tailscale up

     Login with same Tailscale account as your phone (just using e-mail address to login)
    
     Get Tailscale IP with tailscale status



- Android phone Setup

1. Install Tailscale from Play Store
   
2. Log in with same Tailscale account
   
3. CX File Explorer (Samba) to connect
   - Tap on '+ new location'
   - Tap 'remote' --> 'smb'
   - Enter the IP Tailscale has asssigned to the server,
   - Under 'Display name' add name/description of the device you are connecting to
Note: for the demontration purpouses, I have enabled "guest" login to the samba server, without additional passwords 

---
   
## Features
- ✅ Works behind CG-NAT (5G or ISP NAT)
- ✅ No need for public IP or port forwarding
- ✅ Peer-to-peer encrypted connection via Tailscale
- ✅ Remote SSH & Samba access from Android
- ✅ Firewall (OPNsense) remains untouched for inbound traffic

---

## Security Considerations
- Tailscale connections are encrypted end-to-end
- Limit who can access services using ACLs in Tailscale admin panel
- Use `--ssh` flag for secure identity-based SSH login

---

## Troubleshooting
- Ubuntu VM can't connect to Internet?
  → Check that OPNsense is doing NAT and has outbound WAN access

- Tailscale won't connect?
  → Ensure time sync, DNS working, and no firewall blocking outbound HTTPS

- Samba not accessible?
  → Confirm `smb.conf` shares, allow firewall or ufw for port 445

---

## Acknowledgements
- Tailscale Docs
- OPNsense community
