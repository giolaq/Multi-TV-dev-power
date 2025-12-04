# Technology Stack: React Native Multi-TV App

## Build System & Package Management

### Monorepo Structure

**Yarn Workspaces v4**
- Version: 4.5.0+ (configured via `packageManager` field in root `package.json`)
- Workspace organization: `apps/*` and `packages/*`
- Shared dependency management at root level
- Efficient hoisting and linking
- Plug'n'Play (PnP) mode available

**Package Manager Commands:**
```bash
# Install all dependencies across workspaces
yarn install

# Bootstrap the entire monorepo (run postinstall scripts)
yarn bootstrap

# Clean all node_modules across workspaces
yarn clean:all

# Add dependency to specific workspace
yarn workspace expo-multi-tv add react-native-video

# Run command in specific workspace
yarn workspace shared-ui test
```

**Workspace Configuration (root package.json):**
```json
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ],
  "packageManager": "yarn@4.5.0"
}
```

## Core Technologies

### React Native & Expo

**React Native**
- Version: v0.74.2
- Fork: `react-native-tvos` (TV platform support)
- Provides: Native components, APIs, and platform bridges
- Platforms: iOS (tvOS), Android (TV), Web

**Expo SDK**
- Version: v51
- Used in: `apps/expo-multi-tv` only (not in Vega app)
- Benefits: Simplified configuration, over-the-air updates, managed workflow
- Modules: expo-av, expo-font, expo-splash-screen, etc.

**React**
- Version: v18.2.0
- Hooks: useState, useEffect, useContext, custom hooks
- Functional components preferred over class components

**TypeScript**
- Version: v5.3.3
- Strict mode enabled
- Target: ESNext
- Module: CommonJS
- JSX: react-native

**TypeScript Configuration:**
```typescript
// tsconfig.base.json (shared)
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "commonjs",
    "lib": ["ESNext"],
    "jsx": "react-native",
    "strict": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "allowSyntheticDefaultImports": true
  }
}
```

### Navigation & Focus Management

**React Navigation v6**
- Navigation library for React Native
- Drawer Navigator: Side menu navigation
- Stack Navigator: Screen transitions
- Type-safe navigation with TypeScript
- Deep linking support

**Installation:**
```bash
yarn workspace expo-multi-tv add @react-navigation/native @react-navigation/drawer @react-navigation/stack
yarn workspace expo-multi-tv add react-native-screens react-native-safe-area-context
```

**React TV Space Navigation**
- Version: v3.6.1
- Purpose: Spatial navigation for TV remotes (D-pad control)
- Features:
  - Focus management
  - Arrow key navigation
  - Remote control integration
  - Focus trap for modals
  - Customizable navigation behavior

**Installation:**
```bash
yarn workspace shared-ui add react-tv-space-navigation
```

**Usage Example:**
```typescript
import { SpatialNavigationRoot, SpatialNavigationNode } from 'react-tv-space-navigation';

<SpatialNavigationRoot>
  <SpatialNavigationNode>
    <FocusablePressable onPress={handlePress}>
      <Text>Focusable Item</Text>
    </FocusablePressable>
  </SpatialNavigationNode>
</SpatialNavigationRoot>
```

### Video Playback

**react-native-video**
- Version: v6.x
- Used in: `apps/expo-multi-tv`
- Platforms: iOS, Android, Web
- Features: Playback controls, fullscreen, callbacks, streaming

**Installation:**
```bash
yarn workspace expo-multi-tv add react-native-video
```

**W3C Media APIs**
- Used in: `apps/vega` (Fire TV Vega OS)
- Native HTML5 video APIs
- Better performance on Vega OS
- Platform-specific implementation

**Video Player Pattern:**
```typescript
// PlayerScreen.tsx (universal)
import Video from 'react-native-video';

<Video
  source={{ uri: videoUrl }}
  style={styles.video}
  controls={false}
  resizeMode="contain"
  onLoad={handleLoad}
  onError={handleError}
/>

// PlayerScreen.vega.tsx (Vega-specific)
// Uses W3C Media APIs with platform optimizations
```

### Platform-Specific SDKs

**Amazon Vega SDK**
- Used in: `apps/vega`
- Package prefix: `@amazon-devices`
- Kepler SDK components for Fire TV
- Optimized for Vega OS devices

