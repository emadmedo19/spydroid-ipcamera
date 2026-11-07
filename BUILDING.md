# Building Spydroid

Requirements: JDK 17, Android SDK Platform 35 and Build Tools 35.0.0.
Set `ANDROID_HOME` to the SDK directory, or add `sdk.dir=/path/to/android-sdk`
to a local (untracked) `local.properties` file.

```sh
./gradlew assembleDebug
```

The installable, debug-signed APK is written to
`build/outputs/apk/debug/spydroid-ipcamera-debug.apk`.
No release signing key is included. A debug build cannot update an existing
installation signed with the original developer's key.

GitHub Actions runs the same build on pushes to `master`, `build/**` branches
and version tags. Open the completed **Build Android APK** run and download
the `spydroid-debug-*` artifact. It contains the APK in a ZIP file. GitHub
requires signing in to download Actions artifacts. Each fresh CI runner uses
its own debug signing key, so use the Release APK consistently for updates.

## Compatibility changes

- Gradle 8.9 / Android Gradle Plugin 8.7.3 replace the obsolete Ant build.
- Target API 28 enables runtime permissions and permits normal sideload
  installation on recent Android versions. This is not a Play Store release;
  meeting current Play target requirements needs further streaming/service work.
- Camera and microphone permissions are requested before initializing servers.
  Denying a permission closes the app without starting its servers.
- Android 8+ notifications use a notification channel.
- Codec probe files use the app's private cache, so storage permission is no
  longer needed.
- The bundled HTTP server explicitly declares the legacy Apache HTTP library.

The original camera, MediaRecorder, support-library, advertising and streaming
implementations remain legacy dependencies. Background camera/microphone
streaming is subject to Android restrictions; keep the app visible for testing.
The browser interface requires a discontinued VLC browser plugin; use a current
VLC client with the RTSP URL shown in the app instead.

## Device checks

1. Install the APK and grant camera/microphone access on first launch.
2. Deny permissions in a separate run and confirm no server starts.
3. On the same trusted network, open the displayed HTTP address and RTSP URL.
4. Enable video/audio in settings, play the stream in VLC, and test both cameras.
5. Check the notification on Android 8+, rotation, return from settings and Quit.

Build/signature checks do not establish that camera streaming works on a device.

## Validation of this update

`assembleDebug lintDebug` passed with 0 lint errors and 113 warnings.
The Play target-level check is deliberately classified as a warning in
`lint.xml`, because this build is for sideloading. Other warnings include old
APIs, layout/accessibility issues and legacy dependencies.
APK v1/v2 signature verification and ZIP alignment checks passed.
No Android device or emulator was connected, so live streaming and permission
dialogs still require device testing.
