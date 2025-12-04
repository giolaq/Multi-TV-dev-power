# Spatial Navigation for TV

## Overview

Spatial navigation enables users to navigate TV apps using D-pad controls (up, down, left, right) instead of touch or mouse input. This is critical for TV applications where the primary input method is a remote control.

## React TV Space Navigation

### Core Concepts

**SpatialNavigationRoot**
- Top-level wrapper for spatial navigation
- Manages focus state across the app
- Required for spatial navigation to work

**SpatialNavigationNode**
- Represents a focusable group or region
- Can contain multiple focusable elements
- Handles navigation between child elements

**FocusablePressable**
- Focusable interactive element (custom component)
- Handles focus states and visual feedback
- Replacement for TouchableOpacity in TV apps

### Basic Setup

```typescript
import {
  SpatialNavigationRoot,
  SpatialNavigationNode,
} from 'react-tv-space-navigation';
import { FocusablePressable } from '@multi-tv/shared-ui';

function MyScreen() {
  return (
    <SpatialNavigationRoot>
      <SpatialNavigationNode>
        <FocusablePressable onPress={() => console.log('Pressed')}>
          <View style={styles.item}>
            <Text>Focusable Item</Text>
          </View>
        </FocusablePressable>
      </SpatialNavigationNode>
    </SpatialNavigationRoot>
  );
}
```

## FocusablePressable Component

### Implementation

Located in `packages/shared-ui/src/components/FocusablePressable.tsx`

**Key Features:**
- Focus state management
- Visual feedback when focused
- onPress handler for selection
- Customizable focus styles
- Remote control integration

**Usage:**
```typescript
import { FocusablePressable } from '@multi-tv/shared-ui';

<FocusablePressable
  onPress={handlePress}
  style={styles.button}
  focusedStyle={styles.buttonFocused}
>
  <Text>Click Me</Text>
</FocusablePressable>
```

**Focus Styling:**
```typescript
const styles = StyleSheet.create({
  button: {
    padding: spacing.md,
    backgroundColor: colors.surface,
    borderRadius: 8,
    borderWidth: 2,
    borderColor: 'transparent',
  },
  buttonFocused: {
    borderColor: colors.primary,
    backgroundColor: colors.primaryLight,
    transform: [{ scale: 1.05 }],
  },
});
```

### Focus State Best Practices

**1. Clear Visual Feedback**
- Always provide visible focus indication
- Use border, background color, or scale changes
- Minimum 2px border for focus state
- High contrast with background

**2. Size and Spacing**
- Minimum 80x80 logical pixels for focusable elements
- Adequate spacing between items (16px minimum)
- Larger touch targets than mobile apps

**3. Animations**
- Smooth transitions between focus states
- Use `transform: scale` for subtle emphasis
- Keep animations under 200ms for responsiveness

## Grid Layouts with Spatial Navigation

### Implementing Content Grids

**Example: Movie Grid**
```typescript
import { FlatList } from 'react-native';
import { SpatialNavigationNode } from 'react-tv-space-navigation';
import { FocusablePressable } from '@multi-tv/shared-ui';

function MovieGrid({ movies }) {
  const renderItem = ({ item }) => (
    <SpatialNavigationNode>
      <FocusablePressable onPress={() => navigateToDetails(item.id)}>
        <Image source={{ uri: item.poster }} style={styles.poster} />
        <Text style={styles.title}>{item.title}</Text>
      </FocusablePressable>
    </SpatialNavigationNode>
  );

  return (
    <SpatialNavigationRoot>
      <FlatList
        data={movies}
        renderItem={renderItem}
        numColumns={4}
        keyExtractor={(item) => item.id}
        contentContainerStyle={styles.grid}
      />
    </SpatialNavigationRoot>
  );
}

const styles = StyleSheet.create({
  grid: {
    padding: spacing.lg,
  },
  poster: {
    width: 200,
    height: 300,
    borderRadius: 8,
  },
  title: {
    ...typography.caption,
    marginTop: spacing.sm,
  },
});
```

### Grid Configuration

**Column Count:**
- 1080p (HD): 4-5 columns
- 4K (UHD): 6-8 columns
- Adjust based on content size

**Item Sizing:**
- Poster: 200x300 (2:3 aspect ratio)
- Landscape: 300x170 (16:9 aspect ratio)
- Square: 200x200 (1:1 aspect ratio)

**Spacing:**
- Between items: 16-24px
- Grid padding: 48-64px
- Row gap: 32-48px

## Remote Control Integration

### Platform-Specific Remote Control Managers

Located in `packages/shared-ui/src/app/remote-control/`

**RemoteControlManager.android.ts** (Android TV & Fire TV Fire OS)
```typescript
import { useEffect } from 'react';
import ReactNativeKeyEvent from 'react-native-keyevent';

export function useRemoteControl(callbacks) {
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
      }
    };

    ReactNativeKeyEvent.onKeyDownListener(handleKeyEvent);
    return () => ReactNativeKeyEvent.removeKeyDownListener();
  }, [callbacks]);
}
```

