# -*- mode: ruby -*-
# vi: set ft=ruby :

# OpenClaw VM - Sandboxed Environment for OpenClaw AI Agent
# A safe, isolated VM for running and testing OpenClaw
# https://github.com/openclaw/openclaw

Vagrant.configure("2") do |config|
  # Base box - Ubuntu 22.04 LTS (Jammy Jellyfish)
  # Note: Canonical dropped official 24.04 Vagrant boxes due to HashiCorp BSL.
  # Use ubuntu/jammy64 which is well-supported and works with Node.js 22.
  config.vm.box = "ubuntu/jammy64"
  config.vm.box_check_update = true

  # VM identification
  config.vm.hostname = "openclaw-vm"

  # Network configuration
  # OpenClaw Gateway dashboard
  config.vm.network "forwarded_port", guest: 18789, host: 18789, auto_correct: true

  # VirtualBox provider configuration
  config.vm.provider "virtualbox" do |vb|
    vb.name = "OpenClaw VM"

    # Enable GUI mode for desktop environment
    vb.gui = true

    # Resource allocation
    vb.memory = "4096"  # 4GB RAM
    vb.cpus = 2         # 2 CPU cores

    # Graphics configuration
    vb.customize ["modifyvm", :id, "--vram", "128"]
    vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
    vb.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]

    # Enhanced features
    vb.customize ["modifyvm", :id, "--clipboard-mode", "bidirectional"]
    vb.customize ["modifyvm", :id, "--draganddrop", "bidirectional"]

    # Audio
    vb.customize ["modifyvm", :id, "--audio", "pulse"]
    vb.customize ["modifyvm", :id, "--audioout", "on"]

    # Nested virtualization (useful if OpenClaw spawns containers)
    vb.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
  end

  # Main provisioning script
  config.vm.provision "shell", inline: <<-SHELL
    set -e
    export DEBIAN_FRONTEND=noninteractive

    echo "=========================================="
    echo " OpenClaw VM - Environment Setup"
    echo "=========================================="

    # ──────────────────────────────────────────────
    # 1. System update and core packages
    # ──────────────────────────────────────────────
    echo "[1/8] Updating system packages..."
    apt-get update && apt-get upgrade -y

    # ──────────────────────────────────────────────
    # 2. Desktop environment (XFCE)
    # ──────────────────────────────────────────────
    echo "[2/8] Installing desktop environment..."
    apt-get install -y \
      xfce4 \
      xfce4-goodies \
      lightdm \
      lightdm-gtk-greeter \
      xorg \
      dbus-x11

    # ──────────────────────────────────────────────
    # 3. VirtualBox Guest Additions
    # ──────────────────────────────────────────────
    echo "[3/8] Installing VirtualBox Guest Additions..."
    # The ubuntu/jammy64 box ships with basic guest additions.
    # Install what's available; skip packages that don't exist in this release.
    apt-get install -y virtualbox-guest-utils virtualbox-guest-x11 || true

    # ──────────────────────────────────────────────
    # 4. Common desktop applications
    # ──────────────────────────────────────────────
    echo "[4/8] Installing common applications..."

    # Core utilities
    apt-get install -y \
      git \
      curl \
      wget \
      unzip \
      vim \
      nano \
      htop \
      net-tools \
      ca-certificates \
      gnupg \
      software-properties-common \
      apt-transport-https

    # LibreOffice suite
    apt-get install -y libreoffice

    # VLC media player
    apt-get install -y vlc

    # File manager and archive tools
    apt-get install -y \
      thunar-archive-plugin \
      xarchiver \
      p7zip-full

    # Terminal emulator
    apt-get install -y xfce4-terminal

    # Text editor
    apt-get install -y mousepad

    # Screenshot tool
    apt-get install -y xfce4-screenshooter

    # PDF viewer
    apt-get install -y evince

    # Image viewer
    apt-get install -y ristretto

    # System monitor
    apt-get install -y gnome-system-monitor

    # ──────────────────────────────────────────────
    # 5. Google Chrome
    # ──────────────────────────────────────────────
    echo "[5/8] Installing Google Chrome..."
    wget -q -O /tmp/google-chrome.deb \
      "https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb"
    apt-get install -y /tmp/google-chrome.deb || true
    apt-get install -f -y
    rm -f /tmp/google-chrome.deb

    # ──────────────────────────────────────────────
    # 6. Snapd
    # ──────────────────────────────────────────────
    echo "[6/8] Installing and configuring snapd..."
    apt-get install -y snapd
    systemctl enable snapd
    systemctl start snapd || true

    # ──────────────────────────────────────────────
    # 7. Node.js 22 LTS and OpenClaw
    # ──────────────────────────────────────────────
    echo "[7/8] Installing Node.js 22 and OpenClaw..."

    # Install Node.js 22 via NodeSource
    curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
    apt-get install -y nodejs

    # Install pnpm globally
    npm install -g pnpm

    # Install OpenClaw globally
    npm install -g openclaw@latest

    # Create systemd service for OpenClaw gateway daemon
    cat > /etc/systemd/system/openclaw-gateway.service <<SYSTEMD
