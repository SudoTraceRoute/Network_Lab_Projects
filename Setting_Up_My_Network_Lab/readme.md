Here you will find the instructions on how to install and configure Ubuntu server runing Samba, Lubuntu and OPNsense in VirtualBox.

'''
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

'''
