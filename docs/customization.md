# Customization Guide

How to customize the OpenClaw VM environment for your needs.

## Table of Contents

- [VM Resources](#vm-resources)
- [Desktop Environment](#desktop-environment)
- [Additional Software](#additional-software)
- [Shared Folders](#shared-folders)
- [Network Configuration](#network-configuration)
- [Audio Configuration](#audio-configuration)
- [Snapshot Management](#snapshot-management)
- [Multiple VMs](#multiple-vms)
- [Environment Variables](#environment-variables)

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
- For heavy agent workloads: 8GB+ RAM, 4+ CPUs

### Video Memory

```ruby
vb.customize ["modifyvm", :id, "--vram", "256"]  # 256MB
```

### Disk Size

The default disk is usually 64GB. To resize:

```bash
VBoxManage modifyhd "path/to/disk.vdi" --resize 102400  # 100GB
```

---

## Desktop Environment

### Switch to Different DE

Replace XFCE with another desktop environment by modifying the provisioning script:

#### GNOME (heavier, more features)

```bash
apt-get install -y ubuntu-desktop gnome-shell
```

#### LXDE (lighter than XFCE)

```bash
apt-get install -y lxde lxdm
```

### Disable Auto-Login

```bash
sudo rm /etc/lightdm/lightdm.conf.d/50-autologin.conf
sudo systemctl restart lightdm
```

---

## Additional Software

### Install via Snap

After provisioning, you can install snap packages inside the VM:

```bash
sudo snap install code --classic       # VS Code
sudo snap install firefox              # Firefox
sudo snap install telegram-desktop     # Telegram
sudo snap install discord              # Discord
sudo snap install slack                # Slack
```

### Install VS Code via Provisioning

Add to the Vagrantfile provisioning script:

```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list
apt-get update
apt-get install -y code
```

### Install Docker (for OpenClaw sandbox mode)

OpenClaw supports running non-main sessions in Docker sandboxes. Add to provisioning:

```bash
curl -fsSL https://get.docker.com | sh
usermod -aG docker vagrant
```

Then configure `agents.defaults.sandbox.mode: "non-main"` in OpenClaw settings.

---

## Shared Folders

### Enable Shared Folder

Share a folder between host and VM:

```ruby
config.vm.synced_folder "./shared", "/home/vagrant/shared"
```

Create the folder on the host:

```bash
mkdir shared
```

### Disable Default Shared Folder

```ruby
config.vm.synced_folder ".", "/vagrant", disabled: true
```

### NFS Shared Folders (faster, Linux/macOS hosts)

```ruby
config.vm.synced_folder "./shared", "/home/vagrant/shared",
  type: "nfs",
  nfs_udp: false
```

---

## Network Configuration

### Port Forwarding

Forward additional ports from guest to host:

```ruby
# Forward a web server
config.vm.network "forwarded_port", guest: 3000, host: 3000

# Forward custom SSH port
config.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh"
```

### Bridged Network

Connect VM directly to your network:

```ruby
config.vm.network "public_network"
```

### Private Network

Create a host-only network:

```ruby
config.vm.network "private_network", ip: "192.168.33.10"
```

---

## Audio Configuration

```ruby
config.vm.provider "virtualbox" do |vb|
  vb.customize ["modifyvm", :id, "--audio", "pulse"]        # Linux
  # vb.customize ["modifyvm", :id, "--audio", "coreaudio"]  # macOS
  # vb.customize ["modifyvm", :id, "--audio", "dsound"]     # Windows

  vb.customize ["modifyvm", :id, "--audioout", "on"]
  vb.customize ["modifyvm", :id, "--audioin", "on"]
end
```

---

## Snapshot Management

Snapshots let you save and restore VM state:

```bash
# Save a snapshot after initial setup
vagrant snapshot save clean-state

# List snapshots
vagrant snapshot list

# Restore to a snapshot (revert all changes)
vagrant snapshot restore clean-state

# Delete a snapshot
vagrant snapshot delete clean-state
```

This is useful for OpenClaw testing -- take a snapshot before letting the agent perform tasks, then restore if anything goes wrong.

### Automatic Snapshot on Boot

```ruby
config.trigger.after :up do |trigger|
  trigger.info = "Creating snapshot after boot..."
  trigger.run = {inline: "vagrant snapshot save post-boot"}
end
```

---

## Multiple VMs

Define separate VMs for different purposes:

```ruby
Vagrant.configure("2") do |config|
  # Full desktop VM for interactive OpenClaw use
  config.vm.define "desktop" do |desktop|
    desktop.vm.box = "ubuntu/jammy64"
    desktop.vm.hostname = "openclaw-desktop"
    desktop.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.gui = true
    end
  end

  # Headless VM for gateway-only testing
  config.vm.define "headless" do |headless|
    headless.vm.box = "ubuntu/jammy64"
    headless.vm.hostname = "openclaw-headless"
    headless.vm.network "forwarded_port", guest: 18789, host: 18790
    headless.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.gui = false
    end
  end
end
```

Usage:

```bash
vagrant up desktop
vagrant up headless
vagrant ssh headless
```

---

## Environment Variables

Pass environment variables to provisioning:

```ruby
config.vm.provision "shell" do |s|
  s.env = {
    "OPENCLAW_VERSION" => "latest",
    "NODE_VERSION" => "22"
  }
  s.inline = "npm install -g openclaw@$OPENCLAW_VERSION"
end
```

---

## Tips

1. **Take snapshots** before letting OpenClaw perform destructive tasks
2. **Test incrementally** -- use `vagrant provision` to re-run provisioning
3. **Use shared folders** to pass API keys or config files into the VM
4. **Disable network** if you want to test OpenClaw in an air-gapped environment
5. **Keep Vagrantfile clean** -- move complex provisioning to separate scripts in a `scripts/` directory

---

For more information:
- [Vagrant Documentation](https://www.vagrantup.com/docs)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [OpenClaw Documentation](https://docs.openclaw.ai/)
