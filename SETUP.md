# GOALZY — Setup Instructions

## Prerequisites

- Flutter 3.44+ (stable channel)
- Dart 3.12+
- Android Studio (Android) or Xcode (iOS)
- An emulator/simulator or physical device

Verify installation:

```bash
flutter doctor
```

## Quick Start

```bash
# From project root
flutter pub get
flutter run
```

## Project Structure

```
lib/
├── core/           # Theme, routing, DI, errors, network stubs
├── shared/         # Entities, mock data, design system widgets
└── features/       # Feature-first clean architecture
    ├── authentication/
    ├── dashboard/
    ├── goals/
    ├── tasks/
    ├── habits/
    ├── calendar/
    ├── analytics/
    ├── ai_assistant/
    ├── profile/
    ├── settings/
    └── notifications/
```

Each feature contains:

- `presentation/` — UI screens, providers
- `domain/` — Repository interfaces
- `data/` — Mock implementations (swap for Supabase later)

## Development Mode

All backend integrations use **mock services**:

- `MockAuthRepository` — authentication
- `MockData` — seed data for goals, tasks, habits, etc.
- `MockAiService` — AI chat responses
- `MockSupabaseClient` — Supabase stub

No API keys required.

## Connecting Real Services (Later)

| Layer | Stub | Replace With |
|-------|------|--------------|
| Auth & DB | `MockSupabaseClient` | `supabase_flutter` package |
| AI | `MockAiService` | FastAPI + Gemini Flash |
| Vector Memory | `GoalzyBrainService` | Qdrant via backend |

Swap implementations in `lib/core/di/providers.dart` — no UI rewrites needed.

## Android Studio — Run Instructions

1. Open Android Studio → **File → Open** → select the `mobileapp` folder
2. Wait for Gradle sync and `flutter pub get` to finish
3. Select a device (emulator or connected phone) in the toolbar
4. Click **Run** (green play button) or press `Shift+F10`
5. App launches on splash → login → main dashboard

### Android Emulator Setup

1. **Tools → Device Manager → Create Device**
2. Pick Pixel 7 or similar, API 34+ system image
3. Start emulator, then run the app

### Troubleshooting

| Issue | Fix |
|-------|-----|
| Gradle sync fails | Run `flutter clean && flutter pub get` |
| No devices | Start emulator or enable USB debugging |
| Font loading slow | First run downloads Plus Jakarta Sans via google_fonts |

## iPhone / iOS

```bash
cd ios && pod install && cd ..
flutter run -d ios
```

Requires macOS with Xcode installed.

## Build for Release

```bash
# Android APK
flutter build apk --release

# Android App Bundle (Play Store)
flutter build appbundle --release

# iOS (macOS only)
flutter build ios --release
```

## Demo Login

Use any email with `@` and password ≥ 6 characters, or tap **Continue as Guest**.