**Vega Dependencies:**
```bash
yarn workspace vega add @amazon-devices/kepler-sdk
```

**react-native-keyevent**
- Purpose: Android TV remote control integration
- Captures hardware key events (D-pad, menu, back, etc.)
- Platform: Android TV & Fire TV (Fire OS)

**Installation:**
```bash
yarn workspace expo-multi-tv add react-native-keyevent
```

**TVEventHandler**
- Native API for tvOS and Vega OS remote events
- Built into React Native (no install needed)
- Handles: Play/Pause, Select, Menu, Arrow keys

**Usage:**
```typescript
import { TVEventHandler } from 'react-native';

useEffect(() => {
  const tvEventHandler = new TVEventHandler();
  tvEventHandler.enable(null, (component, event) => {
    if (event.eventType === 'playPause') {
      togglePlayback();
    }
  });

  return () => tvEventHandler.disable();
}, []);
```

## Development Tools

### Code Quality

**ESLint**
- Version: v8.57.1
- Plugins: TypeScript, React, React Native
- Configuration: `eslint.config.js` at root
- Rules: Enforces code style and catches errors

**Commands:**
```bash
# Lint all workspaces
yarn lint:all

# Lint specific workspace
yarn workspace expo-multi-tv lint

# Auto-fix issues
yarn lint:all --fix
```

**Prettier**
- Version: v3.3.3
- Purpose: Code formatting
- Configuration: `.prettierrc` at root
- Settings: 120 char width, single quotes, semicolons

**Commands:**
```bash
# Format all code
yarn format

# Check formatting
yarn format --check
```

**Husky**
- Version: v9.1.6
- Purpose: Git hooks
- Hooks: pre-commit (lint-staged), commit-msg (commitlint)

**Commitlint**
- Enforces: Conventional commit messages
- Format: `type(scope): message`
- Types: feat, fix, docs, style, refactor, test, chore

**Example Commits:**
```
feat(navigation): add drawer menu support
fix(player): resolve video buffering issue
docs(readme): update installation steps
```

### Testing

**Jest**
- Version: v29.7.0
- Purpose: Unit testing framework
- Configuration: `jest.config.js` in each workspace
- Preset: react-native

**React Test Renderer**
- Version: v18.2.0
- Purpose: Component snapshot testing
- Works with Jest

**@testing-library/react-native**
- Purpose: Component testing utilities
- Encourages good testing practices
- Query components by accessibility labels

**Test Commands:**
```bash
# Run all tests across workspaces
yarn test:all

# Run tests in specific workspace
yarn workspace shared-ui test

# Run tests in watch mode
yarn workspace shared-ui test --watch

# Run tests with coverage
yarn workspace shared-ui test --coverage
```

**Test Example:**
```typescript
import { render } from '@testing-library/react-native';
import { HomeScreen } from '../HomeScreen';

test('renders home screen', () => {
  const { getByText } = render(<HomeScreen />);
  expect(getByText('Welcome')).toBeTruthy();
});
```

### TypeScript Configuration

**Base Configuration (tsconfig.base.json):**
- Inherited by all workspaces
- Strict mode enabled
- Path aliases for workspace packages
- ESNext target for modern features

**Workspace Configuration (tsconfig.json):**
- Extends base config
- Workspace-specific paths
- Include/exclude patterns

**Type Checking:**
```bash
# Type check all workspaces
yarn typecheck

# Type check specific workspace
yarn workspace expo-multi-tv typecheck
```

## Common Commands Reference

### Root Level (Monorepo)

**Installation & Setup:**
```bash
# Install all dependencies
yarn install

# Bootstrap entire project (run postinstall scripts)
yarn bootstrap

# Clean all node_modules
yarn clean:all
```

**Development:**
```bash
# Start universal app
yarn dev

# Start on specific platforms
yarn dev:android        # Android TV & Fire TV (Fire OS)
yarn dev:ios            # Apple TV (tvOS)
yarn dev:web            # Web TV platforms

# Start Vega app
yarn dev:vega
```

**Building:**
```bash
# Build all workspaces
yarn build:all

# Build Vega app
yarn build:vega
yarn build:vega:debug
```

