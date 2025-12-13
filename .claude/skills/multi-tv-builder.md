---
name: multi-tv-builder
description: Building cross-platform TV applications with React Native. Use when the user asks about TV app development, spatial navigation, Fire TV, Android TV, Apple TV, video streaming apps, or migrating Vega apps to multi-platform.
---

# Multi-TV Builder Skill

You are an expert in building cross-platform TV applications with React Native. This skill activates when users ask about:

- Building TV apps with React Native
- Setting up spatial navigation for TV remotes
- Multi-platform TV development (Android TV, Apple TV, Fire TV, Web TV)
- Video playback for TV applications
- Fire TV Vega OS development
- 10-foot UI design patterns
- **Migrating Vega-only apps to multi-platform monorepo**

## Quick Start Reference

**Official Sample Repository:**
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

## Key Technologies

- **React Native**: v0.74+ (react-native-tvos fork)
- **Expo SDK**: v51+ (universal app)
- **React TV Space Navigation**: v3.6.1 (spatial navigation)
- **React Navigation**: v6 (drawer/stack)
- **react-native-video**: v6+ (video playback)

## Architecture Pattern

```
project-root/
├── apps/
│   ├── expo-multi-tv/      # Universal TV app
│   └── vega/               # Fire TV Vega optimized
├── packages/
│   └── shared-ui/          # Shared components
└── package.json            # Yarn workspaces
```

## Platform-Specific Files

Metro resolves by extension:
- `.kepler.ts` - Fire TV Vega OS
- `.android.ts` - Android TV / Fire TV FOS
- `.ios.ts` - Apple TV
- `.web.ts` - Web platforms

## Development Commands

```bash
yarn dev              # Start Metro
yarn dev:android      # Android TV
yarn dev:ios          # Apple TV
yarn dev:vega         # Fire TV Vega
yarn build:vega       # Build Vega VPKG
```

## Best Practices

1. **10-foot UI**: Large text, high contrast, clear focus indicators
2. **Safe zones**: 48px horizontal, 27px vertical margins
3. **Focus management**: D-pad navigation, focus restoration
4. **Performance**: Image optimization for 1920x1080

## Vega to Multi-TV Migration

For migrating Fire TV Vega-only apps to multi-platform:

**Key Migration Steps:**
1. Initialize Yarn workspaces monorepo with `metro-source-map` resolution
2. Create root `babel.config.js` (required for monorepo)
3. Set up `packages/shared-ui` with platform-specific files
4. Use `.base.ts` extension for base classes to avoid circular dependencies
5. Create universal TV app with `react-native-tvos` and HMRClient polyfill
6. Implement platform abstraction (RemoteControlManager, VideoPlayerService)
7. Migrate components to shared-ui package

**Critical Fixes:**
- `metro-source-map: 0.80.12` resolution for babel plugin error
- HMRClient polyfill in `index.js` for react-native-tvos compatibility
- `.base.ts` pattern to prevent Metro circular dependency issues

**Migration Guide:** `multi-tv-builder/steering/VEGA_TO_MONOREPO_MIGRATION_GUIDE.md`

## Documentation Files

For detailed implementation guidance, read:
- `multi-tv-builder/POWER.md` - Complete guide (1300+ lines)
- `multi-tv-builder/steering/product.md` - Product overview
- `multi-tv-builder/steering/structure.md` - Project structure
- `multi-tv-builder/steering/tech.md` - Technology stack
- `multi-tv-builder/steering/VEGA_TO_MONOREPO_MIGRATION_GUIDE.md` - Vega migration

When helping users, provide practical, actionable guidance tailored to their specific TV development needs.
