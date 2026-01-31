# OpenClaw VM

A sandboxed Vagrant virtual machine for safely running and testing [OpenClaw](https://github.com/openclaw/openclaw) - the open-source autonomous AI agent (formerly Moltbot / Clawdbot).

## Why a VM?

OpenClaw is a powerful AI agent with full system access -- shell, files, browser, messaging platforms. Running it directly on your personal machine [raises security concerns](https://blogs.cisco.com/ai/personal-ai-agents-like-openclaw-are-a-security-nightmare). This project provides a pre-configured, isolated VM so you can:

- **Test safely** -- OpenClaw runs inside a disposable VM, not on your host
- **Experiment freely** -- try agent capabilities without risk to your data
- **Tear down and rebuild** -- `vagrant destroy && vagrant up` for a clean slate
- **Access from host** -- Gateway dashboard forwarded to `localhost:18789`

## What's Included

| Category | Software |
|----------|----------|
| **OS** | Ubuntu 22.04 LTS with XFCE desktop |
| **AI Agent** | OpenClaw (latest), Node.js 22, pnpm |
| **Browser** | Google Chrome |
| **Office** | LibreOffice |
| **Media** | VLC |
| **Dev Tools** | Git, vim, nano, htop, curl, wget |
| **Package Mgr** | snapd, apt |
| **Extras** | Evince (PDF), Ristretto (images), p7zip, system monitor |

## Prerequisites

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) (7.0 or later)
- [Vagrant](https://www.vagrantup.com/downloads) (2.4 or later)

<details>
<summary>Windows</summary>

1. Download and install VirtualBox from https://www.virtualbox.org/wiki/Downloads
2. Download and install Vagrant from https://www.vagrantup.com/downloads
3. Reboot your system after installation

</details>

<details>
<summary>macOS</summary>

```bash
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
git clone https://github.com/YOUR_USERNAME/openclaw-vm.git
cd openclaw-vm

# Start the VM (first run downloads the base box and provisions)
vagrant up

# The XFCE desktop GUI opens automatically
# Login: vagrant / vagrant
```

## Setting Up OpenClaw

After the desktop loads:

1. **Run the onboarding wizard** -- double-click "OpenClaw Setup" on the desktop, or open a terminal and run:

   ```bash
   openclaw onboard --install-daemon
   ```

   This walks you through:
   - Choosing an AI provider and entering API keys
   - Configuring messaging channels (Telegram, Discord, WhatsApp, etc.)
   - Installing the gateway as a systemd service

2. **Open the dashboard** -- double-click "OpenClaw Dashboard" or visit `http://127.0.0.1:18789/` in Chrome inside the VM.

3. **Use the TUI** -- double-click "OpenClaw TUI" or run `openclaw` in a terminal.

The dashboard is also accessible from your **host machine** at `http://localhost:18789/` via port forwarding.

## VM Management

```bash
vagrant up        # Start the VM
vagrant halt      # Shut down the VM
vagrant reload    # Restart the VM
vagrant ssh       # SSH into the VM (headless)
vagrant destroy   # Delete the VM entirely
vagrant provision # Re-run provisioning on existing VM
```

## Configuration

Default VM specs (edit `Vagrantfile` to adjust):

| Setting | Default |
|---------|---------|
| RAM | 4 GB |
| CPUs | 2 cores |
| Video RAM | 128 MB |
| 3D Acceleration | Enabled |
| Clipboard | Bidirectional |
| Drag and Drop | Bidirectional |
| Nested Virtualization | Enabled |

## Troubleshooting

See [docs/troubleshooting.md](docs/troubleshooting.md) for detailed solutions. Quick fixes:

<details>
<summary>GUI window doesn't appear</summary>

Ensure `vb.gui = true` is set in the Vagrantfile. Check that VirtualBox Extension Pack is installed.

</details>

<details>
<summary>OpenClaw command not found</summary>

SSH into the VM and reinstall:
```bash
vagrant ssh
sudo npm install -g openclaw@latest
```

</details>

<details>
<summary>Port 18789 already in use on host</summary>

Vagrant auto-corrects port conflicts. Check `vagrant port` to see the actual mapping, or stop whatever is using port 18789 on your host.

</details>

<details>
<summary>VM is slow</summary>

Increase resources in the Vagrantfile:
```ruby
vb.memory = "8192"  # 8GB RAM
vb.cpus = 4         # 4 CPU cores
```

</details>

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License -- see [LICENSE](LICENSE) for details.

## Links

- [OpenClaw](https://github.com/openclaw/openclaw) -- Main OpenClaw repository
- [OpenClaw Docs](https://docs.openclaw.ai/) -- Official documentation
- [openclaw.ai](https://openclaw.ai/) -- Project website
- [Vagrant](https://www.vagrantup.com/) -- VM automation
- [VirtualBox](https://www.virtualbox.org/) -- Virtualization platform
