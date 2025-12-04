---
name: "react-native-multi-tv"
displayName: "React Native Multi-TV App"
description: "Build production-ready cross-platform TV applications with React Native for Android TV, Apple TV, Fire TV, and Web TV platforms"
keywords: ["react-native", "tv", "android-tv", "apple-tv", "tvos", "fire-tv", "vega", "webos", "tizen", "streaming", "video", "spatial-navigation", "monorepo", "expo"]
author: "Giovanni Laquidara"
---

# React Native Multi-TV App Power

Build production-ready cross-platform TV applications with a single React Native codebase targeting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV platforms.

## Overview

This power provides comprehensive guidance for developing TV applications using React Native with support for multiple TV platforms. It includes patterns for spatial navigation, video playback, platform-specific optimizations, and monorepo architecture.

**Key capabilities:**
- **Multi-Platform Support**: Single codebase for Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV
- **TV-Optimized Navigation**: Spatial navigation with D-pad/remote control support
- **Video Playback**: Integrated video player with custom controls
- **Platform-Specific Builds**: Automatic platform detection and optimized rendering
- **Monorepo Architecture**: Shared component library with Yarn Workspaces
- **Responsive Layouts**: 10-foot UI optimized for TV screens

**Perfect for:**
- Building streaming video applications
- Creating content discovery and browsing experiences
- Developing TV-optimized user interfaces
- Cross-platform TV development
- Multi-tenant TV applications

## Onboarding

### Step 1: Validate Environment

Before starting with React Native Multi-TV development, ensure the following are installed:

**Required Tools:**
- **Node.js**: v18+ recommended
  - Verify with: `node --version`
- **Yarn**: v4.5.0+ (using Corepack)
  - Verify with: `yarn --version`
- **Git**: For version control
  - Verify with: `git --version`

**Platform-Specific Requirements:**

For **Android TV & Fire TV (Fire OS)**:
- **Android Studio**: Latest stable version
- **Android SDK**: API Level 28+ for TV
- **Android TV Emulator** or physical device
- Verify Java installation: `java --version`

For **Apple TV (tvOS)**:
- **Xcode**: Latest stable version (macOS only)
- **tvOS Simulator** or physical Apple TV device
- Command Line Tools installed

For **Fire TV (Vega OS)**:
- **Amazon Vega SDK**: Kepler SDK components
- **Android Studio**: For building Vega builds
- Fire TV device with Vega OS

For **Web TV (WebOS/Tizen)**:
- Modern web browser for testing
- Platform-specific SDKs for production deployment

### Step 2: Project Setup

Initialize or navigate to your Multi-TV project:

```bash
# Clone the repository (if starting from template)
git clone <your-repo-url>
cd react-native-multi-tv-app-sample

# Install all dependencies
yarn install

# Bootstrap the entire monorepo
yarn bootstrap
```

### Step 3: Verify Installation

Run these commands to ensure everything is set up correctly:

```bash
# Type check all workspaces
yarn typecheck

# Lint all workspaces
yarn lint:all

# Run tests
yarn test:all
```

### Step 4: Development Workflow

**For Universal App (expo-multi-tv):**
```bash
# Start Metro bundler
yarn dev

# Run on specific platforms
yarn dev:android    # Android TV & Fire TV (Fire OS)
yarn dev:ios        # Apple TV
yarn dev:web        # Web TV platforms
```

**For Vega App (Fire TV Vega OS):**
```bash
# Start Metro bundler
yarn dev:vega

# Build for Fire TV Vega
yarn build:vega
yarn build:vega:debug
```

## When to Load Steering Files

This power uses multiple steering files for different aspects of TV development:

- **Understanding the product and features** → `steering/product-overview.md`
- **Understanding project structure and architecture** → `steering/project-structure.md`
- **Working with technology stack, dependencies, or build commands** → `steering/tech-stack.md`
- **Implementing spatial navigation or remote control handling** → `steering/spatial-navigation.md`
- **Adding video playback features** → `steering/video-playback.md`
- **Creating platform-specific implementations** → `steering/platform-specific.md`
- **Setting up shared UI components** → `steering/shared-components.md`
- **Working with TV-optimized layouts and design** → `steering/tv-design-patterns.md`

