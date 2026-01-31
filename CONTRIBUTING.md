# Contributing to OpenClaw Vagrant

First off, thank you for considering contributing to OpenClaw Vagrant! It's people like you that make this project a great tool for the community.

## Code of Conduct

This project and everyone participating in it is governed by our commitment to providing a welcoming and inclusive environment. By participating, you are expected to uphold this standard.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When you create a bug report, include as many details as possible:

**Bug Report Template:**

```markdown
**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected behavior**
A clear and concise description of what you expected to happen.

**Environment:**
 - Host OS: [e.g. Windows 11, macOS 14, Ubuntu 22.04]
 - VirtualBox Version: [e.g. 7.0]
 - Vagrant Version: [e.g. 2.3.4]
 - Error logs/screenshots if applicable

**Additional context**
Add any other context about the problem here.
```

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion, include:

- **Use a clear and descriptive title**
- **Provide a detailed description of the suggested enhancement**
- **Explain why this enhancement would be useful**
- **List any alternative solutions you've considered**

### Pull Requests

1. **Fork the repository** and create your branch from `main`
2. **Make your changes** with clear, descriptive commit messages
3. **Test your changes** - ensure the VM provisions correctly
4. **Update documentation** - update README.md if you change functionality
5. **Submit a pull request** with a clear description of changes

#### Pull Request Process

1. Ensure any install or build dependencies are documented
2. Update the README.md with details of changes if applicable
3. Test the Vagrantfile with `vagrant up` and `vagrant destroy` before submitting
4. Your PR will be reviewed by maintainers who may request changes

#### Commit Message Guidelines

- Use present tense ("Add feature" not "Added feature")
- Use imperative mood ("Move cursor to..." not "Moves cursor to...")
- Limit first line to 72 characters
- Reference issues and pull requests when relevant

Examples:
```
Add support for Ubuntu 24.04
Fix 3D acceleration on macOS hosts
Update dependencies to latest versions
```

### Development Setup

To work on this project:

```bash
# Fork and clone the repository
git clone https://github.com/YOUR_USERNAME/openclaw-vagrant.git
cd openclaw-vagrant

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

### Testing Checklist

Before submitting a PR, verify:

- [ ] VM provisions successfully (`vagrant up`)
- [ ] GUI displays correctly
- [ ] All dependencies install without errors
- [ ] OpenClaw source code clones successfully
- [ ] Desktop environment loads properly
- [ ] Documentation is updated
- [ ] No unnecessary files are committed

### Project Structure

```
openclaw-vagrant/
├── Vagrantfile           # Main VM configuration
├── README.md            # Project documentation
├── CONTRIBUTING.md      # This file
├── LICENSE             # MIT License
├── .gitignore          # Git ignore rules
└── docs/               # Additional documentation
    └── troubleshooting.md
```

## Styleguides

### Vagrantfile Style

- Use 2 spaces for indentation
- Add comments for non-obvious configuration
- Keep provisioning scripts organized and readable
- Use variables for configurable values

### Documentation Style

- Use Markdown for all documentation
- Use clear, concise language
- Include code examples where helpful
- Keep line length reasonable (80-100 characters)

## Additional Resources

- [Vagrant Documentation](https://www.vagrantup.com/docs)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [OpenClaw Project](https://github.com/openclaw/openclaw)

## Questions?

Feel free to open an issue with the `question` label, and we'll be happy to help!

---

Thank you for contributing! 🎮
