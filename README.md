# AR Room Planner

A mobile AR application built with Unity and AR Foundation that lets you visualize and arrange furniture in real-world spaces. Detect floor planes, place furniture models, reposition and rotate them in AR, and persist your layouts across app sessions.

## Features

- **Real-time plane detection** — automatically detects horizontal surfaces (floors, tables) using device sensors
- **Tap-to-place furniture** — select furniture from a palette and tap the floor to anchor it in AR space
- **Intuitive manipulation** — drag to reposition, pinch to rotate, scale gestures for resizing
- **Persistent layouts** — save your room design and reload it the next time you open the app
- **Graceful tracking recovery** — handles tracking loss and relocalization smoothly
- **Clean mobile UI** — furniture palette, selection indicators, delete functionality

## Tech Stack

- **Engine:** Unity 6.3 LTS
- **AR Framework:** AR Foundation (cross-platform ARKit/ARCore)
- **Scripting:** C#
- **Persistence:** JSON (PlayerPrefs)
- **3D Models:** Low-poly furniture from Sketchfab

## Architecture

```
AR Foundation (plane detection + anchoring)
    ↓
Raycast input system (tap-to-place)
    ↓
Furniture prefab instantiation + ARAnchor attachment
    ↓
Local scene state (List<FurnitureData>)
    ↓
JSON serialization → PlayerPrefs (persistence)
```

**Key classes:**
- `PlaneDetectionManager` — detects and visualizes planes
- `FurnitureSpawner` — handles tap input and prefab instantiation
- `FurnitureController` — selection, drag, rotate, delete per-item
- `LayoutPersistence` — save/load furniture positions and rotations

## How It Works

1. **Plane Detection** — AR Foundation's `ARPlaneManager` runs continuously, detecting horizontal surfaces. Detected planes render as translucent meshes.
2. **Placing Furniture** — Select a furniture type from the UI palette, tap a detected plane. A raycast hits the plane; an `ARAnchor` is created at that point, and the furniture prefab is instantiated as a child of the anchor.
3. **Manipulation** — Single-touch drag moves the furniture. Two-finger pinch rotates around the vertical axis. Tap to select, button to delete.
4. **Persistence** — On app quit, the list of placed furniture (prefab ID, world position, rotation) serializes to JSON and stores in `PlayerPrefs`. On app start, the layout reloads and re-anchors furniture to world space.
5. **Tracking Loss** — If the device loses plane tracking (fast camera movement, low light), a message prompts the user to move slowly. Anchored furniture stays visually in place; tracking recovers automatically.

## Building & Running

### Prerequisites
- Unity 6.3 LTS
- AR Foundation package (`com.unity.xr.arfoundation`)
- ARCore XR Plugin (Android) or ARKit XR Plugin (iOS)
- Android API level 24+ or iOS 14.3+
- A device with ARCore/ARKit support

### Android Build
1. Open Build Settings → Switch Platform to Android
2. Player Settings → XR Plug-in Management → enable ARCore
3. Build and run on device (USB debugging enabled)

### iOS Build
1. Open Build Settings → Switch Platform to iOS
2. Player Settings → XR Plug-in Management → enable ARKit
3. Xcode: add camera + microphone permissions to Info.plist (only camera needed)
4. Build and run

## Demo

[See demo video](link-to-demo-video-here) — 30 seconds of scanning a floor, placing furniture, moving it, and reloading the layout.

## Learnings & Challenges

- **Anchor persistence across tracking loss:** Initially, anchors drifted when tracking reestablished. Solved by storing world positions separately and re-anchoring on scene load rather than relying on anchor serialization.
- **Float precision in PlayerPrefs:** Similar to GPS coordinate persistence in earlier work — store floats as strings with `"R"` round-trip format to avoid precision loss.
- **UI responsiveness during plane detection:** Plane mesh updates each frame can stutter on older devices. Implemented update throttling (every 5 frames) for visualization.

## Future Improvements

- Cloud anchor support for multi-device collaboration
- Occlusion handling (virtual furniture hidden behind real objects)
- More furniture categories and import pipeline for custom models
- AR snapshot / sharing layouts as QR codes
- 6DoF controller support for VR handset input

## Project Structure

```
Assets/
├── Scripts/
│   ├── PlaneDetectionManager.cs
│   ├── FurnitureSpawner.cs
│   ├── FurnitureController.cs
│   ├── LayoutPersistence.cs
│   └── UI/
│       └── FurniturePaletteUI.cs
├── Prefabs/
│   ├── Furniture/
│   │   ├── Chair.prefab
│   │   ├── Table.prefab
│   │   └── Sofa.prefab
│   └── UI Canvas.prefab
├── Models/ (imported from Sketchfab)
└── Scenes/
    └── MainScene.unity
```

## License

MIT

## Contact

Muhammad Saqib — https://muhammadsaqibshoaib.github.io/

Questions about AR Foundation, anchoring strategies, or the persistence system? Feel free to open an issue.
