---
inclusion: manual
description: "Step-by-step guide for migrating Fire TV Vega-only apps to a multi-platform TV monorepo supporting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV"
---

# Fire TV Vega to Multi-TV Monorepo Migration Guide

This document provides step-by-step instructions for converting a Fire TV Vega-only app into a multi-platform TV application supporting Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV using Expo and React Native.

## Prerequisites

- Node.js 18+
- Yarn 4.x (Berry)
- Java 17 (for Android builds)
- Android SDK with TV emulator
- Xcode (for Apple TV builds)

## Placeholders Used

Throughout this guide, replace these placeholders with your actual values:

| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{APP_NAME}` | Your app name (PascalCase) | `MyTVApp` |
| `{PACKAGE_NAME}` | npm package scope | `@mycompany` |
| `{ANDROID_PACKAGE}` | Android package identifier | `com.mycompany.tvapp` |
| `{IOS_BUNDLE_ID}` | iOS bundle identifier | `com.mycompany.tvapp` |

---

## Phase 1: Monorepo Setup

### 1.1 Initialize Yarn Workspaces

Create root `package.json`:

```json
{
  "name": "{PACKAGE_NAME}/multi-tv",
  "version": "1.0.0",
  "private": true,
  "packageManager": "yarn@4.5.0",
  "workspaces": [
    "apps/*",
    "packages/*"
  ],
  "resolutions": {
    "metro-source-map": "0.80.12"
  }
}
```

**CRITICAL**: The `metro-source-map` resolution is required to fix a babel plugin error. Without it, you'll get:
```
.plugins[0] must be a string, object, function
```

This happens because older versions of `metro-source-map` don't export `functionMapBabelPlugin`, which Metro's transform worker passes to babel as `plugins: [undefined]`.

### 1.2 Create Directory Structure

```
project-root/
├── apps/
│   ├── universal-tv/      # Universal TV app (Android TV, Apple TV, Fire TV FOS)
│   └── vega/              # Fire TV Vega-specific app (original app)
├── packages/
│   └── shared-ui/         # Shared components, hooks, services
├── package.json
├── babel.config.js        # Root babel config (required)
├── tsconfig.base.json
└── yarn.lock
```

### 1.3 Create Root Babel Config

**CRITICAL**: Create `babel.config.js` at monorepo root. Metro looks for babel config starting from the project root, and in a monorepo it may find the wrong one or none at all.

```javascript
const path = require('path');

module.exports = function (api) {
  api.cache(true);
  
  // Try to resolve reanimated plugin from the universal TV app
  let reanimatedPlugin;
  try {
    reanimatedPlugin = require.resolve('react-native-reanimated/plugin', {
      paths: [path.join(__dirname, 'apps/universal-tv/node_modules')]
    });
  } catch (e) {
    try {
      reanimatedPlugin = require.resolve('react-native-reanimated/plugin');
    } catch (e2) {
      reanimatedPlugin = null;
    }
  }
  
  const plugins = reanimatedPlugin ? [reanimatedPlugin] : [];
  
  return {
    presets: ['babel-preset-expo'],
    plugins,
  };
};
```

### 1.4 Create Base TypeScript Config

Create `tsconfig.base.json`:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "lib": ["ES2020"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  }
}
```

---

## Phase 2: Shared UI Package Setup

### 2.1 Create shared-ui Package

Create `packages/shared-ui/package.json`:

```json
{
  "name": "{PACKAGE_NAME}/shared-ui",
  "version": "0.1.0",
  "private": true,
  "main": "src/index.ts",
  "types": "src/index.ts",
  "peerDependencies": {
    "react": ">=18.0.0",
    "react-native": ">=0.74.0"
  },
  "dependencies": {
    "react-tv-space-navigation": "^3.6.1"
  }
}
```

### 2.2 Create Platform-Specific File Structure

For platform abstraction, use Metro's file extension resolution:

| Extension | Platform | Description |
|-----------|----------|-------------|
| `.ts` / `.tsx` | All | Base/shared implementation |
| `.native.ts` | React Native | Android TV, Apple TV, Fire TV FOS |
| `.android.ts` | Android | Android TV, Fire TV FOS |
| `.ios.ts` | iOS/tvOS | Apple TV |
| `.kepler.ts` | Vega | Fire TV Vega OS specific |
| `.web.ts` | Web | Web TV specific |

