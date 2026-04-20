

# GridFolio — Android Image Gallery App

A Material Design 3 image gallery app built with Java for Android, featuring Firebase Auth, Firestore, and Cloud Storage.

## Features

- Email/Password + Google Sign-In (Firebase Auth)
- Smart Grid switcher (GridLayoutManager ↔ StaggeredGridLayoutManager)
- Cloud image upload to Firebase Storage with metadata in Firestore
- Albums / Collections with multi-select and drag-reorder
- Full-screen viewer with pinch-to-zoom (PhotoView) and ViewPager2
- Share via Android ShareSheet, export album as ZIP
- Material You dynamic colors + dark mode toggle

## Setup

### 1. Prerequisites
- Android Studio Hedgehog (2023.1.1) or later
- JDK 17
- Android SDK 34 (min SDK 21)

### 2. Firebase Configuration
1. Go to https://console.firebase.google.com and create a new project named **GridFolio**.
2. Add an Android app with package name: `com.gridfolio.app`
3. Download the generated `google-services.json` and place it in `app/` (replacing the placeholder).
4. In Firebase Console enable:
   - **Authentication** → Email/Password + Google providers
   - **Firestore Database** (start in test mode, then apply rules below)
   - **Storage** (default bucket)
5. For Google Sign-In, add your debug SHA-1 fingerprint:
   ```
   ./gradlew signingReport
   ```
   Copy the SHA-1 into Firebase Console → Project Settings → Your App.

### 3. Firestore Security Rules (suggested)
```
rules_version = '2';
service cloud.firestore {
  match /databases/{db}/documents {
    match /users/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
    match /galleries/{gid} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.user_id;
      allow create: if request.auth != null;
    }
    match /images/{iid} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.user_id;
      allow create: if request.auth != null;
    }
  }
}
```

### 4. Build & Run
```
./gradlew assembleDebug
```
Or open the project in Android Studio and click **Run**.

### 5. Generate Signed APK
**Build → Generate Signed Bundle / APK → APK**, choose release variant.

## Project Structure
See the master spec; folder layout under `app/src/main/java/com/gridfolio/app/` is organized by:
`activities/`, `fragments/`, `adapters/`, `models/`, `repositories/`, `viewmodels/`, `utils/`.

## Notes
- The bundled `google-services.json` is a **placeholder** — the app will NOT build until you replace it with your real one.
- All user-facing strings live in `res/values/strings.xml`.
- Dark mode overrides in `res/values-night/themes.xml`.
