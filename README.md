# wingu-android-sdk

wingu-android-sdk is an  SDK for [wingu.de](www.wingu.de) service. This framework will allow to connect with beacons and geofences nearby user location. 

## Overview

wingu SDK requires `minSdkVersion >= 21` and `targetSdkVersion <= 27`.

[API docs](https://speicher210.github.io/wingu-android-sdk/dokka/wingu-android-sdk/)

## Quickstart

### In `build.gradle` of your application module add this:

```
android {
  compileSdkVersion 27

  defaultConfig {
    minSdkVersion 21
    targetSdkVersion 27
    multiDexEnabled true
  }
}

repositories {
  maven { url "https://maven.google.com" }
  maven { url "https://jitpack.io" }
  maven { url "https://github.com/Speicher210/wingu-android-sdk/raw/master/releases" }
}

dependencies {
  compile 'com.github.Speicher210.wingu-android-sdk:sdk:3.0.2'
  compile 'com.github.Speicher210.wingu-android-sdk:component-video:3.0.2' // optional
}
```

If you are using __Kotlin__ standard library, __Support Libraries__, __Google Play Services__ or have issues with dependencies, read [dependency management](./docs/dependency-management.md).

### Initialize the SDK

In your `Application` subclass add this:

```
public class App extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
      .registerComponent(LocationWinguComponent.spec..) // optional
      .registerComponent(VideoWinguComponent.spec(new PlainString(your_google_api_key))) // optional
      .build();
  }
}
```

### Location (map) component

There are two ways in which the location component is rendered.

#### Option 1 - static maps

By default, the map is rendered using "Google Static Maps API" without Google API key. This is fine for development, but for production you should provide your own Google API key:

```
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
  .registerComponent(LocationWinguComponent.specStaticMaps(new PlainString(your_google_api_key)))
  //...
```

You can also provide a URL signing secret which tags requests to Google Static Maps API with a higher degree of security and is required if you exceed the free daily quota.

However, when using static maps, it's not really possible to restrict the usage of your Google API key.

[Static Maps API Quickstart](https://developers.google.com/maps/documentation/static-maps/get-api-key)

### Notifications 

To enable and configure notifications `ChannelNotificationConfig` needs to be registered while initializing SDK: 

```
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
  .channelNotificationsConfig(channelNotificationConfig)
  //...
  .build()
```

`ChannelNotificationConfig.Builder(ChannelNotificationCallback calback)` has to be used to create notifiaction config. 
The mandatory step is to provide implementation of `ChannelNotifcationCallback`to Builder's constructor. It's used to convert nearby channels to colection of `WinguNotification`. More detailed documetation about notifications can be found [here](https://docs.google.com/document/d/1U9qOQJBh5pXty8Z6YkOU0KLSQ5dZh7nZYk6_n9ov53U/edit?usp=sharing) 
 
#### Option 2 - map view

If you want to limit the usage of your Google API key to your app's package ID ([more about API key restrictions](https://developers.google.com/maps/documentation/android-api/signup#restrict-key)), use the "Maps Android API" variant:

```
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
  .registerComponent(LocationWinguComponent.specMapView())
  //...
```

Make sure that Google API key is added to your `AndroidManifest.xml` file, otherwise the map will not be displayed.

[Maps Android API Quickstart](https://developers.google.com/maps/documentation/android-api/signup).

### Video component

To use Video component, you need to obtain Google API key, making sure that "YouTube Data API v3" is enabled.

For more information go to [YouTube Android Quickstart](https://developers.google.com/youtube/v3/quickstart/android).

### Prerequisites

To be able to listen for nearby channels (iBeacons and geofences) you need to have:

- runtime LOCATION permission (on Android 6+; either ACCESS_COARSE_LOCATION or ACCESS_FINE_LOCATION)
- bluetooth on
- location services on
- Google Play services

You can handle this on your own, or use a `PrerequisitesChecker` class:

```
private static final int WINGU_SDK_PREREQUISITES_REQUEST = 535;

@Override
protected void onStart() {
  super.onStart();
  if (PrerequisitesChecker.checkWithDefaultDialogs(this, WINGU_SDK_PREREQUISITES_REQUEST)) {
    // All prerequisites are met
    listenForNearbyChannels();
  }
  // if `checkWithDefaultDialogs` above returned `false`,
  // dialogs are shown to resolve any issues,
  // and results are returned in `onActivityResult`
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

### Listening for nearby channels

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

### Displaying a channel

To display channel’s contents, use `ChannelDetailsFragment`:

```
ChannelKey channelKey = ChannelKey.from(channel);
ChannelDetailsFragment fragment = ChannelDetailsFragment.newInstance(channelKey);
```
## Demo App
[Demo app](https://github.com/Speicher210/wingu-sdk-android-demoapp)

## Authors

Android Developers @ wingu AG


[Mateusz](https://github.com/armatys)  
[Wiktor](https://github.com/wingu-wiktor)

## wingu

This is a wingu open source project. With wingu platform, API and SDK it is easier then ever to use proximity technologies in new and exciteing ways, such as creating a simple app or adding proximity functionality to your existing application. For more information check out: [https://www.wingu.de/en/developer/](https://www.wingu.de/en/developer/) or start a free trail at [https://wingu-portal.de/register.](https://wingu-portal.de/register.)

## License

winguSDK-android is available under the Apache 2.0 license. See the LICENSE file for more info.
