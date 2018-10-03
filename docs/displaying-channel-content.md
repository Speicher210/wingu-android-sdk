# Displaying channel content


You can display wingu content using [ChannelDetailsActivity](https://speicher210.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk.ui.details/-channel-details-activity/index.html), or by inserting [ChannelDetailsFragment](https://speicher210.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk.ui.details/-channel-details-fragment/index.html) into your own activity.

To create `ChannelDetailsFragment`:

```kotlin
val channelKey: ChannelKey = ChannelKey.from(channel)
val fragment: ChannelDetailsFragment = ChannelDetailsFragment.newInstance(channelKey)

// use FragmentManager to insert and display the fragment
```
