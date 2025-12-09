# Multi-TV Setup Guide

Provide step-by-step setup instructions for Multi-TV app development.

## Instructions

Guide the user through setting up their development environment for cross-platform TV app development.

### Quick Start (Recommended)

```bash
# Clone the official sample repository
git clone https://github.com/AmazonAppDev/react-native-multi-tv-app-sample.git
cd react-native-multi-tv-app-sample

# Install dependencies
yarn install

# Bootstrap the monorepo
yarn bootstrap
```

### What's Included in the Sample

- Pre-configured monorepo with Yarn workspaces
- expo-multi-tv app (Android TV, Apple TV, Fire TV FOS, Web)
- vega app (Fire TV Vega OS)
- shared-ui component library with:
  - Spatial navigation setup
  - Platform-specific remote control managers
  - Video player with custom controls
  - TV-optimized components
  - Theme system (colors, spacing, typography, safe zones)
  - Navigation structure (Drawer + Stack)
  - Sample screens

### Expo TV Setup

```bash
# Set environment variable for TV builds
export EXPO_TV=1

# Run prebuild to generate native projects
npx expo prebuild --clean

# Run on platforms
yarn dev:android  # Android TV
yarn dev:ios      # Apple TV
```

### Vega SDK Setup (Optional - Fire TV Vega OS)

1. Install Vega SDK from Amazon developer portal
2. Configure environment:
   ```bash
   export KEPLER_SDK_PATH=/Users/<user>/kepler/sdk/<version>
   export PATH=$KEPLER_SDK_PATH/bin:$PATH
   ```
3. Verify installation: `kepler --version`

### Build from Scratch Option

For custom setups, reference:
- `multi-tv-builder/POWER.md` - "Build from Scratch" section
- `multi-tv-builder/steering/structure.md` - Project structure guide

Ask the user about their specific setup needs and provide tailored instructions.
