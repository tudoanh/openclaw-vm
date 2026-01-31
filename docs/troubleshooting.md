# Troubleshooting Guide

Common issues and solutions for the OpenClaw VM environment.

## Table of Contents

- [Provisioning Issues](#provisioning-issues)
- [GUI Issues](#gui-issues)
- [OpenClaw Issues](#openclaw-issues)
- [Performance Issues](#performance-issues)
- [VirtualBox Issues](#virtualbox-issues)
- [Network Issues](#network-issues)

---

## Provisioning Issues

### VM fails to provision

**Symptoms**: `vagrant up` fails during provisioning

**Solutions**:

1. Check your internet connection -- provisioning downloads packages
2. Try re-provisioning from scratch:
   ```bash
   vagrant destroy -f
   vagrant up
   ```
3. Check VirtualBox logs:
   ```bash
   VBoxManage list vms
   VBoxManage showvminfo "OpenClaw VM"
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

### Node.js installation fails

**Symptoms**: NodeSource setup script errors

**Solutions**:

1. SSH in and install manually:
   ```bash
   vagrant ssh
   curl -fsSL https://deb.nodesource.com/setup_22.x | sudo bash -
   sudo apt-get install -y nodejs
   ```
2. Verify Node version: `node --version` (should be >= 22)

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
3. Restart the display manager:
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

## OpenClaw Issues

### `openclaw` command not found

**Solutions**:

1. Reinstall globally:
   ```bash
   sudo npm install -g openclaw@latest
   ```
2. Check npm global bin is in PATH:
   ```bash
   npm config get prefix
   echo $PATH
   ```

### OpenClaw gateway won't start

**Solutions**:

1. Check if the daemon is installed:
   ```bash
   systemctl --user status openclaw-gateway
   ```
2. Re-run onboarding:
   ```bash
   openclaw onboard --install-daemon
   ```
3. Start manually:
   ```bash
   openclaw gateway
   ```

### Dashboard not accessible from host

**Symptoms**: `http://localhost:18789/` doesn't load on host machine

**Solutions**:

1. Check port forwarding is active:
   ```bash
   vagrant port
   ```
2. Verify gateway is running inside VM:
   ```bash
   vagrant ssh
   curl http://127.0.0.1:18789/
   ```
3. Check if port was auto-corrected due to conflict -- `vagrant port` shows actual mapping

### OpenClaw onboarding fails

**Solutions**:

1. Check Node.js version:
   ```bash
   node --version  # Must be >= 22
   ```
2. Check network connectivity (needed for API key validation):
   ```bash
   curl -s https://api.openai.com > /dev/null && echo "OK" || echo "FAIL"
   ```
3. Try running with verbose output:
   ```bash
   DEBUG=* openclaw onboard --install-daemon
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

### High CPU usage

**Solutions**:

1. Disable 3D acceleration if not needed
2. Check for runaway processes:
   ```bash
   vagrant ssh
   htop
   ```
3. OpenClaw gateway may consume CPU during active tasks -- this is expected

### Disk I/O is slow

**Solutions**:

1. Use SSD on host machine if possible
2. Enable host I/O caching:
   ```ruby
   vb.customize ["storagectl", :id, "--name", "SCSI", "--hostiocache", "on"]
   ```

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

---

## Getting More Help

If these solutions don't resolve your issue:

1. Check existing [GitHub Issues](https://github.com/YOUR_USERNAME/openclaw-vm/issues)
2. Create a new issue with:
   - Detailed description
   - Steps to reproduce
   - Error logs
   - System information
3. Check [OpenClaw documentation](https://docs.openclaw.ai/) for agent-specific issues

### Collecting Debug Info

```bash
# System information
vagrant --version
VBoxManage --version
uname -a

# Vagrant status
vagrant status

# Detailed logs
vagrant up --debug > vagrant-debug.log 2>&1

# Inside VM
node --version
openclaw --version
systemctl --user status openclaw-gateway
```
