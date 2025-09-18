#  Segmented Virtual Network with OPNsense Firewall (VirtualBox Lab)

This project sets up two isolated subnets in VirtualBox using **OPNsense** as the router/firewall. It demonstrates subnet segmentation, internet access, and strict firewall controls using free and open-source tools. Here we are showing on a small scale, some of the practices that are often used in compnanies.

---

##  Network Topology

![Screenshot](https://github.com/SudoTraceRoute/Network_Lab_Projects/blob/main/3.%20Segmented%20Virtual%20Network%20with%20OPNsense%20Firewall%20(VirtualBox%20Lab)/Network_topology.png)



---

##  VirtualBox VM Configuration

###  OPNsense VM

- **Adapter 1 (em0)**: Bridged Adapter (WAN)
- **Adapter 2 (em1)**: Internal Network `intnet1` (LAN)
- **Adapter 3 (em2)**: Internal Network `intnet2` (LAN2)

###  Other VMs

| VM           | Adapter Network | IP Address      | Gateway         |
|--------------|------------------|------------------|------------------|
| Ubuntu Server| `intnet1`       | 192.168.10.10    | 192.168.10.1     |
| Lubuntu      | `intnet1`       | 192.168.10.20    | 192.168.10.1     |
| Lubuntu_2    | `intnet2`       | 192.168.20.20    | 192.168.20.1     |

All guest VMs use **static IPs**.

---

##  OPNsense Configuration

### Interfaces

- **WAN (em0)**: Bridged, DHCP
- **LAN (em1)**: 192.168.10.1/24
- **LAN2 / OPT1 (em2)**: 192.168.20.1/24

### Aliases

**Private_Nets**:
- 192.168.0.0/16
- 10.0.0.0/8
- 172.16.0.0/12

### NAT > Outbound

Mode: **Hybrid Outbound NAT**

| Interface | Source           | Translation        |
|-----------|------------------|---------------------|
| WAN       | LAN net          | Interface address   |
| WAN       | LAN2 net         | Interface address   |

✅ **Disable "Reply-to"** to prevent routing issues.

---

##  Firewall Rules

### LAN (192.168.10.0/24)

| Rule Order | Source          | Destination        | Action | Description                    |
|------------|------------------|---------------------|--------|--------------------------------|
| 1          | LAN net         | LAN net            | Pass   | Allow LAN intra-communication |
| 2          | LAN net         | !Private_Nets      | Pass   | Allow Internet only           |
| 3 (opt)    | 192.168.10.10   | 192.168.20.20      | Pass   | Allow server to reach Lubuntu_2 |

### LAN2 (192.168.20.0/24)

| Rule Order | Source          | Destination        | Action | Description                    |
|------------|------------------|---------------------|--------|--------------------------------|
| 1          | 192.168.20.20   | 192.168.20.1       | Pass   | Allow ping to gateway          |
| 2          | 192.168.20.20   | 192.168.10.10      | Pass   | Allow access to Ubuntu Server  |
| 3          | 192.168.20.20   | !Private_Nets      | Pass   | Allow Internet only            |

---

## 🧪 Troubleshooting Done

- ✅ Switched from NAT to Bridged mode to fix WAN connectivity
- ✅ Used `Interfaces > Diagnostics > Ping` to verify routing
- ✅ Edited `/etc/systemd/resolved.conf` on Lubuntu_2 to fix DNS:
  ```ini
  [Resolve]
  DNS=8.8.8.8
    • ✅ Verified firewall state tables and packet capture
    • ✅ Confirmed OPNsense NAT & Firewall behavior using logging and Live View


📌 Notes
    • OPNsense is a stateful firewall — return traffic is allowed automatically
    • Default rule set: deny all unless allowed
    • All segmentation enforced via firewall rules

📚 Credits
    • OPNsense
    • VirtualBox
    • Guide authored via hands-on network configuration and iterative troubleshooting
