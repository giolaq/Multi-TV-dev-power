# Product Overview: React Native Multi-TV App

## What is This?

A production-ready TV application template for building cross-platform TV apps with a single React Native codebase. This template demonstrates best practices for TV development including spatial navigation, video playback, and platform-specific optimizations.

## Supported Platforms

### Android TV
- Google's TV platform
- Runs on Android TV devices and set-top boxes
- Uses Android SDK with TV-specific APIs
- D-pad navigation and remote control support

### Apple TV (tvOS)
- Apple's TV operating system
- Runs on Apple TV hardware (4th gen and later)
- Uses tvOS SDK with Focus Engine
- Siri Remote and standard remote support

### Fire TV (Fire OS)
- Amazon's Fire TV platform based on Android
- Runs on Fire TV Stick, Fire TV Cube, and Fire TV Edition TVs
- Uses Android SDK with Fire TV optimizations
- Alexa Voice Remote support

### Fire TV (Vega OS)
- Amazon's optimized Fire TV platform
- Uses Amazon Vega SDK (Kepler components)
- Enhanced performance for Fire TV devices
- Separate optimized build in `apps/vega/`

### Web TV
- Browser-based TV platforms
- **LG WebOS**: LG Smart TVs
- **Samsung Tizen**: Samsung Smart TVs
- Web standards with TV-specific adaptations
- Remote control via Web APIs

## Core Features

### 1. Video Playback
- **Integrated Video Player**: Custom video player with overlay controls
- **Remote Control Support**: Play, pause, stop, rewind, fast-forward via remote
- **Platform-Specific Players**:
  - `react-native-video` for Expo multi-tv app
  - W3C Media APIs for Vega OS
- **Custom Overlay Controls**: TV-optimized playback controls
- **Fullscreen Support**: Seamless transition to fullscreen mode
- **Error Handling**: Graceful handling of playback errors and buffering

### 2. Spatial Navigation
- **D-Pad Navigation**: Full support for TV remote D-pad controls
- **Focus Management**: Visual feedback for focused elements
- **React TV Space Navigation**: v3.6.1 integration for spatial navigation
- **Focus Trapping**: Proper focus containment in modals and overlays
- **Tab Order Configuration**: Customizable navigation flow
- **Hardware Key Support**: Back button, menu button, play/pause controls

### 3. Dynamic Content
- **External Catalog API**: Load movie metadata from external sources
- **Content Grid Layouts**: TV-optimized grid for browsing content
- **Lazy Loading**: Efficient loading of content as user scrolls
- **Content Details**: Rich detail screens with metadata display
- **Poster Images**: High-quality artwork for content items
- **Categories**: Organized content by genre, trending, etc.

### 4. Drawer Navigation
- **Left-Side Menu**: Slide-out navigation drawer
- **Custom Drawer Content**: Customizable menu items and branding
- **Focus-Aware**: Proper focus management when drawer opens/closes
- **Navigation Integration**: React Navigation drawer integration
- **Menu Items**: Home, Explore, TV, Settings, and customizable items
- **Theme Support**: Consistent styling with app theme

### 5. Responsive Layouts
- **10-Foot UI Design**: Optimized for viewing from 10 feet away
- **Responsive Scaling**: `useScale` hook for device-specific scaling
- **Grid Layouts**: Adaptive grids that respond to screen size
- **Safe Zones**: Content kept within TV-safe areas (defined in theme)
- **Large Touch Targets**: Minimum 80x80 logical pixels for focusable elements
- **Typography Scale**: Large, readable fonts for TV screens

### 6. Platform-Specific Optimizations
- **Automatic Platform Detection**: Detect and optimize for each platform
- **Platform File Resolution**: Metro bundler resolves `.android`, `.ios`, `.kepler`, `.vega`, `.web` files
- **Optimized Rendering**: Platform-specific rendering optimizations
- **Native Features**: Access to platform-specific TV APIs
- **Performance Tuning**: Platform-specific performance optimizations
- **Remote Control Abstraction**: Unified interface for different remote types

## App Variants

### 1. expo-multi-tv (Universal App)
Located in `apps/expo-multi-tv/`

**Platforms Supported:**
- Android TV
- Apple TV (tvOS)
- Fire TV (Fire OS)
- Web (WebOS, Tizen)

**Technology Stack:**
- Expo SDK v51
- React Native v0.74.2 (tvOS fork)
- React Navigation v6
- React TV Space Navigation v3.6.1
- react-native-video v6.x

**Use Cases:**
- Maximum platform coverage with single codebase
- Leverage Expo ecosystem and tools
- Faster development with Expo APIs
- Over-the-air updates capability

