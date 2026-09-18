# Base44 Build Environment — ProtonVPN Android

## Project Overview
This is **ProtonVPN for Android** — a native Android app (Kotlin + Jetpack Compose).
There is no web server or web frontend; it produces an APK.

## Build Requirements
- **JDK 17** (eclipse-temurin:17-jdk base image)
- **Android SDK**: platform-tools, platforms;android-36, build-tools;36.0.0
- **Gradle 8.13** (via wrapper)
- **AGP 8.9.1**, **Kotlin 2.2.21**, compileSdk 36, minSdk 26, targetSdk 36
- No NDK/CMake/SWIG needed for the build (native libs come from dependencies)

## Build Command
```bash
docker compose -f docker-compose.base44.yml run --rm builder
```
This runs:
```
./gradlew assembleProductionVanillaOpenSourceRelease --no-daemon --max-workers=2 -Dorg.gradle.jvmargs='-Xmx4g ...'
```

## Key Notes
- **No git tags** in this repo. The build uses `OVERRIDE_VERSION_NAME=5.10.0.0` env var to bypass git-tag-based versioning (see `GitFullVersionNameSource.kt`).
- **Memory**: The sandbox has ~8GB RAM. Default Gradle heap is 6GB which causes OOM during dexing. Reduced to 4GB + 4GB swap.
- **Signing**: Uses the default Android debug keystore (auto-generated). No custom keystore needed.
- **Output APK**: `app/build/outputs/apk/productionVanillaOpenSource/release/ProtonVPN-*.apk`
- **APK size**: Release build with R8 minification + resource shrinking is ~63MB (down from ~89MB debug).

## Variant
`productionVanillaOpenSourceRelease` — production environment, vanilla functionality (no Google services), open-source distribution, release build type (minified + resource-shrunk for smaller APK).

## No Preview
This is a native Android app — it cannot be shown in the browser-based preview (which expects a web server on port 3000). The APK must be installed on an Android device or emulator.
