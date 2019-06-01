# Overview of Privacy Changes in Android Q (Google I/O'19)

> https://www.youtube.com/watch?v=bFp2n5OxYEE

---

### Storage permission alternatives:

```java
// Read/write files in app's sandboxed external storage directory
Context.getExternalFilesDir(DIRECTORY_MUSIC)

// Contribute directly to the user's media collection
MediaStore.Images.getContentUri(volumeName)

// Work with arbitrary documents or by MIME type via file picker
Intent.ACTION_OPEN_DOCUMENT

// Manage files in a directory hierarchy via file picker
Intent.ACTION_OPEN_DOCUMENT_TREE
```

### Ensure users are not surprised with your data usage. Some best practices:

1. Collect only the data that you need.
2. Only share the data once you get explicit user consent/
3. If you're transferring data off the device, ensure that it is encrypted and secure.
4. Only keep data for as long as you need it.

### Device Identifiers

* Lock down hardware-based device identifiers. Immutable device identifiers will no longer be available via **READ_PHONE_STATE**
* MAC address randomization

### Notification APIs

* setFullScreenIntent API: allow you to set an intent, which will be fired off if the user is not actively using the device. So if the device is locked, the user will see a full-sreen experience. If the user is currently using the device, they will see the heads-up notifications.

```kotlin
val builder = NotificationCompat.Builder(this, 
    CHANNEL_ID)
    ...

    // Require the USE_FULL_SCREEN_INTENT permission
    .setFullScreenIntent(createFullScreenIntent(), true)
```

### Background app launching will be blocked

### Foreground services

* A service that wants to become a foreground service should specify its foreground service type(s) in the manifest. (Service types: **sync, media playback, media projection, phone call, location, connected device**).
* Example foreground service requires location type:

```xml
// New in Q, declare service type in manifest
<service
    android:name="..."
    android:foregroundServiceType="location"/>
```

## Reference link

* [Android Q privacy change: Scoped storage](https://developer.android.com/preview/privacy/scoped-storage)

