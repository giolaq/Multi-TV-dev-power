# Video Playback for TV

## Overview

Video playback is a core feature of TV applications. This guide covers implementing video players with remote control integration, custom overlays, and platform-specific optimizations.

## Platform-Specific Video Players

### Universal App (expo-multi-tv)

Uses **react-native-video** for iOS, Android, and Web platforms.

**Installation:**
```bash
yarn workspace expo-multi-tv add react-native-video
```

**PlayerScreen.tsx Implementation:**
```typescript
import React, { useState, useRef } from 'react';
import { View, StyleSheet } from 'react-native';
import Video from 'react-native-video';
import { useRemoteControl } from '@multi-tv/shared-ui';

export function PlayerScreen({ route }) {
  const { videoUrl, title } = route.params;
  const videoRef = useRef(null);
  const [paused, setPaused] = useState(false);
  const [currentTime, setCurrentTime] = useState(0);
  const [duration, setDuration] = useState(0);

  // Remote control integration
  useRemoteControl({
    onPlayPause: () => setPaused(!paused),
    onRewind: () => seekTo(currentTime - 10),
    onFastForward: () => seekTo(currentTime + 10),
    onBack: () => navigation.goBack(),
  });

  const seekTo = (time) => {
    videoRef.current?.seek(Math.max(0, Math.min(time, duration)));
  };

  return (
    <View style={styles.container}>
      <Video
        ref={videoRef}
        source={{ uri: videoUrl }}
        style={styles.video}
        paused={paused}
        resizeMode="contain"
        onLoad={(data) => setDuration(data.duration)}
        onProgress={(data) => setCurrentTime(data.currentTime)}
        onEnd={() => setPaused(true)}
        onError={(error) => console.error('Video error:', error)}
      />
      <ControlOverlay
        paused={paused}
        currentTime={currentTime}
        duration={duration}
        onPlayPause={() => setPaused(!paused)}
        onSeek={seekTo}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#000',
  },
  video: {
    width: '100%',
    height: '100%',
  },
});
```

### Vega App (Fire TV Vega OS)

Uses **W3C Media APIs** for optimized Fire TV performance.

**PlayerScreen.vega.tsx Implementation:**
```typescript
import React, { useState, useEffect, useRef } from 'react';
import { View, StyleSheet } from 'react-native';
import { useRemoteControl } from '@multi-tv/shared-ui';

export function PlayerScreen({ route }) {
  const { videoUrl, title } = route.params;
  const [paused, setPaused] = useState(false);
  const [currentTime, setCurrentTime] = useState(0);
  const [duration, setDuration] = useState(0);
  const videoElement = useRef(null);

  useEffect(() => {
    // Initialize W3C Media API video element
    videoElement.current = document.createElement('video');
    videoElement.current.src = videoUrl;
    videoElement.current.style.width = '100%';
    videoElement.current.style.height = '100%';

    // Event listeners
    videoElement.current.addEventListener('loadedmetadata', () => {
      setDuration(videoElement.current.duration);
    });

    videoElement.current.addEventListener('timeupdate', () => {
      setCurrentTime(videoElement.current.currentTime);
    });

    videoElement.current.addEventListener('ended', () => {
      setPaused(true);
    });

    return () => {
      videoElement.current?.pause();
      videoElement.current = null;
    };
  }, [videoUrl]);

  useRemoteControl({
    onPlayPause: () => {
      if (paused) {
        videoElement.current?.play();
      } else {
        videoElement.current?.pause();
      }
      setPaused(!paused);
    },
    onRewind: () => {
      videoElement.current.currentTime -= 10;
    },
    onFastForward: () => {
      videoElement.current.currentTime += 10;
    },
    onBack: () => navigation.goBack(),
  });

  return (
    <View style={styles.container}>
      {/* W3C video element mounted here */}
      <ControlOverlay
        paused={paused}
        currentTime={currentTime}
        duration={duration}
        onPlayPause={() => togglePlayback()}
        onSeek={(time) => (videoElement.current.currentTime = time)}
      />
    </View>
  );
}
```

## Control Overlay Component

### Implementation

Located in `packages/shared-ui/src/components/player/ControlOverlay.tsx`

