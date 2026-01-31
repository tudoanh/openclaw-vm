# Customization Guide

Learn how to customize the OpenClaw Vagrant environment for your needs.

## Table of Contents

- [VM Resources](#vm-resources)
- [Desktop Environment](#desktop-environment)
- [Additional Software](#additional-software)
- [Shared Folders](#shared-folders)
- [Network Configuration](#network-configuration)

---

## VM Resources

### Memory and CPU

Edit `Vagrantfile` to adjust allocated resources:

```ruby
config.vm.provider "virtualbox" do |vb|
  vb.memory = "8192"  # 8GB RAM
  vb.cpus = 4         # 4 CPU cores
end
```

**Recommendations**:
- Minimum: 2GB RAM, 2 CPUs
- Recommended: 4GB RAM, 2-4 CPUs
- For heavy development: 8GB+ RAM, 4+ CPUs

### Video Memory

Adjust video RAM for better graphics performance:

```ruby
vb.customize ["modifyvm", :id, "--vram", "256"]  # 256MB
```

**Options**: 8, 16, 32, 64, 128, 256 MB

### Disk Size

The default disk is usually 64GB. To resize:

```bash
# After first vagrant up
VBoxManage modifyhd "path/to/disk.vdi" --resize 102400  # 100GB
```

---

## Desktop Environment

### Switch to Different DE

Replace XFCE with another desktop environment:

#### GNOME (heavier, more features)

```ruby
apt-get install -y \
  ubuntu-desktop \
  gnome-shell
```

#### LXDE (lighter than XFCE)

```ruby
apt-get install -y \
  lxde \
  lxdm
```

#### KDE Plasma

```ruby
apt-get install -y \
  kde-plasma-desktop \
  sddm
```

### Disable Auto-Login

Remove auto-login configuration:

```bash
sudo rm /etc/lightdm/lightdm.conf.d/50-autologin.conf
sudo systemctl restart lightdm
```

---

## Additional Software

### Add Development Tools

Add to provisioning script in Vagrantfile:

```ruby
# C++ development
apt-get install -y \
  gdb \
  valgrind \
  clang \
  lldb

# Version control
apt-get install -y \
  git-gui \
  gitk \
  meld

# Text editors/IDEs
apt-get install -y \
  code \
  geany \
  vim-gtk3
```

### Install Visual Studio Code

```ruby
# Add to provisioning script
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list
apt-get update
apt-get install -y code
```

### Install Gaming Tools

```ruby
apt-get install -y \
  steam \
  wine \
  playonlinux
```

---

## Shared Folders

### Enable Shared Folder

Share a folder between host and VM:

```ruby
# In Vagrantfile
config.vm.synced_folder "./shared", "/home/vagrant/shared"
```

Create the folder:

```bash
mkdir shared
```

### Disable Default Shared Folder

```ruby
config.vm.synced_folder ".", "/vagrant", disabled: true
```

### NFS Shared Folders (faster)

Linux/macOS hosts:

```ruby
config.vm.synced_folder "./shared", "/home/vagrant/shared",
  type: "nfs",
  nfs_udp: false
```

---

## Network Configuration

### Port Forwarding

Forward ports from guest to host:

```ruby
# Forward guest port 8080 to host port 8080
config.vm.network "forwarded_port", guest: 8080, host: 8080

# Forward SSH (custom)
config.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh"
```

### Bridged Network

Connect VM directly to your network:

```ruby
config.vm.network "public_network"
```

With specific adapter:

```ruby
config.vm.network "public_network", bridge: "en0: Wi-Fi (AirPort)"
```

### Private Network

Create a private network between host and VM:

```ruby
config.vm.network "private_network", ip: "192.168.33.10"
```

---

## Audio Configuration

### Enable Audio

```ruby
config.vm.provider "virtualbox" do |vb|
  vb.customize ["modifyvm", :id, "--audio", "pulse"]      # Linux
  # vb.customize ["modifyvm", :id, "--audio", "coreaudio"]  # macOS
  # vb.customize ["modifyvm", :id, "--audio", "dsound"]     # Windows

  vb.customize ["modifyvm", :id, "--audioout", "on"]
  vb.customize ["modifyvm", :id, "--audioin", "on"]
end
```

---

## USB Support

### Enable USB 2.0/3.0

Requires VirtualBox Extension Pack:

```ruby
config.vm.provider "virtualbox" do |vb|
  vb.customize ["modifyvm", :id, "--usb", "on"]
  vb.customize ["modifyvm", :id, "--usbehci", "on"]  # USB 2.0
  # vb.customize ["modifyvm", :id, "--usbxhci", "on"]  # USB 3.0
end
```

### USB Device Filters

Add specific USB devices:

```ruby
vb.customize ["usbfilter", "add", "0",
  "--target", :id,
  "--name", "USB Gamepad",
  "--vendorid", "0x046d",
  "--productid", "0xc216"]
```

---

## Performance Tuning

### PAE/NX

Enable PAE/NX for better memory handling:

```ruby
vb.customize ["modifyvm", :id, "--pae", "on"]
vb.customize ["modifyvm", :id, "--nestedpaging", "on"]
```

### I/O APIC

Enable I/O APIC for multi-core support:

```ruby
vb.customize ["modifyvm", :id, "--ioapic", "on"]
```

### Host I/O Cache

Enable host I/O caching:

```ruby
vb.customize ["storagectl", :id, "--name", "SATA Controller", "--hostiocache", "on"]
```

---

## Custom Provisioning

### Multiple Provisioners

Run multiple provisioning steps:

```ruby
# Initial setup
config.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y build-essential
SHELL

# Install OpenClaw dependencies
config.vm.provision "shell", path: "scripts/install-openclaw-deps.sh"

# Configure user environment
config.vm.provision "shell", privileged: false, inline: <<-SHELL
  git config --global user.name "Your Name"
  git config --global user.email "you@example.com"
SHELL
```

### Ansible Provisioning

Use Ansible instead of shell scripts:

```ruby
config.vm.provision "ansible" do |ansible|
  ansible.playbook = "playbook.yml"
end
```

---

## Multiple VMs

### Create Multiple VMs

Define multiple VMs in one Vagrantfile:

```ruby
Vagrant.configure("2") do |config|
  # Development VM
  config.vm.define "dev" do |dev|
    dev.vm.box = "ubuntu/jammy64"
    dev.vm.hostname = "openclaw-dev"
    dev.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.gui = true
    end
  end

  # Testing VM
  config.vm.define "test" do |test|
    test.vm.box = "ubuntu/jammy64"
    test.vm.hostname = "openclaw-test"
    test.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.gui = false
    end
  end
end
```

Usage:

```bash
vagrant up dev
vagrant up test
vagrant ssh dev
```

---

## Snapshot Management

### Create Snapshots

```bash
# Take a snapshot
vagrant snapshot save clean-state

# List snapshots
vagrant snapshot list

# Restore snapshot
vagrant snapshot restore clean-state

# Delete snapshot
vagrant snapshot delete clean-state
```

Add to Vagrantfile for automatic snapshots:

```ruby
config.trigger.after :up do |trigger|
  trigger.info = "Creating snapshot after boot..."
  trigger.run = {inline: "vagrant snapshot save post-boot"}
end
```

---

## Environment Variables

### Pass Environment Variables

```ruby
config.vm.provision "shell" do |s|
  s.env = {
    "OPENCLAW_VERSION" => "latest",
    "BUILD_TYPE" => "Release"
  }
  s.inline = "echo Build type: $BUILD_TYPE"
end
```

---

## Tips

1. **Keep Vagrantfile clean** - Move complex provisioning to separate scripts
2. **Test incrementally** - Test changes with `vagrant provision` before destroying VM
3. **Use version control** - Commit Vagrantfile changes to git
4. **Document customizations** - Add comments explaining non-obvious configurations
5. **Share configurations** - Create different Vagrantfiles for different use cases

---

For more information, visit:
- [Vagrant Documentation](https://www.vagrantup.com/docs)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
