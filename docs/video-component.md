# Enabling video component

To use Video component, you need to obtain a Google API key. Then you need to register video component when initializing wingu SDK:

```kotlin
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
    .registerComponent(VideoWinguComponent.spec(PlainString("GOOGLE_API_KEY")))
    //...
```

For more information go to [YouTube Android Quickstart](https://developers.google.com/youtube/v3/quickstart/android).
