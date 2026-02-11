# Network Monitoring

To report network activity to `SmartRating`, simply call the manual reporting methods in your network layer:

### Basic Usage

```dart
// On successful response (2xx)
SmartRating().reportNetworkSuccess();

// On error response
SmartRating().reportNetworkFailure();
```

### Integration Examples

#### Using Dio
If you use [Dio](https://pub.dev/packages/dio), you can add a simple interceptor in your own codebase:

```dart
class SmartRatingInterceptor extends Interceptor {
  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) {
    SmartRating().reportNetworkSuccess();
    super.onResponse(response, handler);
  }

  @override
  void onError(DioException err, ErrorInterceptorHandler handler) {
    SmartRating().reportNetworkFailure();
    super.onError(err, handler);
  }
}

// Then add it to your dio instance:
final dio = Dio();
dio.interceptors.add(SmartRatingInterceptor());
```

#### Using http package
```dart
final response = await http.get(Uri.parse('https://example.com'));
if (response.statusCode >= 200 && response.statusCode < 300) {
  SmartRating().reportNetworkSuccess();
} else {
  SmartRating().reportNetworkFailure();
}
```
