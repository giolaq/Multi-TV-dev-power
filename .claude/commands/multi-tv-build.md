# Multi-TV Build Commands

Provide build commands and guidance for all supported TV platforms.

## Instructions

Help the user build their TV application for their target platform(s).

### Development Commands

```bash
# Start Metro bundler for expo-multi-tv
yarn dev

# Run on specific platforms
yarn dev:android    # Android TV & Fire TV FOS
yarn dev:ios        # Apple TV
yarn dev:web        # Web TV platforms

# Start Metro bundler for vega
yarn dev:vega
```

### Production Builds

#### Expo Multi-TV App

```bash
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

#### Fire TV Vega App

```bash
# From project root
yarn build:vega         # Build release VPKG
yarn build:vega:debug   # Build debug VPKG

# From apps/vega directory
cd apps/vega
npm run build:app       # Builds for all architectures

# Build specific architecture
kepler build --arch armv7 --buildType release    # Fire TV Stick
kepler build --arch aarch64 --buildType release  # M-series Mac
kepler build --arch x86_64 --buildType release   # Intel virtual devices
```

### Testing & Quality

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

### Build Outputs

| Platform | Output Location |
|----------|----------------|
| Android TV | `apps/expo-multi-tv/android/app/build/outputs/` |
| Apple TV | `apps/expo-multi-tv/ios/build/` |
| Web | `apps/expo-multi-tv/dist/` |
| Fire TV Vega | `apps/vega/build/<arch>-<type>/*.vpkg` |

### Troubleshooting

- **Metro issues**: `yarn start --reset-cache`
- **iOS/tvOS**: Check Node path in `.xcode.env.local`
- **Vega SDK not found**: Verify `$KEPLER_SDK_PATH` is set

Ask the user which platform they're targeting and provide specific guidance.