**Quality Checks:**
```bash
# Run all tests
yarn test:all

# Lint all code
yarn lint:all

# Type check all code
yarn typecheck

# Format all code
yarn format
```

### App-Specific (expo-multi-tv)

**Navigate to workspace:**
```bash
cd apps/expo-multi-tv
```

**Development:**
```bash
yarn start              # Start Metro bundler
yarn android            # Run on Android TV
yarn ios                # Run on Apple TV
yarn web                # Run on Web
```

**Quality:**
```bash
yarn test               # Run tests
yarn lint               # Lint code
yarn typecheck          # Type check
```

**Building:**
```bash
yarn build:android      # Build Android APK/AAB
yarn build:ios          # Build iOS/tvOS IPA
yarn build:web          # Build web bundle
```

### App-Specific (vega)

**Navigate to workspace:**
```bash
cd apps/vega
```

**Development:**
```bash
yarn start              # Start Metro bundler
yarn build              # Build for Fire TV Vega
yarn build:debug        # Debug build with source maps
```

**Quality:**
```bash
yarn test               # Run tests
yarn lint               # Lint code
yarn typecheck          # Type check
```

### Package-Specific (shared-ui)

**Navigate to workspace:**
```bash
cd packages/shared-ui
```

**Commands:**
```bash
yarn test               # Run tests
yarn lint               # Lint code
yarn typecheck          # Type check
```

## Platform-Specific File Extensions

Metro bundler resolves files in this priority order:

**Extension Priority:**
1. `.kepler.ts` / `.kepler.tsx` → Fire TV Vega OS
2. `.android.ts` / `.android.tsx` → Android TV & Fire TV (Fire OS)
3. `.ios.ts` / `.ios.tsx` → Apple TV (tvOS)
4. `.vega.tsx` → Vega-specific screens (alternative naming)
5. `.web.ts` / `.web.tsx` → Web platforms
6. `.ts` / `.tsx` → Default/shared implementation

**Example File Set:**
```
RemoteControlManager.ts             # Base (fallback)
RemoteControlManager.android.ts     # Android TV & Fire TV (Fire OS)
RemoteControlManager.ios.ts         # Apple TV (tvOS)
RemoteControlManager.kepler.ts      # Fire TV Vega OS
```

**Metro Configuration:**
```javascript
// metro.config.js
module.exports = {
  resolver: {
    sourceExts: ['kepler.tsx', 'kepler.ts', 'android.tsx', 'android.ts',
                 'ios.tsx', 'ios.ts', 'vega.tsx', 'web.tsx', 'web.ts',
                 'tsx', 'ts', 'jsx', 'js'],
  },
};
```

## Key Dependencies Overview

### UI & Styling

**react-native-linear-gradient**
- Purpose: Gradient backgrounds
- Cross-platform support
- Used in: PlatformLinearGradient component

```bash
yarn workspace shared-ui add react-native-linear-gradient
```

**@expo/vector-icons**
- Purpose: Icon library
- Includes: Material, FontAwesome, Ionicons, etc.
- Large selection of TV-appropriate icons

```bash
yarn workspace expo-multi-tv add @expo/vector-icons
```

**react-native-safe-area-context**
- Purpose: Safe area handling
- Critical for: TV-safe zones
- Provides: useSafeAreaInsets hook

```bash
yarn workspace shared-ui add react-native-safe-area-context
```

### State & Data

**React Context API**
- Built into React
- Purpose: State management
- Used for: Menu state, theme, user preferences

**Custom Hooks**
- Purpose: Reusable logic
- Examples: useScale, useRemoteControl, useFocus

### Animations

**react-native-reanimated**
- Purpose: High-performance animations
- Features: Worklet-based animations, smooth transitions
- Optimized for: TV performance

```bash
yarn workspace shared-ui add react-native-reanimated
```

### Development

**@babel/core**
- Purpose: JavaScript compiler
- Transpiles: Modern JS to compatible versions
- Presets: @babel/preset-env, @babel/preset-typescript

**metro-react-native-babel-preset**
- Purpose: Metro bundler Babel preset
- Includes: React Native transformations
- Configuration: babel.config.js

