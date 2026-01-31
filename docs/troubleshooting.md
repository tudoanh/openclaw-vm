# Troubleshooting Guide

Common issues and their solutions for the OpenClaw Vagrant environment.

## Table of Contents

- [Provisioning Issues](#provisioning-issues)
- [GUI Issues](#gui-issues)
- [Performance Issues](#performance-issues)
- [Build Issues](#build-issues)
- [VirtualBox Issues](#virtualbox-issues)
- [Network Issues](#network-issues)

---

## Provisioning Issues

### VM fails to provision

**Symptoms**: `vagrant up` fails during provisioning

**Solutions**:

1. Check your internet connection
2. Try re-provisioning:
   ```bash
   vagrant destroy -f
   vagrant up
   ```

3. Check VirtualBox logs:
   ```bash
   VBoxManage list vms
   VBoxManage showvminfo "OpenClaw Development Environment"
   ```

### Package installation fails

**Symptoms**: APT errors during provisioning

**Solutions**:

1. SSH into the VM and update manually:
   ```bash
   vagrant ssh
   sudo apt-get update
   sudo apt-get upgrade
   ```

2. Check Ubuntu mirror status and try a different mirror

---

## GUI Issues

### GUI window doesn't appear

**Solutions**:

1. Verify GUI is enabled in Vagrantfile:
   ```ruby
   vb.gui = true
   ```

2. Check if VM is running:
   ```bash
   vagrant status
   VBoxManage list runningvms
   ```

3. Try restarting the display manager:
   ```bash
   vagrant ssh
   sudo systemctl restart lightdm
   ```

### Black screen on boot

**Solutions**:

1. Disable 3D acceleration in Vagrantfile:
   ```ruby
   # Comment out this line:
   # vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
   ```

2. Change graphics controller:
   ```ruby
   vb.customize ["modifyvm", :id, "--graphicscontroller", "vboxvga"]
   ```

3. Reduce video memory:
   ```ruby
   vb.customize ["modifyvm", :id, "--vram", "64"]
   ```

### Screen resolution issues

**Solutions**:

1. Install VirtualBox Guest Additions:
   ```bash
   vagrant ssh
   sudo apt-get install virtualbox-guest-x11
   sudo reboot
   ```

2. Manually set resolution in VM:
   - Go to Display Settings in XFCE
   - Select desired resolution

### Clipboard not working

**Solutions**:

1. Ensure Guest Additions are installed:
   ```bash
   vagrant ssh
   lsmod | grep vbox
   ```

2. Restart VirtualBox services:
   ```bash
   sudo systemctl restart vboxadd-service
   ```

---

## Performance Issues

### VM is slow/laggy

**Solutions**:

1. Increase allocated resources in Vagrantfile:
   ```ruby
   vb.memory = "8192"  # 8GB
   vb.cpus = 4
   ```

2. Close other applications on host machine

3. Disable unnecessary services in VM:
   ```bash
   vagrant ssh
   sudo systemctl disable bluetooth
   sudo systemctl disable cups
   ```

4. Use a lighter desktop environment or disable compositing

### High CPU usage

**Solutions**:

1. Disable 3D acceleration if not needed
2. Reduce video memory
3. Check for runaway processes:
   ```bash
   vagrant ssh
   htop
   ```

### Disk I/O is slow

**Solutions**:

1. Use SSD on host machine if possible
2. Adjust disk controller settings:
   ```ruby
   vb.customize ["storagectl", :id, "--name", "SCSI", "--hostiocache", "on"]
   ```

---

## Build Issues

### CMake configuration fails

**Solutions**:

1. Ensure all dependencies are installed:
   ```bash
   sudo apt-get install build-essential cmake libsdl2-dev libsdl2-mixer-dev \
       libsdl2-image-dev libsdl2-ttf-dev libpng-dev zlib1g-dev libtinyxml-dev \
       libglew-dev libopenal-dev libvorbis-dev libfreetype6-dev
   ```

2. Clean build directory:
   ```bash
   cd ~/openclaw
   rm -rf Build
   mkdir Build
   cd Build
   cmake ..
   ```

### Compilation errors

**Solutions**:

1. Check GCC version:
   ```bash
   gcc --version
   ```

2. Update build tools:
   ```bash
   sudo apt-get update
   sudo apt-get install --upgrade build-essential
   ```

3. Check OpenClaw GitHub issues for known build problems

### Missing game assets

**Symptoms**: "Cannot find CLAW.REZ" or similar

**Solutions**:

1. You need the original Captain Claw game files
2. Copy CLAW.REZ to the appropriate directory
3. See [OpenClaw documentation](https://github.com/openclaw/openclaw) for details

---

## VirtualBox Issues

### VT-x/AMD-V not available

**Symptoms**: VM fails to start with virtualization error

**Solutions**:

1. Enable virtualization in BIOS/UEFI:
   - Restart computer
   - Enter BIOS (usually F2, F10, or DEL)
   - Enable VT-x (Intel) or AMD-V (AMD)
   - Save and exit

2. On Windows, disable Hyper-V:
   ```powershell
   # Run as Administrator
   bcdedit /set hypervisorlaunchtype off
   ```
   Reboot required.

### Kernel driver not installed

**Symptoms**: VirtualBox fails to start VMs

**Solutions**:

1. On Linux, reinstall VirtualBox kernel modules:
   ```bash
   sudo /sbin/vboxconfig
   ```

2. On macOS, allow kernel extension in Security & Privacy settings

### USB devices not working

**Solutions**:

1. Install VirtualBox Extension Pack
2. Add your user to vboxusers group (Linux):
   ```bash
   sudo usermod -a -G vboxusers $USER
   ```
   Log out and back in.

---

## Network Issues

### Cannot access internet from VM

**Solutions**:

1. Check NAT networking is enabled (default)
2. Restart networking:
   ```bash
   vagrant ssh
   sudo systemctl restart NetworkManager
   ```

3. Try different network mode in Vagrantfile:
   ```ruby
   config.vm.network "public_network"
   ```

### Git clone fails

**Solutions**:

1. Check internet connectivity:
   ```bash
   vagrant ssh
   ping github.com
   ```

2. Try HTTPS instead of SSH for git:
   ```bash
   git clone https://github.com/openclaw/openclaw
   ```

---

## Getting More Help

If you've tried these solutions and still have issues:

1. Check existing [GitHub Issues](https://github.com/YOUR_USERNAME/openclaw-vagrant/issues)
2. Create a new issue with:
   - Detailed description
   - Steps to reproduce
   - Error logs
   - System information
3. Join the OpenClaw community discussions

---

## Reporting Issues

When reporting issues, include:

```bash
# System information
vagrant --version
VBoxManage --version
uname -a

# Vagrant status
vagrant status

# Logs
vagrant up --debug > vagrant-debug.log 2>&1
```
