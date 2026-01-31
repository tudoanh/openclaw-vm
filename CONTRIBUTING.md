# Contributing to OpenClaw VM

Thank you for considering contributing to OpenClaw VM. This project provides a sandboxed Vagrant environment for safely running the OpenClaw AI agent.

## Code of Conduct

This project and everyone participating in it is governed by our commitment to providing a welcoming and inclusive environment. By participating, you are expected to uphold this standard.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, check existing issues to avoid duplicates. When filing a bug report, include:

- Description of the bug
- Steps to reproduce
- Expected behavior
- Host OS, VirtualBox version, Vagrant version
- Error logs (`vagrant up --debug > vagrant.log 2>&1`)

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. Include:

- A clear and descriptive title
- Detailed description of the suggested enhancement
- Why this enhancement would be useful
- Alternative solutions you've considered

### Pull Requests

1. Fork the repository and create your branch from `main`
2. Make your changes with clear, descriptive commit messages
3. Test your changes -- ensure the VM provisions correctly
4. Update documentation if you change functionality
5. Submit a pull request with a clear description

#### Testing Checklist

Before submitting a PR, verify:

- [ ] VM provisions successfully (`vagrant up`)
- [ ] GUI displays correctly after reboot
- [ ] OpenClaw installs and is available on PATH
- [ ] Common applications (Chrome, VLC, LibreOffice) launch
- [ ] Port forwarding works (gateway dashboard on host:18789)
- [ ] Desktop shortcuts function correctly
- [ ] Documentation is updated
- [ ] No unnecessary files are committed

#### Commit Message Guidelines

- Use present tense ("Add feature" not "Added feature")
- Use imperative mood ("Move cursor to..." not "Moves cursor to...")
- Limit first line to 72 characters
- Reference issues and pull requests when relevant

### Development Setup

```bash
# Fork and clone the repository
git clone https://github.com/YOUR_USERNAME/openclaw-vm.git
cd openclaw-vm

# Create a feature branch
git checkout -b feature/your-feature-name

# Test your changes
vagrant up
# ... test the VM ...
vagrant destroy

# Commit and push
git add .
git commit -m "Add your feature"
git push origin feature/your-feature-name
```

### Project Structure

```
openclaw-vm/
├── Vagrantfile              # Main VM configuration and provisioning
├── README.md                # Project documentation
├── CONTRIBUTING.md          # This file
├── CHANGELOG.md             # Version history
├── LICENSE                  # MIT License
├── .gitignore               # Git ignore rules
├── .markdownlint.json       # Markdown linting config
├── .markdown-link-check.json # Link checker config
├── docs/
│   ├── customization.md     # VM customization guide
│   └── troubleshooting.md   # Troubleshooting guide
└── .github/
    ├── workflows/
    │   └── vagrant-validate.yml  # CI pipeline
    └── ISSUE_TEMPLATE/
        ├── bug_report.md
        └── feature_request.md
```

## Style Guides

### Vagrantfile Style

- Use 2 spaces for indentation
- Add comments for non-obvious configuration
- Keep provisioning scripts organized with numbered stages
- Use variables for configurable values

### Documentation Style

- Use Markdown for all documentation
- Use clear, concise language
- Include code examples where helpful

## Additional Resources

- [Vagrant Documentation](https://www.vagrantup.com/docs)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [OpenClaw Project](https://github.com/openclaw/openclaw)
- [OpenClaw Docs](https://docs.openclaw.ai/)

## Questions?

Open an issue with the `question` label.
