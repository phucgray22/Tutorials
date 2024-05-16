# Flutter call native code using Method Channel (Kotlin/Swift)

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

## 2. Setup native Kotlin (Android) code

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

## 3. Setup native Swift (IOS) code

```swift
import UIKit
import Flutter

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
    override func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        GeneratedPluginRegistrant.register(with: self)
        
        if let controller = window?.rootViewController as? FlutterViewController {
            // Same name with Flutter method channel
            let channel = FlutterMethodChannel(
                name: "ihrp.dev/example",
                binaryMessenger: controller.binaryMessenger
            )
            
            channel.setMethodCallHandler({ [weak self] (
                call: FlutterMethodCall,
                result: @escaping FlutterResult) -> Void in
                switch call.method {
                case "getDeviceInfo":
                    var map: [String: Any] = [:]
                    
                    if let infoDictionary = Bundle.main.infoDictionary {
                        map["versionName"] = infoDictionary["CFBundleShortVersionString"]
                        map["versionCode"] = infoDictionary["CFBundleVersion"]
                    } 

                    // call "result(data)" to send data to Flutter
                    result(map)
                default:
                    result(FlutterMethodNotImplemented)
                }
            })
        }
        
        return super.application(application, didFinishLaunchingWithOptions: launchOptions)
    }
}
```