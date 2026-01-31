# OpenClaw Vagrant Environment

A sandboxed Vagrant virtual machine for building, testing, and playing [OpenClaw](https://github.com/openclaw/openclaw) - the open-source reimplementation of Captain Claw.

## Features

- 🖥️ **Full GUI Desktop Environment** - Ubuntu 22.04 with XFCE
- 🔒 **Sandboxed Testing** - Isolated VM environment for safe testing
- 📦 **Pre-configured Dependencies** - All OpenClaw build requirements included
- 🚀 **One-Command Setup** - `vagrant up` and you're ready to go
- 🎮 **Ready to Build** - OpenClaw source code pre-cloned and ready to compile

## Prerequisites

Before you begin, ensure you have the following installed:

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) (6.1 or later)
- [Vagrant](https://www.vagrantup.com/downloads) (2.2 or later)

### Installation Guides

<details>
<summary>Windows</summary>

1. Download and install VirtualBox from https://www.virtualbox.org/wiki/Downloads
2. Download and install Vagrant from https://www.vagrantup.com/downloads
3. Reboot your system after installation

</details>

<details>
<summary>macOS</summary>

```bash
# Using Homebrew
brew install --cask virtualbox
brew install --cask vagrant
```

</details>

<details>
<summary>Linux</summary>

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install virtualbox vagrant

# Fedora
sudo dnf install virtualbox vagrant

# Arch Linux
sudo pacman -S virtualbox vagrant
```

</details>

## Quick Start

```bash
# Clone this repository
git clone https://github.com/YOUR_USERNAME/openclaw-vagrant.git
cd openclaw-vagrant

# Start the VM (first run will take 10-15 minutes)
vagrant up

# The Ubuntu desktop GUI will open automatically
# Login credentials: vagrant / vagrant
```

Once the desktop loads, you'll find OpenClaw source code in `~/openclaw`.

## Building OpenClaw

Inside the VM, open a terminal and run:

```bash
cd ~/openclaw/Build
cmake ..
make -j$(nproc)

# Run the game (requires original game assets)
./openclaw
```

### Getting Game Assets

OpenClaw requires the original Captain Claw game assets. You can:

1. Copy your legitimate game files into the VM
2. Follow the [official OpenClaw guide](https://github.com/openclaw/openclaw#obtaining-game-assets)

## VM Management

```bash
# Start the VM
vagrant up

# Shutdown the VM
vagrant halt

# Restart the VM
vagrant reload

# SSH into the VM (headless access)
vagrant ssh

# Destroy the VM (clean slate)
vagrant destroy
```

## Configuration

The VM is configured with:

- **OS**: Ubuntu 22.04 LTS (Jammy Jellyfish)
- **Desktop**: XFCE4 (lightweight and responsive)
- **RAM**: 4GB (adjustable in Vagrantfile)
- **CPUs**: 2 cores (adjustable in Vagrantfile)
- **Video Memory**: 128MB with 3D acceleration
- **Shared Features**: Bidirectional clipboard and drag-and-drop

### Customizing Resources

Edit `Vagrantfile` and adjust these values:

```ruby
vb.memory = "4096"  # RAM in MB
vb.cpus = 2         # CPU cores
```

## Troubleshooting

<details>
<summary>GUI window doesn't appear</summary>

Ensure VirtualBox GUI is enabled:
```ruby
vb.gui = true
```

Check VirtualBox Extension Pack is installed for enhanced features.

</details>

<details>
<summary>3D acceleration issues</summary>

If you experience graphics glitches:
1. Disable 3D acceleration in Vagrantfile
2. Reduce VRAM to 64MB or 32MB

</details>

<details>
<summary>VM is slow/laggy</summary>

Increase VM resources:
```ruby
vb.memory = "8192"  # 8GB RAM
vb.cpus = 4         # 4 CPU cores
```

</details>

<details>
<summary>Build errors</summary>

Ensure all dependencies are installed:
```bash
sudo apt-get update
sudo apt-get install build-essential cmake libsdl2-dev libsdl2-mixer-dev \
    libsdl2-image-dev libsdl2-ttf-dev libpng-dev zlib1g-dev libtinyxml-dev \
    libglew-dev libopenal-dev libvorbis-dev libfreetype6-dev
```

</details>

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [OpenClaw](https://github.com/openclaw/openclaw) - The amazing open-source Captain Claw reimplementation
- [Vagrant](https://www.vagrantup.com/) - Development environment automation
- [VirtualBox](https://www.virtualbox.org/) - Virtualization platform

## Related Projects

- [OpenClaw](https://github.com/openclaw/openclaw) - Main OpenClaw project
- [Captain Claw Reverse Engineering](https://github.com/pjasicek/CaptainClaw) - Original reverse engineering effort

## Support

- Open an [issue](https://github.com/YOUR_USERNAME/openclaw-vagrant/issues) for bug reports
- Check [OpenClaw documentation](https://github.com/openclaw/openclaw/wiki) for game-specific questions
- Visit the [OpenClaw community](https://github.com/openclaw/openclaw/discussions)

---

Made with ❤️ for the OpenClaw community