## Core Architecture Principles

### Monorepo Organization
- **Workspace Structure**: `apps/` for applications, `packages/` for shared code
- **Shared UI Library**: `@multi-tv/shared-ui` package for reusable components
- **Platform-Specific Resolution**: Metro bundler auto-resolves `.android`, `.ios`, `.kepler`, `.vega`, `.web` extensions
- **Centralized Configuration**: Root-level TypeScript, ESLint, and Prettier configs

### Platform Support Strategy
- **Universal App (expo-multi-tv)**: Targets Android TV, Apple TV, Fire TV (Fire OS), and Web
- **Vega App**: Optimized specifically for Fire TV Vega OS with Amazon SDK
- **Platform Detection**: Automatic platform identification and optimized rendering
- **Shared Components**: Maximum code reuse through platform-agnostic components

### TV-Specific Considerations
- **10-Foot UI**: Larger fonts, spacing, and touch targets for viewing from distance
- **Safe Zones**: Content kept within TV-safe areas (defined in `theme/safeZones.ts`)
- **Spatial Navigation**: D-pad/remote-friendly navigation using React TV Space Navigation
- **Focus Management**: Visual feedback for focused elements
- **Remote Control Support**: Platform-specific remote control managers

## Best Practices

### Component Development
- **Shared Components First**: Build components in `packages/shared-ui/src/components/`
- **Platform Files**: Create platform-specific implementations only when necessary (`.android.tsx`, `.ios.tsx`, `.kepler.tsx`)
- **FocusablePressable**: Use for all interactive TV elements instead of TouchableOpacity
- **Export Pattern**: Export all components from `packages/shared-ui/src/index.ts`

### Styling & Layout
- **useScale Hook**: Use for responsive scaling based on screen size
- **Theme System**: Use centralized theme tokens from `theme/` directory
- **Grid Layouts**: Optimize for TV screens with proper spacing
- **Avoid Small Touch Targets**: Minimum 80x80 logical pixels for focusable elements

### Navigation
- **React Navigation**: Use for screen transitions and drawer menus
- **Spatial Navigation**: Configure focus order and navigation behavior
- **Hardware Back Button**: Handle properly on Android TV and Fire TV
- **Remote Control Events**: Use platform-specific remote control managers

### Performance
- **Lazy Loading**: Defer loading of screens and heavy components
- **Image Optimization**: Use appropriate image sizes for TV resolutions
- **Video Optimization**: Implement proper buffering and error handling
- **Memory Management**: Monitor memory usage on resource-constrained TV devices

### Platform-Specific Code
- **File Extensions**: Use `.android`, `.ios`, `.kepler`, `.vega`, `.web` for platform-specific code
- **Minimal Duplication**: Share as much code as possible, specialize only when needed
- **Feature Detection**: Use platform checks sparingly, prefer feature detection
- **Testing**: Test on actual TV devices, not just simulators/emulators

## Development Commands

### Root Level (Monorepo)
```bash
# Install dependencies
yarn install

# Bootstrap all workspaces
yarn bootstrap

# Clean all node_modules
yarn clean:all

# Build all workspaces
yarn build:all

# Test all workspaces
yarn test:all

# Lint all workspaces
yarn lint:all

# Type check all workspaces
yarn typecheck

# Format code
yarn format
```

### App-Specific (expo-multi-tv)
```bash
cd apps/expo-multi-tv

# Development
yarn start           # Start Metro bundler
yarn android         # Run on Android TV
yarn ios             # Run on Apple TV
yarn web             # Run on Web

# Quality
yarn test            # Run tests
yarn lint            # Lint code
yarn typecheck       # Type check
```

### App-Specific (vega)
```bash
cd apps/vega

# Development
yarn start           # Start Metro bundler
yarn build           # Build for Fire TV Vega
yarn build:debug     # Debug build

# Quality
yarn test            # Run tests
yarn lint            # Lint code
yarn typecheck       # Type check
```

## Common Patterns

