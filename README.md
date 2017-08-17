# wingu-android-sdk

![Wingu](https://pbs.twimg.com/profile_banners/4774303042/1452516691/1500x500)
wingu-android-sdk is an  SDK for [wingu.de](www.wingu.de) service. This framework will allow to connect with beacons and geofences nearby user location. 

## Overview

wingu SDK requires `minSdkVersion >= 21`.

[javadoc](https://speicher210.github.io/wingu-android-sdk/javadoc/)

## Quickstart

#### In `build.gradle` of your application module add this:

```
android {
  dataBinding {
    enabled true // data binding is required to display the contents of a channel
  }

  defaultConfig {
    multiDexEnabled true
  }
}

repositories {
  maven { url "https://maven.google.com" }
  maven { url "https://jitpack.io" }
  maven { url "https://github.com/Speicher210/wingu-android-sdk/raw/master/releases" }
}

dependencies {
  compile 'com.github.Speicher210.wingu-android-sdk:sdk:0.3.2'
  compile 'com.github.Speicher210.wingu-android-sdk:component-video:0.3.2' // optional
}
```

#### (Optional) Add Google API key

To use the Location component with Google Maps, add your Google API key in `AndroidManifest.xml`:

```
<application>
  <meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="TODO_PUT_YOUR_KEY"/>
</application>
```

#### Initialize the SDK

In your `Application` subclass add this:

```
@Override
public void onCreate() {
  super.onCreate();
  WinguSDKBuilder.with(this, YOUR_API_KEY)
    .registerComponent(VideoWinguComponent.spec(R.string.google_api_key)) // optional
    .build();
}
```

#### Prerequisites

To be able to listen for nearby channels you need to have:

- runtime LOCATION permission (on Android 6+; either ACCESS_COARSE_LOCATION or ACCESS_FINE_LOCATION)
- bluetooth on
- location services on
- Google Play services

You can handle this on your own, or use a `PrerequisitesChecker` class

```
private static final int WINGU_SDK_PREREQUISITES_REQUEST = 535;

@Override
protected void onStart() {
  super.onStart();
  if (PrerequisitesChecker.checkWithDefaultDialogs(this, WINGU_SDK_PREREQUISITES_REQUEST)) {
    // All prerequisites are met
    listenForNearbyChannels();
  } // otherwise, we show dialogs to resolve any issues, and return results in onActivityResult
}

@Override
protected void onStop() {
  stopListeningForNearbyChannels();
  super.onStop();
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  super.onActivityResult(requestCode, resultCode, data);
  if (requestCode == WINGU_SDK_PREREQUISITES_REQUEST) {
    final ResolveResults results = PrerequisitesChecker.getResolveResults(data);
    if (results != null && results.getFailed().size() == 0) {
      listenForNearbyChannels();
    } else {
      Log.e(TAG, "Could not get ResolveResults or some failed");
    }
  }
}
```

#### Listening for nearby channels

Once you have all the prerequisites, you can listen for nearby channel events.

```
@Nullable
private Subscription channelsSubscription;

private void listenForNearbyChannels() {
  if (channelsSubscription != null) {
    channelsSubscription.unsubscribe();
  }

  channelsSubscription = WinguSDK.getInstance().getNearbyChannelObserver()
      .getChannelEvents()
      .subscribe(new Action1<ChannelEvents>() {
        @Override
        public void call(ChannelEvents channelEvents) {
          for (Channel channel : channelEvents.getChannels()) {
            Log.d(TAG, "Channel event " + channelEvents.getEventType() + " " + channel);
          }
        }
      }, new Action1<Throwable>() {
        @Override
        public void call(Throwable throwable) {
          Log.e(TAG, "Could not get nearby channels", throwable);
        }
      });
}

private void stopListeningForNearbyChannels() {
  if (channelsSubscription != null) {
    channelsSubscription.unsubscribe();
    channelsSubscription = null;
  }
}
```

#### Displaying a channel

To display channel’s contents, use `ChannelDetailsFragment`:

```
ChannelKey channelKey = ChannelKey.from(channel);
ChannelDetailsFragment fragment = ChannelDetailsFragment.newInstance(channelKey);
```
## Authors

Android Developers @ wingu AG


[Mateusz](https://github.com/armatys)
[Wiktor](https://github.com/wingu-wiktor)

## License

winguSDK-iOS is available under the Apache 2.0 license. See the LICENSE file for more info.
