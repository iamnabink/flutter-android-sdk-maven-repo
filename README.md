# Maven Repository

This directory contains a local Maven repository with prebuilt Flutter AAR (Android Archive) artifacts. These artifacts are generated from the Flutter SDK module and can be used by Android applications without requiring the Flutter toolchain.

## Overview

The `maven-repo/` directory contains compiled Flutter modules packaged as AAR files, organized in standard Maven repository structure:

```
maven-repo/
└── dev/
    └── nabrajkhadka/
        └── example/
            └── flutter_module/
                ├── flutter_debug/1.0/
                ├── flutter_release/1.0/
                └── flutter_profile/1.0/
```

## Contents

This repository contains the following artifacts:

### Flutter Module Artifacts

- **Group ID**: `dev.nabrajkhadka.example.flutter_module`
- **Artifact IDs**:
  - `flutter_debug` (version 1.0) - Debug build variant
  - `flutter_release` (version 1.0) - Release build variant
  - `flutter_profile` (version 1.0) - Profile build variant

Each artifact includes:
- `.aar` file - The compiled Android Archive
- `.pom` file - Project Object Model with dependency metadata

## How It's Created

The maven repository is populated by running the `move_to_maven_repo.sh` script from the project root:

```bash
./move_to_maven_repo.sh
```

This script:
1. Locates the Flutter AAR build outputs from `flutter_sdk/build/host/outputs/repo`
2. Moves them to the `maven-repo/` directory
3. Organizes them in the proper Maven repository structure

**Prerequisites:**
- You must first build the Flutter AAR files:
  ```bash
  cd flutter_sdk
  flutter build aar
  ```

## Usage in Android Projects

### Step 1: Add Repository

Add the local Maven repository to your `app/build.gradle` or root `build.gradle`:

```gradle
repositories {
    maven {
        // Local maven repository containing Flutter AAR artifacts
        url '../maven-repo'  // Adjust path relative to your project
        // Or use absolute path:
        // url file('path/to/maven-repo')
    }
    
    // Required: Flutter's Android embedding dependencies
    maven {
        url 'https://storage.googleapis.com/download.flutter.io'
    }
    
    google()
    mavenCentral()
}
```

### Step 2: Add Dependencies

Add the Flutter module dependencies to your `app/build.gradle`:

```gradle
dependencies {
    // Release build variant
    releaseImplementation ('dev.nabrajkhadka.example.flutter_module:flutter_release:1.0@aar') {
        transitive = true
    }
    
    // Debug build variant
    debugImplementation ('dev.nabrajkhadka.example.flutter_module:flutter_debug:1.0@aar') {
        transitive = true
    }
    
    // Profile build variant (optional)
    profileImplementation ('dev.nabrajkhadka.example.flutter_module:flutter_profile:1.0@aar') {
        transitive = true
    }
    
    // Required: MultiDex support
    implementation 'androidx.multidex:multidex:2.0.1'
}
```

**Important Notes:**
- `transitive = true` is required to include all SDK dependencies
- Use the appropriate build variant (debug/release/profile) for your build type
- Ensure `minSdkVersion` is set to 24 or higher

### Step 3: Configure AndroidManifest.xml

Add Flutter embedding metadata inside the `<application>` tag:

```xml
<meta-data
    android:name="flutterEmbedding"
    android:value="2" />
```

## Updating the Repository

To update the artifacts in this repository:

1. **Build new AAR files:**
   ```bash
   cd flutter_sdk
   flutter clean
   flutter pub get
   flutter build aar
   ```

2. **Move to maven-repo:**
   ```bash
   cd ..
   ./move_to_maven_repo.sh
   ```

3. **Sync your Android project:**
   - In Android Studio: Click "Sync Now" or run `./gradlew build --refresh-dependencies`

## Repository Structure

The Maven repository follows the standard Maven directory layout:

```
maven-repo/
└── <groupId>/
    └── <artifactId>/
        └── <version>/
            ├── <artifactId>-<version>.aar
            └── <artifactId>-<version>.pom
```

Where:
- `groupId` = `dev.nabrajkhadka.example.flutter_module`
- `artifactId` = `flutter_debug`, `flutter_release`, or `flutter_profile`
- `version` = `1.0`

## Troubleshooting

### "Could not find flutter_embedding"
- Ensure the Flutter dependencies repository is added:
  ```gradle
  maven {
      url 'https://storage.googleapis.com/download.flutter.io'
  }
  ```

### "Could not resolve dependency"
- Verify the path to `maven-repo` is correct in your `build.gradle`
- Ensure the repository contains the expected artifacts
- Try running `./gradlew build --refresh-dependencies`

### "minSdkVersion mismatch"
- Set `minSdkVersion` to 24 or higher in your `build.gradle`

### Repository is empty
- Run `flutter build aar` first, then run `./move_to_maven_repo.sh`
- Check that `flutter_sdk/build/host/outputs/repo` contains the build artifacts

## Distribution

This directory is typically:
- **Included in version control** if you want to distribute prebuilt artifacts with your project
- **Excluded from version control** (via `.gitignore`) if artifacts should be built locally

Check the root `.gitignore` to see if `maven-repo/` is tracked or ignored.

## Related Files

- `move_to_maven_repo.sh` - Script to populate this repository
- `example_android/app/build.gradle` - Example Android project using this repository
- `flutter_sdk/` - Source Flutter module that generates these artifacts

## See Also

- [Main Project README](../README.md)
- [Android Integration Guide](../example_android/README.md)
- [Flutter SDK Documentation](../flutter_sdk/README.md)
