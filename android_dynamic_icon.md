# Android Dynamic Icon (Kotlin)

## 1. Drag and drop icon into `drawable` folder

![](/images/Screenshot%202024-05-07%20at%2009.23.55.png)

## 2. For each icon -> create a `<activity-alias>` tag in `AndroidManifest.xml` file

![](/images/Screenshot%202024-05-07%20at%2009.31.11.png)

```
<activity-alias
    android:name=".IconBlue"
    android:exported="true"
    android:enabled="false"
    android:icon="@drawable/blue"
    android:roundIcon="@drawable/blue"
    android:targetActivity=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity-alias>

<activity-alias
    android:name=".IconGreen"
    android:exported="true"
    android:enabled="false"
    android:icon="@drawable/green"
    android:roundIcon="@drawable/green"
    android:targetActivity=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity-alias>
```

## 3. Create `changeIcon` function

```
private fun changeIcon(icon: String?) {
        val aliases = listOf("MainActivity", "IconBlue", "IconGreen")

        val manager = packageManager

        aliases.forEach { alias ->
            if (alias == icon)
            {
                manager.setComponentEnabledSetting(
                    ComponentName(
                        this@MainActivity,
                        "com.example.demo.${alias}"
                    ),
                    PackageManager.COMPONENT_ENABLED_STATE_ENABLED,
                    PackageManager.DONT_KILL_APP
                )

                Toast
                    .makeText(
                        this@MainActivity, "Enable New Icon",
                        Toast.LENGTH_LONG
                    )
                    .show()
            }
            else
                manager.setComponentEnabledSetting(
                    ComponentName(
                        this@MainActivity,
                        "com.example.demo.${alias}"
                    ),
                    PackageManager.COMPONENT_ENABLED_STATE_DISABLED,
                    PackageManager.DONT_KILL_APP
                )
        }
    }
```

## 4. Usage

```
// Change new icon
changeIcon("IconRed")

// Reset default
changeIcon("MainActivity")
```