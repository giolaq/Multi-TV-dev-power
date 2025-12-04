# Platform-Specific Development

## Overview

This guide covers creating platform-specific implementations for Android TV, Apple TV, Fire TV (Fire OS & Vega OS), and Web TV platforms.

## Platform File Resolution

### Metro Bundler Resolution Order

Metro automatically resolves platform-specific files based on extension priority:

1. `.kepler.ts` / `.kepler.tsx` → Fire TV Vega OS
2. `.android.ts` / `.android.tsx` → Android TV & Fire TV (Fire OS)
3. `.ios.ts` / `.ios.tsx` → Apple TV (tvOS)
4. `.vega.tsx` → Vega-specific screens
5. `.web.ts` / `.web.tsx` → Web TV platforms
6. `.ts` / `.tsx` → Default/shared implementation

### File Organization Example

```
src/components/
├── VideoPlayer.ts                    # Base implementation (fallback)
├── VideoPlayer.android.ts            # Android TV & Fire TV (Fire OS)
├── VideoPlayer.ios.ts                # Apple TV (tvOS)
├── VideoPlayer.kepler.ts             # Fire TV Vega OS
└── VideoPlayer.web.ts                # Web TV platforms
```

### Import Pattern

```typescript
// Import base name only - Metro resolves platform file automatically
import { VideoPlayer } from './VideoPlayer';

// On Android TV → loads VideoPlayer.android.ts
// On Apple TV → loads VideoPlayer.ios.ts
// On Fire TV Vega → loads VideoPlayer.kepler.ts
// On Web → loads VideoPlayer.web.ts
// Fallback → loads VideoPlayer.ts
```

## When to Create Platform-Specific Files

### ✅ Create Platform Files When:

1. **Using Platform-Specific APIs**
   - Native modules available only on one platform
   - Platform SDK features (Vega SDK, tvOS APIs)
   - Hardware-specific capabilities

2. **Performance Optimizations**
   - Platform-specific rendering optimizations
   - Different video playback implementations
   - Memory management differences

3. **Different UI Implementations**
   - Platform-specific design guidelines
   - Unique navigation patterns
   - Platform-specific components

4. **Hardware Differences**
   - Different remote control types
   - Platform-specific input methods
   - Device capability variations

### ❌ Don't Create Platform Files When:

1. **Simple Conditional Logic**
   - Use `Platform.OS` for simple checks
   - Small styling differences
   - Feature flags

2. **Runtime Feature Detection**
   - Check capabilities at runtime
   - API availability checks
   - Device feature detection

3. **Minimal Differences**
   - Minor styling variations (use theme)
   - Simple text changes
   - Trivial logic differences

## Platform-Specific Examples

### Remote Control Managers

Each platform has different remote control APIs and button mappings.

**RemoteControlManager.android.ts** (Android TV & Fire TV Fire OS)
```typescript
import { useEffect } from 'react';
import ReactNativeKeyEvent from 'react-native-keyevent';

export interface RemoteCallbacks {
  onPlayPause?: () => void;
  onRewind?: () => void;
  onFastForward?: () => void;
  onBack?: () => void;
  onUp?: () => void;
  onDown?: () => void;
  onLeft?: () => void;
  onRight?: () => void;
  onSelect?: () => void;
}

export function useRemoteControl(callbacks: RemoteCallbacks) {
  useEffect(() => {
    const handleKeyEvent = (keyEvent) => {
      switch (keyEvent.keyCode) {
        case 'KEYCODE_DPAD_CENTER':
        case 'KEYCODE_ENTER':
          callbacks.onSelect?.();
          break;
        case 'KEYCODE_DPAD_UP':
          callbacks.onUp?.();
          break;
        case 'KEYCODE_DPAD_DOWN':
          callbacks.onDown?.();
          break;
        case 'KEYCODE_DPAD_LEFT':
          callbacks.onLeft?.();
          break;
        case 'KEYCODE_DPAD_RIGHT':
          callbacks.onRight?.();
          break;
        case 'KEYCODE_BACK':
          callbacks.onBack?.();
          break;
        case 'KEYCODE_MEDIA_PLAY_PAUSE':
          callbacks.onPlayPause?.();
          break;
        case 'KEYCODE_MEDIA_REWIND':
          callbacks.onRewind?.();
          break;
        case 'KEYCODE_MEDIA_FAST_FORWARD':
          callbacks.onFastForward?.();
          break;
      }
    };

    ReactNativeKeyEvent.onKeyDownListener(handleKeyEvent);

    return () => {
      ReactNativeKeyEvent.removeKeyDownListener();
    };
  }, [callbacks]);
}
```