### 2. vega (Fire TV Vega OS Optimized)
Located in `apps/vega/`

**Platform Supported:**
- Fire TV (Vega OS only)

**Technology Stack:**
- Amazon Vega SDK
- @amazon-devices Kepler components
- React Native v0.74.2 (tvOS fork)
- W3C Media APIs for video
- Custom Vega optimizations

**Use Cases:**
- Maximum performance on Fire TV devices
- Access to Fire TV exclusive features
- Optimized for Amazon ecosystem
- Alexa integration capabilities

## Key Use Cases

### Streaming Video Applications
- Build Netflix-like streaming apps
- VOD (Video on Demand) platforms
- Live streaming applications
- Sports streaming services
- Educational video platforms

### Content Discovery and Browsing
- Media library browsers
- Content aggregation apps
- Channel browsers
- Movie and TV show discovery
- Podcast and audio content apps

### TV-Optimized User Interfaces
- Learn TV-specific UI patterns
- Implement spatial navigation
- Build 10-foot UI designs
- Handle remote control inputs
- Create focus-aware interfaces

### Cross-Platform TV Development Reference
- Reference implementation for TV apps
- Best practices demonstration
- Platform-specific patterns
- Monorepo organization examples
- Shared component architecture

## Architecture Highlights

### Monorepo Structure
- **Root**: Workspace configuration and shared tooling
- **apps/expo-multi-tv**: Universal TV app
- **apps/vega**: Fire TV Vega OS app
- **packages/shared-ui**: Shared component library

### Shared Component Library
- Single source of truth for UI components
- Platform-agnostic components with platform-specific variants
- Reusable across both apps
- Centralized theme and design tokens
- Export/import pattern: `import { Component } from '@multi-tv/shared-ui'`

### Platform-Specific Resolution
Metro bundler automatically resolves platform files:
1. `.kepler.tsx` → Fire TV Vega OS
2. `.android.tsx` → Android TV & Fire TV (Fire OS)
3. `.ios.tsx` → Apple TV (tvOS)
4. `.vega.tsx` → Vega-specific screens
5. `.web.tsx` → Web platforms
6. `.tsx` → Default/shared implementation

### Theme System
- Centralized design tokens in `packages/shared-ui/src/theme/`
- `colors.ts`: Color palette
- `spacing.ts`: Spacing scale
- `typography.ts`: Font styles and sizes
- `safeZones.ts`: TV-safe area definitions
- Responsive scaling with `useScale` hook

## Getting Started Quick Reference

### Initial Setup
```bash
# Install dependencies
yarn install

# Bootstrap monorepo
yarn bootstrap

# Verify setup
yarn typecheck
yarn lint:all
```

### Development
```bash
# Universal app
yarn dev              # Start Metro bundler
yarn dev:android      # Run on Android TV
yarn dev:ios          # Run on Apple TV
yarn dev:web          # Run on Web

# Vega app
yarn dev:vega         # Start Metro bundler
yarn build:vega       # Build for Fire TV Vega
```

### Production Builds
```bash
# Build all workspaces
yarn build:all

# Build Vega app
yarn build:vega
```

## Design Philosophy

### TV-First Approach
- Designed for remote control, not touch
- Large, clear visual elements
- Spatial navigation as primary input method
- Distance viewing optimization (10-foot UI)

### Code Reusability
- Maximum sharing through `@multi-tv/shared-ui`
- Platform-specific code only when necessary
- Shared business logic and state management
- Consistent UX across platforms

### Developer Experience
- Fast development with hot reload
- TypeScript for type safety
- ESLint and Prettier for code quality
- Conventional commits for clean history
- Monorepo for easy management

### Performance
- Optimized for resource-constrained TV devices
- Lazy loading of screens and components
- Efficient video playback and buffering
- Memory management best practices
- Platform-specific optimizations

## What This Template Is NOT

This template is a **reference implementation and starting point**, not:
- ❌ A production streaming service (implement your own backend)
- ❌ A DRM-protected video player (add your own DRM solution)
- ❌ A complete CMS (integrate your content management system)
- ❌ A recommendation engine (implement your own algorithms)
- ❌ A user authentication system (add your auth provider)

Instead, it provides:
- ✅ Architecture and patterns for TV apps
- ✅ Spatial navigation implementation
- ✅ Video playback foundation
- ✅ Platform-specific examples
- ✅ Monorepo organization
- ✅ Best practices and conventions

## Next Steps

After understanding the product overview:
- Review **project-structure.md** to understand code organization
- Review **tech-stack.md** to understand technologies and commands
- Review **spatial-navigation.md** for implementing TV navigation
- Review **video-playback.md** for adding video features
- Review **platform-specific.md** for platform customizations
