# Android TV Remote Control Support

## Overview

This document describes the Android TV remote control (D-pad) navigation support added to Animeko.

## Changes Made

### 1. AndroidManifest.xml
Added declarations to support Android TV devices:

```xml
<!-- Declare TV support -->
<uses-feature
        android:name="android.hardware.touchscreen"
        android:required="false" />
<uses-feature
        android:name="android.software.leanback"
        android:required="false" />
```

Added LEANBACK_LAUNCHER category to make the app visible in Android TV launcher:

```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
    <category android:name="android.intent.category.LEANBACK_LAUNCHER" />
</intent-filter>
```

### 2. D-pad Center Button Support (Focus.kt)
Added a new utility modifier `onDPadCenter()` for handling the D-pad center/select button on TV remotes:

```kotlin
/**
 * Handles D-pad center button press (OK/Select button on TV remote).
 * This is useful for making clickable elements work with TV remote controls.
 */
fun Modifier.onDPadCenter(onSelect: () -> Unit)
```

This maps to `Key.DirectionCenter` or `Key.Enter` key events.

### 3. Existing D-pad Support
The app already had comprehensive keyboard navigation support that works with TV remote D-pad keys:

- **D-pad Up/Down**: Handled by `Key.DirectionUp` and `Key.DirectionDown`
  - Used for volume control in video player
  - Used for list item selection
  - Used for scrolling

- **D-pad Left/Right**: Handled by `Key.DirectionLeft` and `Key.DirectionRight`
  - Used for video seeking (forward/backward)
  - Respects layout direction (RTL/LTR)

- **Back button**: Already handled by `BackHandler` API

## How It Works

### Video Player Controls
The video player gesture system (`PlayerGestureHost.kt`) already supports keyboard input through the `GestureFamily` configuration:

- **Left/Right D-pad**: Seek video backward/forward by 5 seconds
- **Up/Down D-pad**: Adjust volume
- **Shift + Up/Down D-pad**: Fine volume adjustment
- **Space/D-pad Center**: Play/pause (when `keyboardSpaceForPauseResume` is enabled)

### Navigation
- **Tab/Enter/D-pad Center**: Move focus to next component (`moveFocusOnEnter`)
- **D-pad directional keys**: Navigate between focusable UI elements
- **Back button**: Navigate back in the app

## Testing on Android TV

To test the app on Android TV:

1. Build and install the APK on an Android TV device or emulator
2. The app will appear in the TV launcher
3. Use the TV remote to navigate:
   - D-pad to move between elements
   - D-pad center/OK button to select/click
   - Back button to go back
   - Play/Pause button for video playback control

## Technical Details

### Key Mappings
Android TV remote D-pad keys automatically map to Compose `Key` constants:
- D-pad Up → `Key.DirectionUp`
- D-pad Down → `Key.DirectionDown`
- D-pad Left → `Key.DirectionLeft`
- D-pad Right → `Key.DirectionRight`
- D-pad Center/OK → `Key.DirectionCenter` or `Key.Enter`
- Back button → `Key.Back` (handled by Android BackHandler)

### Focus Management
The app uses Compose's built-in focus system which automatically handles D-pad navigation between focusable elements. Components that use:
- `Modifier.clickable()`
- `Modifier.focusable()`
- Interactive components (Button, TextField, etc.)

...are automatically navigable with the D-pad.

## Future Improvements

Potential enhancements for better TV experience:
1. Add a TV-optimized banner image for the launcher
2. Implement larger touch targets for better remote control usability
3. Add visual focus indicators for better user feedback
4. Optimize layout for TV screen sizes (use `Devices.TV_720p` and `Devices.TV_1080p` in previews)
5. Consider adding a "TV mode" UI variant with larger text and simplified navigation

## References

- [Android TV Input Compatibility](https://developer.android.com/training/tv/start/hardware#input-compatibility)
- [Android TV App Manifest](https://developer.android.com/training/tv/start/start#manifest)
- [Compose Focus System](https://developer.android.com/jetpack/compose/touch-input#focus)
