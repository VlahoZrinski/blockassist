 — BlockAssist Setup

This guide walks you through running BlockAssist on Windows using an Ubuntu 22.04 virtual machine in Oracle VirtualBox. Use this when WSL is not supported.

## Requirements
- Windows 10/11 with hardware virtualization enabled (Intel VT‑x/AMD‑V)
- Oracle VirtualBox 7.x
- Ubuntu Desktop 22.04 LTS ISO
- Resources: 8 GB RAM minimum (16 GB recommended), multi‑core CPU, 100 GB disk space

## 1) Install VirtualBox and download Ubuntu
- Download and install VirtualBox: `https://www.virtualbox.org/wiki/Downloads`
- Download Ubuntu 22.04 LTS ISO: `https://releases.ubuntu.com/22.04/`

## 2) Create a new Ubuntu VM
1. Open VirtualBox → New
2. Name and Operating System:
   - Name: Ubuntu
   - ISO Image: select the Ubuntu 22.04 ISO
3. Unattended Install:
   - Username and Password: choose a user you’ll use inside Ubuntu
   - **Highly recommended to check “Install Guest Additions” box, Virtualbox will auto-install some quality and resolution features**
4. Hardware:
   - Base Memory: 8 GB minimum (16 GB recommended)
   - Processors: allocate multiple CPU cores
5. Hard Disk:
   - Create a virtual hard disk now → VDI → Dynamically allocated → Size: 100 GB
6. Create VM, then open Settings before first boot:
   - Display → Video Memory: 128 MB (max)
   - Display → Graphics Controller: VMSVGA
   - Display → Enable 3D Acceleration: ON (keep OFF if VM is unstable)
   - General → Advanced → Shared Clipboard: Bidirectional
   - General → Advanced → Drag’n’Drop: Bidirectional
   - Network: NAT is fine by default; Bridged Adapter is optional

## 3) Grant your user admin (sudo) privileges:
1. Restart the VM and hold Right-Shift (or press Esc repeatedly) to show GRUB.
2. Select “Advanced options for Ubuntu” → choose the entry with “(recovery mode)”.
3. In the Recovery Menu, choose “root – Drop to root shell”. → Insert a password if prompted.
4. Now execute these commands:
```bash
mount -o remount,rw /
adduser your-user sudo
passwd your-user
reboot
```
   - Replace `your-user` with your Ubuntu username.

## 4) Troubleshooting
- Clipboard/Drag-and-drop: ensure Guest Additions installed and `VBoxClient` processes are running
- Low FPS/lag: assign more CPU/RAM; keep 3D Acceleration OFF; close heavy apps on Windows host
- Display scaling: use View → Virtual Screen options; avoid HiDPI scaling inside guest when possible
- Enable in-game mouse movement: VM window tabs > Input > Mouse Integration. Then toggle off/on with `right-ctrl` (default host key)

---

## Install VirtualBox Guest Additions
If you ever got into trouble with displays and clipboard functionality, you can install Guest Additions manually:

### 1) VirtualBox
Inside Ubuntu:
```bash
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```
Then from the VM window menu: Devices → "Insert Guest Additions CD Image…"
```bash
sudo mkdir -p /mnt/cdrom
sudo mount /dev/cdrom /mnt/cdrom
cd /mnt/cdrom
sudo ./VBoxLinuxAdditions.run
sudo reboot
```

### 2) Enable clipboard and drag-and-drop reliably
If clipboard or drag-and-drop does not work after reboot, run:
```bash
VBoxClient --clipboard &
VBoxClient --draganddrop &
VBoxClient --checkhostversion &
```
Make clipboard autostart on login:
```bash
mkdir -p ~/.config/autostart
cat > ~/.config/autostart/vboxclient-clipboard.desktop << 'EOF'
[Desktop Entry]
Type=Application
Name=VBoxClient Clipboard
Exec=/usr/bin/VBoxClient --clipboard
X-GNOME-Autostart-enabled=true
EOF
```
