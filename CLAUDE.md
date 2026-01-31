# CLAUDE.md

## Project: openclaw-vm

Vagrant-based VM for safely running and testing **OpenClaw** (the open-source autonomous AI agent, formerly Moltbot/Clawdbot by Peter Steinberger). This is NOT the Captain Claw game reimplementation -- it was originally scaffolded that way but has been fully rewritten.

## What OpenClaw is

- Open-source self-hosted AI agent: https://github.com/openclaw/openclaw
- Installed via `npm install -g openclaw@latest` (requires Node >= 22)
- Gateway daemon runs on port 18789 (web dashboard)
- Onboarding wizard: `openclaw onboard --install-daemon`
- Docs: https://docs.openclaw.ai/
- Has full system access (shell, files, browser, messaging) -- hence the need for VM isolation

## Project structure

```
Vagrantfile              # VM config: Ubuntu 22.04 + XFCE + OpenClaw + desktop apps
README.md                # User-facing docs
CONTRIBUTING.md          # Contributor guidelines
CHANGELOG.md             # Follows Keep a Changelog
LICENSE                  # MIT
docs/customization.md    # Advanced VM customization (resources, Docker, snapshots, multi-VM)
docs/troubleshooting.md  # Common issues (provisioning, GUI, OpenClaw, VirtualBox, network)
.github/workflows/vagrant-validate.yml  # CI: vagrant validate + ruby syntax + markdown lint
.github/ISSUE_TEMPLATE/  # Bug report + feature request templates
```

## VM provisions (Vagrantfile)

8-stage provisioning:
1. System update
2. XFCE desktop + LightDM (auto-login as vagrant)
3. VirtualBox Guest Additions
4. Common apps: git, curl, wget, vim, nano, htop, LibreOffice, VLC, Thunar plugins, Evince, Ristretto, gnome-system-monitor
5. Google Chrome (downloaded .deb)
6. snapd
7. Node.js 22 (NodeSource) + pnpm + OpenClaw
8. Desktop config: 3 shortcuts (OpenClaw Setup, Dashboard, TUI) + README.txt

Port forwarding: guest 18789 -> host 18789 (OpenClaw gateway dashboard)
Nested virtualization enabled.

## Current state

- Initial commit has the old game-focused scaffolding
- All files have been rewritten for OpenClaw AI agent but changes are uncommitted
- No remote repository configured yet
- Vagrantfile passes `ruby -c` and `vagrant validate`
- Tested successfully with `vagrant up` -- all 8 provisioning stages pass
- Verified inside VM: Node.js 22.22.0, OpenClaw 2026.1.29, Chrome, VLC, LibreOffice all present

## Known issues / gotchas

- `ubuntu/noble64` (24.04) does NOT exist on Vagrant Cloud -- Canonical dropped official boxes due to HashiCorp BSL
- `virtualbox-guest-dkms` does not exist in jammy repos -- use `virtualbox-guest-utils virtualbox-guest-x11` with `|| true`
- KVM and VirtualBox conflict: if host has `kvm_amd`/`kvm_intel` loaded, VirtualBox will fail with `VERR_SVM_IN_USE`. Fix: `sudo modprobe -r kvm_amd kvm`
- Guest Additions version mismatch warning (6.0.0 vs 7.0) is harmless -- clipboard/drag-drop still work
- LightDM `systemctl enable` shows a warning about unit files having no install config -- this is cosmetic, auto-login works fine

## Key decisions

- Base box: `ubuntu/jammy64` (22.04 LTS) -- Canonical dropped official 24.04 Vagrant boxes due to HashiCorp BSL
- Desktop: XFCE (lightweight)
- OpenClaw installed globally via npm (not Docker, not from source)
- User runs onboarding wizard manually after first boot (needs API keys)
- Chrome installed via direct .deb download (not snap) for reliability

## Commands

```bash
ruby -c Vagrantfile      # Validate Vagrantfile syntax
vagrant validate         # Full Vagrant validation (needs Vagrant installed)
vagrant up               # Build and start VM
vagrant destroy -f       # Tear down VM
vagrant provision        # Re-run provisioning on existing VM
vagrant ssh              # SSH into running VM
```