**CRITICAL - Avoiding Circular Dependencies**: 

When you have a base class that platform-specific files extend, Metro's platform resolution can create a circular dependency. For example:

```
# BAD - Creates circular dependency
RemoteControlManager.ts        # Base class
RemoteControlManager.android.ts # Imports from RemoteControlManager.ts
```

Metro resolves `RemoteControlManager` to `RemoteControlManager.android.ts` on Android, which then imports from itself.

**FIX**: Rename base files to use `.base.ts` extension:

```
# GOOD - No circular dependency
RemoteControlManager.base.ts    # Base class
RemoteControlManager.android.ts # Imports from RemoteControlManager.base.ts
RemoteControlManager.ios.ts     # Imports from RemoteControlManager.base.ts
```

### 2.3 Package Structure

```
packages/shared-ui/
├── package.json
├── tsconfig.json
└── src/
    ├── index.ts                    # Main exports
    ├── components/
    │   ├── core/                   # Basic UI components
    │   │   ├── Button.tsx
    │   │   ├── Text.tsx
    │   │   └── index.ts
    │   └── carousels/              # TV-specific carousels
    │       ├── HorizontalCarousel.tsx
    │       └── index.ts
    ├── hooks/
    │   ├── useRemoteControl.ts
    │   └── index.ts
    ├── services/
    │   └── videoPlayer/
    │       ├── VideoPlayerService.ts       # Interface/types only
    │       ├── VideoPlayerService.native.ts # React Native implementation
    │       ├── VideoPlayerService.kepler.ts # Vega implementation
    │       └── index.ts
    ├── theme/
    │   ├── colors.ts
    │   ├── spacing.ts
    │   └── index.ts
    └── models/
        └── index.ts
```

### 2.4 Export Structure

Create `packages/shared-ui/src/index.ts`:

```typescript
// Components
export * from './components/core';
export * from './components/carousels';

// Hooks
export * from './hooks';

// Services - types only, implementations resolved by Metro
export * from './services/videoPlayer';

// Theme
export * from './theme';

// Types/Models
export * from './models';
```

---

## Phase 3: Universal TV App Setup (Expo)

### 3.1 Create Universal TV App

Create `apps/universal-tv/package.json`:

```json
{
  "name": "{PACKAGE_NAME}/universal-tv",
  "version": "0.1.0",
  "private": true,
  "main": "index.js",
  "scripts": {
    "start": "expo start",
    "android": "expo run:android",
    "ios": "expo run:ios",
    "prebuild": "EXPO_TV=1 expo prebuild --clean"
  },
  "dependencies": {
    "{PACKAGE_NAME}/shared-ui": "workspace:*",
    "expo": "~51.0.0",
    "expo-status-bar": "~1.12.1",
    "react": "18.2.0",
    "react-native": "npm:react-native-tvos@~0.74.3-0",
    "react-native-gesture-handler": "~2.16.2",
    "react-native-reanimated": "~3.10.1",
    "react-native-safe-area-context": "4.10.1",
    "react-native-screens": "3.31.1",
    "react-tv-space-navigation": "^3.6.1"
  },
  "devDependencies": {
    "@babel/core": "^7.24.0",
    "@react-native-tvos/config-tv": "^0.0.12",
    "@types/react": "~18.2.79",
    "babel-plugin-module-resolver": "^5.0.2",
    "typescript": "~5.3.3"
  }
}
```

**CRITICAL**: Use `react-native-tvos` instead of standard `react-native`:
```json
"react-native": "npm:react-native-tvos@~0.74.3-0"
```

### 3.2 Configure Metro for Monorepo

Create `apps/universal-tv/metro.config.js`:

```javascript
const { getDefaultConfig } = require('expo/metro-config');
const path = require('path');

const config = getDefaultConfig(__dirname);

// Explicitly set project root
config.projectRoot = __dirname;

// Configure Metro to watch monorepo packages
config.watchFolders = [
  path.resolve(__dirname, '../../packages/shared-ui'),
  path.resolve(__dirname, '../..'), // Root for node_modules
];

config.resolver = {
  ...config.resolver,
  unstable_enableSymlinks: true,
  // IMPORTANT: App's node_modules MUST come first for correct package versions
  nodeModulesPaths: [
    path.resolve(__dirname, 'node_modules'),
    path.resolve(__dirname, '../../node_modules'),
  ],
  extraNodeModules: {
    '{PACKAGE_NAME}/shared-ui': path.resolve(__dirname, '../../packages/shared-ui/src'),
  },
};

config.transformer = {
  ...config.transformer,
  // Disable looking up babel config from parent directories
  enableBabelRCLookup: false,
};

// Enable TV-specific file extensions when EXPO_TV=1
if (process.env?.EXPO_TV === '1') {
  const originalSourceExts = config.resolver.sourceExts;
  config.resolver.sourceExts = [
    ...originalSourceExts.map((e) => `tv.${e}`),
    ...originalSourceExts,
  ];
}

module.exports = config;
```

