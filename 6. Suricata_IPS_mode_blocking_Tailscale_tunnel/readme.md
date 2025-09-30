Suricata IPS Setup on OPNsense to Block Tailscale

This guide outlines the steps taken to configure Suricata in IPS mode on OPNsense, with custom rules to detect and block Tailscale and WireGuard traffic.

✅ Summary

Enabled Suricata IPS mode on OPNsense.

Created a custom rules file with Suricata rules to block:

UDP port 41641 (Tailscale/WireGuard)

UDP port 51820 (standard WireGuard)

TCP port 443 (Tailscale DERP fallback / HTTPS)

📁 Custom Rules Directory

Create a new directory for organizing custom rules:

mkdir -p /usr/local/etc/suricata/opnsense-rules/

✍️ Create Tailscale Rules File

Edit the custom rule file:

vi /usr/local/etc/suricata/opnsense-rules/tailscale.rules


Paste the following rules:

# Block Tailscale - WireGuard UDP on port 41641
drop udp any any -> any 41641 (msg:"TAILSCALE - Detected UDP traffic on port 41641 (Tailscale/WireGuard)"; sid:1000010; rev:1;)

# Block WireGuard default UDP port 51820
drop udp any any -> any 51820 (msg:"TAILSCALE - Detected UDP traffic on port 51820 (Standard WireGuard)"; sid:1000011; rev:1;)

# Block potential fallback to HTTPS (TCP 443)
drop tcp any any -> any 443 (msg:"TAILSCALE - Detected TCP traffic on port 443 (Possible HTTPS fallback)"; sid:1000012; rev:1;)

🔗 Link Custom Rules into Suricata
ln -s /usr/local/etc/suricata/opnsense-rules/tailscale.rules /usr/local/etc/suricata/rules/tailscale.rules

⚙️ Enable the Rules in Suricata

Edit /usr/local/etc/suricata/suricata.yaml and make sure your custom file is listed under rule-files:

rule-files:
  - tailscale.rules

🔄 Restart Suricata
service suricata restart

🧪 Verify Rule Load
suricata -T -c /usr/local/etc/suricata/suricata.yaml -v


Look for tailscale.rules in the output and ensure no errors are shown.

📋 Notes

Rules set to drop will actively block traffic.

Use alert instead of drop during testing to only log traffic.

Make sure Suricata is in IPS mode and netmap is enabled on the correct interfaces.

Blocking TCP 443 will disrupt general HTTPS access — use with caution.
