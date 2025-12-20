# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

**dt_flutter_smart_rating** is a Flutter package that provides intelligent rating dialogs for Flutter apps. The package monitors network activity (via Dio interceptor or manual reporting) and automatically prompts users to rate the app after a period of successful network usage.

## Commands

### Development
```bash
# Get dependencies
flutter pub get

# Analyze code
flutter analyze

# Run example app
cd example && flutter run
```

### Testing
```bash
# Run all tests (currently minimal test coverage)
flutter test

# Run tests in example project
cd example && flutter test
```

### Linting
```bash
# Analyze with flutter_lints rules
flutter analyze
```

Note: `lib/src/network/smart_rating_dio_interceptor.dart` is excluded from analysis because Dio is an optional peer dependency.

## Architecture

### Core Components

**Singleton Pattern**: The package uses a singleton (`SmartRating`) that must be initialized once at app startup with a `SmartRatingConfig` and a `GlobalKey<NavigatorState>` to display dialogs without requiring a build context.

**Network Monitoring**: Tracks consecutive successful network requests. A single failure resets the counter. When the threshold is reached (`minimumSuccessfulRequests`, default 20), a timer starts (`waitDurationAfterSuccess`, default 10 minutes). After the timer expires, the dialog is shown if cooldown period has passed.

**Persistence**: Uses `shared_preferences` to track:
- `smart_rating_last_shown_date`: Last time dialog was shown
- `smart_rating_last_action_date`: Last time user rated or gave feedback

Dialog respects a `dialogInterval` (default 30 days) before showing again.

**User Flow**:
- 4-5 stars → Opens store URL via `url_launcher`
- 1-3 stars → Shows in-app feedback form
- Both paths → Show thank you dialog → Reset counters

### Directory Structure

```
lib/
├── dt_flutter_smart_rating.dart          # Main export (public API)
├── src/
    ├── core/
    │   ├── smart_rating.dart             # Singleton controller
    │   ├── smart_rating_config.dart      # Config & localizations
    │   └── smart_rating_storage.dart     # SharedPreferences wrapper
    ├── network/
    │   ├── smart_rating_dio_interceptor.dart  # Optional Dio integration
    │   └── README.md
    └── ui/
        ├── rating_dialog.dart            # Main rating dialog
        ├── smart_rating_theme.dart       # Theming system
        └── thank_you_dialog.dart         # Post-action confirmation
```

### Key Design Decisions

**Optional Dio Dependency**: The Dio interceptor is intentionally NOT exported from the main package and excluded from analysis. This allows the package to work without requiring Dio. Apps using Dio should import the interceptor directly from `src/network/` or copy it to their project.

**Manual vs Automatic Triggering**: Set `autoTrigger: false` in config to disable automatic dialog display. Manual mode still respects the cooldown interval when `showRatingDialog()` is called.

**Theming**: Provides 3 built-in themes (`modernLight()`, `modernDark()`, `vibrantGradient()`) and full customization via `SmartRatingTheme`.

## Integration Example

```dart
// main.dart
final navigatorKey = GlobalKey<NavigatorState>();

void main() {
  runApp(const MyApp());
}

class MyApp extends StatefulWidget {
  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  void initState() {
    super.initState();
    SmartRating().initialize(
      SmartRatingConfig(
        appName: 'My App',
        storeUrl: 'https://apps.apple.com/app/id...',
        navigatorKey: navigatorKey,
        appIcon: Image.asset('assets/icon.png'),
        autoTrigger: true,  // or false for manual control
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      navigatorKey: navigatorKey,
      home: HomePage(),
    );
  }
}

// With Dio
import 'package:dt_flutter_smart_rating/src/network/smart_rating_dio_interceptor.dart';

final dio = Dio();
dio.interceptors.add(SmartRatingDioInterceptor());

// Manual reporting (without Dio)
SmartRating().reportNetworkSuccess();
SmartRating().reportNetworkFailure();
```