### 3.3 Configure Babel for App

Create `apps/universal-tv/babel.config.js`:

```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: [
      [
        'module-resolver',
        {
          root: ['./'],
          alias: {
            '{PACKAGE_NAME}/shared-ui': '../../packages/shared-ui/src',
          },
        },
      ],
      require.resolve('react-native-reanimated/plugin'),
    ],
  };
};
```

### 3.4 Fix HMRClient Error (CRITICAL)

**CRITICAL**: The `react-native-tvos` fork has an HMR (Hot Module Replacement) incompatibility that causes continuous errors:

```
Invariant Violation: Failed to call into JavaScript module method HMRClient.setup()
Invariant Violation: No callback found with cbID X and callID Y
```

**Fix**: Add HMRClient polyfill to `apps/universal-tv/index.js`:

```javascript
import { registerRootComponent } from 'expo';
import { AppRegistry } from 'react-native';
import App from './App';

// Polyfill for HMRClient to prevent "Module has not been registered as callable" error
// This is needed for react-native-tvos compatibility with Expo's dev server
if (!AppRegistry.getRunnable('HMRClient')) {
  AppRegistry.registerCallableModule('HMRClient', {
    setup: () => {},
    enable: () => {},
    disable: () => {},
  });
}

registerRootComponent(App);
```

### 3.5 Configure app.json

Create `apps/universal-tv/app.json`:

```json
{
  "expo": {
    "name": "{APP_NAME}",
    "slug": "{app-name-slug}",
    "version": "1.0.0",
    "orientation": "landscape",
    "userInterfaceStyle": "dark",
    "splash": {
      "resizeMode": "contain",
      "backgroundColor": "#0D1117"
    },
    "assetBundlePatterns": ["**/*"],
    "ios": {
      "supportsTablet": true,
      "bundleIdentifier": "{IOS_BUNDLE_ID}"
    },
    "android": {
      "package": "{ANDROID_PACKAGE}"
    },
    "plugins": [
      "@react-native-tvos/config-tv"
    ]
  }
}
```

### 3.6 Create TypeScript Config

Create `apps/universal-tv/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "{PACKAGE_NAME}/shared-ui": ["../../packages/shared-ui/src"],
      "{PACKAGE_NAME}/shared-ui/*": ["../../packages/shared-ui/src/*"]
    }
  },
  "include": ["**/*.ts", "**/*.tsx", ".expo/types/**/*.ts", "expo-env.d.ts"]
}
```

---

## Phase 4: Platform Abstraction Layer

### 4.1 Remote Control Manager Pattern

Create base class in `packages/shared-ui/src/app/remote-control/RemoteControlManager.base.ts`:

```typescript
export type RemoteKey =
  | 'up' | 'down' | 'left' | 'right'
  | 'select' | 'back'
  | 'play' | 'pause' | 'playPause'
  | 'fastForward' | 'rewind'
  | 'menu';

export interface RemoteKeyEvent {
  key: RemoteKey;
  action: 'keyDown' | 'keyUp';
  timestamp: number;
}

export type RemoteKeyHandler = (event: RemoteKeyEvent) => void;

export interface RemoteControlManagerInterface {
  initialize(): void;
  cleanup(): void;
  addKeyListener(handler: RemoteKeyHandler): () => void;
  removeKeyListener(handler: RemoteKeyHandler): void;
}

export class BaseRemoteControlManager implements RemoteControlManagerInterface {
  protected listeners: Set<RemoteKeyHandler> = new Set();

  initialize(): void {
    // Override in platform-specific implementation
  }

  cleanup(): void {
    this.listeners.clear();
  }

  addKeyListener(handler: RemoteKeyHandler): () => void {
    this.listeners.add(handler);
    return () => this.removeKeyListener(handler);
  }

  removeKeyListener(handler: RemoteKeyHandler): void {
    this.listeners.delete(handler);
  }

  protected notifyListeners(event: RemoteKeyEvent): void {
    this.listeners.forEach((handler) => handler(event));
  }
}
```

