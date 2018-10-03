# Notifications

To enable and configure notifications, [ChannelNotificationConfig](https://speicher210.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk.notification/-channel-notifications-config/index.html) needs to be registered while initializing SDK and at least one of the background scanning modes has to be enabled.

```kotlin
val context: Context = this

val channelNotificationCallback = object : ChannelNotificationsConfig.ChannelNotificationCallback {
    override fun winguNotifications(channels: Collection<Channel>): Collection<WinguNotification> {
        // you can return any number of WinguNotifications — each will represent
        // a separate status bar notification.

        if (channels.isEmpty()) {
        	// don't show notifications if there are no nearby channels
            return emptyList()
        }

        // otherwise, create a single grouped notification
        // for all nearby channels:
        val pendingIntent = PendingIntent.getActivity(context, 0, Intent(context, YourMainActivity::class.java), 0)
        return listOf(WinguNotification.createGroupNotification(channels, "Nearby channels", pendingIntent))
    }
}

val channelNotificationConfig = ChannelNotificationConfig.Builder(channelNotificationCallback)
    .build()

WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
    .channelNotificationsConfig(channelNotificationConfig)
    .ibeaconBackgroundScan(true)
    .geofenceBackgroundScan(true)
    //...
    .build()
```

To customize your notifications look at [ChannelNotificationsConfig.Builder](https://speicher210.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk.notification/-channel-notifications-config/-builder/index.html) class. Among others, it allows you to add a [ChannelFilter](https://speicher210.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk.channel.filter/-channel-filter/index.html) which changes the way the notifications show up.
