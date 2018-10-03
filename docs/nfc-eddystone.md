# NFC & Eddystone

Wingu SDK can intercept NFC tags and Eddystone URLs and display content attached to them. This behavior can be turned on during initialization:

```kotlin
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
    .nfc(true)
    .eddystoneUrl(true)
    //...
    .build()
```

Both functions `nfc` and `eddystoneUrl` are overloaded. Second function variant has an argument that can be used to create custom activity intent. Be default, `ChannelsDetailsActivity` is displayed when an NFC or Eddystone is intercepted.
