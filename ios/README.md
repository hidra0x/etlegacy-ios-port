# ET: Legacy iOS port (prototype)

This directory contains the initial iOS packaging/toolchain files for an ET: Legacy prototype.

## Status

- **Prototype/WIP**. Not installable as a signed App Store/TestFlight build from Windows.
- Prepared on Windows; **actual compilation and signing require macOS + Xcode** (or GitHub Actions `macos` runner).
- Unsigned `.ipa` produced by CI is **not installable on a normal iPhone** without proper Apple signing/provisioning.

## What was added

- `ios/Info.plist.in` - bundle metadata, landscape orientation, file sharing enabled.
- `ios/ios-toolchain.cmake` - minimal CMake iOS toolchain variables.
- `.github/workflows/ios.yml` - GitHub Actions macOS workflow that configures/builds and packages an unsigned IPA artifact.

## CMake iOS flags

The root CMake config has an initial `IOS` mode that forces:
- GLES renderer (`FEATURE_RENDERER_GLES=ON`)
- disables renderer1/2/Vulkan, server, IRC, autoupdate, dynamic renderer
- `BUNDLED_LIBS=ON`, `FEATURE_OPENAL=OFF`
- `BUILD_CLIENT=ON`, `BUILD_CLIENT_MOD=ON`

## Building on macOS / CI

```bash
cmake -S . -B build-ios -G Ninja   -DCMAKE_TOOLCHAIN_FILE=ios/ios-toolchain.cmake   -DCMAKE_SYSTEM_NAME=iOS   -DCMAKE_OSX_ARCHITECTURES=arm64   -DCMAKE_OSX_SYSROOT=iphoneos   -DCMAKE_OSX_DEPLOYMENT_TARGET=15.0   -DIOS=ON   -DFEATURE_RENDERER_GLES=ON   -DBUILD_SERVER=OFF -DBUILD_SERVER_MOD=OFF   -DRENDERER_DYNAMIC=OFF -DFEATURE_AUTOUPDATE=OFF   -DFEATURE_OPENAL=OFF -DFEATURE_CURL=OFF -DFEATURE_SSL=OFF   -DBUNDLED_LIBS=ON -DBUNDLED_SDL=OFF
cmake --build build-ios --config Release --parallel
```

## Game data (not bundled)

ET: Legacy requires original Wolfenstein: Enemy Territory data files. Do **not** bundle proprietary assets.
Place legally obtained files (at minimum `pak0.pk3`, typically also `pak1.pk3`, `pak2.pk3`) into the app's Documents/game data location exposed via iTunes/Finder File Sharing (`UIFileSharingEnabled`).

## Known gaps / next work

- SDL2 iOS integration: ensure SDL_main/UIKit lifecycle is correctly used; current ETL entrypoint may need iOS-specific bootstrap.
- Touch controls: Android has virtual joysticks/UI; iOS needs native touch overlay or SDL touch mapping.
- OpenGL ES renderer compatibility on iOS/mobile GPUs.
- CGame/UI modules loading on iOS: ETL uses dynamic modules; iOS has restrictions on loading unsigned/dynamic code - may require static/builtin mod build.
- Codesigning/provisioning for real device install.
- Audio: OpenAL disabled in prototype; SDL audio path should be validated.
- Networking/cURL/SSL: disabled for initial compile pass; re-enable with iOS-compatible backends.
- Full QA on device/simulator.

## Legal

ET: Legacy is GPLv3. Any distributed binary/source fork must comply with GPLv3 requirements.
Wolfenstein: Enemy Territory assets are proprietary and must be supplied by the user legally.