**patch-package**
- Purpose: Apply patches to node_modules
- Useful for: Fixing third-party package issues
- Patches stored in: `patches/` directory

```bash
yarn workspace expo-multi-tv add -D patch-package
```

## Build Outputs

### Expo Multi-TV

**Android (APK/AAB)**
- Platform: Android TV & Fire TV (Fire OS)
- Output: `.apk` (debug) or `.aab` (production)
- Location: `android/app/build/outputs/`
- Command: `yarn build:android`

**iOS/tvOS (IPA)**
- Platform: Apple TV
- Output: `.ipa` file
- Location: `ios/build/`
- Command: `yarn build:ios`
- Requires: Xcode, provisioning profiles

**Web (Static Bundle)**
- Platform: Web TV (LG WebOS, Samsung Tizen)
- Output: HTML, JS, CSS bundle
- Location: `web-build/`
- Command: `yarn build:web`

### Vega

**Fire TV APK**
- Platform: Fire TV Vega OS
- Output: `.apk` file optimized for Vega
- Location: `android/app/build/outputs/`
- Command: `yarn build:vega`
- Includes: Amazon Vega SDK optimizations

## Metro Bundler

**Purpose:**
- JavaScript bundler for React Native
- Resolves modules and dependencies
- Handles platform-specific file resolution
- Supports hot reload and fast refresh

**Configuration:**
```javascript
// metro.config.js
const { getDefaultConfig } = require('expo/metro-config');
const path = require('path');

const config = getDefaultConfig(__dirname);

// Monorepo support: watch workspace packages
config.watchFolders = [
  path.resolve(__dirname, '../../packages'),
];

// Node modules resolution for workspaces
config.resolver.nodeModulesPaths = [
  path.resolve(__dirname, 'node_modules'),
  path.resolve(__dirname, '../../node_modules'),
];

module.exports = config;
```

**Commands:**
```bash
# Start Metro bundler
yarn start

# Start with cache reset
yarn start --reset-cache

# Start with specific port
yarn start --port 8082
```

## Environment Setup

### Required Tools

**Node.js**
- Version: v18+ recommended
- Verify: `node --version`
- Download: https://nodejs.org/

**Yarn**
- Version: v4.5.0+ (via Corepack)
- Enable: `corepack enable`
- Verify: `yarn --version`

**Git**
- Version: Latest stable
- Verify: `git --version`
- Download: https://git-scm.com/

### Platform SDKs

**For Android TV & Fire TV (Fire OS):**
- Android Studio: Latest stable
- Android SDK: API Level 28+
- Android TV Emulator or physical device
- Java: JDK 11 or later

**For Apple TV (tvOS):**
- Xcode: Latest stable (macOS only)
- tvOS SDK: Included with Xcode
- tvOS Simulator or physical Apple TV
- Command Line Tools

**For Fire TV (Vega OS):**
- Android Studio
- Amazon Vega SDK
- Fire TV device with Vega OS
- Android Debug Bridge (ADB)

**For Web TV:**
- Modern web browser
- LG webOS SDK (for production)
- Samsung Tizen SDK (for production)

## Troubleshooting

### Dependency Issues

**Problem:** Module not found errors
**Solution:**
```bash
# Clean and reinstall
yarn clean:all
yarn install
yarn bootstrap
```

### Metro Cache Issues

**Problem:** Stale cache causing build errors
**Solution:**
```bash
# Reset Metro cache
yarn start --reset-cache

# Or manually delete cache
rm -rf $TMPDIR/metro-*
rm -rf $TMPDIR/haste-map-*
```

### Platform Resolution Issues

**Problem:** Wrong platform file being loaded
**Solution:**
1. Verify file extension matches platform
2. Check Metro config `sourceExts` order
3. Clear Metro cache
4. Restart bundler

### TypeScript Errors

**Problem:** Type errors in workspace imports
**Solution:**
```bash
# Rebuild TypeScript
yarn typecheck

# Clean and rebuild
yarn clean:all
yarn install
yarn typecheck
```

## Next Steps

- Review **spatial-navigation.md** for TV navigation implementation
- Review **video-playback.md** for video player integration
- Review **platform-specific.md** for platform customization
- Review **shared-components.md** for component development patterns
