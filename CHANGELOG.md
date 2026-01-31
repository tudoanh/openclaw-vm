# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Initial project setup for OpenClaw AI agent VM sandbox
- Ubuntu 22.04 LTS with XFCE desktop environment
- VirtualBox configuration with GUI, nested virtualization
- OpenClaw auto-installation via npm (Node.js 22)
- Common desktop applications: Google Chrome, VLC, LibreOffice
- snapd package manager
- Development tools: Git, vim, nano, htop, curl, wget
- Desktop shortcuts for OpenClaw setup, dashboard, and TUI
- Port forwarding for OpenClaw gateway dashboard (18789)
- Comprehensive documentation (README, CONTRIBUTING, troubleshooting, customization)
- GitHub Actions CI for Vagrantfile validation and markdown linting
- GitHub issue templates
- MIT License

### Changed

- Kept Ubuntu 22.04 LTS base box (Canonical dropped official 24.04 Vagrant boxes)
- Replaced game build dependencies with OpenClaw AI agent stack (Node.js, npm, pnpm)
- Rewritten provisioning to install common desktop apps and OpenClaw

### Removed

- C/C++ build toolchain (build-essential, cmake, SDL2, etc.)
- Captain Claw game asset references

---

## How to Update This Changelog

When making changes:

1. Add entries under the `[Unreleased]` section
2. Use the appropriate subsection (Added, Changed, Fixed, etc.)
3. Write changes from the user's perspective
4. Include issue/PR numbers when applicable

When releasing a version, move entries from `[Unreleased]` to a new version section:

```markdown
## [1.0.0] - 2026-01-31

### Added
- Initial release
```