**RemoteControlManager.ios.ts** (Apple TV)
```typescript
import { useEffect } from 'react';
import { TVEventHandler } from 'react-native';

export function useRemoteControl(callbacks) {
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
      }
    });

    return () => tvEventHandler.disable();
  }, [callbacks]);
}
```

**RemoteControlManager.kepler.ts** (Fire TV Vega OS)
```typescript
import { useEffect } from 'react';
import { TVEventHandler } from 'react-native';

export function useRemoteControl(callbacks) {
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

    return () => tvEventHandler.disable();
  }, [callbacks]);
}
```

### Using Remote Control Managers

```typescript
import { useRemoteControl } from '@multi-tv/shared-ui';

function VideoPlayerScreen() {
  const remoteCallbacks = {
    onPlayPause: () => togglePlayback(),
    onRewind: () => seek(-10),
    onFastForward: () => seek(10),
    onBack: () => navigation.goBack(),
  };

  useRemoteControl(remoteCallbacks);

  return (
    <View>
      {/* Player UI */}
    </View>
  );
}
```

## Navigation Patterns

### Drawer Navigation

**Implementation:**
```typescript
import { DrawerNavigator } from '@multi-tv/shared-ui';
import { NavigationContainer } from '@react-navigation/native';

function App() {
  return (
    <NavigationContainer>
      <DrawerNavigator />
    </NavigationContainer>
  );
}
```

**Focus Management:**
- Drawer opens: Focus moves to first menu item
- Drawer closes: Focus returns to previous element
- Use `onFocus` and `onBlur` callbacks

**Custom Drawer Content:**
Located in `packages/shared-ui/src/components/CustomDrawerContent.tsx`

```typescript
function CustomDrawerContent({ navigation }) {
  return (
    <SpatialNavigationRoot>
      <SpatialNavigationNode>
        <FocusablePressable onPress={() => navigation.navigate('Home')}>
          <Text>Home</Text>
        </FocusablePressable>
        <FocusablePressable onPress={() => navigation.navigate('Explore')}>
          <Text>Explore</Text>
        </FocusablePressable>
        <FocusablePressable onPress={() => navigation.navigate('Settings')}>
          <Text>Settings</Text>
        </FocusablePressable>
      </SpatialNavigationNode>
    </SpatialNavigationRoot>
  );
}
```

### Stack Navigation

**Configuration:**
```typescript
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function AppNavigator() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Home" component={HomeScreen} />
      <Stack.Screen name="Details" component={DetailsScreen} />
      <Stack.Screen name="Player" component={PlayerScreen} />
    </Stack.Navigator>
  );
}
```

**Focus on Screen Change:**
- Reset focus to first element on screen enter
- Save focus position when leaving screen
- Restore focus when returning to screen

### Focus Trap for Modals

**Implementation:**
```typescript
import { SpatialNavigationNode } from 'react-tv-space-navigation';

function Modal({ visible, onClose }) {
  if (!visible) return null;

  return (
    <View style={styles.modalOverlay}>
      <SpatialNavigationNode isFocusBoundary>
        <View style={styles.modalContent}>
          <FocusablePressable onPress={onClose}>
            <Text>Close</Text>
          </FocusablePressable>
          <FocusablePressable onPress={handleConfirm}>
            <Text>Confirm</Text>
          </FocusablePressable>
        </View>
      </SpatialNavigationNode>
    </View>
  );
}
```

**Focus Boundary:**
- Use `isFocusBoundary` prop to trap focus
- Focus cannot leave modal until dismissed
- Essential for dialogs and overlays

## Advanced Navigation Patterns

### Horizontal Scrolling Lists

**Example: Featured Content Row**
```typescript
function FeaturedRow({ items }) {
  return (
    <SpatialNavigationNode orientation="horizontal">
      <FlatList
        horizontal
        data={items}
        renderItem={({ item }) => (
          <FocusablePressable onPress={() => selectItem(item)}>
            <Image source={{ uri: item.image }} style={styles.thumbnail} />
          </FocusablePressable>
        )}
        showsHorizontalScrollIndicator={false}
      />
    </SpatialNavigationNode>
  );
}
```

**Auto-Scroll on Focus:**
```typescript
import { useRef } from 'react';

function FeaturedRow({ items }) {
  const listRef = useRef(null);

  const handleFocus = (index) => {
    listRef.current?.scrollToIndex({ index, animated: true });
  };

  return (
    <FlatList
      ref={listRef}
      horizontal
      data={items}
      renderItem={({ item, index }) => (
        <FocusablePressable
          onPress={() => selectItem(item)}
          onFocus={() => handleFocus(index)}
        >
          <Image source={{ uri: item.image }} />
        </FocusablePressable>
      )}
    />
  );
}
```

### Multi-Row Grids

