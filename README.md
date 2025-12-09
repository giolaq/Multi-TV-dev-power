# Multi-TV Builder Power

Build production-ready cross-platform TV applications with React Native - supporting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV platforms with a single codebase.

## What is This?

This is an AI-powered development assistant that combines monorepo architecture, shared UI components, platform-specific optimizations, and TV-optimized navigation into a unified development framework.

**Supported AI Assistants:**
- **Claude Code** (Anthropic's CLI) - Primary support
- **Kiro** (Amazon's AI coding agent) - Legacy support

---

## Using with Claude Code

### Installation

1. Clone this repository into your project or as a reference:
   ```bash
   git clone https://github.com/moinsen-dev/Multi-TV-dev-power.git
   ```

2. Copy the `.claude/` directory and `CLAUDE.md` to your TV app project root

### Usage

Claude Code automatically reads `CLAUDE.md` for project context. Additionally, you can use these **slash commands**:

| Command | Description |
|---------|-------------|
| `/multi-tv-help` | General TV development guidance |
| `/multi-tv-setup` | Setup instructions for new projects |
| `/multi-tv-build` | Build commands for all platforms |
| `/multi-tv-platform` | Platform-specific implementation help |

### Example Prompts

- "Help me build a TV app with React Native"
- "How do I set up spatial navigation for Fire TV?"
- "What's the best way to structure a multi-TV app?"
- "Show me how to implement video playback for TV"

### Structure

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

## Using with Kiro (Legacy)

### Installation

1. Open Kiro
2. Go to Powers panel
3. Click "Add power from Local Path" or "Add power from GitHub"
4. Select this directory or provide the repository URL

### Usage

The power activates automatically when you mention TV development in your conversation with Kiro:

- "Help me build a TV app with React Native"
- "How do I set up spatial navigation for Fire TV?"
- "What's the best way to structure a multi-TV app?"
- "Show me how to implement video playback for TV"

---

## Common Use Cases

- **Building streaming video apps** (Netflix-like experiences)
- **Content discovery platforms** (browse and explore content)
- **TV-optimized interfaces** (10-foot UI design)
- **Multi-platform TV deployment** (single codebase, multiple platforms)
- **Learning TV development** (comprehensive examples and guides)

## Quick Start

Clone the official sample repository to get started:

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

**Version**: 2.0.0
**Author**: Giovanni Laquidara
**Last Updated**: December 2025
**Compatible with**: Claude Code, Kiro Developer Tools

For detailed implementation guidance, see:
- [CLAUDE.md](./CLAUDE.md) - Claude Code context
- [POWER.md](./multi-tv-builder/POWER.md) - Complete implementation guide
- [Steering Files](./multi-tv-builder/steering/) - Detailed documentation
