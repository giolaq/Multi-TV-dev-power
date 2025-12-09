---
name: "multi-tv-builder"
displayName: "Multi-TV Builder"
description: "Build production-ready cross-platform TV applications with React Native - supporting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV platforms with a single codebase"
keywords: ["tv", "react-native", "android-tv", "apple-tv", "fire-tv", "vega", "tvos", "web-tv", "video", "streaming", "spatial-navigation", "monorepo"]
author: "Giovanni Laquidara"
---

# Multi-TV Builder Power

Build production-ready cross-platform TV applications with React Native, supporting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV platforms with a single codebase.

## Overview

The Multi-TV Builder power provides comprehensive guidance for building scalable, performant TV applications using React Native. It combines monorepo architecture, shared UI components, platform-specific optimizations, and TV-optimized navigation into a unified development framework.

**Quick Start:** Clone the official [React Native Multi-TV App Sample](https://github.com/AmazonAppDev/react-native-multi-tv-app-sample) repository to get started with a complete, working implementation that includes all features and best practices configured out of the box.

## Core Capabilities

- **Cross-platform TV support** with single codebase for 5+ platforms
- **Monorepo architecture** with Yarn workspaces for apps and shared packages
- **Spatial navigation** optimized for TV remote controls and D-pad input
- **Video playback** with custom controls and remote integration
- **Platform-specific optimizations** using automatic file resolution
- **Shared UI library** with reusable TV-optimized components
- **TypeScript-first** with strict type safety across all packages

## Steering Files

This power includes detailed steering documentation to guide your implementation:

### Available Steering Files

Located in `steering/` directory:

1. **product.md** - Product Overview
   - Comprehensive overview of the Multi-TV app sample
   - Supported platforms and their characteristics
   - Core features (video playback, spatial navigation, content discovery)
   - App variants (expo-multi-tv and vega)
   - Architecture highlights and design philosophy
   - Getting started quick reference

2. **structure.md** - Project Structure
   - Complete monorepo organization guide
   - Detailed directory structure for apps and packages
   - Shared UI package architecture
   - Platform-specific file resolution patterns
   - Configuration files overview
   - Adding new features and components

3. **tech.md** - Technology Stack
   - Build system and package management (Yarn v4)
   - Core technologies (React Native, Expo, TypeScript)
   - Navigation and focus management libraries
   - Video playback implementations
   - Platform-specific SDKs (Vega, tvOS, Android TV)
   - Development tools (ESLint, Prettier, Jest)
   - All development commands reference

### When to Reference Steering Files

- **Starting a new project** → Review `product.md` for overview and `structure.md` for architecture
- **Setting up development environment** → Reference `tech.md` for dependencies and tools
- **Understanding monorepo organization** → Read `structure.md` for detailed structure
- **Adding new components or screens** → Check `structure.md` for patterns and conventions
- **Working with specific technologies** → Consult `tech.md` for library usage and commands
- **Troubleshooting setup issues** → Reference `tech.md` for environment requirements

These steering files complement the official sample repository and provide in-depth explanations of architectural decisions, implementation patterns, and best practices.

## Supported Platforms

| Platform | Technology | Output |
|----------|-----------|--------|
| Android TV | react-native-tvos | APK/AAB |
| Apple TV (tvOS) | react-native-tvos + Expo | IPA |
| Fire TV (Fire OS) | react-native-tvos | APK |
| Fire TV (Vega OS) | Amazon Vega SDK | Optimized VPKG |
| Web TV | React Native Web | HTML/JS Bundle |

## Architecture Principles

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

### Shared UI Library Pattern
- All reusable components live in `@project/shared-ui` package
- Apps import from shared package, never local files
- Platform-specific implementations use file extensions
- Single source of truth for UI components and screens

### Platform-Specific File Resolution
Metro bundler automatically resolves platform files:
- `.kepler.ts/tsx` - Fire TV Vega OS (highest priority for Vega builds)
- `.android.ts/tsx` - Android TV & Fire TV Fire OS
- `.ios.ts/tsx` - Apple TV (tvOS)
- `.web.ts/tsx` - Web platforms (LG WebOS, Samsung Tizen)
- `.ts/tsx` - Default/shared implementation (lowest priority)

Example:
```
RemoteControlManager.android.ts  → Android TV
RemoteControlManager.ios.ts      → Apple TV
RemoteControlManager.kepler.ts   → Fire TV Vega
```

### TV-First Design
- **10-foot UI**: Larger touch targets, high-contrast colors, readable typography
- **Safe zones**: Content positioned within TV safe areas (see theme/safeZones.ts)
- **Focus management**: Clear visual focus indicators for all interactive elements
- **Remote control**: D-pad navigation with platform-specific key mappings

## Product Requirements

### Core Features
1. **Video Playback**
   - Integrated video player with react-native-video (universal) or W3C Media APIs (Vega)
   - Custom overlay controls with play/pause, seek bar, and time display
   - Remote control integration (play/pause, seek ±10s, exit)
   - Auto-hide controls after 5s of inactivity
   - Buffering indicators during loading

2. **Spatial Navigation**
   - React TV Space Navigation for focus management
   - D-pad/arrow key navigation optimized for TV remotes
   - Platform-specific remote control managers
   - Focus restoration when returning from player

3. **Content Discovery**
   - Dynamic content loading from external catalog API
   - Grid layouts optimized for TV screens
   - Detail screens with rich metadata (genre, rating, duration, year)
   - Dynamic hero banner based on focused content
   - Trending flags and content ratings

4. **Navigation**
   - Drawer navigation with left-side menu
   - Stack navigation for detail screens
   - Platform-specific navigation optimizations
   - Deep linking support (optional)

5. **Responsive Design**
   - Adaptive layouts for different TV screen sizes
   - Responsive scaling with useScale hook
   - Platform-specific layout adjustments
   - Safe zone handling for TV overscan

## Technology Stack

### Build System
- **Yarn Workspaces v4+**: Monorepo management
- **Metro Bundler**: Module bundling with platform-specific resolution
- **TypeScript v5.3+**: Strict mode enabled across all packages

### Core Technologies
- **React Native**: v0.74+ (react-native-tvos fork)
- **Expo SDK**: v51+ (for universal app)
- **React**: v18.2+
- **TypeScript**: v5.3+ with strict mode

### Navigation & Focus
- **React Navigation v6**: Drawer and stack navigators
- **React TV Space Navigation**: Spatial navigation for TV remotes
- **Platform Remote Managers**: Android (react-native-keyevent), iOS (TVEventHandler), Vega (Kepler SDK)

### Video Playback
- **react-native-video v6+**: Universal app video player
- **W3C Media APIs**: Native video for Vega OS
- Custom overlay controls with spatial navigation

### Platform-Specific SDKs
- **Amazon Vega SDK**: Fire TV Vega OS builds
- **@amazon-devices packages**: Kepler SDK components
- **react-native-tvos**: TV platform support (Android TV, Apple TV, Fire TV FOS)

### Development Tools
- **ESLint**: Code quality with TypeScript and React plugins
- **Prettier**: Code formatting (120 char width, single quotes)
- **Husky**: Git hooks for pre-commit checks
- **Commitlint**: Conventional commit enforcement
- **Jest**: Unit testing

## Project Structure

### Apps Directory

#### `apps/expo-multi-tv/`
Universal TV application built with Expo SDK.

**Key Files:**
- `App.tsx` - Main entry point
- `app.json` - Expo configuration
- `package.json` - App-specific dependencies
- `tsconfig.json` - TypeScript config extending base
- `metro.config.js` - Metro bundler configuration
- `ios/` - iOS/tvOS native code
- `android/` - Android TV native code

**Platforms Supported:**
- Android TV
- Apple TV (tvOS)
- Fire TV (Fire OS)
- Web (LG WebOS, Samsung Tizen)

#### `apps/vega/`
Fire TV Vega OS optimized application using Amazon Vega SDK.

**Key Files:**
- `App.tsx` - Main entry point
- `package.json` - Vega-specific dependencies
- `tsconfig.json` - TypeScript config
- `metro.config.js` - Metro bundler configuration
- `android/` - Fire TV Vega native code

**Platform Supported:**
- Fire TV (Vega OS)

### Packages Directory

#### `packages/shared-ui/`
Shared component library used by all apps.

**Structure:**
```
packages/shared-ui/src/
├── app/                           # App-level configuration
│   └── remote-control/           # Platform-specific remote managers
│       ├── RemoteControlManager.android.ts
│       ├── RemoteControlManager.ios.ts
│       └── RemoteControlManager.kepler.ts
├── assets/                        # Static assets (fonts, images, icons)
├── components/                    # Reusable UI components
│   ├── player/                   # Video player components
│   ├── CustomDrawerContent.tsx   # Drawer menu
│   ├── FocusablePressable.tsx    # TV-optimized pressable
│   ├── LoadingIndicator.tsx      # Loading spinner
│   ├── MenuContext.tsx           # Menu state management
│   └── PlatformLinearGradient.tsx # Cross-platform gradient
├── data/                          # Data and API
│   └── moviesData.ts             # Movie catalog API
├── hooks/                         # Custom React hooks
│   └── useScale.ts               # Responsive scaling
├── navigation/                    # Navigation configuration
│   ├── AppNavigator.tsx          # Main app navigator
│   ├── DrawerNavigator.tsx       # Drawer navigation
│   ├── RootNavigator.tsx         # Root wrapper
│   └── types.ts                  # Navigation types
├── screens/                       # Screen components
│   ├── DetailsScreen.tsx         # Content detail
│   ├── ExploreScreen.tsx         # Content exploration
│   ├── HomeScreen.tsx            # Home/landing
│   ├── PlayerScreen.tsx          # Video player
│   ├── PlayerScreen.vega.tsx     # Vega-specific player
│   ├── SettingsScreen.tsx        # Settings
│   └── TVScreen.tsx              # TV content
├── theme/                         # Design system
│   ├── colors.ts                 # Color palette
│   ├── spacing.ts                # Spacing scale
│   ├── typography.ts             # Font styles
│   ├── safeZones.ts              # TV safe zones
│   └── index.ts                  # Theme exports
├── utils/                         # Utility functions
└── index.ts                       # Package exports
```

**Export Pattern:**
All components, screens, hooks exported from `src/index.ts`:
```typescript
import { Component, Screen, Hook } from '@project/shared-ui';
```

## Implementation Patterns

### Workspace Configuration

**Root `package.json`:**
```json
{
  "name": "tv-app-project",
  "private": true,
  "workspaces": ["apps/*", "packages/*"],
  "packageManager": "yarn@4.5.0",
  "scripts": {
    "dev": "yarn workspace @project/expo-multi-tv start",
    "dev:android": "yarn workspace @project/expo-multi-tv android",
    "dev:ios": "yarn workspace @project/expo-multi-tv ios",
    "dev:web": "yarn workspace @project/expo-multi-tv web",
    "dev:vega": "yarn workspace @project/vega start",
    "build:vega": "yarn workspace @project/vega run build",
    "test:all": "yarn workspaces foreach -pt run test",
    "lint:all": "yarn workspaces foreach -pt run lint",
    "clean:all": "yarn workspaces foreach run clean"
  }
}
```

### Theme System

Centralized design tokens in `packages/shared-ui/src/theme/`:

**colors.ts:**
```typescript
export const colors = {
  primary: '#E50914',      // Netflix-style red
  background: '#141414',   // Dark background
  card: '#2F2F2F',        // Card background
  text: '#FFFFFF',        // Primary text
  textSecondary: '#B3B3B3' // Secondary text
};
```

**spacing.ts:**
```typescript
export const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48
};
```

**safeZones.ts:**
```typescript
export const safeZones = {
  horizontal: 48,  // Left/right margins for TV overscan
  vertical: 27     // Top/bottom margins
};
```

### Responsive Scaling

**useScale hook:**
```typescript
import { Dimensions } from 'react-native';

const BASE_WIDTH = 1920;
const BASE_HEIGHT = 1080;

export const useScale = () => {
  const { width, height } = Dimensions.get('window');

  const scaleWidth = width / BASE_WIDTH;
  const scaleHeight = height / BASE_HEIGHT;
  const scale = Math.min(scaleWidth, scaleHeight);

  return {
    scale: (size: number) => size * scale,
    width,
    height
  };
};
```

### Remote Control Manager Interface

All platform managers implement this interface:

```typescript
export interface RemoteControlManagerInterface {
  initialize(): void;
  cleanup(): void;
}
```

**Android/Fire TV FOS (`RemoteControlManager.android.ts`):**
```typescript
import ReactNativeKeyEvent from 'react-native-keyevent';

export class RemoteControlManager implements RemoteControlManagerInterface {
  initialize() {
    ReactNativeKeyEvent.onKeyDownListener((keyEvent) => {
      // Handle Android TV remote keys
    });
  }

  cleanup() {
    ReactNativeKeyEvent.removeKeyDownListener();
  }
}
```

**Apple TV (`RemoteControlManager.ios.ts`):**
```typescript
import { TVEventHandler } from 'react-native';

export class RemoteControlManager implements RemoteControlManagerInterface {
  private tvEventHandler: TVEventHandler | null = null;

  initialize() {
    this.tvEventHandler = new TVEventHandler();
    this.tvEventHandler.enable(this, (component, evt) => {
      // Handle tvOS remote events
    });
  }

  cleanup() {
    this.tvEventHandler?.disable();
  }
}
```

**Fire TV Vega (`RemoteControlManager.kepler.ts`):**
```typescript
import { TVEventHandler } from '@amazon-devices/react-native-kepler';

export class RemoteControlManager implements RemoteControlManagerInterface {
  private tvEventHandler: TVEventHandler | null = null;

  initialize() {
    this.tvEventHandler = new TVEventHandler();
    this.tvEventHandler.enable(this, (component, evt) => {
      // Handle Vega remote events
    });
  }

  cleanup() {
    this.tvEventHandler?.disable();
  }
}
```

### Spatial Navigation Setup

**Integration with React TV Space Navigation:**
```typescript
import { SpatialNavigation } from 'react-tv-space-navigation';

// Initialize in App.tsx
SpatialNavigation.init({
  debug: __DEV__,
  visualDebug: __DEV__
});

// Use in components
import { useFocusable } from 'react-tv-space-navigation';

const MyComponent = () => {
  const { ref, focused } = useFocusable();

  return (
    <View ref={ref} style={focused ? styles.focused : styles.default}>
      {/* Content */}
    </View>
  );
};
```

### Video Player Implementation

**Universal App (expo-multi-tv):**
```typescript
import Video from 'react-native-video';

<Video
  source={{ uri: videoUrl }}
  style={styles.video}
  controls={Platform.OS === 'ios'} // Native controls on iOS
  resizeMode="contain"
  onLoad={handleLoad}
  onProgress={handleProgress}
  ref={videoRef}
/>
```

**Vega App:**
```typescript
// Use W3C Media APIs through Kepler SDK
import { VideoView } from '@amazon-devices/react-native-kepler';

<VideoView
  source={{ uri: videoUrl }}
  style={styles.video}
  onLoad={handleLoad}
  ref={videoRef}
/>
```

### Dynamic Content Loading

**Catalog API Integration:**
```typescript
// packages/shared-ui/src/data/moviesData.ts
export interface Movie {
  id: string;
  title: string;
  genre: string;
  rating: number;
  contentRating: string;
  year: number;
  duration: number;
  videoUrl: string;
  imageUrl: string;
  trending: boolean;
}

export const fetchMovies = async (): Promise<Movie[]> => {
  const response = await fetch('https://api.example.com/catalog.json');
  const data = await response.json();
  return transformCatalogData(data);
};
```

## Common Commands

### Development
```bash
# Install all dependencies
yarn install

# Start Metro bundler for expo-multi-tv
yarn dev

# Run on specific platforms
yarn dev:android    # Android TV & Fire TV FOS
yarn dev:ios        # Apple TV
yarn dev:web        # Web TV platforms

# Start Metro bundler for vega
yarn dev:vega
```

### Building

**Expo Multi-TV App:**
```bash
# Build for all platforms
cd apps/expo-multi-tv

# Android TV / Fire TV (Fire OS)
npx expo build:android
# or: npx expo run:android

# Apple TV (tvOS)
npx expo build:ios
# or: npx expo run:ios

# Web TV platforms
npx expo export --platform web
```

**Fire TV Vega App:**
```bash
# From project root
yarn build:vega         # Build release VPKG
yarn build:vega:debug   # Build debug VPKG

# From apps/vega directory
cd apps/vega

# Build with npm (default from Vega CLI)
npm run build:app       # Builds for all architectures

# Build with Kepler CLI (more control)
kepler build --arch armv7 --buildType release    # Fire TV Stick
kepler build --arch aarch64 --buildType release  # M-series Mac virtual devices
kepler build --arch x86_64 --buildType release   # Intel virtual devices

# Clean build
rm -rf build && npm run build:app
```

### Testing & Linting
```bash
# Run tests across all workspaces
yarn test:all

# Lint all workspaces
yarn lint:all

# Type check
yarn typecheck

# Format code
yarn format
```

### Vega CLI Commands

**Project Management:**
```bash
# List available project templates
kepler project list-templates

# Generate new Vega project
kepler project generate \
  --template hello-world \
  --name myapp \
  --packageId com.company.myapp \
  --outputDir myapp

# Verify Vega SDK installation
kepler --version
```

**Building:**
```bash
# Build for specific architecture and build type
kepler build --arch <armv7|aarch64|x86_64> --buildType <debug|release>

# Examples:
kepler build --arch armv7 --buildType release     # Production Fire TV Stick build
kepler build --arch aarch64 --buildType debug     # Debug build for M-series Mac
kepler build --arch x86_64 --buildType debug      # Debug build for Intel virtual device
```

**Device Management:**
```bash
# List connected devices
kepler device list

# Install VPKG on device
kepler install --vpkg <path-to-vpkg-file>

# Run app on device
kepler run --packageId com.company.myapp
```

## Getting Started

### Quick Start (Recommended)

The fastest way to get started is to clone the official React Native Multi-TV App Sample repository, which contains a complete, working implementation with all features configured.

**Clone the Repository:**
```bash
# Clone the official sample repository
git clone https://github.com/AmazonAppDev/react-native-multi-tv-app-sample.git
cd react-native-multi-tv-app-sample

# Install dependencies
yarn install

# Bootstrap the monorepo
yarn bootstrap
```

**What's Included:**
- ✅ **Pre-configured monorepo** with Yarn workspaces
- ✅ **expo-multi-tv app** - Universal app for Android TV, Apple TV, Fire TV (Fire OS), and Web
- ✅ **vega app** - Fire TV Vega OS optimized app
- ✅ **shared-ui package** - Complete component library with:
  - Spatial navigation setup
  - Platform-specific remote control managers
  - Video player with custom controls
  - TV-optimized components (FocusablePressable, LoadingIndicator, etc.)
  - Theme system (colors, spacing, typography, safe zones)
  - Navigation structure (Drawer + Stack)
  - Sample screens (Home, Details, Player, Settings)
- ✅ **All configuration files** - Metro, TypeScript, ESLint, Prettier, Commitlint

**Next Steps After Cloning:**
1. **Review the codebase** - Explore the monorepo structure and existing implementations
2. **Set up for Expo TV** - Follow Expo TV documentation to configure for TV development
3. **Set up for Vega** (optional) - Install Vega SDK if targeting Fire TV Vega OS
4. **Run on platforms:**
   ```bash
   # Universal app (expo-multi-tv)
   export EXPO_TV=1
   npx expo prebuild --clean
   yarn dev:android  # Android TV
   yarn dev:ios      # Apple TV

   # Vega app (requires Vega SDK)
   yarn dev:vega
   yarn build:vega
   ```
5. **Customize for your needs** - Modify branding, content, features
6. **Review steering files** - Learn implementation patterns and best practices

**Repository Structure:**
```
react-native-multi-tv-app-sample/
├── apps/
│   ├── expo-multi-tv/      # Universal TV app
│   └── vega/               # Vega OS app
├── packages/
│   └── shared-ui/          # Shared components
├── package.json            # Workspace config
├── tsconfig.base.json      # Shared TypeScript
└── [configuration files]
```

---

### Alternative: Build from Scratch

If you prefer to build from scratch for learning purposes or need a completely custom setup, follow these step-by-step instructions.

> **Important**: For creating the `apps/expo-multi-tv` universal TV app, follow the official Expo TV documentation which provides complete setup instructions, templates, and platform-specific configuration. See step 4 below for details.

### 1. Initialize Project Structure

```bash
# Create project directory
mkdir my-tv-app && cd my-tv-app

# Initialize package.json
yarn init -y

# Set up workspace configuration
# Edit package.json to add workspaces: ["apps/*", "packages/*"]

# Create directory structure
mkdir -p apps packages/shared-ui
# Note: apps/expo-multi-tv will be created in step 4 using Expo TV templates
```

### 2. Configure Shared TypeScript

Create `tsconfig.base.json`:
```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "CommonJS",
    "lib": ["ES2021"],
    "jsx": "react-native",
    "strict": true,
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

### 3. Set Up Shared UI Package

```bash
cd packages/shared-ui
yarn init -y

# Install dependencies
yarn add react react-native
yarn add -D typescript @types/react @types/react-native

# Create src directory structure
mkdir -p src/{components,screens,navigation,hooks,theme,app/remote-control,data,utils,assets}
```

### 4. Create Universal App (expo-multi-tv)

**IMPORTANT:** Follow the official Expo TV setup guide for creating the universal TV app.

The complete setup instructions are documented in the Expo TV guide, which covers:
- System requirements for Android TV and Apple TV development
- Using the official TV templates (`with-tv` or `with-router-tv`)
- Modifying dependencies to use `react-native-tvos`
- Adding the `@react-native-tvos/config-tv` plugin
- Running prebuild with `EXPO_TV=1` environment variable
- Building for Android TV and Apple TV
- Setting up EAS Build profiles for TV

**Quick Start Options:**

Option 1: Start with the basic TV template:
```bash
cd apps
npx create-expo-app@latest expo-multi-tv -e with-tv
```

Option 2: Start with the TV Router template (recommended):
```bash
cd apps
npx create-expo-app@latest expo-multi-tv -e with-router-tv
```

**After creating the app:**

1. Link shared-ui package by editing `apps/expo-multi-tv/package.json`:
   ```json
   {
     "dependencies": {
       "@project/shared-ui": "workspace:*"
     }
   }
   ```

2. Install additional TV dependencies:
   ```bash
   cd apps/expo-multi-tv
   yarn add react-tv-space-navigation
   yarn add @react-navigation/native @react-navigation/drawer @react-navigation/stack
   yarn add react-native-video react-native-keyevent
   ```

3. Configure Metro bundler for monorepo (see step 7 below)

**For detailed setup instructions, refer to the Expo TV documentation which covers:**
- Prerequisites and system requirements
- Quick start with templates
- Integration with existing projects
- Supported Expo SDK libraries for TV
- Building and running on Android TV and Apple TV emulators/devices
- Creating EAS Build profiles for TV

**Key Points from Expo TV Setup:**
- Use `react-native-tvos` instead of `react-native`
- Install `@react-native-tvos/config-tv` plugin
- Set `EXPO_TV=1` environment variable before running `npx expo prebuild --clean`
- The config plugin automatically modifies Android and iOS files for TV
- Prebuild is required for TV apps (bare workflow)
- Use `npx expo run:android` for Android TV and `npx expo run:ios` for Apple TV

### 5. Create Vega App (Optional - Fire TV Vega OS Only)

**IMPORTANT:** Before creating a Vega app, you must first install and configure the Vega SDK. Follow the complete setup instructions for Vega development.

#### Prerequisites: Install Vega SDK

The Vega SDK installation instructions cover:

**Required Dependencies (macOS):**
- Homebrew package manager
- Node.js (version 16.x or later)
- Development utilities: binutils, coreutils, gawk, findutils, grep, jq, lz4, gnu-sed, watchman
- Rosetta 2 (for Apple Silicon Macs)

**Install development utilities:**
```bash
# For Apple Silicon Macs (includes Rosetta 2)
[[ $(arch) == "arm64" ]] && softwareupdate --install-rosetta --agree-to-license; brew update && brew install binutils coreutils gawk findutils grep jq lz4 gnu-sed watchman

# For Intel Macs
brew update && brew install binutils coreutils gawk findutils grep jq lz4 gnu-sed watchman
```

**Install Vega SDK:**
1. Close VS Code before installing the SDK
2. Download and run the Vega SDK installer
3. Follow on-screen instructions (default install location: `<HOME>/kepler/sdk/`)
4. Add export commands to your shell configuration file:
   ```bash
   # Add to ~/.zshrc or ~/.bashrc
   export KEPLER_SDK_PATH=/Users/<user>/kepler/sdk/<version>
   export PATH=$KEPLER_SDK_PATH/bin:$PATH
   ```
5. Apply the configuration changes:
   ```bash
   source ~/.zshrc  # or source ~/.bashrc for Bash users
   ```
6. Verify installation:
   ```bash
   kepler --version
   ```
7. Restart VS Code after installation

**Important Notes:**
- The Vega SDK collects telemetry by default (see Vega documentation for opt-out instructions)
- If you see 'Electron quit unexpectedly' during installation, restart VS Code afterward

#### Create Vega Project Using Vega CLI

Once the Vega SDK is installed, use the Vega CLI to generate your project.

**Step 1: Choose a Project Template**

List available templates:
```bash
kepler project list-templates
```

**Available Templates:**
- `hello-world` - React Native v0.72 app (recommended starting point)
- `basic-turbo-module` - Custom Turbo Module template
- `idl-turbo-module` - Compatible with all supported React Native versions and native IDL APIs

**Step 2: Generate the Vega Project**

Navigate to your apps directory and create the Vega app:
```bash
cd apps

kepler project generate \
  --template hello-world \
  --name vega \
  --packageId com.yourcompany.yourapp.vega \
  --outputDir vega
```

**Important:**
- Replace `com.yourcompany.yourapp.vega` with your unique package ID
- Use reverse DNS format: `<top-level domain>.<company_name>.<app_name>`
- DO NOT use `com.amazon` as your package ID
- The app name should not contain spaces or special characters

**Step 3: Build the Vega App**

Navigate to the Vega app directory and install dependencies:
```bash
cd vega

# Install dependencies
npm install

# Build the app (creates VPKG files)
npm run build:app
```

**Build Output:**

The build creates VPKG (Vega Package) files for different architectures:
```
apps/vega/
└── build/
    ├── x86_64-release/
    │   └── vega_x86_64.vpkg      # For x86 virtual devices
    ├── armv7-release/
    │   └── vega_armv7.vpkg        # For Fire TV Stick
    └── aarch64-release/
        └── vega_aarch64.vpkg      # For M-series Mac virtual devices
```

**VPKG File Locations:**
- Development build: `build/<architecture>-debug/<name>_<architecture>.vpkg`
- Release build: `build/<architecture>-release/<name>_<architecture>.vpkg`

**Step 4: Integrate with Monorepo**

After creating the Vega app with the Vega CLI, integrate it with your monorepo:

1. **Update package.json** to use Yarn instead of npm:
   ```json
   {
     "scripts": {
       "build:app": "yarn build",
       "start": "yarn metro"
     }
   }
   ```

2. **Link shared-ui package** by editing `apps/vega/package.json`:
   ```json
   {
     "dependencies": {
       "@project/shared-ui": "workspace:*"
     }
   }
   ```

3. **Install workspace dependencies**:
   ```bash
   # From project root
   yarn install
   ```

4. **Configure Metro for monorepo** (see step 7 below)

**Development Workflow:**

Start Metro bundler:
```bash
cd apps/vega
yarn start
# or from root: yarn dev:vega
```

Build for different architectures:
```bash
# Development build
npm run build:app

# Specific architecture
kepler build --arch armv7 --buildType debug
```

**Next Steps for Vega Development:**
- Review Vega documentation for device setup and deployment
- Install Vega Studio extension for VS Code (optional but recommended)
- Test on Vega virtual devices or physical Fire TV devices
- Implement platform-specific features using `@amazon-devices/react-native-kepler`

### 6. Implement Core Features

Order of implementation:
1. Theme system and design tokens
2. Remote control managers (platform-specific)
3. Spatial navigation setup
4. Basic screens (Home, Details)
5. Navigation structure (Drawer + Stack)
6. Video player with controls
7. Dynamic content loading
8. Platform-specific optimizations

### 7. Configure Metro Bundler

**metro.config.js** (in each app):
```javascript
const { getDefaultConfig } = require('expo/metro-config');
const path = require('path');

const config = getDefaultConfig(__dirname);

// Add monorepo support
const projectRoot = __dirname;
const workspaceRoot = path.resolve(projectRoot, '../..');

config.watchFolders = [workspaceRoot];
config.resolver.nodeModulesPaths = [
  path.resolve(projectRoot, 'node_modules'),
  path.resolve(workspaceRoot, 'node_modules'),
];

module.exports = config;
```

## Best Practices

### TV-Specific Design
- Always test on actual TV hardware, not just emulators
- Maintain 10-foot UI standards (large text, high contrast)
- Position critical UI within safe zones to avoid TV overscan
- Provide clear focus indicators (borders, scale, highlights)
- Design for remote control first, touch second

### Focus Management
- Set default focus on screen mount
- Restore focus when returning from navigation
- Prevent focus traps (ensure all UI is reachable)
- Test navigation flow with D-pad only
- Handle edge cases (first/last item in grid)

### Performance
- Optimize image sizes for TV resolution (1920x1080)
- Use FlatList for long scrolling lists
- Implement image caching strategy
- Lazy load video content
- Monitor memory usage on low-end devices

### Platform-Specific Code
- Use file extensions for platform variants
- Keep shared logic in base files
- Document platform differences
- Test each platform independently
- Abstract platform APIs behind common interfaces

### Video Player
- Always show buffering state
- Handle network failures gracefully
- Implement playback controls with remote support
- Test with various video formats and codecs
- Support multiple video qualities/bitrates

### Content Loading
- Implement retry logic for failed requests
- Show loading states during fetch
- Cache catalog data locally
- Handle empty states gracefully
- Validate API response structure

## Testing

### Unit Tests
- Test business logic in shared-ui package
- Mock platform-specific modules
- Test remote control event handlers
- Validate navigation flows
- Test data transformations

### Integration Tests
- Test cross-platform file resolution
- Validate navigation between screens
- Test video player controls
- Verify focus management
- Test catalog API integration

### Platform Testing
- Test on Android TV emulator and device
- Test on Apple TV simulator and device
- Test on Fire TV device
- Test on web browser with keyboard navigation
- Verify remote control functionality per platform

## Troubleshooting

### Metro Bundler Issues
- Clear cache: `yarn start --reset-cache`
- Check for port conflicts (default 8081)
- Verify workspace resolution in metro.config.js

### Platform-Specific Build Failures
- **iOS/tvOS**: Check Node binary path in `.xcode.env.local`
- **Android TV**: Verify Android SDK and emulator setup
- **Vega**: See Vega-specific troubleshooting below

### Vega SDK Issues

**Installation Problems:**
- **'Electron quit unexpectedly' during installation**
  - This is expected if VS Code is running during installation
  - Restart VS Code after installation completes
  - Reload window: `Cmd+Shift+P` → "Reload Window"

- **Vega SDK not found / kepler command not available**
  - Verify Vega SDK is installed: `kepler --version`
  - Check environment variables are set:
    ```bash
    echo $KEPLER_SDK_PATH
    echo $PATH | grep kepler
    ```
  - Add exports to shell config if missing:
    ```bash
    export KEPLER_SDK_PATH=/Users/<user>/kepler/sdk/<version>
    export PATH=$KEPLER_SDK_PATH/bin:$PATH
    ```
  - Reload shell configuration: `source ~/.zshrc` (or `~/.bashrc`)

- **Development utilities missing (macOS)**
  - Install required tools:
    ```bash
    brew update && brew install binutils coreutils gawk findutils grep jq lz4 gnu-sed watchman
    ```
  - For Apple Silicon Macs, install Rosetta 2:
    ```bash
    softwareupdate --install-rosetta --agree-to-license
    ```

**Build Failures:**
- **npm run build:app fails**
  - Ensure all dependencies are installed: `npm install`
  - Check Node.js version (requires 16.x or later): `node --version`
  - Verify package ID format in manifest (reverse DNS, no `com.amazon`)
  - Check Metro bundler is not already running on port 8081

- **VPKG file not generated**
  - Check build output directory: `build/<architecture>-<buildType>/`
  - Verify build command completed without errors
  - Try cleaning build: `rm -rf build && npm run build:app`

**Runtime Issues:**
- **App crashes on Vega device**
  - Check device logs for error messages
  - Verify VPKG architecture matches device (armv7 for Fire TV Stick)
  - Test with debug build first before release build
  - Ensure all native dependencies are compatible with Vega SDK

- **Metro bundler connection issues**
  - Verify Metro is running: `yarn start` in vega app directory
  - Check firewall settings allow Metro connections
  - Ensure device and development machine are on same network

### Navigation Issues
- Verify React Navigation installation
- Check focus restoration logic
- Test with D-pad/arrow keys only
- Enable spatial navigation debug mode

### Video Playback Issues
- Verify video URL accessibility
- Check video format compatibility (MP4 recommended)
- Test with different video codecs
- Monitor network bandwidth

## Additional Resources

### Sample Repositories

**React Native Multi-TV App Sample (Recommended Starting Point)**
- **Repository**: https://github.com/AmazonAppDev/react-native-multi-tv-app-sample
- **Description**: Official reference implementation from Amazon AppDev
- **Includes**:
  - Complete monorepo setup with Yarn workspaces
  - Working expo-multi-tv app (Android TV, Apple TV, Fire TV FOS, Web)
  - Working vega app (Fire TV Vega OS)
  - Shared UI component library with spatial navigation
  - Platform-specific remote control managers
  - Video player implementations (react-native-video + W3C Media APIs)
  - TV-optimized design system (theme, safe zones, typography)
  - Sample screens and navigation structure
  - All configuration files (Metro, TypeScript, ESLint, etc.)
- **Use Cases**:
  - Quick start for new TV app projects
  - Reference implementation for best practices
  - Learning resource for TV development patterns
  - Base for customization and extension

### Official Documentation

**Expo TV Documentation**
Complete guide for building Expo apps for TV platforms:
- System requirements and prerequisites
- Quick start with TV templates (`with-tv`, `with-router-tv`)
- Integration with existing Expo projects
- Supported Expo SDK libraries for TV
- EAS Build profiles for TV
- Platform-specific build instructions

**Vega SDK Documentation**
Complete guide for Fire TV Vega OS development:
- **Installation Guide**: Installing Vega SDK, required dependencies, environment setup
  - macOS and Ubuntu dependencies (Homebrew, Node.js 16.x+, development utilities)
  - Vega SDK installation process and PATH configuration
  - VS Code and Vega Studio extension setup
  - Telemetry information and opt-out instructions
- **Vega CLI Guide**: Building apps using the command-line interface
  - Project templates: `hello-world`, `basic-turbo-module`, `idl-turbo-module`
  - `kepler project generate` command usage
  - Building and packaging apps (VPKG files)
  - Architecture-specific builds (x86_64, armv7, aarch64)
  - Metro bundler integration
- **Vega Studio**: IDE extension for visual development (optional)
- **Device Setup**: Configuring Fire TV devices for development and testing
- **Deployment**: Publishing apps to Amazon Appstore

### Steering Documentation

This power includes three comprehensive steering files in the `steering/` directory:

**steering/product.md** - Product Overview
- Complete overview of the Multi-TV app sample and its capabilities
- Detailed breakdown of supported platforms (Android TV, Apple TV, Fire TV, Web TV)
- Core features: video playback, spatial navigation, content discovery, drawer navigation
- App variants: expo-multi-tv (universal) and vega (Fire TV Vega OS)
- Architecture highlights: monorepo structure, shared components, theme system
- Design philosophy: TV-first approach, code reusability, developer experience

**steering/structure.md** - Project Structure
- Monorepo organization with Yarn workspaces
- Complete directory structure for apps/ and packages/
- Shared UI package architecture and component organization
- Platform-specific file resolution patterns (`.android`, `.ios`, `.kepler`, `.vega`, `.web`)
- Configuration files (TypeScript, ESLint, Prettier, Metro)
- Patterns for adding new features, components, and screens
- Import/export best practices

**steering/tech.md** - Technology Stack
- Build system: Yarn Workspaces v4, Metro bundler
- Core technologies: React Native 0.74+, Expo SDK 51+, TypeScript 5.3+
- Navigation: React Navigation v6, React TV Space Navigation
- Video playback: react-native-video, W3C Media APIs (Vega)
- Platform-specific SDKs: Vega SDK, tvOS APIs, Android TV APIs
- Development tools: ESLint, Prettier, Jest, Husky, Commitlint
- Complete command reference for development, building, testing, and Vega CLI

**Usage:** Reference these files alongside the official sample repository for comprehensive guidance on architecture, patterns, and implementation details.

### External Resources
- [React Native TV (react-native-tvos)](https://github.com/react-native-tvos/react-native-tvos) - Core TV platform support
- [React TV Space Navigation](https://github.com/bamlab/react-tv-space-navigation) - Spatial navigation library
- [Expo Examples - TV](https://github.com/expo/examples/tree/master/with-tv) - Official TV template
- [Expo Examples - TV Router](https://github.com/expo/examples/tree/master/with-router-tv) - TV template with Expo Router
- [React Navigation](https://reactnavigation.org/) - Navigation library
- [React Native Directory](https://reactnative.directory/?tvos=true) - TV-compatible third-party libraries

## Next Steps

### Recommended Path (Quick Start)

**Start with the Official Sample:**
1. **Clone the repository** - `git clone https://github.com/AmazonAppDev/react-native-multi-tv-app-sample.git`
2. **Install dependencies** - `yarn install && yarn bootstrap`
3. **Review the codebase** - Explore monorepo structure, shared-ui components, and sample implementations
4. **Review documentation** - Read Expo TV and Vega SDK documentation for platform-specific setup
5. **Review steering files** - Learn implementation patterns and best practices

**Set Up Development Environment:**
6. **Configure for Expo TV** - Install `@react-native-tvos/config-tv` plugin, run `EXPO_TV=1 npx expo prebuild --clean`
7. **Install Vega SDK** (optional) - Follow Vega SDK installation guide if targeting Fire TV Vega OS
8. **Test on platforms** - Run on Android TV, Apple TV, Fire TV emulators/devices

**Customize for Your Project:**
9. **Modify branding** - Update app name, icons, splash screens, color scheme
10. **Customize content** - Replace sample movie data with your content API
11. **Add features** - Implement additional screens, authentication, user profiles, etc.
12. **Platform optimizations** - Enhance platform-specific implementations as needed

**Deploy:**
13. **Test thoroughly** - Verify on all target platforms with real devices
14. **Build for production** - Create release builds (APK/AAB, IPA, VPKG)
15. **Submit to stores** - Publish to Google Play, App Store, Amazon Appstore

---

### Alternative Path (Build from Scratch)

For learning purposes or highly custom requirements, follow the step-by-step "Build from Scratch" guide in the Getting Started section above. This approach helps you understand:
- Monorepo setup with Yarn workspaces
- Expo TV configuration from scratch
- Vega SDK integration and project generation
- Shared component library architecture
- Platform-specific file resolution
- Theme system implementation
- Spatial navigation setup
- Remote control integration

After building from scratch, still reference the official sample repository for implementation patterns and best practices.