Create Android implementation in `RemoteControlManager.android.ts`:

```typescript
import {
  BaseRemoteControlManager,
  type RemoteKey,
  type RemoteKeyEvent,
} from './RemoteControlManager.base';

const ANDROID_KEY_MAP: Record<number, RemoteKey> = {
  19: 'up',      // KEYCODE_DPAD_UP
  20: 'down',    // KEYCODE_DPAD_DOWN
  21: 'left',    // KEYCODE_DPAD_LEFT
  22: 'right',   // KEYCODE_DPAD_RIGHT
  23: 'select',  // KEYCODE_DPAD_CENTER
  66: 'select',  // KEYCODE_ENTER
  4: 'back',     // KEYCODE_BACK
  85: 'playPause',
};

export class RemoteControlManager extends BaseRemoteControlManager {
  override initialize(): void {
    try {
      const KeyEvent = require('react-native-keyevent').default;
      KeyEvent.onKeyDownListener((keyCode: number) => {
        const key = ANDROID_KEY_MAP[keyCode];
        if (key) {
          this.notifyListeners({
            key,
            action: 'keyDown',
            timestamp: Date.now(),
          });
        }
      });
    } catch {
      console.warn('react-native-keyevent not available');
    }
  }
}

export default new RemoteControlManager();
```

Create index in `packages/shared-ui/src/app/remote-control/index.ts`:

```typescript
export * from './RemoteControlManager.base';
// Platform-specific manager is auto-resolved by Metro bundler
```

### 4.2 Video Player Service Pattern

Create interface in `packages/shared-ui/src/services/videoPlayer/VideoPlayerService.ts`:

```typescript
export interface VideoSource {
  uri: string;
  type?: 'hls' | 'dash' | 'mp4';
  headers?: Record<string, string>;
  drm?: DRMConfig;
}

export interface DRMConfig {
  type: 'widevine' | 'fairplay' | 'playready';
  licenseServer: string;
  headers?: Record<string, string>;
}

export interface PlaybackState {
  isPlaying: boolean;
  isPaused: boolean;
  isBuffering: boolean;
  currentTime: number;
  duration: number;
}

export interface VideoPlayerService {
  initialize(): Promise<void>;
  load(source: VideoSource): Promise<void>;
  play(): Promise<void>;
  pause(): Promise<void>;
  seek(timeInSeconds: number): Promise<void>;
  destroy(): Promise<void>;
  getPlaybackState(): PlaybackState;
}

export const DEFAULT_SEEK_SECONDS = 10;
```

Create native implementation in `VideoPlayerService.native.ts` (uses react-native-video).
Create Vega implementation in `VideoPlayerService.kepler.ts` (uses Shaka/W3C Media).

---

## Phase 5: Component Migration

### 5.1 Migration Workflow

For each component to migrate:

1. **Copy** component from `apps/vega/src/components/` to `packages/shared-ui/src/components/`
2. **Update imports** to use relative paths within shared-ui
3. **Replace** Vega-specific APIs with platform-abstracted versions
4. **Add types** for all props and exports
5. **Export** from shared-ui index
6. **Update** Vega app to import from `{PACKAGE_NAME}/shared-ui`
7. **Test** on all target platforms

### 5.2 Common Migration Issues

**Issue**: VirtualizedLists nested in ScrollViews warning
```
VirtualizedLists should never be nested inside plain ScrollViews
```
**Fix**: Use `FlatList` with `ListHeaderComponent` and `ListFooterComponent` instead of wrapping in `ScrollView`, or use `ScrollView` only for non-virtualized content.

**Issue**: TypeScript path resolution errors
**Fix**: Ensure all `tsconfig.json` files have correct paths configuration.

**Issue**: Missing platform-specific implementations
**Fix**: Create stub implementations that log warnings for unsupported platforms.

---

## Phase 6: Running the Apps

### 6.1 Universal TV App (Android TV)