```typescript
import React, { useState } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { SpatialNavigationNode } from 'react-tv-space-navigation';
import { FocusablePressable, colors, spacing } from '@multi-tv/shared-ui';

interface ControlOverlayProps {
  paused: boolean;
  currentTime: number;
  duration: number;
  onPlayPause: () => void;
  onSeek: (time: number) => void;
}

export function ControlOverlay({
  paused,
  currentTime,
  duration,
  onPlayPause,
  onSeek,
}: ControlOverlayProps) {
  const [visible, setVisible] = useState(true);

  const formatTime = (seconds: number) => {
    const mins = Math.floor(seconds / 60);
    const secs = Math.floor(seconds % 60);
    return `${mins}:${secs.toString().padStart(2, '0')}`;
  };

  const handleRewind = () => onSeek(Math.max(0, currentTime - 10));
  const handleFastForward = () => onSeek(Math.min(duration, currentTime + 10));

  return (
    <View style={[styles.overlay, !visible && styles.hidden]}>
      <View style={styles.controls}>
        <SpatialNavigationNode orientation="horizontal">
          <FocusablePressable
            onPress={handleRewind}
            style={styles.button}
            focusedStyle={styles.buttonFocused}
          >
            <Text style={styles.buttonText}>⏪</Text>
          </FocusablePressable>

          <FocusablePressable
            onPress={onPlayPause}
            style={styles.button}
            focusedStyle={styles.buttonFocused}
          >
            <Text style={styles.buttonText}>{paused ? '▶' : '⏸'}</Text>
          </FocusablePressable>

          <FocusablePressable
            onPress={handleFastForward}
            style={styles.button}
            focusedStyle={styles.buttonFocused}
          >
            <Text style={styles.buttonText}>⏩</Text>
          </FocusablePressable>
        </SpatialNavigationNode>
      </View>

      <View style={styles.progressBar}>
        <View
          style={[
            styles.progressFilled,
            { width: `${(currentTime / duration) * 100}%` },
          ]}
        />
      </View>

      <View style={styles.timeInfo}>
        <Text style={styles.timeText}>{formatTime(currentTime)}</Text>
        <Text style={styles.timeText}>{formatTime(duration)}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  overlay: {
    position: 'absolute',
    bottom: 0,
    left: 0,
    right: 0,
    backgroundColor: 'rgba(0, 0, 0, 0.7)',
    padding: spacing.lg,
  },
  hidden: {
    opacity: 0,
  },
  controls: {
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center',
    marginBottom: spacing.md,
  },
  button: {
    width: 80,
    height: 80,
    backgroundColor: colors.surface,
    borderRadius: 40,
    justifyContent: 'center',
    alignItems: 'center',
    marginHorizontal: spacing.md,
    borderWidth: 2,
    borderColor: 'transparent',
  },
  buttonFocused: {
    borderColor: colors.primary,
    backgroundColor: colors.primaryLight,
    transform: [{ scale: 1.1 }],
  },
  buttonText: {
    fontSize: 32,
    color: colors.text,
  },
  progressBar: {
    height: 8,
    backgroundColor: colors.surfaceVariant,
    borderRadius: 4,
    overflow: 'hidden',
    marginBottom: spacing.sm,
  },
  progressFilled: {
    height: '100%',
    backgroundColor: colors.primary,
  },
  timeInfo: {
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
  timeText: {
    color: colors.text,
    fontSize: 18,
  },
});
```

## Remote Control Integration for Video

### Playback Controls

**Standard Remote Buttons:**
- **Play/Pause**: Toggle playback
- **Rewind**: Seek backward 10 seconds
- **Fast Forward**: Seek forward 10 seconds
- **Back**: Exit player and return to previous screen

**Implementation:**
```typescript
const remoteCallbacks = {
  onPlayPause: () => {
    setPaused(!paused);
    showControls();
  },
  onRewind: () => {
    seekTo(currentTime - 10);
    showControls();
  },
  onFastForward: () => {
    seekTo(currentTime + 10);
    showControls();
  },
  onBack: () => {
    videoRef.current?.pause();
    navigation.goBack();
  },
};

useRemoteControl(remoteCallbacks);
```

### Advanced Seeking

**Variable Seek Intervals:**
```typescript
const [seekInterval, setSeekInterval] = useState(10);

const handleRewind = () => {
  seekTo(currentTime - seekInterval);
  // Double interval on consecutive presses
  setSeekInterval((prev) => Math.min(prev * 2, 60));
};

const handleFastForward = () => {
  seekTo(currentTime + seekInterval);
  setSeekInterval((prev) => Math.min(prev * 2, 60));
};

// Reset interval on other actions
useEffect(() => {
  const timer = setTimeout(() => setSeekInterval(10), 2000);
  return () => clearTimeout(timer);
}, [currentTime]);
```

