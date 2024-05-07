# IOS Dynamic Icon (Swift)

## 1. Drag and drop icons into `icons` folder
Create icons at appicon.co[](https://www.appicon.co/)

![](/images/Screenshot%202024-05-07%20at%2009.59.18.png)

> #### Note
> - Prepare icon to generate:
> 1024x1024
> - Naming: 
> icon@2x, icon@3x



## 2. Create `changeIcon` function

```
func changeIcon(name: String?) {
    UIApplication.shared.setAlternateIconName(name)
}
```

You can change icon without alert or you want to custom alert

```swift
func changeIconWithoutAlert(name: String?) {
    if UIApplication.shared.responds(to: #selector(getter: UIApplication.supportsAlternateIcons)) && UIApplication.shared.supportsAlternateIcons {
        typealias setAlternateIconName = @convention(c) (NSObject, Selector, NSString, @escaping (NSError) -> ()) -> ()
        let selectorString = "_setAlternateIconName:completionHandler:"
        let selector = NSSelectorFromString(selectorString)
        let imp = UIApplication.shared.method(for: selector)
        let method = unsafeBitCast(imp, to: setAlternateIconName.self)
        method(UIApplication.shared, selector, name as NSString, { _ in })
    }
}
```

## 3. Usage

Change new icon:

```swift
changeIcon("IconRed")
```

Change new icon without alert:

```swift
changeIconWithoutAlert("IconRed")
```

Reset default:
```swift
changeIcon(nil)
```