**RemoteControlManager.ios.ts** (Apple TV)
```typescript
import { useEffect } from 'react';
import { TVEventHandler } from 'react-native';

export interface RemoteCallbacks {
  onPlayPause?: () => void;
  onRewind?: () => void;
  onFastForward?: () => void;
  onBack?: () => void;
  onUp?: () => void;
  onDown?: () => void;
  onLeft?: () => void;
  onRight?: () => void;
  onSelect?: () => void;
}

export function useRemoteControl(callbacks: RemoteCallbacks) {
  useEffect(() => {
    const tvEventHandler = new TVEventHandler();

    tvEventHandler.enable(null, (component, event) => {
      switch (event.eventType) {
        case 'select':
          callbacks.onSelect?.();
          break;
        case 'up':
          callbacks.onUp?.();
          break;
        case 'down':
          callbacks.onDown?.();
          break;
        case 'left':
          callbacks.onLeft?.();
          break;
        case 'right':
          callbacks.onRight?.();
          break;
        case 'menu':
          callbacks.onBack?.();
          break;
        case 'playPause':
          callbacks.onPlayPause?.();
          break;
        case 'longPlayPause':
          callbacks.onPlayPause?.();
          break;
      }
    });

    return () => {
      tvEventHandler.disable();
    };
  }, [callbacks]);
}
```

**RemoteControlManager.kepler.ts** (Fire TV Vega OS)
```typescript
import { useEffect } from 'react';
import { TVEventHandler } from 'react-native';

export interface RemoteCallbacks {
  onPlayPause?: () => void;
  onRewind?: () => void;
  onFastForward?: () => void;
  onBack?: () => void;
  onUp?: () => void;
  onDown?: () => void;
  onLeft?: () => void;
  onRight?: () => void;
  onSelect?: () => void;
}

export function useRemoteControl(callbacks: RemoteCallbacks) {
  useEffect(() => {
    const tvEventHandler = new TVEventHandler();

    tvEventHandler.enable(null, (component, event) => {
      switch (event.eventType) {
        case 'select':
          callbacks.onSelect?.();
          break;
        case 'up':
          callbacks.onUp?.();
          break;
        case 'down':
          callbacks.onDown?.();
          break;
        case 'left':
          callbacks.onLeft?.();
          break;
        case 'right':
          callbacks.onRight?.();
          break;
        case 'menu':
        case 'back':
          callbacks.onBack?.();
          break;
        case 'playPause':
          callbacks.onPlayPause?.();
          break;
        case 'rewind':
          callbacks.onRewind?.();
          break;
        case 'fastForward':
          callbacks.onFastForward?.();
          break;
      }
    });

    return () => {
      tvEventHandler.disable();
    };
  }, [callbacks]);
}
```

### Video Player Implementations

**PlayerScreen.tsx** (Universal - using react-native-video)
```typescript
import React, { useRef, useState } from 'react';
import { View, StyleSheet } from 'react-native';
import Video from 'react-native-video';

export function PlayerScreen({ route }) {
  const { videoUrl } = route.params;
  const videoRef = useRef(null);
  const [paused, setPaused] = useState(false);

  return (
    <View style={styles.container}>
      <Video
        ref={videoRef}
        source={{ uri: videoUrl }}
        style={styles.video}
        paused={paused}
        resizeMode="contain"
        controls={false}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#000' },
  video: { width: '100%', height: '100%' },
});
```

**PlayerScreen.vega.tsx** (Fire TV Vega - using W3C Media APIs)
```typescript
import React, { useRef, useState, useEffect } from 'react';
import { View, StyleSheet } from 'react-native';

export function PlayerScreen({ route }) {
  const { videoUrl } = route.params;
  const videoElement = useRef(null);
  const [paused, setPaused] = useState(false);

  useEffect(() => {
    // Use W3C Media API for better Vega performance
    videoElement.current = document.createElement('video');
    videoElement.current.src = videoUrl;
    videoElement.current.style.width = '100%';
    videoElement.current.style.height = '100%';

    videoElement.current.play();

    return () => {
      videoElement.current?.pause();
      videoElement.current = null;
    };
  }, [videoUrl]);

  return (
    <View style={styles.container}>
      {/* W3C video element rendered here */}
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#000' },
});
```

## Platform Detection at Runtime

### Using Platform.OS

