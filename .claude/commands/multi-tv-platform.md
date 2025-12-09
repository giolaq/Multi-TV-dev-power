# Multi-TV Platform Guide

Provide platform-specific guidance for TV app development.

## Instructions

Help the user with platform-specific implementation details. Ask which platform they need help with if not specified.

### Platform Overview

| Platform | Technology | Key Considerations |
|----------|-----------|-------------------|
| Android TV | react-native-tvos | D-pad navigation, react-native-keyevent |
| Apple TV | react-native-tvos + Expo | Siri Remote, TVEventHandler |
| Fire TV (Fire OS) | react-native-tvos | Same as Android TV, Fire remote |
| Fire TV (Vega OS) | Amazon Vega SDK | Kepler SDK, optimized performance |
| Web TV | React Native Web | Keyboard nav, LG/Samsung remotes |

### Platform-Specific File Resolution

Metro bundler automatically resolves files by extension:
```
RemoteControlManager.android.ts  → Android TV & Fire TV FOS
RemoteControlManager.ios.ts      → Apple TV
RemoteControlManager.kepler.ts   → Fire TV Vega
RemoteControlManager.web.ts      → Web platforms
```

### Android TV / Fire TV (Fire OS)

```typescript
// Remote control handling
import ReactNativeKeyEvent from 'react-native-keyevent';

export class RemoteControlManager {
  initialize() {
    ReactNativeKeyEvent.onKeyDownListener((keyEvent) => {
      // Handle D-pad keys: DPAD_UP, DPAD_DOWN, DPAD_LEFT, DPAD_RIGHT
      // Handle buttons: MEDIA_PLAY_PAUSE, BACK, etc.
    });
  }
}
```

### Apple TV (tvOS)

```typescript
// Remote control handling
import { TVEventHandler } from 'react-native';

export class RemoteControlManager {
  initialize() {
    const tvEventHandler = new TVEventHandler();
    tvEventHandler.enable(this, (component, evt) => {
      // Handle Siri Remote events: playPause, select, swipe gestures
    });
  }
}
```

### Fire TV Vega OS

```typescript
// Remote control handling
import { TVEventHandler } from '@amazon-devices/react-native-kepler';

export class RemoteControlManager {
  initialize() {
    const tvEventHandler = new TVEventHandler();
    tvEventHandler.enable(this, (component, evt) => {
      // Handle Vega remote events with Kepler SDK
    });
  }
}
```

### Web TV (LG WebOS, Samsung Tizen)

```typescript
// Keyboard navigation
document.addEventListener('keydown', (event) => {
  // Handle arrow keys and Enter
  // Map to spatial navigation actions
});
```

### TV Safe Zones

All platforms should respect overscan margins:
- **Horizontal**: 48px (left and right)
- **Vertical**: 27px (top and bottom)

```typescript
export const safeZones = {
  horizontal: 48,
  vertical: 27
};
```

Reference `multi-tv-builder/steering/tech.md` for complete platform SDK details.
