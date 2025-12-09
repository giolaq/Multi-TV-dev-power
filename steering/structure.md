# Project Structure

## Monorepo Organization

```
react-native-multi-tv-app-sample/
├── apps/                    # Application packages
│   ├── expo-multi-tv/      # Universal TV app (Android TV, Apple TV, Fire TV FOS, Web)
│   └── vega/               # Fire TV Vega OS optimized app
├── packages/               # Shared packages
│   └── shared-ui/          # Shared UI components and screens
├── .kiro/                  # Kiro AI assistant configuration
│   └── steering/           # AI steering rules
├── .yarn/                  # Yarn v4 cache and plugins
├── node_modules/           # Root dependencies
├── package.json            # Workspace configuration
├── tsconfig.base.json      # Shared TypeScript config
├── eslint.config.js        # ESLint configuration
├── .prettierrc             # Prettier formatting rules
├── commitlint.config.js    # Commit message conventions
└── yarn.lock               # Dependency lock file
```

## Apps Directory

### `apps/expo-multi-tv/`
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

### `apps/vega/`
Fire TV Vega OS optimized application using Amazon Vega SDK.

**Key Files:**
- `App.tsx` - Main entry point
- `package.json` - Vega-specific dependencies
- `tsconfig.json` - TypeScript config
- `metro.config.js` - Metro bundler configuration
- `android/` - Fire TV Vega native code

**Platform Supported:**
- Fire TV (Vega OS)

## Packages Directory

### `packages/shared-ui/`
Shared component library used by both apps.

**Structure:**
```
packages/shared-ui/src/
├── app/                           # App-level configuration
│   └── remote-control/           # Platform-specific remote control managers
│       ├── RemoteControlManager.android.ts
│       ├── RemoteControlManager.ios.ts
│       └── RemoteControlManager.kepler.ts
├── assets/                        # Static assets
│   ├── fonts/                    # Custom fonts
│   ├── images/                   # Image assets
│   └── tv_icons/                 # TV-specific icons
├── components/                    # Reusable UI components
│   ├── player/                   # Video player components
│   ├── CustomDrawerContent.tsx   # Drawer menu component
│   ├── FocusablePressable.tsx    # TV-optimized pressable
│   ├── LoadingIndicator.tsx      # Loading spinner
│   ├── MenuContext.tsx           # Menu state management
│   └── PlatformLinearGradient.tsx # Cross-platform gradient
├── data/                          # Data and API
│   └── moviesData.ts             # Movie catalog data/API
├── hooks/                         # Custom React hooks
│   └── useScale.ts               # Responsive scaling hook
├── navigation/                    # Navigation configuration
│   ├── AppNavigator.tsx          # Main app navigator
│   ├── DrawerNavigator.tsx       # Drawer navigation
│   ├── RootNavigator.tsx         # Root navigation wrapper
│   └── types.ts                  # Navigation type definitions
├── screens/                       # Screen components
│   ├── DetailsScreen.tsx         # Content detail screen
│   ├── ExploreScreen.tsx         # Content exploration
│   ├── HomeScreen.tsx            # Home/landing screen
│   ├── PlayerScreen.tsx          # Video player screen
│   ├── PlayerScreen.vega.tsx     # Vega-specific player
│   ├── SettingsScreen.tsx        # Settings screen
│   └── TVScreen.tsx              # TV content screen
├── theme/                         # Design system
│   ├── colors.ts                 # Color palette
│   ├── spacing.ts                # Spacing scale
│   ├── typography.ts             # Font styles
│   ├── safeZones.ts              # TV safe zones
│   └── index.ts                  # Theme exports
├── utils/                         # Utility functions
│   ├── AppOverrideMediaControlHandler.kepler.ts
│   ├── VideoHandler.kepler.ts
│   └── VideoHandler.ts
└── index.ts                       # Package exports
```

**Export Pattern:**
All components, screens, hooks, and utilities are exported from `src/index.ts` and imported in apps via:
```typescript
import { Component, Screen, Hook } from '@multi-tv/shared-ui';
```

## Platform-Specific File Resolution

Metro bundler automatically resolves platform-specific files based on extension priority:

1. `.kepler.ts` / `.kepler.tsx` - Fire TV Vega OS
2. `.android.ts` / `.android.tsx` - Android TV & Fire TV (Fire OS)
3. `.ios.ts` / `.ios.tsx` - Apple TV (tvOS)
4. `.vega.tsx` - Vega-specific screens
5. `.web.ts` / `.web.tsx` - Web platforms
6. `.ts` / `.tsx` - Default/shared implementation

**Example:**
```
RemoteControlManager.android.ts  → Used on Android TV
RemoteControlManager.ios.ts      → Used on Apple TV
RemoteControlManager.kepler.ts   → Used on Fire TV Vega
```

## Configuration Files

### Root Level
- **package.json** - Workspace configuration, scripts, and root dependencies
- **tsconfig.base.json** - Base TypeScript configuration inherited by all packages
- **eslint.config.js** - ESLint rules for code quality
- **.prettierrc** - Code formatting rules (120 char width, single quotes, semicolons)
- **commitlint.config.js** - Conventional commit message enforcement
- **.yarnrc.yml** - Yarn v4 configuration
- **react-native.config.js** - React Native CLI configuration

### App Level
- **app.json** / **package.json** - App-specific configuration
- **tsconfig.json** - Extends base TypeScript config
- **metro.config.js** - Metro bundler configuration for each app
- **babel.config.js** - Babel transpilation configuration

## Key Architectural Patterns

### Shared UI Library Pattern
- All reusable components live in `@multi-tv/shared-ui`
- Apps import from shared package, not local files
- Platform-specific implementations use file extensions
- Single source of truth for UI components

### Screen Organization
- Each screen is a self-contained component
- Screens use shared components from `components/`
- Navigation logic separated in `navigation/`
- Platform-specific screens use `.vega.tsx` extension

### Remote Control Abstraction
- Platform-specific managers implement common interface
- Automatic resolution based on platform
- Integration with React TV Space Navigation
- Centralized in `app/remote-control/`

### Theme System
- Centralized design tokens in `theme/`
- Responsive scaling with `useScale` hook
- TV-specific considerations (safe zones, 10-foot UI)
- Consistent spacing and typography

## Adding New Features

### New Shared Component
1. Create in `packages/shared-ui/src/components/ComponentName.tsx`
2. Export from `packages/shared-ui/src/index.ts`
3. Import in apps: `import { ComponentName } from '@multi-tv/shared-ui'`

### Platform-Specific Implementation
1. Create base file: `Feature.ts`
2. Create platform files: `Feature.android.ts`, `Feature.ios.ts`, `Feature.kepler.ts`
3. Metro automatically resolves correct file at build time

### New Screen
1. Create in `packages/shared-ui/src/screens/ScreenName.tsx`
2. Add to navigation in `navigation/AppNavigator.tsx`
3. Export from `packages/shared-ui/src/index.ts`
4. Use in app's navigation configuration