For simple conditional logic, use `Platform.OS`:

```typescript
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  button: {
    padding: Platform.select({
      android: 12,
      ios: 16,
      default: 14,
    }),
    borderRadius: Platform.OS === 'ios' ? 8 : 4,
  },
});
```

### Platform-Specific Components

```typescript
import { Platform } from 'react-native';

function MyComponent() {
  return (
    <View>
      {Platform.OS === 'android' && <AndroidSpecificFeature />}
      {Platform.OS === 'ios' && <IOSSpecificFeature />}
      {Platform.OS === 'web' && <WebSpecificFeature />}
    </View>
  );
}
```

### Checking Platform Version

```typescript
import { Platform } from 'react-native';

if (Platform.Version >= 28) {
  // Android API level 28+
  enableNewFeature();
}
```

## Platform-Specific Native Modules

### Android TV / Fire TV (Fire OS)

**Using react-native-keyevent:**
```typescript
import ReactNativeKeyEvent from 'react-native-keyevent';

useEffect(() => {
  ReactNativeKeyEvent.onKeyDownListener((keyEvent) => {
    console.log('Key pressed:', keyEvent.keyCode);
  });

  return () => {
    ReactNativeKeyEvent.removeKeyDownListener();
  };
}, []);
```

### Apple TV (tvOS)

**Using TVEventHandler:**
```typescript
import { TVEventHandler } from 'react-native';

useEffect(() => {
  const tvEventHandler = new TVEventHandler();

  tvEventHandler.enable(null, (component, event) => {
    console.log('TV Event:', event.eventType);
  });

  return () => {
    tvEventHandler.disable();
  };
}, []);
```

### Fire TV Vega OS

**Using Amazon Vega SDK:**
```typescript
import { KeplerSDK } from '@amazon-devices/kepler-sdk';

useEffect(() => {
  // Initialize Vega SDK features
  KeplerSDK.initialize();

  return () => {
    KeplerSDK.cleanup();
  };
}, []);
```

## Platform-Specific Styling

### Theme Platform Overrides

```typescript
// theme/colors.ts
import { Platform } from 'react-native';

export const colors = {
  primary: Platform.select({
    android: '#4CAF50',
    ios: '#007AFF',
    default: '#6200EE',
  }),
  surface: Platform.select({
    android: '#121212',
    ios: '#1C1C1E',
    default: '#1E1E1E',
  }),
};
```

### Platform-Specific StyleSheet

```typescript
const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      android: {
        backgroundColor: '#000',
        paddingHorizontal: 48,
      },
      ios: {
        backgroundColor: '#0a0a0a',
        paddingHorizontal: 64,
      },
      default: {
        backgroundColor: '#111',
        paddingHorizontal: 56,
      },
    }),
  },
});
```

## Platform-Specific Navigation

### Back Button Handling

**Android TV & Fire TV:**
```typescript
import { BackHandler } from 'react-native';

useEffect(() => {
  const backAction = () => {
    if (canGoBack) {
      navigation.goBack();
      return true; // Prevent default
    }
    return false; // Allow default (exit app)
  };

  const backHandler = BackHandler.addEventListener(
    'hardwareBackPress',
    backAction
  );

  return () => backHandler.remove();
}, [canGoBack]);
```

**Apple TV:**
```typescript
// Menu button acts as back
useRemoteControl({
  onBack: () => {
    if (navigation.canGoBack()) {
      navigation.goBack();
    }
  },
});
```

## Platform-Specific Features

### Android TV / Fire TV Features

**Picture-in-Picture:**
```typescript
import { PictureInPicture } from 'react-native';

const enterPiP = async () => {
  if (Platform.OS === 'android') {
    await PictureInPicture.enter({
      aspectRatio: { numerator: 16, denominator: 9 },
    });
  }
};
```

**Recommendations Row:**
```typescript
// Add content to Android TV recommendations
import { TVRecommendations } from 'react-native';

TVRecommendations.addRecommendation({
  id: 'movie-123',
  title: 'Movie Title',
  description: 'Movie description',
  image: 'https://example.com/poster.jpg',
  contentType: 'movie',
});
```

### Apple TV Features

**Top Shelf:**
```typescript
// Configure tvOS Top Shelf content
// Configured in native iOS code (Info.plist)
```

**Siri Remote Gestures:**
```typescript
useRemoteControl({
  onSwipeUp: () => showMenu(),
  onSwipeDown: () => hideMenu(),
  onLongPress: () => showOptions(),
});
```

### Fire TV Vega Features

