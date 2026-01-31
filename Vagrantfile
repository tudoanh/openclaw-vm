# -*- mode: ruby -*-
# vi: set ft=ruby :

# OpenClaw Vagrant Environment
# A sandboxed VM for building and testing OpenClaw
# https://github.com/openclaw/openclaw

Vagrant.configure("2") do |config|
  # Base box - Ubuntu 22.04 LTS (Jammy Jellyfish)
  config.vm.box = "ubuntu/jammy64"
  config.vm.box_check_update = true

  # VM identification
  config.vm.hostname = "openclaw-dev"

  # Network configuration (optional - uncomment if needed)
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # VirtualBox provider configuration
  config.vm.provider "virtualbox" do |vb|
    # Display name in VirtualBox Manager
    vb.name = "OpenClaw Development Environment"

    # Enable GUI mode for desktop environment
    vb.gui = true

    # Resource allocation
    vb.memory = "4096"  # 4GB RAM (adjust based on your system)
    vb.cpus = 2         # 2 CPU cores (adjust based on your system)

    # Graphics configuration
    vb.customize ["modifyvm", :id, "--vram", "128"]           # Video memory
    vb.customize ["modifyvm", :id, "--accelerate3d", "on"]    # 3D acceleration
    vb.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]

    # Enhanced features
    vb.customize ["modifyvm", :id, "--clipboard-mode", "bidirectional"]
    vb.customize ["modifyvm", :id, "--draganddrop", "bidirectional"]

    # USB and audio (optional)
    vb.customize ["modifyvm", :id, "--audio", "pulse"]
    vb.customize ["modifyvm", :id, "--audioout", "on"]
  end

  # Provisioning script
  config.vm.provision "shell", inline: <<-SHELL
    set -e  # Exit on error

    echo "======================================"
    echo "OpenClaw Development Environment Setup"
    echo "======================================"

    # Update package lists
    echo "[1/5] Updating system packages..."
    export DEBIAN_FRONTEND=noninteractive
    apt-get update

    # Install desktop environment (XFCE - lightweight and fast)
    echo "[2/5] Installing desktop environment..."
    apt-get install -y \
      xfce4 \
      xfce4-goodies \
      lightdm \
      lightdm-gtk-greeter \
      xorg \
      dbus-x11

    # Install VirtualBox Guest Additions for better integration
    echo "[3/5] Installing VirtualBox Guest Additions..."
    apt-get install -y \
      virtualbox-guest-dkms \
      virtualbox-guest-utils \
      virtualbox-guest-x11

    # Install OpenClaw dependencies
    echo "[4/5] Installing OpenClaw build dependencies..."
    apt-get install -y \
      git \
      build-essential \
      cmake \
      libsdl2-dev \
      libsdl2-mixer-dev \
      libsdl2-image-dev \
      libsdl2-ttf-dev \
      libpng-dev \
      zlib1g-dev \
      libtinyxml-dev \
      libglew-dev \
      libopenal-dev \
      libvorbis-dev \
      libfreetype6-dev

    # Install useful development tools
    apt-get install -y \
      vim \
      nano \
      htop \
      curl \
      wget \
      unzip

    # Configure auto-login for vagrant user
    echo "[5/5] Configuring desktop environment..."
    mkdir -p /etc/lightdm/lightdm.conf.d
    cat > /etc/lightdm/lightdm.conf.d/50-autologin.conf <<EOF
[Seat:*]
autologin-user=vagrant
autologin-user-timeout=0
user-session=xfce
EOF

    # Clone OpenClaw repository
    if [ ! -d "/home/vagrant/openclaw" ]; then
      echo "Cloning OpenClaw repository..."
      sudo -u vagrant git clone https://github.com/openclaw/openclaw /home/vagrant/openclaw

      # Create build directory
      sudo -u vagrant mkdir -p /home/vagrant/openclaw/Build

      # Create a helpful readme on the desktop
      sudo -u vagrant mkdir -p /home/vagrant/Desktop
      cat > /home/vagrant/Desktop/README.txt <<DESKTOP_README
OpenClaw Development Environment
================================

Welcome! This VM is pre-configured for building and testing OpenClaw.

Quick Start:
1. Open Terminal
2. cd ~/openclaw/Build
3. cmake ..
4. make -j$(nproc)
5. ./openclaw

Note: You'll need the original Captain Claw game assets to run OpenClaw.
Visit: https://github.com/openclaw/openclaw for more information.

Source code: ~/openclaw
DESKTOP_README
      chmod 644 /home/vagrant/Desktop/README.txt
      chown vagrant:vagrant /home/vagrant/Desktop/README.txt
    fi

    # Set graphical target
    systemctl set-default graphical.target

    # Enable and start display manager
    systemctl enable lightdm
    systemctl set-default graphical.target

    echo "======================================"
    echo "Setup complete!"
    echo "Reboot the VM to start the GUI desktop."
    echo "Login: vagrant / vagrant"
    echo "======================================"
  SHELL

  # Reboot after provisioning to start GUI
  config.vm.provision "shell", inline: "shutdown -r now", run: "once"
end
