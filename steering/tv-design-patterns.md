# TV Design Patterns and Best Practices

## Overview

Designing for TV requires different approaches than mobile or web. This guide covers the principles of 10-foot UI design, TV-safe zones, typography, spacing, and layout patterns specific to TV applications.

## 10-Foot UI Design Principles

### What is 10-Foot UI?

**Definition:** User interface designed to be viewed and interacted with from approximately 10 feet (3 meters) away, typical distance from a TV screen.

**Key Differences from Mobile/Desktop:**
- **Larger Elements**: Everything needs to be bigger and more visible
- **Remote Control Navigation**: D-pad/arrow keys instead of touch or mouse
- **Limited Precision**: Coarse navigation vs. pixel-perfect pointing
- **Lean-Back Experience**: Casual, relaxed interaction vs. lean-forward engagement
- **Focus-Driven**: Clear indication of which element is currently selected

### Core Principles

**1. Bigger is Better**
- Minimum font size: 24px for body text
- Heading sizes: 36px and above
- Minimum touch target: 80x80 logical pixels
- Generous spacing between elements

**2. High Contrast**
- Strong contrast between text and background
- Clear visual hierarchy
- Avoid subtle color differences
- Use bold, distinct colors for focus states

**3. Clear Focus Indicators**
- Always visible focus state
- High-contrast borders or backgrounds
- Scale or shadow effects for emphasis
- Smooth animations between focus changes

**4. Simple Navigation**
- Predictable navigation flow
- Minimal nesting of menus
- Clear path forward and backward
- Logical spatial relationships

**5. Minimize Text Input**
- Avoid forms and text entry when possible
- Use pickers, toggles, and selections instead
- If text input required, use on-screen keyboard
- Save input history for reuse

## TV-Safe Zones

### What are Safe Zones?

**Title Safe Zone:** Area where all important content (text, logos) must be placed
**Action Safe Zone:** Area where all interactive elements must be placed

**Why Needed:**
- Some TVs overscan (crop edges of image)
- Older TVs may have curved edges
- Bezels can obscure content
- Ensure content visible on all TV models

### Safe Zone Dimensions

**Recommended Safe Zone:**
- **Title Safe:** 5% margin on all sides (90% of screen)
- **Action Safe:** 10% margin on all sides (80% of screen)

**Implementation:**
Located in `packages/shared-ui/src/theme/safeZones.ts`

```typescript
import { Dimensions } from 'react-native';

const { width, height } = Dimensions.get('window');

export const safeZones = {
  // Title safe zone (90% of screen)
  titleSafe: {
    marginHorizontal: width * 0.05,
    marginVertical: height * 0.05,
  },

  // Action safe zone (80% of screen)
  actionSafe: {
    marginHorizontal: width * 0.1,
    marginVertical: height * 0.1,
  },

  // Commonly used values
  horizontal: width * 0.05,
  vertical: height * 0.05,
  horizontalLarge: width * 0.1,
  verticalLarge: height * 0.1,
};
```

**Usage:**
```typescript
import { safeZones } from '@multi-tv/shared-ui';

const styles = StyleSheet.create({
  container: {
    marginHorizontal: safeZones.horizontal,
    marginVertical: safeZones.vertical,
  },
  criticalContent: {
    marginHorizontal: safeZones.horizontalLarge,
    marginVertical: safeZones.verticalLarge,
  },
});
```

## Typography for TV

### Font Sizes

Located in `packages/shared-ui/src/theme/typography.ts`

```typescript
export const typography = {
  // Headings
  heading1: {
    fontSize: 48,
    fontWeight: '700',
    lineHeight: 56,
  },
  heading2: {
    fontSize: 40,
    fontWeight: '700',
    lineHeight: 48,
  },
  heading3: {
    fontSize: 32,
    fontWeight: '600',
    lineHeight: 40,
  },

  // Body text
  body: {
    fontSize: 24,
    fontWeight: '400',
    lineHeight: 32,
  },
  bodyLarge: {
    fontSize: 28,
    fontWeight: '400',
    lineHeight: 36,
  },

  // Captions and labels
  caption: {
    fontSize: 20,
    fontWeight: '400',
    lineHeight: 28,
  },
  label: {
    fontSize: 22,
    fontWeight: '500',
    lineHeight: 28,
  },

  // Buttons
  button: {
    fontSize: 26,
    fontWeight: '600',
    lineHeight: 32,
  },
};
```

### Font Guidelines

**Minimum Sizes:**
- Body text: 24px minimum
- Captions: 20px minimum
- Headings: 32px minimum
- Buttons: 26px minimum

**Readability:**
- Use sans-serif fonts (better on low-resolution screens)
- Avoid thin font weights (minimum 400)
- Prefer bold weights for emphasis (600-700)
- Adequate line height (1.3-1.5x font size)

