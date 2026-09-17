# MirrorX

MirrorX is a Flutter desktop application for Windows that launches [scrcpy](https://github.com/Genymobile/scrcpy) to mirror an Android device. It provides a small graphical interface for starting and stopping mirroring, switching between USB and wireless operation, recording the mirrored session, and viewing runtime logs.

> Status: This project is an early-stage Windows-focused utility for Android screen mirroring and recording.

## What the project does

MirrorX bundles the scrcpy binaries and supporting DLL files into the Flutter app, extracts them to a temporary directory at runtime, and starts the scrcpy executable to mirror a connected Android device. The UI exposes the core controls needed for everyday mirroring, recording, and diagnostics without requiring manual scrcpy command line usage.

## Why it is useful

- Mirror an Android device from a desktop app with a simple graphical interface.
- Use either USB or Wi‑Fi/TCP-IP mode for a device connection.
- Record mirrored sessions to a `.mkv` file in the Windows `Videos` folder.
- View ADB and scrcpy output in a built-in log panel.
- Control the app remotely over a small local HTTP API on port `8080`.
- Package the required runtime dependencies with the app instead of depending on an external scrcpy install.

## Key features

- Bundled Windows `adb.exe`, `scrcpy.exe`, and required DLLs from the `assets/` directory.
- Auto-detect the Windows IPv4 address and show it in the interface.
- Wireless mode with `adb connect <device-ip>:5555` support.
- Recordings saved to `C:\Users\<user>\Videos` by default.
- Theme-aware desktop UI with start/stop controls and log output.
- Local JSON HTTP actions for `connect`, `start`, `stop`, and `enable_tcpip`.

## Requirements

- Windows 10 or later for the primary workflow.
- [Flutter 3.22+](https://docs.flutter.dev/get-started/install) with the Windows desktop toolchain enabled.
- Visual Studio with the Desktop development with C++ workload and CMake.
- An Android device with USB debugging enabled, or an emulator.
- ADB available on PATH for device connection and wireless setup.

## Getting started

### 1. Clone the repository

```powershell
git clone https://github.com/professortate/mirrorX.3.git
cd mirrorX.3
```

### 2. Install dependencies

```powershell
flutter pub get
flutter doctor
```

Resolve any Windows desktop toolchain issues reported by `flutter doctor` before running the app.

### 3. Run the app

```powershell
flutter run -d windows
```

When the app starts:

1. Confirm the Windows IP address is displayed.
2. Enable wireless mode if needed.
3. Optionally provide a custom recording name.
4. Click **Start Mirroring**.
5. Click **Stop Mirroring** when finished.

### 4. Build a release

```powershell
flutter build windows --release
```

## Usage example

```powershell
# Verify the Android device is visible to ADB
adb devices

# Run the desktop app
flutter run -d windows
```

The app exposes a local HTTP API that accepts JSON requests. Example:

```powershell
$body = @{ action = "start"; enableRecording = $true; recordingName = "demo" } | ConvertTo-Json
Invoke-RestMethod -Uri http://127.0.0.1:8080 -Method Post -ContentType "application/json" -Body $body
```

## Project structure

```text
android/                  Android Flutter project scaffolding
assets/                   Bundled scrcpy, ADB, and DLL runtime files
ios/                      iOS Flutter project scaffolding
lib/                      Main Flutter app and controller logic
linux/                    Linux desktop runner configuration
macos/                    macOS desktop runner configuration
screen_share_server.py    Experimental socket-based receiver utility
test/                     Flutter widget tests
web/                      Web Flutter scaffolding
windows/                  Windows desktop runner configuration
pubspec.yaml              Flutter project configuration and asset list
```

The main application logic lives in `lib/main.dart`. It extracts bundled runtime files, starts the scrcpy process, signals state back to the UI, and hosts a local JSON endpoint for simple automation.

## Support and documentation

- [Flutter docs](https://docs.flutter.dev/)
- [scrcpy project](https://github.com/Genymobile/scrcpy)
- Repository issues: [https://github.com/professortate/mirrorX.3/issues](https://github.com/professortate/mirrorX.3/issues)

## Maintainers and contribution

This project is maintained by the repository owner under the GitHub organization/user context for `professortate`. Contributions are welcome via pull requests.

Before submitting changes:

1. Create a focused branch.
2. Run `flutter analyze` and `flutter test`.
3. Keep platform-specific changes in the correct runner directory.
4. Avoid committing generated build artifacts or private device data.

If you are making larger changes, open an issue first so the direction can be reviewed before implementation.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
