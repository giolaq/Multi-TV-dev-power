# Project Structure: React Native Multi-TV App

## Monorepo Organization

The project uses **Yarn Workspaces v4** to manage a monorepo with multiple applications and shared packages.

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

### apps/expo-multi-tv/ - Universal TV Application

The main application built with Expo SDK that targets multiple TV platforms.

**Directory Structure:**
```
apps/expo-multi-tv/
├── App.tsx                 # Main entry point
├── app.json                # Expo configuration
├── package.json            # App-specific dependencies
├── tsconfig.json           # TypeScript config (extends base)
├── metro.config.js         # Metro bundler configuration
├── babel.config.js         # Babel configuration
├── ios/                    # iOS/tvOS native code
│   ├── Podfile
│   └── [Xcode project files]
├── android/                # Android TV native code
│   ├── build.gradle
│   └── [Android project files]
└── web/                    # Web-specific files (if any)
```

**Platforms Supported:**
- **Android TV**: Google's TV platform
- **Apple TV (tvOS)**: Apple's TV operating system
- **Fire TV (Fire OS)**: Amazon Fire TV with Android base
- **Web**: Browser-based TV platforms (LG WebOS, Samsung Tizen)

**Key Configuration Files:**
- **app.json**: Expo configuration including app name, version, platform settings
- **metro.config.js**: Metro bundler configuration for monorepo support
- **babel.config.js**: Babel transpilation with Expo preset
- **package.json**: Scripts for dev, build, test, lint

**Development Commands:**
```bash
cd apps/expo-multi-tv
yarn start              # Start Metro bundler
yarn android            # Run on Android TV
yarn ios                # Run on Apple TV
yarn web                # Run on Web
yarn test               # Run tests
yarn lint               # Lint code
```

### apps/vega/ - Fire TV Vega OS Application

Optimized application specifically for Fire TV devices running Vega OS using Amazon's SDK.

**Directory Structure:**
```
apps/vega/
├── App.tsx                 # Main entry point
├── package.json            # Vega-specific dependencies
├── tsconfig.json           # TypeScript config (extends base)
├── metro.config.js         # Metro bundler configuration
├── babel.config.js         # Babel configuration
└── android/                # Fire TV Vega native code
    ├── build.gradle        # Gradle build configuration
    └── [Android project files with Vega SDK]
```

**Platform Supported:**
- **Fire TV (Vega OS)**: Amazon's optimized TV platform

**Key Features:**
- Uses Amazon Vega SDK (@amazon-devices packages)
- Optimized performance for Fire TV hardware
- W3C Media APIs for video playback
- Kepler SDK components integration
- Platform-specific screens (`.vega.tsx` files)

**Development Commands:**
```bash
cd apps/vega
yarn start              # Start Metro bundler
yarn build              # Build for Fire TV Vega
yarn build:debug        # Debug build
yarn test               # Run tests
yarn lint               # Lint code
```

## Packages Directory

### packages/shared-ui/ - Shared Component Library

The core of code reusability. Contains all shared UI components, screens, navigation, theme, and utilities used by both apps.

**Complete Directory Structure:**
```
packages/shared-ui/
├── src/
│   ├── app/                           # App-level configuration
│   │   └── remote-control/           # Platform-specific remote control managers
│   │       ├── RemoteControlManager.android.ts
│   │       ├── RemoteControlManager.ios.ts
│   │       └── RemoteControlManager.kepler.ts
│   │
│   ├── assets/                        # Static assets
│   │   ├── fonts/                    # Custom fonts
│   │   ├── images/                   # Image assets
│   │   └── tv_icons/                 # TV-specific icons
│   │
│   ├── components/                    # Reusable UI components
│   │   ├── player/                   # Video player components
│   │   │   ├── VideoPlayer.tsx
│   │   │   └── ControlOverlay.tsx
│   │   ├── CustomDrawerContent.tsx   # Drawer menu component
│   │   ├── FocusablePressable.tsx    # TV-optimized pressable
│   │   ├── LoadingIndicator.tsx      # Loading spinner
│   │   ├── MenuContext.tsx           # Menu state management
│   │   └── PlatformLinearGradient.tsx # Cross-platform gradient
│   │
│   ├── data/                          # Data and API
│   │   └── moviesData.ts             # Movie catalog data/API
│   │
│   ├── hooks/                         # Custom React hooks
│   │   └── useScale.ts               # Responsive scaling hook
│   │
│   ├── navigation/                    # Navigation configuration
│   │   ├── AppNavigator.tsx          # Main app navigator
│   │   ├── DrawerNavigator.tsx       # Drawer navigation
│   │   ├── RootNavigator.tsx         # Root navigation wrapper
│   │   └── types.ts                  # Navigation type definitions
│   │
│   ├── screens/                       # Screen components
│   │   ├── DetailsScreen.tsx         # Content detail screen
│   │   ├── ExploreScreen.tsx         # Content exploration
│   │   ├── HomeScreen.tsx            # Home/landing screen
│   │   ├── PlayerScreen.tsx          # Video player screen
│   │   ├── PlayerScreen.vega.tsx     # Vega-specific player
│   │   ├── SettingsScreen.tsx        # Settings screen
│   │   └── TVScreen.tsx              # TV content screen
│   │
│   ├── theme/                         # Design system
│   │   ├── colors.ts                 # Color palette
│   │   ├── spacing.ts                # Spacing scale
│   │   ├── typography.ts             # Font styles
│   │   ├── safeZones.ts              # TV safe zones
│   │   └── index.ts                  # Theme exports
│   │
│   ├── utils/                         # Utility functions
│   │   ├── AppOverrideMediaControlHandler.kepler.ts
│   │   ├── VideoHandler.kepler.ts
│   │   └── VideoHandler.ts
│   │
│   └── index.ts                       # Package exports
│
├── package.json                       # Package configuration
└── tsconfig.json                      # TypeScript config (extends base)
```

