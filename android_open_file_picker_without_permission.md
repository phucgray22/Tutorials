# Android Open File Picker (Kotlin) without permissions

> Without permission
>
> - **WRITE_EXTERNAL_STORAGE**,
> - **READ_EXTERNAL_STORAGE**,
> - **MANAGE_EXTERNAL_STORAGE**

## 1. Create `openFilePicker` function

```java
private fun openFilePicker(allowMultipleFiles: Boolean = true) {
  val mimes = arrayOf(
      "application/msword", // .doc
      "application/vnd.openxmlformats-officedocument.wordprocessingml.document",  // .docx
      "application/vnd.ms-excel", // .xls
      "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",  // .xlsx
      "application/pdf", // .pdf
      "image/png", // png
      "text/plain", // txt
  )

  val intent = Intent(Intent.ACTION_OPEN_DOCUMENT)
  intent.type = "*/*"
  intent.putExtra(Intent.EXTRA_ALLOW_MULTIPLE, allowMultipleFiles)
  intent.putExtra(Intent.EXTRA_MIME_TYPES, mimes)

  startActivityForResult(intent, REQUEST_CODE)
}

private fun getFileSize(uri: Uri?): Long {
  if(uri == null) {
      return 0
  }

  val contentResolver = contentResolver
  val cursor = contentResolver.query(uri, null, null, null, null)

  cursor?.use {
      val sizeIndex = it.getColumnIndex(OpenableColumns.SIZE)
      it.moveToFirst()
      return it.getLong(sizeIndex)
  }

  return 0
}

private fun getFileName(uri: Uri): String {
  var result: String? = null

  if (uri.scheme == "content") {
    val cursor = contentResolver.query(uri, null, null, null, null)
    try {
        if (cursor != null && cursor.moveToFirst()) {
            val index = cursor.getColumnIndex(OpenableColumns.DISPLAY_NAME)
            result = cursor.getString(index)
        }
    } finally {
        cursor!!.close()
    }
  }

  if (result == null) {
      result = uri.path
      val cut = result!!.lastIndexOf('/')
      if (cut != -1) {
          result = result.substring(cut + 1)
      }
  }

  return result        
}

private fun getMimeType(uri: Uri, context: Context): String? {
    val mimeType = if (ContentResolver.SCHEME_CONTENT == uri.scheme) {
        val cr: ContentResolver = context.contentResolver
        cr.getType(uri)
    } else {
        val fileExtension = MimeTypeMap.getFileExtensionFromUrl(
            uri
                .toString()
        )
        MimeTypeMap.getSingleton().getMimeTypeFromExtension(
            fileExtension.lowercase(Locale.getDefault())
        )
    }

    return mimeType
}


@Deprecated("Deprecated in Java")
override fun onActivityResult(
    requestCode: Int, resultCode: Int, resultData: Intent?) {
    if (requestCode == REQUEST_CODE && resultCode == Activity.RESULT_OK) 
    {
        val count = resultData?.clipData?.itemCount ?: 0
        val files = mutableListOf<Map<String, Any?>>()
        for(i in 0 until count) 
        {
            val uri = resultData?.clipData?.getItemAt(i)?.uri
            val fileSize = getFileSize(uri)
            if(uri != null) 
            {
                files.add(mapOf(
                    "uri" to uri.toString(),
                    "size" to fileSize,
                    "fileName" to getFileName(uri),
                    "ext" to getFileName(uri).substringAfterLast("."),
                    "mimeType" to getMimeType(uri, context = applicationContext)
                ))
            }
        }
    }
}

companion object {
    const val REQUEST_CODE = 123
}
```

## 2. Usage

```java
// Multiple files
openFilePicker()

// Single file
openFilePicker(allowMultipleFiles = false)
```
