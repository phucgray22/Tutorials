# Flutter: How to use `context` outside BuildContext


## 1. Setup `navigatorKey`

```dart
...

final GlobalKey<NavigatorState> navigatorKey = GlobalKey<NavigatorState>();

class App extends StatefulWidget {
  const App({super.key});

  @override
  State<App> createState() => _AppState();
}

class _AppState extends State<App> {
  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      navigatorKey: navigatorKey, // assign `navigatorKey` here
    );
  }
}
```

## 2. Usage

Use with **NAVIGATION**
```dart
  Navigator.of(navigatorKey.currentContext!).pop();
```

Use with **BLOC**
```dart
navigatorKey.currentContext!.read<CommonBloc>().add(
  const CommonLoading(true),
);
```