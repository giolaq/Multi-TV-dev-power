# Multi-TV Builder - AI-Powered Development Assistant

> **Fork Notice**: This is a fork of the original [Multi-TV Builder Power](https://github.com/AmazonAppDev/react-native-multi-tv-app-sample) with added support for **Claude Code** (Anthropic's CLI). The original Kiro Power functionality is fully preserved.

Build production-ready cross-platform TV applications with React Native - supporting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV platforms with a single codebase.

## What is This?

This repository provides an **AI-powered development assistant** for building TV applications. It works with multiple AI coding assistants:

| AI Assistant | Provider | Support Level |
|--------------|----------|---------------|
| **Claude Code** | Anthropic | Full support (skills, commands, context) |
| **Kiro** | Amazon | Full support (powers, steering files) |

The assistant combines monorepo architecture, shared UI components, platform-specific optimizations, and TV-optimized navigation into a unified development framework.

---

## Using with Claude Code

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) is Anthropic's official CLI for AI-assisted development.

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/moinsen-dev/Multi-TV-dev-power.git
   ```

2. To use in your own TV app project, copy these files to your project root:
   - `.claude/` directory (commands and skills)
   - `CLAUDE.md` (project context)

### Features

- **Automatic Context Loading**: `CLAUDE.md` is read automatically when Claude Code starts
- **Custom Slash Commands**: Quick access to TV development guidance
- **Skill Definition**: Activates expert TV development knowledge

### Slash Commands

| Command | Description |
|---------|-------------|
| `/multi-tv-help` | General TV development guidance |
| `/multi-tv-setup` | Setup instructions for new projects |
| `/multi-tv-build` | Build commands for all platforms |
| `/multi-tv-platform` | Platform-specific implementation help |

### Claude Code Structure

```
.claude/
├── commands/
│   ├── multi-tv-help.md      # General help command
│   ├── multi-tv-setup.md     # Setup guide command
│   ├── multi-tv-build.md     # Build commands
│   └── multi-tv-platform.md  # Platform-specific guide
└── skills/
    └── multi-tv-builder.md   # TV builder skill definition
CLAUDE.md                      # Project context file
```

---

## Using with Kiro

[Kiro](https://kiro.dev) is Amazon's AI coding agent. This repository maintains full compatibility with the [Kiro Powers](https://kiro.dev/docs/powers/) format.

### Installation

1. Open Kiro
2. Go to Powers panel
3. Click "Add power from Local Path" or "Add power from GitHub"
4. Select this directory or provide the repository URL

### Features

- **Automatic Activation**: Power activates when TV development topics are mentioned
- **Steering Files**: Detailed guidance in `multi-tv-builder/steering/`
- **Complete Documentation**: Full implementation guide in `POWER.md`

### Kiro Power Structure

```
multi-tv-builder/
├── POWER.md              # Main power documentation
└── steering/
    ├── product.md        # Product overview
    ├── structure.md      # Project structure guide
    └── tech.md           # Technology stack reference
```

---

## Example Prompts (Works with Both)

These prompts work with both Claude Code and Kiro:

- "Help me build a TV app with React Native"
- "How do I set up spatial navigation for Fire TV?"
- "What's the best way to structure a multi-TV app?"
- "Show me how to implement video playback for TV"
- "What are the TV safe zones I should use?"
- "How do I handle remote control input on different platforms?"

---

## Common Use Cases

- **Building streaming video apps** (Netflix-like experiences)
- **Content discovery platforms** (browse and explore content)
- **TV-optimized interfaces** (10-foot UI design)
- **Multi-platform TV deployment** (single codebase, multiple platforms)
- **Learning TV development** (comprehensive examples and guides)

## Quick Start

Clone the official sample repository to get started with a working implementation:

```bash
git clone https://github.com/AmazonAppDev/react-native-multi-tv-app-sample.git
cd react-native-multi-tv-app-sample
yarn install && yarn bootstrap
```

## Supported Platforms

| Platform | Technology | Output |
|----------|-----------|--------|
| Android TV | react-native-tvos | APK/AAB |
| Apple TV (tvOS) | react-native-tvos + Expo | IPA |
| Fire TV (Fire OS) | react-native-tvos | APK |
| Fire TV (Vega OS) | Amazon Vega SDK | VPKG |
| Web TV | React Native Web | HTML/JS |

---

## Repository Structure

```
Multi-TV-dev-power/
├── .claude/                  # Claude Code configuration
│   ├── commands/            # Slash commands
│   └── skills/              # Skill definitions
├── multi-tv-builder/        # Kiro Power configuration
│   ├── POWER.md            # Main documentation
│   └── steering/           # AI steering files
├── CLAUDE.md               # Claude Code project context
└── README.md               # This file
```

---

## Credits

- **Original Author**: Giovanni Laquidara
- **Original Project**: [React Native Multi-TV App Sample](https://github.com/AmazonAppDev/react-native-multi-tv-app-sample)
- **Claude Code Adaptation**: Fork with added Claude Code support

**Version**: 2.0.0
**Last Updated**: December 2025
**Compatible with**: Claude Code, Kiro

## Documentation

- [CLAUDE.md](./CLAUDE.md) - Claude Code project context
- [POWER.md](./multi-tv-builder/POWER.md) - Complete implementation guide (1300+ lines)
- [Steering Files](./multi-tv-builder/steering/) - Detailed technical documentation