[Unit]
Description=OpenClaw Gateway Daemon
After=network.target

[Service]
Type=simple
User=vagrant
Environment=HOME=/home/vagrant
WorkingDirectory=/home/vagrant
ExecStart=/usr/bin/openclaw gateway
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
SYSTEMD

    # Enable the service (will start on boot after onboarding is complete)
    systemctl daemon-reload
    systemctl enable openclaw-gateway.service

    # ──────────────────────────────────────────────
    # 8. Desktop configuration
    # ──────────────────────────────────────────────
    echo "[8/8] Configuring desktop environment..."

    # Auto-login for vagrant user
    mkdir -p /etc/lightdm/lightdm.conf.d
    cat > /etc/lightdm/lightdm.conf.d/50-autologin.conf <<EOF
[Seat:*]
autologin-user=vagrant
autologin-user-timeout=0
user-session=xfce
EOF

    # Create desktop shortcuts
    sudo -u vagrant mkdir -p /home/vagrant/Desktop

    # OpenClaw onboarding launcher
    cat > /home/vagrant/Desktop/openclaw-setup.desktop <<LAUNCHER
[Desktop Entry]
Version=1.0
Type=Application
Name=OpenClaw Setup
Comment=Run OpenClaw onboarding wizard
Exec=xfce4-terminal -e "bash -c 'openclaw onboard --install-daemon; echo; echo Press Enter to close...; read'"
Icon=utilities-terminal
Terminal=false
Categories=Development;
LAUNCHER
    chmod +x /home/vagrant/Desktop/openclaw-setup.desktop
    chown vagrant:vagrant /home/vagrant/Desktop/openclaw-setup.desktop

    # OpenClaw dashboard launcher
    cat > /home/vagrant/Desktop/openclaw-dashboard.desktop <<LAUNCHER
[Desktop Entry]
Version=1.0
Type=Application
Name=OpenClaw Dashboard
Comment=Open OpenClaw Gateway Dashboard
Exec=google-chrome-stable http://127.0.0.1:18789/
Icon=web-browser
Terminal=false
Categories=Network;
LAUNCHER
    chmod +x /home/vagrant/Desktop/openclaw-dashboard.desktop
    chown vagrant:vagrant /home/vagrant/Desktop/openclaw-dashboard.desktop

    # OpenClaw TUI launcher
    cat > /home/vagrant/Desktop/openclaw-tui.desktop <<LAUNCHER
[Desktop Entry]
Version=1.0
Type=Application
Name=OpenClaw TUI
Comment=Launch OpenClaw Terminal Interface
Exec=xfce4-terminal -e "bash -c 'openclaw || bash'"
Icon=utilities-terminal
Terminal=false
Categories=Development;
LAUNCHER
    chmod +x /home/vagrant/Desktop/openclaw-tui.desktop
    chown vagrant:vagrant /home/vagrant/Desktop/openclaw-tui.desktop

    # Welcome README on desktop
    cat > /home/vagrant/Desktop/README.txt <<DESKTOP_README
OpenClaw VM - Safe Testing Environment
=======================================

This VM provides an isolated environment for running and testing
OpenClaw (https://openclaw.ai), the open-source AI agent.

Getting Started:
1. Double-click "OpenClaw Setup" to run the onboarding wizard
   - Configure your AI provider (API keys)
   - Set up messaging channels (Telegram, Discord, etc.)
   - The gateway daemon is configured to auto-start on boot

2. Start the gateway (first time only):
   - Open terminal and run: sudo systemctl start openclaw-gateway
   - Or just reboot the VM

3. Double-click "OpenClaw Dashboard" to open the web UI
   - Gateway runs at http://127.0.0.1:18789/
   - Also accessible from host at http://localhost:18789/
   - Auto-starts on every boot after first setup

3. Double-click "OpenClaw TUI" for the terminal interface

Why a VM?
- OpenClaw has full system access (shell, files, browser)
- Running in a VM isolates it from your host machine
- Safe to experiment with agent capabilities
- Easy to destroy and recreate: vagrant destroy && vagrant up

Credentials: vagrant / vagrant
DESKTOP_README
    chmod 644 /home/vagrant/Desktop/README.txt
    chown vagrant:vagrant /home/vagrant/Desktop/README.txt

    # Set graphical target
    systemctl set-default graphical.target
    systemctl enable lightdm

    echo "=========================================="
    echo " Setup complete!"
    echo " The VM will reboot to start the desktop."
    echo " Login: vagrant / vagrant"
    echo ""
    echo " After login:"
    echo "   1. Run 'openclaw onboard' to configure"
    echo "   2. Start gateway: sudo systemctl start openclaw-gateway"
    echo "   3. Dashboard: http://127.0.0.1:18789/"
    echo ""
    echo " Gateway auto-starts on boot after setup."
    echo "=========================================="
  SHELL

  # Reboot after provisioning to start GUI
  config.vm.provision "shell", inline: "shutdown -r now", run: "once"
end