**Key Directories Explained:**

#### app/remote-control/
Platform-specific remote control managers that handle hardware button events.

**Files:**
- `RemoteControlManager.android.ts`: Android TV & Fire TV (Fire OS) remote handling
- `RemoteControlManager.ios.ts`: Apple TV Siri Remote handling
- `RemoteControlManager.kepler.ts`: Fire TV Vega OS remote handling

**Usage Pattern:**
```typescript
import { RemoteControlManager } from '@multi-tv/shared-ui';
// Metro automatically resolves correct platform file
```

#### components/
Reusable UI components used across screens.

**Key Components:**
- **FocusablePressable**: TV-optimized pressable with focus states (use instead of TouchableOpacity)
- **CustomDrawerContent**: Drawer menu with TV navigation
- **LoadingIndicator**: Platform-aware loading spinner
- **PlatformLinearGradient**: Cross-platform gradient component
- **player/**: Video player and control overlay components

**Component Creation Pattern:**
1. Create component in `components/ComponentName.tsx`
2. Use `FocusablePressable` for interactive elements
3. Apply theme tokens for styling
4. Export from `src/index.ts`
5. Import: `import { ComponentName } from '@multi-tv/shared-ui'`

#### screens/
Full-screen components representing different app views.

**Available Screens:**
- **HomeScreen**: Landing screen with featured content
- **ExploreScreen**: Content browsing and discovery
- **TVScreen**: TV-specific content view
- **DetailsScreen**: Content detail view with metadata
- **PlayerScreen**: Video playback screen
- **PlayerScreen.vega.tsx**: Vega-optimized player (Fire TV only)
- **SettingsScreen**: App settings

**Screen Creation Pattern:**
1. Create in `screens/ScreenName.tsx`
2. Use shared components from `components/`
3. Configure spatial navigation
4. Handle remote control events
5. Export from `src/index.ts`
6. Add to navigation configuration

#### navigation/
Navigation configuration and routing.

**Files:**
- **AppNavigator.tsx**: Main stack navigator with screen definitions
- **DrawerNavigator.tsx**: Drawer menu configuration
- **RootNavigator.tsx**: Root-level navigation wrapper
- **types.ts**: TypeScript type definitions for navigation

**Navigation Pattern:**
```typescript
import { NavigationContainer } from '@react-navigation/native';
import { RootNavigator } from '@multi-tv/shared-ui';

export default function App() {
  return (
    <NavigationContainer>
      <RootNavigator />
    </NavigationContainer>
  );
}
```

#### theme/
Centralized design system with tokens and constants.

**Files:**
- **colors.ts**: Color palette (primary, secondary, backgrounds, etc.)
- **spacing.ts**: Spacing scale (margins, padding, gaps)
- **typography.ts**: Font families, sizes, weights
- **safeZones.ts**: TV-safe area definitions
- **index.ts**: Exports all theme tokens

**Theme Usage:**
```typescript
import { colors, spacing, typography } from '@multi-tv/shared-ui';

const styles = StyleSheet.create({
  container: {
    backgroundColor: colors.background,
    padding: spacing.lg,
  },
  title: {
    ...typography.heading1,
    color: colors.text,
  },
});
```

#### hooks/
Custom React hooks for common functionality.

**Available Hooks:**
- **useScale**: Responsive scaling based on screen dimensions

**Hook Usage:**
```typescript
import { useScale } from '@multi-tv/shared-ui';

function MyComponent() {
  const scale = useScale();

  return (
    <View style={{ width: scale(100), height: scale(100) }} />
  );
}
```

#### data/
Data fetching, API calls, and mock data.

**Files:**
- **moviesData.ts**: Movie catalog data and API functions

**Data Pattern:**
```typescript
import { getMovies, getFeaturedMovies } from '@multi-tv/shared-ui';

const movies = await getMovies();
const featured = await getFeaturedMovies();
```

## Platform-Specific File Resolution

Metro bundler automatically resolves platform-specific files based on extension priority:

**Resolution Order:**
1. `.kepler.ts` / `.kepler.tsx` → Fire TV Vega OS
2. `.android.ts` / `.android.tsx` → Android TV & Fire TV (Fire OS)
3. `.ios.ts` / `.ios.tsx` → Apple TV (tvOS)
4. `.vega.tsx` → Vega-specific screens (alternate naming)
5. `.web.ts` / `.web.tsx` → Web platforms
6. `.ts` / `.tsx` → Default/shared implementation

**Example File Set:**
```
RemoteControlManager.ts          # Base implementation (fallback)
RemoteControlManager.android.ts  # Android TV & Fire TV (Fire OS)
RemoteControlManager.ios.ts      # Apple TV (tvOS)
RemoteControlManager.kepler.ts   # Fire TV Vega OS
```

**Import Pattern:**
```typescript
// Import base name only, Metro resolves platform file
import { RemoteControlManager } from './RemoteControlManager';

// On Android TV → loads RemoteControlManager.android.ts
// On Apple TV → loads RemoteControlManager.ios.ts
// On Fire TV Vega → loads RemoteControlManager.kepler.ts
```

**When to Use Platform Files:**
- Native API access (platform-specific SDKs)
- Different UI implementation per platform
- Platform-specific optimizations
- Hardware feature differences

**When NOT to Use:**
- Simple conditional logic (use `Platform.OS` instead)
- Styling differences (use theme tokens)
- Feature detection (check capabilities at runtime)

## Configuration Files

### Root Level

**package.json**
- Workspace configuration: `workspaces: ["apps/*", "packages/*"]`
- Root scripts: `dev`, `build:all`, `test:all`, `lint:all`, `typecheck`
- Shared dependencies (installed at root level)
- Package manager: Yarn v4.5.0+ via Corepack

**tsconfig.base.json**
- Base TypeScript configuration inherited by all packages
- Target: ESNext
- Module: CommonJS
- JSX: react-native
- Strict mode enabled
- Module resolution: Node
- Path aliases for workspace packages

**eslint.config.js**
- ESLint rules for code quality
- TypeScript plugin configuration
- React and React Native plugins
- Shared rules across all workspaces

**.prettierrc**
- Code formatting rules
- Line width: 120 characters
- Single quotes
- Semicolons required
- Trailing commas: es5

**commitlint.config.js**
- Conventional commit message enforcement
- Format: `type(scope): message`
- Types: feat, fix, docs, style, refactor, test, chore

**.yarnrc.yml**
- Yarn v4 configuration
- Node linker: node-modules
- Plugin configuration

**react-native.config.js**
- React Native CLI configuration
- Asset linking configuration
- Platform-specific settings

### App Level

**app.json / package.json**
- App-specific configuration
- Expo configuration (for expo-multi-tv)
- App name, version, description
- Platform-specific settings (iOS, Android, Web)

**tsconfig.json**
- Extends `tsconfig.base.json`
- App-specific path mappings
- Include/exclude patterns

**metro.config.js**
- Metro bundler configuration
- Workspace support (monorepo resolution)
- Platform-specific file extensions
- Asset configuration
- Transform options

**babel.config.js**
- Babel transpilation configuration
- Presets: expo, react-native
- Plugin configuration
- Module resolution

## Key Architectural Patterns

### Shared UI Library Pattern
- **Centralized Components**: All reusable UI in `@multi-tv/shared-ui`
- **Single Source of Truth**: Apps import from shared package
- **Platform Resolution**: Platform files for platform-specific implementations
- **Export/Import**: Clean export from `src/index.ts`, import in apps

**Example:**
```typescript
// In packages/shared-ui/src/index.ts
export { HomeScreen } from './screens/HomeScreen';
export { FocusablePressable } from './components/FocusablePressable';
export { colors, spacing } from './theme';

// In apps/expo-multi-tv/App.tsx
import { HomeScreen, FocusablePressable, colors } from '@multi-tv/shared-ui';
```

### Screen Organization
- **Self-Contained Screens**: Each screen is independent component
- **Shared Components**: Screens use components from `components/`
- **Navigation Separation**: Navigation logic in `navigation/`
- **Platform Variants**: Use `.vega.tsx` for platform-specific screens

### Remote Control Abstraction
- **Common Interface**: Platform managers implement same interface
- **Automatic Resolution**: Metro resolves based on platform
- **Integration**: Works with React TV Space Navigation
- **Centralized**: All managers in `app/remote-control/`

### Theme System
- **Design Tokens**: Centralized in `theme/` directory
- **Responsive Scaling**: `useScale` hook for device adaptation
- **TV Considerations**: Safe zones, 10-foot UI principles
- **Consistency**: Shared spacing, colors, typography

## Adding New Features

### Adding a New Shared Component

**Steps:**
1. Create component file:
   ```bash
   touch packages/shared-ui/src/components/MyComponent.tsx
   ```

2. Implement component:
   ```typescript
   import React from 'react';
   import { View, Text } from 'react-native';
   import { FocusablePressable } from './FocusablePressable';
   import { colors, spacing, typography } from '../theme';

   export function MyComponent() {
     return (
       <FocusablePressable>
         <View style={{ padding: spacing.md }}>
           <Text style={{ ...typography.body, color: colors.text }}>
             My Component
           </Text>
         </View>
       </FocusablePressable>
     );
   }
   ```

3. Export from `packages/shared-ui/src/index.ts`:
   ```typescript
   export { MyComponent } from './components/MyComponent';
   ```

4. Import in app:
   ```typescript
   import { MyComponent } from '@multi-tv/shared-ui';
   ```

### Adding Platform-Specific Implementation

**Steps:**
1. Create base file:
   ```bash
   touch packages/shared-ui/src/components/Feature.ts
   ```

2. Create platform files:
   ```bash
   touch packages/shared-ui/src/components/Feature.android.ts
   touch packages/shared-ui/src/components/Feature.ios.ts
   touch packages/shared-ui/src/components/Feature.kepler.ts
   ```

3. Implement each platform:
   ```typescript
   // Feature.android.ts
   export function Feature() {
     // Android TV specific implementation
   }

   // Feature.ios.ts
   export function Feature() {
     // Apple TV specific implementation
   }

   // Feature.kepler.ts
   export function Feature() {
     // Fire TV Vega specific implementation
   }
   ```

4. Metro automatically resolves correct file at build time

### Adding a New Screen

**Steps:**
1. Create screen file:
   ```bash
   touch packages/shared-ui/src/screens/MyScreen.tsx
   ```

2. Implement screen with spatial navigation:
   ```typescript
   import React from 'react';
   import { View, Text } from 'react-native';
   import { SpatialNavigationRoot } from 'react-tv-space-navigation';

   export function MyScreen() {
     return (
       <SpatialNavigationRoot>
         <View>
           <Text>My Screen</Text>
         </View>
       </SpatialNavigationRoot>
     );
   }
   ```

3. Add to navigation in `navigation/AppNavigator.tsx`:
   ```typescript
   import { MyScreen } from '../screens/MyScreen';

   <Stack.Screen name="MyScreen" component={MyScreen} />
   ```

4. Export from `packages/shared-ui/src/index.ts`:
   ```typescript
   export { MyScreen } from './screens/MyScreen';
   ```

5. Use in app's navigation configuration

## File Naming Conventions

- **Components**: PascalCase (`FocusablePressable.tsx`)
- **Screens**: PascalCase with "Screen" suffix (`HomeScreen.tsx`)
- **Hooks**: camelCase with "use" prefix (`useScale.ts`)
- **Utils**: camelCase (`videoHandler.ts`)
- **Platform Files**: Base name + platform extension (`Component.android.tsx`)
- **Types**: PascalCase in `types.ts` files
- **Constants**: SCREAMING_SNAKE_CASE in constant files

## Import/Export Best Practices

✅ **Do:**
```typescript
// Export from index.ts
export { Component } from './Component';

// Import from package
import { Component } from '@multi-tv/shared-ui';

// Group imports
import React from 'react';
import { View, Text } from 'react-native';
import { Component } from '@multi-tv/shared-ui';
```

❌ **Don't:**
```typescript
// Don't import directly from file
import { Component } from '@multi-tv/shared-ui/src/components/Component';

// Don't mix default and named exports inconsistently
export default function Component() {}
export { Component };
```

## Next Steps

- Review **tech-stack.md** for technology details and commands
- Review **spatial-navigation.md** for TV navigation patterns
- Review **platform-specific.md** for platform customization guidelines
- Review **shared-components.md** for component development patterns