```bash
# Terminal 1: Start Metro bundler
cd apps/universal-tv
EXPO_TV=1 yarn start --clear

# Terminal 2: First-time setup - generate native projects and build
export JAVA_HOME=/path/to/java/17
EXPO_TV=1 npx expo prebuild --clean
EXPO_TV=1 npx expo run:android

# Subsequent runs - just start the app
adb reverse tcp:8081 tcp:8081
adb shell am start -n {ANDROID_PACKAGE}/.MainActivity
```

### 6.2 Universal TV App (Apple TV)

```bash
cd apps/universal-tv
EXPO_TV=1 yarn start --clear

# In another terminal
EXPO_TV=1 npx expo run:ios --device "Apple TV"
```

### 6.3 Fire TV Vega App

```bash
cd apps/vega
yarn start
# Deploy via Vega toolchain
```

---

## Troubleshooting

### Error: `.plugins[0] must be a string, object, function`

**Cause**: `metro-source-map` version mismatch in monorepo
**Fix**: Add resolution in root `package.json`:
```json
"resolutions": {
  "metro-source-map": "0.80.12"
}
```
Then run `yarn install`.

### Error: `Super expression must either be null or a function`

**Cause**: Circular dependency in class inheritance due to Metro's platform resolution
**Fix**: Rename base class files from `ClassName.ts` to `ClassName.base.ts` and update imports in platform-specific files.

### Error: `HMRClient.setup() Module has not been registered`

**Cause**: react-native-tvos HMR incompatibility with Expo
**Fix**: Add HMRClient polyfill in `index.js` (see Phase 3.4)

### Error: `Cannot find module '{PACKAGE_NAME}/shared-ui'`

**Cause**: Metro not configured for monorepo workspace resolution
**Fix**: 
1. Add `watchFolders` in metro.config.js pointing to shared packages
2. Add `extraNodeModules` mapping for the package
3. Run `yarn install` from monorepo root

### Warning: `Require cycle: X.ts -> X.ts`

**Cause**: Platform-specific file importing itself due to Metro resolution
**Fix**: Rename base file to use `.base.ts` extension

### Warning: `[React Spatial Navigation] You probably forgot to configure the remote control`

**Cause**: Spatial navigation library not initialized
**Fix**: Call the configuration function in your app's initialization:
```typescript
import { configureRemoteControl } from 'react-tv-space-navigation';

configureRemoteControl({
  // your remote control configuration
});
```

### Build Error: `Could not determine java version`

**Cause**: Java 17 not configured for Android Gradle plugin
**Fix**: Set JAVA_HOME before running build commands:
```bash
export JAVA_HOME=/path/to/java/17
```

---

## Version Compatibility Matrix

| Package | Version | Notes |
|---------|---------|-------|
| expo | ~51.0.0 | Required for TV support |
| react | 18.2.0 | Must match expo requirement |
| react-native-tvos | ~0.74.3-0 | Use instead of react-native |
| metro-source-map | 0.80.12 | Force via resolution |
| react-native-reanimated | ~3.10.1 | For animations |
| @react-native-tvos/config-tv | ^0.0.12 | Expo plugin for TV |
| react-tv-space-navigation | ^3.6.1 | Spatial navigation |
| Java | 17 | Required for Android Gradle |
| Node.js | 18+ | Required for Expo |
| Yarn | 4.x | Berry with workspaces |

---

## Checklist

### Root Level
- [ ] `package.json` with workspaces and `metro-source-map` resolution
- [ ] `babel.config.js` with reanimated plugin resolution
- [ ] `tsconfig.base.json` with shared compiler options
- [ ] `.yarnrc.yml` for Yarn Berry configuration

### packages/shared-ui
- [ ] `package.json` with peer dependencies
- [ ] `tsconfig.json` extending base config
- [ ] `src/index.ts` with all exports
- [ ] Platform-specific files use `.base.ts` for base classes
- [ ] No circular dependencies in imports

### apps/universal-tv
- [ ] `package.json` with `react-native-tvos` dependency
- [ ] `metro.config.js` with monorepo configuration
- [ ] `babel.config.js` with module-resolver plugin
- [ ] `index.js` with HMRClient polyfill
- [ ] `app.json` with `@react-native-tvos/config-tv` plugin
- [ ] `tsconfig.json` with path mappings
- [ ] `App.tsx` entry component

### apps/vega (original app)
- [ ] Updated imports to use shared-ui package
- [ ] Platform-specific implementations for Vega APIs
- [ ] Tested with original Vega toolchain
