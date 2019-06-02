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

New **MediaStore.Downloads** collection (besides Audio, Video and Images) for storing content downloaded by user.

Any app can contribute but picking items requires user interaction through ACTION_OPEN_DOCUMENT

### Contribute

### Discovery

### Editing

## Reference

* https://developer.android.com/preview/privacy/scoped-storage




