![Platform](https://img.shields.io/badge/platform-Android%205.0+-%23A4C639.svg)
[![Version](https://img.shields.io/badge/version-3.6.0-%2346C800.svg)](https://wingu-GmbH.github.io/wingu-android-sdk/CHANGELOG.html)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](https://github.com/wingu-GmbH/wingu-android-sdk/blob/master/LICENSE)
[![API docs](https://img.shields.io/badge/API-docs-%2346C800.svg)](https://wingu-GmbH.github.io/wingu-android-sdk/dokka/wingu-android-sdk/)


# wingu-android-sdk

wingu-android-sdk is an SDK for [wingu.de](www.wingu.de) service. This framework will allow you to find nearby beacons and geofences at user's location, and display content attached to those channels. It also supports discovering content via QR codes and NFC tags.

# Table of contents

1. [Installation](#installation)
2. [Requirements](#requirements)
3. [Listening for nearby channels](#listening-nearby-channels)
4. [Documentation](#documentation)
5. [About wingu](#wingu)
6. [Authors](#authors)
7. [License](#license)

<h2 id="installation">1. Installation</h2>

In `build.gradle` of your application module add this:

```
android {
  compileSdkVersion 27

  defaultConfig {
    // wingu SDK requires `minSdkVersion >= 21` and `targetSdkVersion <= 27`.
    minSdkVersion 21
    targetSdkVersion 27
    multiDexEnabled true
  }
}

repositories {
  maven { url "https://maven.google.com" }
  maven { url "https://jitpack.io" }
  maven { url "https://github.com/wingu-GmbH/wingu-android-sdk/raw/master/releases" }
}

dependencies {
  implementation 'com.github.Speicher210.wingu-android-sdk:sdk:3.6.0'
  implementation 'com.github.Speicher210.wingu-android-sdk:component-video:3.6.0' // optional
  implementation 'com.github.Speicher210.wingu-android-sdk:qrcodescanner:3.6.0' // optional
}
```

If you are using __Kotlin__ standard library, __Support Libraries__, __Google Play Services__ or have issues with dependencies, read [dependency management](https://wingu-GmbH.github.io/wingu-android-sdk/dependency-management).

Initialize wingu SDK in your `Application` subclass:

```kotlin
override fun onCreate() {
    super.onCreate()
    WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
//      .registerComponent(..) // optionally activate maps & video components
        .build()
}
```

> For start, you can use `WinguSDKBuilder.DEMO_APP_ID` key.

<h2 id="requirements">2. Requirements</h2>

To be able to listen for nearby channels (iBeacons and geofences) you need to have:

- runtime LOCATION permission (on Android 6+; either ACCESS_COARSE_LOCATION or ACCESS_FINE_LOCATION)
- Bluetooth on
- location services on
- Google Play services

You can check this on your own, or use a `PrerequisitesChecker` class, for example in your main activity:

```kotlin
private val WINGU_SDK_PREREQUISITES_REQUEST = 100

override fun onStart() {
    super.onStart()
    if (PrerequisitesChecker.checkWithDefaultDialogs(this, WINGU_SDK_PREREQUISITES_REQUEST)) {
        // all prerequisites are met
        listenForNearbyChannels()
    }
    // if `checkWithDefaultDialogs` above returned `false`,
    // dialogs are shown to resolve any issues,
    // and results are returned in `onActivityResult`
}

override fun onStop() {
    stopListeningForNearbyChannels()
    super.onStop()
}

override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    super.onActivityResult(requestCode, resultCode, data)
    if (requestCode == WINGU_SDK_PREREQUISITES_REQUEST) {
        val results = PrerequisitesChecker.getResolveResults(data)
        if (results != null && results.failed.isEmpty()) {
            listenForNearbyChannels()
        } else {
            Log.e("wingu", "Could not get ResolveResults or some failed")
        }
    }
}
```

<h2 id="listening-nearby-channels">3. Listening for nearby channels</h2>

Once you have all the prerequisites, you can listen for nearby channel events. Using [NearbyChannelObserver](https://wingu-GmbH.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk.utils/-nearby-channel-observer/index.html) you can be notified for example when you enter a geofence zone, or go out of beacon range.

```kotlin
private var channelsSubscription: Subscription? = null

private fun listenForNearbyChannels() {
    channelsSubscription?.unsubscribe()
    channelsSubscription = WinguSDK.getInstance()
        .getNearbyChannelObserver()
        .getChannelEvents()
        .subscribe(
            { channelEvents -> Log.d("wingu", "$channelEvents") },
            { throwable -> Log.e("wingu", "Could not get nearby channels", throwable) }
        )
}

private fun stopListeningForNearbyChannels() {
    channelsSubscription?.unsubscribe()
    channelsSubscription = null
}
```

Alternatively, you can fetch all channels that are nearby at any given moment:

```kotlin
val nearbyChannels = WinguSDK.getInstance().getNearbyChannelObserver().getCurrentNearbyChannels()
```

Finally, you can display content of selected channel:

```kotlin
private fun onChannelClicked(channel: Channel) {
    val intent = ChannelDetailsActivity.newInstance(this, channel, channel.name)
    startActivity(intent)
}
```

> If you used `WinguSDKBuilder.DEMO_APP_ID` in the first step, you can launch
> the Android Emulator and set latitude to `37.422` and longitude to `-122.084`.
> Within a few seconds you will receive your first channel event.
> If you change the location (for example set the latitude to `38.422`) you will receive a `LOST` event, and the content will no longer be accessible.
> _Note_: due to limitations in the Emulator, it's not possible to receive iBeacon or geofence events when the app is in background.

<h2 id="documentation">4. Documentation</h2>

- [API docs](https://wingu-GmbH.github.io/wingu-android-sdk/dokka/wingu-android-sdk/)
- [Changelog](https://wingu-GmbH.github.io/wingu-android-sdk/CHANGELOG.html)
- [Demo app](https://github.com/wingu-GmbH/wingu-sdk-android-demoapp)

- [Dependency management](https://wingu-GmbH.github.io/wingu-android-sdk/dependency-management)
- [Displaying channel content](https://wingu-GmbH.github.io/wingu-android-sdk/displaying-channel-content)
- [Enabling location (maps) component](https://wingu-GmbH.github.io/wingu-android-sdk/location-component)
- [Enabling video component](https://wingu-GmbH.github.io/wingu-android-sdk/video-component)
- [Notifications](https://wingu-GmbH.github.io/wingu-android-sdk/notifications)
- [NFC & Eddystone](https://wingu-GmbH.github.io/wingu-android-sdk/nfc-eddystone)
- [Permissions](https://wingu-GmbH.github.io/wingu-android-sdk/permissions)
- [QR codes](https://wingu-GmbH.github.io/wingu-android-sdk/qr-codes)
- [Scan configuration & autostart](https://wingu-GmbH.github.io/wingu-android-sdk/scan-configuration)

<h2 id="wingu">5. About wingu</h2>

This is a wingu open source project. With wingu platform, API and SDK it is easier then ever to use proximity technologies in new and exciting ways, such as creating a simple app or adding proximity functionality to your existing application. For more information check out: [https://www.wingu.de/en/developer/](https://www.wingu.de/en/developer/) or start a free trail at [https://wingu-portal.de/register](https://wingu-portal.de/register).

<h2 id="authors">6. Authors</h2>

Android Developers @ wingu GmbH

[Mateusz](https://github.com/armatys)  
[Wiktor](https://github.com/wingu-wiktor)

<h2 id="license">7. License</h2>

wingu-android-sdk is available under the Apache 2.0 license. See the [LICENSE](https://github.com/wingu-GmbH/wingu-android-sdk/blob/master/LICENSE) file for more info.