### Creating a New Screen
1. Create screen in `packages/shared-ui/src/screens/ScreenName.tsx`
2. Use shared components and hooks
3. Configure spatial navigation
4. Add to navigation in `navigation/AppNavigator.tsx`
5. Export from `packages/shared-ui/src/index.ts`
6. Import in app: `import { ScreenName } from '@multi-tv/shared-ui'`

### Creating a Shared Component
1. Create in `packages/shared-ui/src/components/ComponentName.tsx`
2. Use `FocusablePressable` for interactive elements
3. Apply theme tokens for styling
4. Export from `packages/shared-ui/src/index.ts`
5. Import in screens: `import { ComponentName } from '@multi-tv/shared-ui'`

### Platform-Specific Implementation
1. Create base file: `Feature.ts` (default implementation)
2. Create platform files as needed:
   - `Feature.android.ts` (Android TV & Fire TV Fire OS)
   - `Feature.ios.ts` (Apple TV)
   - `Feature.kepler.ts` (Fire TV Vega OS)
   - `Feature.web.ts` (Web TV)
3. Metro automatically resolves the correct file at build time
4. Import normally: `import { Feature } from './Feature'`

### Handling Remote Control Events
1. Use platform-specific RemoteControlManager
2. Configure in app initialization
3. Handle playback controls (play, pause, stop, rewind, fast-forward)
4. Integrate with React TV Space Navigation
5. Test on physical devices with actual remotes

## Troubleshooting

### Metro Bundler Issues
**Symptom**: Build fails or hot reload not working
**Solution**:
```bash
# Clear Metro cache
yarn start --reset-cache

# Clean and reinstall
yarn clean:all
yarn install
```

### Platform-Specific File Not Loading
**Symptom**: Wrong platform file being used
**Solution**:
- Check file extension order: `.kepler` > `.android` > `.ios` > `.vega` > `.web` > base
- Verify file is in the same directory as base file
- Clear Metro cache and rebuild

### Spatial Navigation Not Working
**Symptom**: Focus not moving between elements
**Solution**:
- Ensure using `FocusablePressable` components
- Check spatial navigation configuration in screen
- Verify remote control manager is initialized
- Test on actual TV device, not simulator

### Video Playback Issues
**Symptom**: Video not playing or controls not working
**Solution**:
- Check video URL is accessible
- Verify platform-specific video player implementation
- Test video format compatibility (MP4 recommended)
- Check remote control integration for playback controls

### Build Errors on Specific Platform
**Symptom**: Build succeeds on some platforms but fails on others
**Solution**:
- Check platform-specific dependencies are installed
- Verify SDK versions (Android SDK, Xcode, Vega SDK)
- Review platform-specific native code
- Check `metro.config.js` platform resolution

### Yarn Workspace Issues
**Symptom**: Module not found errors
**Solution**:
```bash
# Reinstall dependencies
yarn install

# Bootstrap workspaces
yarn bootstrap

# Check workspace configuration in root package.json
```

## Tips for TV Development

1. **Test on Real Devices**: Simulators/emulators don't fully replicate TV hardware
2. **Remote Control First**: Design navigation for D-pad/remote, not touch
3. **Visible Focus States**: Always show clear visual feedback for focused elements
4. **Large Touch Targets**: Minimum 80x80 logical pixels for focusable elements
5. **Safe Zones**: Keep critical content within TV-safe areas
6. **Performance Matters**: TV devices often have limited resources
7. **Video Formats**: Stick to widely supported formats (MP4, HLS)
8. **Loading States**: Always show feedback during content loading
9. **Error Handling**: Gracefully handle network and playback errors
10. **Platform Testing**: Test on each target platform before release

## Resources

- **React Native TV**: https://github.com/react-native-tvos/react-native-tvos
- **React TV Space Navigation**: https://github.com/bamlab/react-tv-space-navigation
- **Expo Documentation**: https://docs.expo.dev/
- **Amazon Vega SDK**: https://developer.amazon.com/
- **Apple TV Development**: https://developer.apple.com/tvos/
- **Android TV Guidelines**: https://developer.android.com/tv

---

**Stack**: React Native, Expo, TypeScript, Yarn Workspaces
**Platforms**: Android TV, Apple TV, Fire TV (Fire OS & Vega OS), Web TV
**License**: As per your project
**Monorepo**: Yarn v4 with shared component library
