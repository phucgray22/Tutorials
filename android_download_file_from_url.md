# Android Download File From URL (Kotlin)

> Without permission
>  - **WRITE_EXTERNAL_STORAGE**, 
>  - **READ_EXTERNAL_STORAGE**, 
>  - **MANAGE_EXTERNAL_STORAGE** 

## 1. Setup `AndroidManifest.xml` file

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" xmlns:tools="http://schemas.android.com/tools">
    // Add this permission
    <uses-permission android:name="android.permission.INTERNET" />

    <application>
        ...
    </application>

</manifest>
```

## 2. Create `downloadFileFromURL ` function

```java
private fun downloadFileFromURL(url: String, fileName: String) {
  val request = DownloadManager.Request(Uri.parse(url))
      .setTitle(fileName)
      .setDescription("Downloading...")
      .setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED)
      .setAllowedOverMetered(true)
      .setDestinationInExternalPublicDir(Environment.DIRECTORY_DOWNLOADS, fileName)
      
  val dm = getSystemService(DOWNLOAD_SERVICE) as DownloadManager
  dm.enqueue(request)
}
```

## 3. Usage

```java
// pdf
downloadFileFromURL(
  url = "https://www.orimi.com/pdf-test.pdf", 
  fileName = "pdf_test.pdf"
)

// word
downloadFileFromURL(
  url = "https://homepages.inf.ed.ac.uk/neilb/TestWordDoc.doc",
  fileName = "TestWordDoc.doc"
)
```