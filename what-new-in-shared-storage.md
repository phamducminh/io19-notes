# What’s New in Shared Storage (Google I/O'19)

> https://www.youtube.com/watch?v=3EtBw5s9iRY

---

### What is shared storage?
> Also known as external storage

Shared storage is a common storage pool that is granted by the storage permission

Functions that return shared storage locations include:

```java
Context.getExternalFilesDirs()
  e.g. /sdcard/Android/data/<packagename>/files
Context.getExternalCacheDirs()
  e.g. /sdcard/Android/data/<packagename>/cache
Context.getExternalMediaDirs()
  e.g. /sdcard/Android/media/<packagename>/
Context.getObbDirs()
  e.g. /sdcard/Android/obb/<packagename>/
```

Android P

![](https://github.com/phamducminh/io19-notes/blob/master/resources/storage-android-p.png)

Android Q

![](https://github.com/phamducminh/io19-notes/blob/master/resources/storage-android-q.png)

### MediaStore
> Collection of common media that belongs to the user

#### What belongs?

**MediaStore** is designed to hold the content that belongs to the user, and which users expect to see in other apps:

    MediaStore.Audio
    MediaStore.Video
    MediaStore.Images

* **Content contributed to MediaStore persists post app uninstall**
* Once the app is uninstalled, the content is considered to be orphaned. If the same app is then reinstall later, it will need to obtain the storage permission from the user to see content it had previously contributed.

#### What doesn't?

Media that is only for your app or which is confusing to users, such as album art or stickers.

#### Downloads

New `MediaStore.Downloads` collection (besides Audio, Video and Images) for storing content downloaded by user.

Any app can contribute but picking items requires user interaction through `ACTION_OPEN_DOCUMENT`

#### Contribute

> In Android Q, you can now contribute to MediaStore collections with no additional permissions required. The "Storage" runtime permission is only needed if you want to view media contributed by other apps.

> Any items that you contribute to MediaStore persist beyond the lifetime of your app.

```kotlin
// Create a new image in the user's collection
val values = ContentValues().apply {
    put(MediaStore.Images.Media.DISPLAY_NAME, "IMG1024.JPG")
    put(MediaStore.Images.Media.MIME_TYPE, "image/jpeg")
    put(MediaStore.Images.Media.IS_PENDING, 1)
}

val resolver = context.getContentResolver()
val collection = MediaStore.Images.Media
        .getContentUri(MediaStore.VOLUME_EXTERNAL_PRIMARY)
val item = resolver.insert(collection, values)
```

##### The IS_PENDING flag

* When you inserted an item that is marked as pending, by default, it will be hidden from other applications on the device. When they go query MediaStore, they're not going to see that item that's pending.

```kotlin
// Write data into our pending image
resolver.openFileDescriptor(item, "w", null).user { pfd -> 
    // ...
}

// Now that we're finished, reveal to other apps
values.clear()
values.put(MediaStore.Images.Media.IS_PENDING, 0)
resolver.update(item, values, null, null)
```

##### Influencing directories

* Newly contributed media is organized based on its type. For example, new image/* files are placed in the `Pictures` directory by default.
* You can set `MediaColumns.RELATIVE_PATH` to influence where new files are placed on disk. You can also move files on disk by changing `RELATIVE_PATH` or `DISPLAY_NAME` during an `update()` call.
* Starting in Q, you can contribute media to any attached storage device by using its volume name. `VOLUME_EXTERNAL_PRIMARY` is the primary shared storage device, and other volumes can be discovered using `MediaStore.getExternalVolumeNames()`.
* To query, insert, update, or delete for a specific volume, pass the volume name to any `MediaStore.*.getContentUri()` method.

```kotlin
// Publishing audio into a specific directory on specific device
val values = ContentValues().apply {
    put(MediaStore.Audio.Media.RELATIVE_PATH, "Music/My Artist/My Album")
    put(MediaStore.Audio.Media.DISPLAY_NAME, "My Song.mp3")
}

val volumeNames = MediaStore.getExternalVolumeNames(context)
// ...
val collection = MediaStore.Audio.Media.getContentUri(volumeName)
val item = resolver.insert(collection, values)
```

#### Discovery

> You can discover items via `ContentResolver.query()` and read/write them using `ContentResolver.openFileDescriptor()`. Always use these methods instead of the deprecated DATA columns.

> Pending items are hidden by default, but they can be revealed with `MediaStore.setIncludePending()`

```kotlin
// Find all videos (both published and pending)
val collection = MediaStore.Video.Media.getContentUri(volumeName)
val collectionWithPending = MediaStore.setIncludePending(collection)
reolver.query(collectionWithPending, null, null, null).use { c ->
    // ...
}

// Open a specific media item
resolver.openFileDescriptor(item, mode).use { pfd ->
    // ...
}

// Load thumbnail of specific media item
val thumb = resolver.loadThumbnail(item, Size(640, 480), null)
```

#### Editing

> The "Storage" runtime permission lets you discover and read media owned by other apps. If you want to modify that media, you'll need to involve the user to gain write access.

> Calls to update(), delete(), or openFileDescriptor() will throw a `RecoverableSecurityException` if you don't have the necessary permissions.

#### Metadata

> Android Q protects location metadata embedded in media files. If you need to access location data, add the `ACCESS_MEDIA_LOCATION` permission to your app

> The LATITUDE and LONGITUDE fields in `MediaStore` are now deprecated, use `ExifInterface` instead.

### Scoped Storage

* When your app target Q, it's put into a "scoped" storage mode, meanning it no longer has direct access to /sdcard/. Attempting direct access will result in a `FileNotFoundException` or EPERM error
* You continue to have direct access to all your [package-specific directories](what-new-in-shared-storage.md#what-is-shared-storage). Use either `MediaStore` or the Storage Access Framework to work with content beyond those locations.

#### Beyond local

> Intents like `ACTION_OPEN_DOCUMENT` and `ACTION_CREATE_DOCUMENT` give users the freedom to work with files in any local or remoe storage locations.

![](https://github.com/phamducminh/io19-notes/blob/master/resources/beyond-local.png)

Benefits:
* Instead of building your own file-picking experience, they're now presented with a single uniform experience.

#### Helping you adapt

```xml
<!-- Temporarily opt-out when targeting Q. -->
<use-sdk android:targetSdkVersion="29" />
<application
    android:requestLegacyExternalStorage="true">
```

```java
// Check what storage mode your app is running under
if (Environment.isExternalStorageLegacy()) {
    // ...
}
```

# Reference

* [Scoped Storage](https://developer.android.com/preview/privacy/scoped-storage)
* [Improvements in creating files on external storage](https://developer.android.com/preview/features#create-files-external-storage)




