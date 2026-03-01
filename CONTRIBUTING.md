# Contributing to FamilyCloud

Thank you for your interest in contributing to FamilyCloud! This project aims to help people build their own private family cloud servers and break free from Big Tech's limited storage and pricing.

## How Can You Contribute?

### 1. Reporting Issues

If you encounter any problems while following the guide:

- Check the [Troubleshooting section](README.md#troubleshooting) first
- Search existing [Issues](../../issues) to see if it's already reported
- If not, create a new issue with:
  - **Clear title** describing the problem
  - **Your hardware specs** (PC model, RAM, CPU, storage)
  - **Ubuntu version** (run `lsb_release -a`)
  - **Full error messages** (copy-paste the complete output)
  - **Steps you've already tried**
  - **Screenshots** if applicable

### 2. Suggesting Improvements

Have ideas to make the guide better?

- Open an issue with tag `enhancement`
- Describe your suggestion clearly
- Explain why it would help users
- If it's a new feature, describe the use case

### 3. Improving Documentation

Found a typo? Unclear instructions? Outdated information?

- Fork the repository
- Make your changes
- Submit a Pull Request with:
  - Clear description of what you changed
  - Why the change improves the guide
  - Screenshots of before/after if relevant

### 4. Adding New Features

Want to contribute code or new sections?

**Before writing code:**
- Open an issue first to discuss your idea
- Get feedback from maintainers
- Ensure it aligns with the project goals

**Code/Documentation Standards:**
- Keep it beginner-friendly
- Test all commands on a fresh Ubuntu installation
- Include error handling and troubleshooting tips
- Follow the existing documentation style
- No jargon without explanation

### 5. Testing on Different Hardware

Tried the guide on different hardware?

- Report your success/failures
- Share any hardware-specific issues you encountered
- Help expand the compatibility list

## Pull Request Process

1. **Fork** the repository
2. **Create a branch** for your changes (`git checkout -b feature/improvement-name`)
3. **Make your changes**
4. **Test thoroughly** - ideally on a fresh Ubuntu installation
5. **Commit** with clear messages (`git commit -m "Add troubleshooting for issue X"`)
6. **Push** to your fork (`git push origin feature/improvement-name`)
7. **Create Pull Request** with:
   - Clear title
   - Description of changes
   - Why it's needed
   - Testing you've done

## What We're Looking For

**High Priority:**
- Fixes for errors in existing documentation
- Additional troubleshooting solutions
- Hardware compatibility reports
- Security improvements
- Performance optimizations
- Alternative methods that are simpler

**Medium Priority:**
- New features for v2 roadmap
- Better explanations of complex topics
- Screenshots and diagrams
- Video tutorials

**Low Priority:**
- Cosmetic changes
- Minor rewording (unless it improves clarity significantly)

## What We Won't Accept

- Changes that make the guide harder for beginners
- Proprietary or paid-only solutions (unless clearly better and affordable)
- Anything that compromises security
- Overly complex solutions when simple ones exist
- Content promoting specific commercial products (unless genuinely best option)

## Code of Conduct

### Our Standards

**Be Respectful:**
- We're all learning
- No such thing as a "stupid question"
- Beginners are welcome and valued
- Constructive criticism only

**Be Helpful:**
- Assume good intentions
- Provide detailed explanations
- Share your knowledge freely
- Link to resources when helpful

**Be Patient:**
- Not everyone has the same technical background
- Some people are trying this for the first time
- Response times may vary

**Unacceptable Behavior:**
- Harassment or discriminatory language
- Insulting or derogatory comments
- Trolling or spam
- Publishing others' private information
- Any conduct that would be inappropriate in a professional setting

## Questions?

- Open an issue with tag `question`
- Be specific about what you need help with
- Include context about what you're trying to achieve

## Recognition

Contributors will be:
- Listed in the README credits section
- Thanked in release notes for significant contributions
- Given credit for their specific contributions

## Development Setup

Since this is primarily a documentation project, you just need:

1. **Git** - for version control
2. **Text editor** - VSCode, Sublime, or any markdown editor
3. **Virtual machine (optional)** - for testing the guide
   - VirtualBox or VMware
   - Ubuntu Server 24.04 LTS ISO

## Testing Your Changes

**Before submitting a PR:**

1. **Spell check** your documentation
2. **Test all commands** on a fresh Ubuntu installation if possible
3. **Verify all links** work
4. **Check formatting** renders correctly on GitHub
5. **Read it as a beginner** - is it clear?

## Release Process

Maintainers will:
1. Review PRs for quality and accuracy
2. Test changes when possible
3. Merge approved changes
4. Update version numbers for significant changes
5. Create release notes

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

**Thank you for helping make self-hosted family cloud servers accessible to everyone!**