**Navigation Between Rows:**
```typescript
function ContentGrid() {
  return (
    <SpatialNavigationRoot>
      <View>
        {/* Row 1: Featured */}
        <SpatialNavigationNode orientation="horizontal">
          <FeaturedRow items={featuredItems} />
        </SpatialNavigationNode>

        {/* Row 2: Trending */}
        <SpatialNavigationNode orientation="horizontal">
          <FeaturedRow items={trendingItems} />
        </SpatialNavigationNode>

        {/* Row 3: Recently Added */}
        <SpatialNavigationNode orientation="horizontal">
          <FeaturedRow items={recentItems} />
        </SpatialNavigationNode>
      </View>
    </SpatialNavigationRoot>
  );
}
```

## Common Issues and Solutions

### Issue: Focus Not Moving Between Elements

**Causes:**
- Missing `SpatialNavigationNode` wrapper
- Not using `FocusablePressable` component
- Incorrect nesting of navigation components

**Solution:**
```typescript
// ❌ Wrong
<View>
  <TouchableOpacity onPress={handlePress}>
    <Text>Button</Text>
  </TouchableOpacity>
</View>

// ✅ Correct
<SpatialNavigationNode>
  <FocusablePressable onPress={handlePress}>
    <Text>Button</Text>
  </FocusablePressable>
</SpatialNavigationNode>
```

### Issue: Focus State Not Visible

**Causes:**
- Missing focus styles
- Low contrast between focused and unfocused states
- Not using `focusedStyle` prop

**Solution:**
```typescript
<FocusablePressable
  style={{
    borderWidth: 2,
    borderColor: 'transparent',
  }}
  focusedStyle={{
    borderColor: colors.primary,
    backgroundColor: colors.primaryLight,
  }}
>
  <Text>Visible Focus</Text>
</FocusablePressable>
```

### Issue: Remote Control Not Working

**Causes:**
- Remote control manager not initialized
- Platform-specific file not being resolved
- Missing remote control callbacks

**Solution:**
1. Verify platform-specific file exists (`.android.ts`, `.ios.ts`, `.kepler.ts`)
2. Initialize remote control manager in screen
3. Provide required callbacks
4. Test on actual device, not simulator

### Issue: Focus Getting Stuck

**Causes:**
- Focus boundary incorrectly configured
- Circular navigation not handled
- Disabled elements in focus path

**Solution:**
```typescript
// Handle circular navigation
<SpatialNavigationNode
  orientation="horizontal"
  isCircular={true} // Allows wrapping from last to first
>
  {items.map((item) => (
    <FocusablePressable key={item.id} onPress={() => select(item)}>
      <ItemView item={item} />
    </FocusablePressable>
  ))}
</SpatialNavigationNode>
```

## Testing Spatial Navigation

### Manual Testing Checklist

- [ ] Focus moves correctly with D-pad (up, down, left, right)
- [ ] Focus indicator clearly visible
- [ ] Select button activates focused element
- [ ] Back button returns to previous screen
- [ ] Focus restored when returning to screen
- [ ] No focus dead-ends (focus can always move)
- [ ] Modal focus traps work correctly
- [ ] Drawer navigation focus works
- [ ] Grid navigation works in all directions
- [ ] Horizontal scroll lists navigate properly

### Testing on Devices

**Android TV / Fire TV:**
- Use physical remote or ADB commands
- Test with different remote types (standard, game controller)

**Apple TV:**
- Use Siri Remote or standard Apple TV remote
- Test swipe gestures on Siri Remote

**Fire TV Vega:**
- Use Alexa Voice Remote
- Test voice commands integration

## Best Practices

### 1. Always Use FocusablePressable

```typescript
// ✅ Correct
<FocusablePressable onPress={handlePress}>
  <Text>Button</Text>
</FocusablePressable>

// ❌ Wrong
<TouchableOpacity onPress={handlePress}>
  <Text>Button</Text>
</TouchableOpacity>
```

### 2. Provide Clear Focus Indicators

```typescript
const styles = {
  button: {
    padding: spacing.md,
    borderWidth: 2,
    borderColor: 'transparent',
  },
  buttonFocused: {
    borderColor: colors.primary,
    borderWidth: 3,
    transform: [{ scale: 1.05 }],
  },
};
```

### 3. Test on Real Devices

- Simulators don't fully replicate remote control behavior
- Test with actual TV remotes
- Verify focus visibility from 10 feet away

### 4. Handle Back Button Gracefully

```typescript
useRemoteControl({
  onBack: () => {
    if (canGoBack) {
      navigation.goBack();
    } else {
      // Handle exit app or show exit confirmation
    }
  },
});
```

### 5. Optimize Grid Performance

```typescript
<FlatList
  data={items}
  renderItem={renderItem}
  removeClippedSubviews={true}
  maxToRenderPerBatch={10}
  windowSize={5}
  getItemLayout={(data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index,
  })}
/>
```

## Next Steps

- Review **video-playback.md** for integrating video controls
- Review **platform-specific.md** for platform customizations
- Review **tv-design-patterns.md** for UI/UX best practices
