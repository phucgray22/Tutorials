# Flutter call native code using Method Channel (Kotlin)

## 1. Setup Flutter code

```dart 
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

class CallNativeExample extends StatefulWidget {
  const CallNativeExample({Key key}) : super(key: key);

  @override
  State<CallNativeExample> createState() => _CallNativeExampleState();
}

class _CallNativeExampleState extends State<CallNativeExample> {
  // Create Method Channel
  static const methodChannel = MethodChannel('ihrp.dev/example');

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        const Text('Call native'),
        ElevatedButton(
          onPressed: () async {
            try {
              // Call native method using "invokeMethod"

              final result = await methodChannel.invokeMethod('getDeviceInfo') as Map;

              result.forEach((key, value) { 
                print('%% ${key}: ${value}');
              });
            } catch (e) {
              debugPrint(e);
            }
          },
          child: const Text('GET'),
        ),
      ],
    );
  }
}
```

## 2. Setup native kotlin code

```kotlin
package com.example.demo_abc

import io.flutter.embedding.android.FlutterFragmentActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.MethodChannel

class MainActivity : FlutterFragmentActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)

        MethodChannel(
            flutterEngine.dartExecutor.binaryMessenger,
            CHANNEL
        ).setMethodCallHandler { call, result ->
            when (call.method) {
                "getDeviceInfo" -> {
                    val map = LinkedHashMap<String, Any>()

                    map["versionName"] = BuildConfig.VERSION_NAME
                    map["versionCode"] = BuildConfig.VERSION_CODE
                    map["device"] = android.os.Build.DEVICE

                    // call "result.success(data)" to send data to Flutter
                    result.success(map)
                }
            }
        }
    }

    companion object {
        // Same name with Flutter method channel
        private const val CHANNEL = "ihrp.dev/example"
    }
}
```