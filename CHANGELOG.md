## 0.0.4

* **Breaking Change**: Removed `dio` dependency and `SmartRatingDioInterceptor`. Applications should now manually report network activity or implement their own interceptor (see `lib/src/network/README.md` for examples).
* **Configuration**: Updated SDK constraints and project metadata.

## 0.0.3

### ✨ New Features
* **Custom Feedback Handling**: Added `onSubmitFeedback` callback to `showRatingDialog()`, allowing developers to process user feedback (e.g., sending to a backend or analytics service).

## 0.0.2

### ✨ New Features
* **Failure Tracking System**: Track network failures with `failureCount`, `hasFailures` getters
* **Smart Manual Triggering**: New parameters for `showRatingDialog()`:
  - `onlyIfNoFailures`: Only show dialog if no failures occurred
  - `requireMinimumSuccess`: Only show if minimum success count reached
  - `maximumAllowedFailures`: Set a tolerance level for failures
* **Session Management**: New `resetCounters()` method to reset all counters
* **Public Getters**: Access `successCount`, `failureCount`, and `hasFailures`

### 🔧 Improvements
* Enhanced `reportNetworkFailure()` to track failure count
* Better debug logging with failure counts
* Improved documentation with use cases and examples

### ⚠️ Breaking Changes
* **NONE** - All new features are backward compatible with default parameter values

## 0.0.1

* Initial release
* Smart rating dialog based on network success
* Automatic and manual triggering modes
* Configurable theming and localization
* Dio interceptor for automatic network monitoring

