# Maven Repository

This directory contains a Maven repository with prebuilt Flutter AAR (Android Archive) artifacts. These artifacts are generated from the Flutter SDK module and can be used by Android applications without requiring the Flutter toolchain.

**🌐 Hosted on GitHub Pages:** [https://iamnabink.github.io/flutter-android-sdk-maven-repo](https://iamnabink.github.io/flutter-android-sdk-maven-repo)

## Overview

The `maven-repo/` directory contains compiled Flutter modules packaged as AAR files, organized in standard Maven repository structure. This repository is hosted on GitHub Pages, making it accessible to any Android project via a simple URL.

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

## Usage in Android Projects

### Step 1: Add Repository

Add the Maven repository to your `app/build.gradle` or root `build.gradle`:

**Option 1: Use GitHub Pages (Recommended)**

```gradle
repositories {
    maven {
        // GitHub Pages hosted Maven repository
        url 'https://iamnabink.github.io/flutter-android-sdk-maven-repo'
    }
    
    // Required: Flutter's Android embedding dependencies
    maven {
        url 'https://storage.googleapis.com/download.flutter.io'
    }
    
    google()
    mavenCentral()
}
```

**Option 2: Use Local Repository**

If you prefer to use a local copy of the repository:

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
- If using GitHub Pages: Verify the URL is correct: `https://iamnabink.github.io/flutter-android-sdk-maven-repo`
- If using local path: Verify the path to `maven-repo` is correct in your `build.gradle`
- Ensure the repository contains the expected artifacts
- Check your internet connection if using GitHub Pages
- Try running `./gradlew build --refresh-dependencies`

### "minSdkVersion mismatch"
- Set `minSdkVersion` to 24 or higher in your `build.gradle`

## Publishing to GitHub Pages

This repository is configured to be served via GitHub Pages. To set up GitHub Pages for your repository:

1. **Enable GitHub Pages:**
   - Go to your repository settings on GitHub
   - Navigate to "Pages" section
   - Set source to the branch containing `maven-repo/` (usually `main` or `master`)
   - Set the folder to `/maven-repo` (or `/` if `maven-repo` is in root)

2. **Access the repository:**
   - Your Maven repository will be available at: `https://<username>.github.io/<repo-name>/maven-repo`
   - For this project: `https://iamnabink.github.io/flutter-android-sdk-maven-repo`

3. **Update artifacts:**
   - Commit and push updates to the `maven-repo/` directory
   - GitHub Pages will automatically update

## Distribution

This directory is:
- **Included in version control** to enable GitHub Pages hosting
- The artifacts are publicly accessible via the GitHub Pages URL

For local development, you can also use the local path option in your `build.gradle`.

## Related Files

- `example_android/app/build.gradle` - Example Android project using this repository

## See Also

- [Main Project README](../README.md)
- [Android Integration Guide](../example_android/README.md)
- [Flutter SDK Documentation](../flutter_sdk/README.md)
