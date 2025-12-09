# Technology Stack

## Build System & Package Management

### Monorepo Structure
- **Yarn Workspaces** - v4.5.0 (configured via packageManager field)
- **Workspace Organization** - `apps/*` and `packages/*` structure
- **Shared Dependencies** - Centralized dependency management at root level

### Package Manager Commands
```bash
# Install all dependencies
yarn install

# Bootstrap entire project
yarn bootstrap

# Clean all node_modules
yarn clean:all
```

## Core Technologies

### React Native & Expo
- **React Native** - v0.74.2 (react-native-tvos fork for TV support)
- **Expo SDK** - v51 (expo-multi-tv app only)
- **TypeScript** - v5.3.3 with strict mode enabled
- **React** - v18.2.0

### Navigation & Focus Management
- **React Navigation** - v6 (drawer and stack navigators)
- **React TV Space Navigation** - v3.6.1 (spatial navigation for TV remotes)
  - Handles D-pad/arrow key navigation
  - Focus management for TV interfaces
  - Integration with platform-specific remote controls

### Video Playback
- **react-native-video** - v6.x (expo-multi-tv)
- **W3C Media APIs** - Native video for Vega OS
- Custom overlay controls with spatial navigation support

### Platform-Specific SDKs
- **Amazon Vega SDK** - For Fire TV Vega OS builds
- **@amazon-devices packages** - Kepler SDK components for Fire TV
- **react-native-keyevent** - Android TV remote control integration
- **TVEventHandler** - Native tvOS and Vega OS remote events

## Development Tools

### Code Quality
- **ESLint** - v8.57.1 with TypeScript and React plugins
- **Prettier** - v3.3.3 for code formatting
- **Husky** - v9.1.6 for git hooks
- **Commitlint** - Conventional commits enforced

### Testing
- **Jest** - v29.7.0 for unit testing
- **React Test Renderer** - v18.2.0
- **Testing Library** - @testing-library/react-native

### TypeScript Configuration
- **Target** - ESNext
- **Module** - CommonJS
- **JSX** - react-native
- **Strict Mode** - Enabled
- **Module Resolution** - Node

## Common Commands

### Development
```bash
# Start Metro bundler for expo-multi-tv
yarn dev

# Run on specific platforms
yarn dev:android    # Android TV & Fire TV (Fire OS)
yarn dev:ios        # Apple TV
yarn dev:web        # Web TV platforms

# Start Metro bundler for vega
yarn dev:vega
```

### Building
```bash
# Build all workspaces
yarn build:all

# Build Fire TV Vega app
yarn build:vega
yarn build:vega:debug
```

### Testing & Linting
```bash
# Run tests across all workspaces
yarn test:all

# Lint all workspaces
yarn lint:all

# Type check all workspaces
yarn typecheck

# Format code with Prettier
yarn format
```

### App-Specific Commands
```bash
# Navigate to app directory first
cd apps/expo-multi-tv
yarn start      # Start Metro bundler
yarn android    # Run on Android TV
yarn ios        # Run on Apple TV
yarn web        # Run on Web
yarn test       # Run tests
yarn lint       # Lint code

cd apps/vega
yarn start      # Start Metro bundler
yarn build      # Build for Fire TV Vega
yarn test       # Run tests
yarn lint       # Lint code
```

## Platform-Specific File Extensions

Metro bundler automatically resolves platform-specific files:
- `.android.ts` / `.android.tsx` - Android TV & Fire TV (Fire OS)
- `.ios.ts` / `.ios.tsx` - Apple TV (tvOS)
- `.kepler.ts` / `.kepler.tsx` - Fire TV Vega OS
- `.vega.tsx` - Vega-specific screens
- `.web.ts` / `.web.tsx` - Web platforms

Example: `RemoteControlManager.android.ts`, `RemoteControlManager.ios.ts`, `RemoteControlManager.kepler.ts`

## Key Dependencies

### UI & Styling
- **react-native-linear-gradient** - Gradient backgrounds
- **@expo/vector-icons** - Icon library
- **react-native-safe-area-context** - Safe area handling

### State & Data
- **@urql/core** - GraphQL client (if needed)
- **react-native-reanimated** - Animations

### Development
- **@babel/core** - JavaScript compiler
- **metro-react-native-babel-preset** - Metro bundler preset
- **patch-package** - Apply patches to node_modules

## Build Outputs

### Expo Multi-TV
- **Android** - APK/AAB for Android TV and Fire TV (Fire OS)
- **iOS** - IPA for Apple TV (tvOS)
- **Web** - Static HTML/JS bundle for web TV platforms

### Vega
- **Fire TV** - Optimized build for Fire TV Vega OS devices
