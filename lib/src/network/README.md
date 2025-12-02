# Network Monitoring

## Dio Interceptor

The `SmartRatingDioInterceptor` is provided for convenience if your project uses Dio.

### Important: Optional Dependency

**Dio is NOT a required dependency of this package.** If you want to use the Dio interceptor, you must add Dio to your app's `pubspec.yaml`:

```yaml
dependencies:
  dio: ^5.4.1  # or your preferred version
```

### Usage

```dart
final dio = Dio();
dio.interceptors.add(SmartRatingDioInterceptor());
```

### For Other HTTP Clients

If you use a different HTTP client (http, chopper, etc.), simply call the manual reporting methods:

```dart
// On successful response (2xx)
SmartRating().reportNetworkSuccess();

// On error response
SmartRating().reportNetworkFailure();
```