### Control Overlay Auto-Hide

**Auto-hide After Inactivity:**
```typescript
function ControlOverlay({ paused, ...props }) {
  const [visible, setVisible] = useState(true);
  const hideTimer = useRef(null);

  const showControls = () => {
    setVisible(true);
    resetHideTimer();
  };

  const resetHideTimer = () => {
    if (hideTimer.current) {
      clearTimeout(hideTimer.current);
    }

    // Auto-hide after 5 seconds
    hideTimer.current = setTimeout(() => {
      if (!paused) {
        setVisible(false);
      }
    }, 5000);
  };

  useEffect(() => {
    resetHideTimer();
    return () => {
      if (hideTimer.current) {
        clearTimeout(hideTimer.current);
      }
    };
  }, [paused]);

  // Show controls on any remote button press
  useRemoteControl({
    onUp: showControls,
    onDown: showControls,
    onLeft: showControls,
    onRight: showControls,
    onSelect: showControls,
  });

  return (
    <View style={[styles.overlay, !visible && styles.hidden]}>
      {/* Controls */}
    </View>
  );
}
```

## Video Player Features

### Fullscreen Mode

```typescript
function PlayerScreen() {
  const [fullscreen, setFullscreen] = useState(true);

  useEffect(() => {
    // Enter fullscreen on mount
    StatusBar.setHidden(true);

    return () => {
      // Exit fullscreen on unmount
      StatusBar.setHidden(false);
    };
  }, []);

  return (
    <View style={styles.container}>
      <Video
        source={{ uri: videoUrl }}
        style={styles.video}
        fullscreen={fullscreen}
        fullscreenOrientation="landscape"
      />
    </View>
  );
}
```

### Resume Playback

**Save and Restore Position:**
```typescript
import AsyncStorage from '@react-native-async-storage/async-storage';

function PlayerScreen({ route }) {
  const { videoId, videoUrl } = route.params;
  const [initialTime, setInitialTime] = useState(0);

  useEffect(() => {
    // Load saved position
    const loadPosition = async () => {
      const saved = await AsyncStorage.getItem(`video_${videoId}_position`);
      if (saved) {
        setInitialTime(parseFloat(saved));
      }
    };

    loadPosition();
  }, [videoId]);

  const savePosition = async (time: number) => {
    await AsyncStorage.setItem(`video_${videoId}_position`, time.toString());
  };

  return (
    <Video
      source={{ uri: videoUrl }}
      onLoad={() => {
        if (initialTime > 0) {
          videoRef.current?.seek(initialTime);
        }
      }}
      onProgress={(data) => {
        setCurrentTime(data.currentTime);
        // Save position every 5 seconds
        if (Math.floor(data.currentTime) % 5 === 0) {
          savePosition(data.currentTime);
        }
      }}
      onEnd={() => {
        // Clear saved position when video ends
        AsyncStorage.removeItem(`video_${videoId}_position`);
      }}
    />
  );
}
```

### Error Handling

```typescript
function PlayerScreen() {
  const [error, setError] = useState(null);
  const [retryCount, setRetryCount] = useState(0);

  const handleError = (err) => {
    console.error('Video playback error:', err);
    setError(err);
  };

  const handleRetry = () => {
    setError(null);
    setRetryCount((prev) => prev + 1);
  };

  if (error) {
    return (
      <View style={styles.errorContainer}>
        <Text style={styles.errorText}>
          Unable to play video
        </Text>
        <FocusablePressable onPress={handleRetry}>
          <Text>Retry</Text>
        </FocusablePressable>
        <FocusablePressable onPress={() => navigation.goBack()}>
          <Text>Go Back</Text>
        </FocusablePressable>
      </View>
    );
  }

  return (
    <Video
      key={retryCount} // Force re-render on retry
      source={{ uri: videoUrl }}
      onError={handleError}
    />
  );
}
```

### Buffering Indicator

