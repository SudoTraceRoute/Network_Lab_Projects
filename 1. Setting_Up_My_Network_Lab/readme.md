Here you will find the instructions on how to install and configure Ubuntu server runing Samba, Lubuntu and OPNsense in VirtualBox.


**📘 Lubuntu Installation – VirtualBox Setup Guide**

This guide outlines the process of installing Lubuntu 24.04 (or similar versions) in a VirtualBox virtual machine. Lubuntu was selected as a lightweight desktop client OS for use in a virtual networking lab (with OPNsense and Ubuntu Server).

🖥️ 1. Create New VM in VirtualBox
    1. Open VirtualBox and click New.
    2. Name the VM: Lubuntu-Client (or any preferred name).
    3. Set:
        ◦ Type: Linux
        ◦ Version: Ubuntu (64-bit)
    4. Allocate RAM:
        ◦ ✅ Recommended: 1024–2048 MB
    5. Create a virtual hard disk:
        ◦ Type: VDI (VirtualBox Disk Image)
        ◦ Size: 10 GB or more
        ◦ Dynamically allocated

📀 2. Attach Lubuntu ISO
    1. Go to Settings → Storage.
    2. Under Controller: IDE, click the empty disk icon.
    3. Click the CD icon (on the right) and choose “Choose a disk file…”.
    4. Select your downloaded Lubuntu .iso file.

🌐 3. Configure Network Adapter
    1. Go to Settings → Network.
    2. Adapter 1:
        ◦ Attached to: Internal Network
        ◦ Name: intnet (same as other VMs to enable local communication)
    3. (Optional) Add Adapter 2 later if you want external access.

🚀 4. Start the VM and Begin Installation
    1. Start the VM.
    2. In the boot menu, select:
        ◦ ✅ Try or Install Lubuntu
    3. Wait for the graphical installer to load.

🛠️ 5. Install Lubuntu
    1. Select language, keyboard layout, and continue.
    2. Choose Erase disk and install Lubuntu.
        ◦ Select the virtual disk:
            ▪ e.g., VBOX HARDDISK - 10.00 GB
        ◦ Under Advanced options:
            ▪ ✅ Swap to file
            ▪ ✅ Filesystem: ext4
    3. Continue with installation.

👤 6. User Account Setup
    1. Enter your name, username, and password.
    2. Choose whether to require password at login.
    3. Continue installation.

💾 7. Post-Installation Steps
    1. Wait for the installation to finish (can take 5–15 minutes).
    2. When prompted, click Reboot Now.
    3. If asked to remove installation media:
        ◦ Go to VirtualBox Settings → Storage
        ◦ Remove the Lubuntu .iso from the IDE controller (if still attached).

✅ 8. First Boot & Basic Checks
    1. Log in with the user account you created.
    2. Open a terminal and check IP:
       ip a
        ◦ Confirm that the VM has a virtual IP if connected to intnet.
    3. Test network access to other VMs (e.g., ping Ubuntu Server or OPNsense).


---


**📘 How to Install Ubuntu Server on VirtualBox**

This guide walks you through installing Ubuntu Server on VirtualBox, configuring it for basic usage including SSH access.

Prerequisites
    • Download and install VirtualBox.
    • Download the latest Ubuntu Server ISO from the official site (e.g., 22.04 LTS or 24.04 LTS).

🖥️ Step 1: Create a New Virtual Machine
    1. Open VirtualBox and click New.
    2. Name your VM (e.g., Ubuntu_Server).
    3. Set:
        ◦ Type: Linux
        ◦ Version: Ubuntu (64-bit)
    4. Assign memory (RAM). Recommended: 2048 MB or more.
    5. Create a new virtual hard disk:
        ◦ Format: VDI
        ◦ Storage on physical hard disk: Dynamically allocated
        ◦ Size: At least 10 GB

📀 Step 2: Attach Ubuntu Server ISO
    1. Go to Settings → Storage.
    2. Under Controller: IDE, click the empty optical drive.
    3. Click the small CD icon on the right and select Choose a disk file.
    4. Browse to your downloaded Ubuntu Server ISO and select it.

🌐 Step 3: Configure Network (Optional)
    • For basic internet access, use Adapter 1 as NAT.
    • For advanced setups (e.g., with OPNsense firewall), add Adapter 2 as Internal Network and name it (e.g., intnet).