**Contrast:**
- White text on dark backgrounds (most common)
- Minimum contrast ratio: 4.5:1
- Avoid pure black backgrounds (use dark gray #0a0a0a or #121212)
- Avoid pure white text (use #f5f5f5 or #e0e0e0)

## Spacing and Layout

### Spacing Scale

Located in `packages/shared-ui/src/theme/spacing.ts`

```typescript
export const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48,
  xxxl: 64,
};
```

### Layout Guidelines

**Grid Spacing:**
- Between items: 16-24px (spacing.md - spacing.lg)
- Between rows: 32-48px (spacing.xl - spacing.xxl)
- Screen padding: 48-64px (spacing.xxl - spacing.xxxl)

**Component Spacing:**
- Button padding: 16-24px (spacing.md - spacing.lg)
- Card padding: 24-32px (spacing.lg - spacing.xl)
- Section spacing: 48-64px (spacing.xxl - spacing.xxxl)

**Touch Targets:**
- Minimum size: 80x80px
- Recommended size: 100x100px or larger
- Spacing between targets: 16px minimum

## Color System

### Color Palette

Located in `packages/shared-ui/src/theme/colors.ts`

```typescript
export const colors = {
  // Primary colors
  primary: '#6200EE',
  primaryLight: '#8E24FF',
  primaryDark: '#3700B3',

  // Surface colors
  background: '#0a0a0a',
  surface: '#1E1E1E',
  surfaceVariant: '#2C2C2C',

  // Text colors
  text: '#E0E0E0',
  textSecondary: '#B0B0B0',
  textTertiary: '#808080',

  // State colors
  error: '#CF6679',
  success: '#4CAF50',
  warning: '#FFC107',

  // Focus state
  focus: '#BB86FC',
  focusBackground: 'rgba(187, 134, 252, 0.12)',
};
```

### Color Guidelines

**Dark Theme (Recommended):**
- Dark backgrounds reduce eye strain in dim viewing environments
- Better contrast for content (posters, videos)
- Common in streaming apps (Netflix, Hulu, etc.)

**Contrast Requirements:**
- Text: 4.5:1 minimum contrast ratio
- Focus indicators: 3:1 minimum with background
- Interactive elements: High-contrast colors

**Focus States:**
- Use bright, saturated colors (primary color)
- Add glow or shadow for emphasis
- Ensure visible from 10 feet away

## Common Layout Patterns

### Content Grid

**Use Case:** Browse movies, shows, or content items

```typescript
function ContentGrid({ items }) {
  return (
    <View style={styles.container}>
      <FlatList
        data={items}
        numColumns={4}
        renderItem={({ item }) => (
          <FocusablePressable onPress={() => select(item)}>
            <Image source={{ uri: item.poster }} style={styles.poster} />
            <Text style={styles.title}>{item.title}</Text>
          </FocusablePressable>
        )}
        contentContainerStyle={styles.grid}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingHorizontal: safeZones.horizontal,
  },
  grid: {
    padding: spacing.lg,
  },
  poster: {
    width: 200,
    height: 300,
    borderRadius: 8,
    marginBottom: spacing.sm,
  },
  title: {
    ...typography.caption,
    color: colors.text,
  },
});
```

**Grid Configuration:**
- 1080p: 4-5 columns
- 4K: 6-8 columns
- Item spacing: 16-24px
- Row spacing: 32-48px

### Horizontal Carousel

**Use Case:** Featured content, categories, recommendations

```typescript
function Carousel({ title, items }) {
  return (
    <View style={styles.container}>
      <Text style={styles.heading}>{title}</Text>
      <FlatList
        horizontal
        data={items}
        renderItem={({ item }) => (
          <FocusablePressable onPress={() => select(item)}>
            <Image source={{ uri: item.thumbnail }} style={styles.thumbnail} />
          </FocusablePressable>
        )}
        showsHorizontalScrollIndicator={false}
        contentContainerStyle={styles.carousel}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    marginBottom: spacing.xl,
  },
  heading: {
    ...typography.heading3,
    color: colors.text,
    marginBottom: spacing.md,
    marginLeft: safeZones.horizontal,
  },
  carousel: {
    paddingHorizontal: safeZones.horizontal,
  },
  thumbnail: {
    width: 300,
    height: 170,
    borderRadius: 8,
    marginRight: spacing.md,
  },
});
```

**Carousel Guidelines:**
- Thumbnail size: 300x170 (16:9) or 200x300 (2:3)
- Item spacing: 16px
- Auto-scroll on focus
- Show 3-5 items on screen simultaneously

### Hero Banner

**Use Case:** Featured content, promotions, highlights

```typescript
function HeroBanner({ item }) {
  return (
    <View style={styles.container}>
      <Image source={{ uri: item.backdrop }} style={styles.backdrop} />
      <View style={styles.overlay}>
        <View style={styles.content}>
          <Text style={styles.title}>{item.title}</Text>
          <Text style={styles.description}>{item.description}</Text>
          <View style={styles.actions}>
            <FocusablePressable onPress={() => play(item)} style={styles.playButton}>
              <Text style={styles.buttonText}>▶ Play</Text>
            </FocusablePressable>
            <FocusablePressable onPress={() => showInfo(item)} style={styles.infoButton}>
              <Text style={styles.buttonText}>ℹ More Info</Text>
            </FocusablePressable>
          </View>
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    height: 600,
    marginBottom: spacing.xl,
  },
  backdrop: {
    width: '100%',
    height: '100%',
    position: 'absolute',
  },
  overlay: {
    flex: 1,
    backgroundColor: 'rgba(0, 0, 0, 0.4)',
    justifyContent: 'flex-end',
    paddingBottom: spacing.xxxl,
    paddingHorizontal: safeZones.horizontal,
  },
  content: {
    maxWidth: '50%',
  },
  title: {
    ...typography.heading1,
    color: colors.text,
    marginBottom: spacing.md,
  },
  description: {
    ...typography.body,
    color: colors.textSecondary,
    marginBottom: spacing.lg,
  },
  actions: {
    flexDirection: 'row',
  },
  playButton: {
    backgroundColor: colors.primary,
    paddingHorizontal: spacing.xl,
    paddingVertical: spacing.md,
    borderRadius: 8,
    marginRight: spacing.md,
  },
  infoButton: {
    backgroundColor: colors.surfaceVariant,
    paddingHorizontal: spacing.xl,
    paddingVertical: spacing.md,
    borderRadius: 8,
  },
  buttonText: {
    ...typography.button,
    color: colors.text,
  },
});
```

**Hero Guidelines:**
- Height: 500-700px
- Content on bottom-left (title safe zone)
- Dark gradient overlay for text readability
- 2-3 primary actions maximum

### Detail Screen

**Use Case:** Content details, metadata, actions

```typescript
function DetailScreen({ item }) {
  return (
    <ScrollView style={styles.container}>
      <Image source={{ uri: item.backdrop }} style={styles.backdrop} />

      <View style={styles.content}>
        <Image source={{ uri: item.poster }} style={styles.poster} />

        <View style={styles.info}>
          <Text style={styles.title}>{item.title}</Text>
          <View style={styles.metadata}>
            <Text style={styles.metadataText}>{item.year}</Text>
            <Text style={styles.metadataText}>•</Text>
            <Text style={styles.metadataText}>{item.rating}</Text>
            <Text style={styles.metadataText}>•</Text>
            <Text style={styles.metadataText}>{item.duration}</Text>
          </View>
          <Text style={styles.description}>{item.description}</Text>

          <View style={styles.actions}>
            <FocusablePressable onPress={() => play(item)} style={styles.playButton}>
              <Text>▶ Play</Text>
            </FocusablePressable>
            <FocusablePressable onPress={() => addToList(item)} style={styles.addButton}>
              <Text>+ My List</Text>
            </FocusablePressable>
          </View>
        </View>
      </View>

      {/* Similar content section */}
      <Carousel title="Similar Titles" items={similarItems} />
    </ScrollView>
  );
}
```

**Detail Screen Guidelines:**
- Backdrop at top
- Poster on left, info on right
- Clear hierarchy: title → metadata → description → actions
- Similar content recommendations at bottom

### Settings Screen

**Use Case:** App settings, preferences, account

```typescript
function SettingsScreen() {
  return (
    <ScrollView style={styles.container}>
      <Text style={styles.heading}>Settings</Text>

      <View style={styles.section}>
        <Text style={styles.sectionTitle}>Video Quality</Text>
        <FocusablePressable onPress={() => selectQuality('auto')}>
          <Text>Auto</Text>
        </FocusablePressable>
        <FocusablePressable onPress={() => selectQuality('high')}>
          <Text>High</Text>
        </FocusablePressable>
        <FocusablePressable onPress={() => selectQuality('low')}>
          <Text>Low</Text>
        </FocusablePressable>
      </View>

      <View style={styles.section}>
        <Text style={styles.sectionTitle}>Audio</Text>
        <FocusablePressable onPress={() => toggleSubtitles()}>
          <Text>Subtitles: {subtitlesEnabled ? 'On' : 'Off'}</Text>
        </FocusablePressable>
      </View>
    </ScrollView>
  );
}
```

**Settings Guidelines:**
- Group related settings
- Use toggles and pickers (avoid text input)
- Clear section headings
- Immediate visual feedback on changes

## Responsive Scaling

### useScale Hook

Located in `packages/shared-ui/src/hooks/useScale.ts`

```typescript
import { Dimensions } from 'react-native';

const DESIGN_WIDTH = 1920; // Design based on 1080p width
const DESIGN_HEIGHT = 1080;

export function useScale() {
  const { width, height } = Dimensions.get('window');

  const scaleWidth = width / DESIGN_WIDTH;
  const scaleHeight = height / DESIGN_HEIGHT;
  const scale = Math.min(scaleWidth, scaleHeight);

  return (size: number) => Math.round(size * scale);
}
```

**Usage:**
```typescript
function MyComponent() {
  const scale = useScale();

  return (
    <View style={{
      width: scale(200),
      height: scale(300),
      padding: scale(16),
    }}>
      <Text style={{ fontSize: scale(24) }}>Scaled Text</Text>
    </View>
  );
}
```

**When to Use:**
- Scaling for different screen sizes (1080p vs 4K)
- Maintaining consistent visual proportions
- Adapting to various TV models

**When NOT to Use:**
- Typography (use theme typography instead)
- Spacing (use theme spacing instead)
- Simple layouts (use flex instead)

## Animation and Transitions

### Focus Animations

```typescript
const styles = StyleSheet.create({
  button: {
    padding: spacing.md,
    borderWidth: 2,
    borderColor: 'transparent',
    transition: 'all 0.2s ease',
  },
  buttonFocused: {
    borderColor: colors.focus,
    backgroundColor: colors.focusBackground,
    transform: [{ scale: 1.05 }],
    shadowColor: colors.focus,
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.5,
    shadowRadius: 8,
  },
});
```

### Screen Transitions

```typescript
import { createStackNavigator, TransitionPresets } from '@react-navigation/stack';

const Stack = createStackNavigator();

<Stack.Navigator
  screenOptions={{
    ...TransitionPresets.SlideFromRightIOS,
    animationEnabled: true,
    transitionSpec: {
      open: { animation: 'timing', config: { duration: 300 } },
      close: { animation: 'timing', config: { duration: 300 } },
    },
  }}
>
  <Stack.Screen name="Home" component={HomeScreen} />
</Stack.Navigator>
```

**Animation Guidelines:**
- Keep animations under 300ms
- Use easing functions (ease-in-out)
- Avoid complex animations (performance)
- Provide immediate visual feedback

## Accessibility

### Focus Management

- Always visible focus indicators
- Logical focus order (left-to-right, top-to-bottom)
- No focus traps (can always navigate out)
- Focus restoration when returning to screens

### High Contrast

- Minimum 4.5:1 contrast ratio for text
- 3:1 for UI components
- Support high-contrast mode if available

### Text Size

- Support system-wide text size settings
- Scalable fonts
- Minimum readable sizes

## Best Practices Summary

### ✅ Do:

1. **Use large, readable fonts** (24px minimum for body text)
2. **Provide clear focus indicators** (borders, backgrounds, scale)
3. **Keep content in safe zones** (5-10% margin on all sides)
4. **Use high contrast** (dark backgrounds, light text)
5. **Design for remote control** (D-pad navigation, not touch)
6. **Test on actual TVs** (10 feet away viewing distance)
7. **Minimize text input** (use pickers and toggles)
8. **Keep navigation simple** (minimal nesting, clear hierarchy)
9. **Use generous spacing** (16-24px between items)
10. **Optimize for lean-back experience** (casual, relaxed interaction)

### ❌ Don't:

1. **Use small fonts** (under 20px)
2. **Assume touch input** (design for remote control)
3. **Place critical content near edges** (may be cropped on some TVs)
4. **Use low contrast colors** (hard to see from distance)
5. **Create complex navigation** (confusing on TV)
6. **Require text input frequently** (difficult with remote)
7. **Use subtle animations** (hard to see from 10 feet)
8. **Ignore safe zones** (content may be cut off)
9. **Design for mobile first** (TV has different constraints)
10. **Skip testing on real TVs** (simulators don't replicate experience)

## Testing Your Design

### Visual Testing Checklist

- [ ] All text readable from 10 feet away
- [ ] Focus indicators clearly visible
- [ ] All content within safe zones
- [ ] High contrast between elements
- [ ] Touch targets at least 80x80px
- [ ] Adequate spacing between elements
- [ ] Navigation flows logically
- [ ] Animations smooth and performant
- [ ] No focus dead-ends
- [ ] Back button works consistently

### Device Testing

- Test on multiple TV sizes (40", 55", 65"+)
- Test on different resolutions (1080p, 4K)
- Test in different lighting conditions
- Test from actual viewing distance (10 feet)
- Test on different TV brands (overscan varies)

## Next Steps

- Review **spatial-navigation.md** for navigation implementation
- Review **video-playback.md** for video UI patterns
- Review **platform-specific.md** for platform variations