```typescript
function PlayerScreen() {
  const [buffering, setBuffering] = useState(false);

  return (
    <View style={styles.container}>
      <Video
        source={{ uri: videoUrl }}
        onLoadStart={() => setBuffering(true)}
        onLoad={() => setBuffering(false)}
        onBuffer={({ isBuffering }) => setBuffering(isBuffering)}
      />

      {buffering && (
        <View style={styles.bufferingOverlay}>
          <ActivityIndicator size="large" color={colors.primary} />
          <Text style={styles.bufferingText}>Loading...</Text>
        </View>
      )}
    </View>
  );
}
```

## Video Formats and Streaming

### Supported Formats

**react-native-video (Universal App):**
- MP4 (H.264 + AAC)
- HLS (.m3u8) for adaptive streaming
- DASH for Android

**W3C Media APIs (Vega App):**
- MP4 (H.264 + AAC)
- HLS (.m3u8)
- WebM (VP8/VP9)

### HLS Adaptive Streaming

```typescript
<Video
  source={{
    uri: 'https://example.com/video/playlist.m3u8',
  }}
  resizeMode="contain"
  // HLS automatically adjusts quality based on bandwidth
/>
```

### Multiple Quality Options

```typescript
function PlayerScreen() {
  const [quality, setQuality] = useState('auto');

  const videoSources = {
    auto: 'https://example.com/video/playlist.m3u8',
    '1080p': 'https://example.com/video/1080p.mp4',
    '720p': 'https://example.com/video/720p.mp4',
    '480p': 'https://example.com/video/480p.mp4',
  };

  return (
    <Video
      source={{ uri: videoSources[quality] }}
    />
  );
}
```

## Performance Optimization

### Preloading

```typescript
function DetailsScreen({ movie }) {
  useEffect(() => {
    // Preload video metadata
    if (movie.videoUrl) {
      const preloader = new Video({
        source: { uri: movie.videoUrl },
        paused: true,
      });
    }
  }, [movie.videoUrl]);

  return (
    <FocusablePressable onPress={() => navigation.navigate('Player', { videoUrl: movie.videoUrl })}>
      <Text>Play Now</Text>
    </FocusablePressable>
  );
}
```

### Memory Management

```typescript
function PlayerScreen() {
  const videoRef = useRef(null);

  useEffect(() => {
    return () => {
      // Cleanup on unmount
      videoRef.current?.pause();
      videoRef.current = null;
    };
  }, []);

  return (
    <Video
      ref={videoRef}
      source={{ uri: videoUrl }}
      onEnd={() => {
        // Release resources
        videoRef.current?.pause();
      }}
    />
  );
}
```

## Testing Video Playback

### Manual Testing Checklist

- [ ] Video plays on all target platforms
- [ ] Remote control buttons work (play/pause, rewind, fast-forward)
- [ ] Progress bar updates correctly
- [ ] Time display shows accurate current/total time
- [ ] Controls auto-hide during playback
- [ ] Controls show on remote button press
- [ ] Fullscreen mode works
- [ ] Back button exits player
- [ ] Error handling displays message
- [ ] Buffering indicator shows during loading
- [ ] Resume playback from saved position
- [ ] Video quality appropriate for platform

### Platform-Specific Testing

**Android TV / Fire TV:**
- Test with different video codecs
- Verify HLS streaming works
- Check memory usage during playback

**Apple TV:**
- Test with Siri Remote gestures
- Verify AirPlay compatibility
- Check Picture-in-Picture support

**Fire TV Vega:**
- Test W3C Media API implementation
- Verify performance optimizations
- Check Alexa voice control integration

## Best Practices

### 1. Always Handle Errors

```typescript
<Video
  source={{ uri: videoUrl }}
  onError={(error) => {
    console.error('Video error:', error);
    showErrorMessage();
  }}
/>
```

### 2. Provide Visual Feedback

- Show buffering indicator during loading
- Display playback progress
- Show clear play/pause state

### 3. Optimize for TV Screens

- Use large, readable fonts for time display
- Ensure controls are easily visible from 10 feet away
- Provide clear focus indicators

### 4. Test on Real Devices

- Simulators don't replicate video performance accurately
- Test with actual video files, not just test URLs
- Verify streaming performance on real network conditions

### 5. Implement Resume Playback

- Save playback position periodically
- Restore position on app relaunch
- Clear saved position when video completes

## Next Steps

- Review **spatial-navigation.md** for integrating controls with navigation
- Review **platform-specific.md** for platform optimizations
- Review **tv-design-patterns.md** for UI/UX best practices
