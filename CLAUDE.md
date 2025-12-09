# Multi-TV Builder - Claude Code Context

Build production-ready cross-platform TV applications with React Native - supporting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV platforms with a single codebase.

## Quick Reference

- **Sample Repo**: https://github.com/AmazonAppDev/react-native-multi-tv-app-sample
- **Slash Commands**: `/multi-tv-help`, `/multi-tv-setup`, `/multi-tv-build`, `/multi-tv-platform`
- **Skills**: `multi-tv-builder`, `apple-tv-troubleshooter`, `tmdb-integration`
- **Author**: Giovanni Laquidara

## Core Capabilities

- **Cross-platform TV support** with single codebase for 5+ platforms
- **Monorepo architecture** with Yarn workspaces for apps and shared packages
- **Spatial navigation** optimized for TV remote controls and D-pad input
- **Video playback** with custom controls and remote integration
- **Platform-specific optimizations** using automatic file resolution
- **Shared UI library** with reusable TV-optimized components
- **TypeScript-first** with strict type safety across all packages

## Supported Platforms

| Platform | Technology | Output |
|----------|-----------|--------|
| Android TV | react-native-tvos | APK/AAB |
| Apple TV (tvOS) | react-native-tvos + Expo | IPA |
| Fire TV (Fire OS) | react-native-tvos | APK |
| Fire TV (Vega OS) | Amazon Vega SDK | Optimized VPKG |
| Web TV | React Native Web | HTML/JS Bundle |

## Architecture Overview

### Monorepo Structure
```
project-root/
├── apps/                    # Application packages
│   ├── expo-multi-tv/      # Universal TV app
│   └── vega/               # Fire TV Vega optimized
├── packages/               # Shared packages
│   └── shared-ui/          # Shared components
├── package.json            # Workspace configuration
└── tsconfig.base.json      # Shared TypeScript config
```

### Platform-Specific File Resolution
Metro bundler automatically resolves platform files:
- `.kepler.ts/tsx` - Fire TV Vega OS (highest priority for Vega builds)
- `.android.ts/tsx` - Android TV & Fire TV Fire OS
- `.ios.ts/tsx` - Apple TV (tvOS)
- `.web.ts/tsx` - Web platforms (LG WebOS, Samsung Tizen)
- `.ts/tsx` - Default/shared implementation

## Technology Stack

### Core
- **React Native**: v0.74+ (react-native-tvos fork)
- **Expo SDK**: v51+ (for universal app)
- **TypeScript**: v5.3+ with strict mode
- **Yarn Workspaces**: v4.5.0

### Navigation & Focus
- **React Navigation v6**: Drawer and stack navigators
- **React TV Space Navigation**: Spatial navigation for TV remotes

### Video Playback
- **react-native-video v6+**: Universal app video player
- **W3C Media APIs**: Native video for Vega OS

## Common Commands

```bash
# Development
yarn dev              # Start Metro for expo-multi-tv
yarn dev:android      # Android TV & Fire TV FOS
yarn dev:ios          # Apple TV
yarn dev:web          # Web TV platforms
yarn dev:vega         # Fire TV Vega OS

# Building
yarn build:vega       # Build release VPKG
yarn build:vega:debug # Build debug VPKG

# Testing & Linting
yarn test:all         # Run all tests
yarn lint:all         # Lint all workspaces
yarn typecheck        # Type check
```

## Best Practices

### TV-Specific Design
- Always test on actual TV hardware, not just emulators
- Maintain 10-foot UI standards (large text, high contrast)
- Position critical UI within safe zones (48px horizontal, 27px vertical)
- Provide clear focus indicators (borders, scale, highlights)

### Focus Management
- Set default focus on screen mount
- Restore focus when returning from navigation
- Prevent focus traps (ensure all UI is reachable)
- Test navigation flow with D-pad only

### Performance
- Optimize image sizes for TV resolution (1920x1080)
- Use FlatList for long scrolling lists
- Implement image caching strategy
- Lazy load video content

## Available Skills

| Skill | Purpose |
|-------|---------|
| `multi-tv-builder` | General TV app development guidance |
| `apple-tv-troubleshooter` | tvOS-specific issues: Siri Remote, focus engine, TVEventHandler |
| `tmdb-integration` | TMDB API integration: movies, TV shows, images, search |

Skills activate automatically based on context. For Apple TV issues, ask about "Siri Remote", "TVFocusGuideView", or "tvOS focus problems". For TMDB, ask about "movie API", "poster images", or "trending content".

## Documentation

See the following files for detailed guidance:
- `multi-tv-builder/POWER.md` - Complete implementation guide
- `multi-tv-builder/steering/product.md` - Product overview
- `multi-tv-builder/steering/structure.md` - Project structure details
- `multi-tv-builder/steering/tech.md` - Technology stack reference