🚀 Step 4: Start the VM and Install Ubuntu Server
    1. Start the VM.
    2. From the GRUB menu, select “Try or Install Ubuntu Server”.
    3. Follow the installation prompts:
        ◦ Select language and keyboard layout.
        ◦ Configure network (default DHCP or manual).
        ◦ Select the virtual hard disk to install to (e.g., VBOX_HARDDISK).
        ◦ Choose guided partitioning (use entire disk with LVM recommended).
        ◦ Create your user account (username and password).
        ◦ Enable OpenSSH server for remote access.
        ◦ Skip installing snaps for now (optional).

🛠️ Step 5: Complete Installation and Reboot
    1. Wait for the installer to complete (this can take several minutes).
    2. When prompted, reboot the VM.
    3. Before reboot or after shutdown, detach the ISO:
        ◦ Go to Settings → Storage.
        ◦ Select the optical drive under Controller: IDE.
        ◦ Click the CD icon and choose Remove disk from virtual drive (or Empty).
    4. Start the VM again — it will boot from the installed system.

👤 Step 6: Login and Post-Installation
    1. First Login using the username and password created during installation.
    2. Update packages:
      sudo apt update && sudo apt upgrade -y
    3. Confirm SSH server is running:
       sudo systemctl status ssh
    4. Install Samba for file sharing:
       sudo apt install samba -y
    5. Create and configure shared folder:
       sudo mkdir -p /srv/samba/share
       sudo chown nobody:nogroup /srv/samba/share
       sudo chmod 2775 /srv/samba/share
    7. Edit Samba config (sudo nano /etc/samba/smb.conf), add:
       [shared]
         path = /srv/samba/share
         browseable = yes
         read only = no
         guest ok = yes
         force user = nobody
    8  Restart Samba:
       sudo systemctl restart smbd

Optional:
    • Set static IP on LAN interface (if you want fixed IP instead of DHCP)
    • Create users for Samba if you want authenticated shares
    • Set up firewall rules on OPNsense to control access


---

**📘 How to install OPNsense**

Prerequisites
    • A host machine with VirtualBox installed (tested with VirtualBox 7.0.4)
    • Basic familiarity with Linux command line and networking concepts

🖥️ Step 1: Download OPNsense Image
    1. Go to the official OPNsense Downloads page.
    2. Select architecture: amd64
    3. Select image type: VGA
    4. Download the .img file (usually compressed).
    5. Decompress .img file using unxz or similar tool.

📀 Step 2: Create OPNsense VM in VirtualBox
    1. Open VirtualBox and click New.
    2. Name: OPNsense
    3. Type: BSD
    4. Version: FreeBSD (64-bit)
    5. Assign RAM (e.g., 1024 MB or less for resource constraints).
    6. Do NOT create a virtual hard disk — boot directly from the .img file.
    7. In VM settings → Storage → Attach the .img file as a virtual optical drive or use a raw disk option.

🌐 Step 3: Configure VirtualBox Network Adapters
    • Adapter 1 (WAN):
        ◦ Attached to: NAT
        ◦ This gives OPNsense internet access.
    • Adapter 2 (LAN):
        ◦ Attached to: Internal Network
        ◦ Name: intnet (or any chosen name)
        ◦ This creates a private LAN network for testing.
Make sure both adapters are enabled.

🚀 Step 4: Install OPNsense
    1. Start the VM.
    2. Follow the console prompts to install OPNsense.
    3. When prompted for interface assignments:
        ◦ Do not configure LAGG or VLANs (choose n to skip).
        ◦ Assign em0 as WAN.
        ◦ Assign em1 as LAN.
    4. Confirm assignments and complete installation.

🛠️ Step 5: Assign Network Interfaces
    • WAN (em0) should receive an IP automatically via DHCP from VirtualBox NAT (e.g., 10.0.2.15).
    • LAN (em1) will use the default IP 192.168.1.1/24.
If no WAN IP is assigned, manually renew the DHCP lease from the console menu.

Step 6: Verify Network Interfaces and IPs
At the OPNsense console, you should see:
WAN (em0) -> 10.0.2.15 (DHCP)
LAN (em1) -> 192.168.1.1/24
This confirms the firewall VM has internet access and is ready to serve LAN clients.

🛠️ Next Steps: Setting Up Client VM
To test the OPNsense LAN and firewall features:
    1. Create a second VM (e.g., lightweight Linux like Alpine Linux, Lubuntu or Debian Netinst).
    2. Attach its network adapter to the same Internal Network (intnet).
    3. Boot the client VM and verify it gets an IP address from OPNsense’s DHCP (e.g., 192.168.1.x).
    4. Test connectivity by pinging the OPNsense LAN IP:
       ping 192.168.1.1
    5. Access the OPNsense web interface at:
       https://192.168.1.1
       Use default credentials:
        ◦ Username: root
        ◦ Password: opnsense

---