**Alexa Integration:**
```typescript
import { AlexaIntegration } from '@amazon-devices/kepler-sdk';

AlexaIntegration.registerVoiceCommand('play movie', () => {
  startPlayback();
});
```

## Testing Platform-Specific Code

### Manual Testing

**Android TV / Fire TV:**
- Test on Android TV emulator
- Test on physical Fire TV device
- Verify remote control buttons
- Check performance on resource-constrained devices

**Apple TV:**
- Test on tvOS simulator
- Test on physical Apple TV device
- Verify Siri Remote gestures
- Check Focus Engine behavior

**Fire TV Vega:**
- Test on Fire TV device with Vega OS
- Verify Vega SDK optimizations
- Test Alexa voice commands
- Check W3C Media API performance

### Automated Testing

```typescript
import { render } from '@testing-library/react-native';
import { Platform } from 'react-native';

describe('Platform-specific tests', () => {
  it('renders Android-specific component', () => {
    Platform.OS = 'android';
    const { getByTestId } = render(<MyComponent />);
    expect(getByTestId('android-feature')).toBeTruthy();
  });

  it('renders iOS-specific component', () => {
    Platform.OS = 'ios';
    const { getByTestId } = render(<MyComponent />);
    expect(getByTestId('ios-feature')).toBeTruthy();
  });
});
```

## Best Practices

### 1. Maintain Common Interface

```typescript
// All platform files should export same interface
export interface RemoteCallbacks {
  onPlayPause?: () => void;
  onBack?: () => void;
  // ... other callbacks
}

export function useRemoteControl(callbacks: RemoteCallbacks) {
  // Platform-specific implementation
}
```

### 2. Minimize Platform-Specific Code

```typescript
// ✅ Good - Share logic, platform-specific only where needed
const sharedLogic = () => {
  // Complex business logic
};

export function useRemoteControl(callbacks) {
  sharedLogic();
  // Platform-specific event handling
}

// ❌ Bad - Duplicating logic across platforms
// Each platform file has duplicate business logic
```

### 3. Document Platform Differences

```typescript
/**
 * Remote control hook
 *
 * Platform differences:
 * - Android: Uses react-native-keyevent for key events
 * - iOS: Uses TVEventHandler for remote events
 * - Vega: Uses TVEventHandler with Kepler SDK enhancements
 */
export function useRemoteControl(callbacks: RemoteCallbacks) {
  // Implementation
}
```

### 4. Test on Real Devices

- Simulators don't replicate all platform features
- Remote control behavior differs on real hardware
- Performance characteristics vary by device
- Native API availability may differ

### 5. Handle Platform Gracefully

```typescript
// ✅ Good - Graceful fallback
const handleFeature = () => {
  if (Platform.OS === 'android' && AndroidSpecificAPI) {
    AndroidSpecificAPI.doSomething();
  } else {
    // Fallback or alternative implementation
    genericImplementation();
  }
};

// ❌ Bad - Crashes on unsupported platform
const handleFeature = () => {
  AndroidSpecificAPI.doSomething(); // May not exist on iOS
};
```

## Common Pitfalls

### 1. Wrong Platform File Extension

```typescript
// ❌ Wrong - .android.tsx used for Fire TV Vega
// Vega uses .kepler.tsx, not .android.tsx

// ✅ Correct
RemoteControlManager.android.ts    // Android TV & Fire TV (Fire OS)
RemoteControlManager.kepler.ts     // Fire TV Vega OS
RemoteControlManager.ios.ts        // Apple TV
```

### 2. Assuming Platform Features

```typescript
// ❌ Wrong - Assumes feature exists
BackHandler.addEventListener('hardwareBackPress', handler);

// ✅ Correct - Check platform first
if (Platform.OS === 'android') {
  BackHandler.addEventListener('hardwareBackPress', handler);
}
```

### 3. Not Cleaning Up Platform Resources

```typescript
// ❌ Wrong - Memory leak
useEffect(() => {
  const handler = TVEventHandler();
  handler.enable(null, callback);
  // Missing cleanup
}, []);

// ✅ Correct - Proper cleanup
useEffect(() => {
  const handler = new TVEventHandler();
  handler.enable(null, callback);

  return () => {
    handler.disable(); // Cleanup
  };
}, []);
```

## Next Steps

- Review **spatial-navigation.md** for TV navigation patterns
- Review **video-playback.md** for platform-specific video implementations
- Review **tv-design-patterns.md** for platform-specific UI guidelines
