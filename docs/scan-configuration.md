# Scan configuration & autostart

## Enabling background and foreground scanning

To enable/disable beacon or geofence scan for foreground or background call following methods on WinguSdkBuilder:

```kotlin
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
    .ibeaconForegroundScan(true)  // default true
    .ibeaconBackgroundScan(true)  // default false
    .geofenceForegroundScan(true) // default true
    .geofenceBackgroundScan(true) // default false
    .build();
```

## Configuring BLE scan mode

There are also overloaded version of methods:

- `ibeaconForegroundScan(enabled: Boolean, scanMode: BleScanMode)` 
- `ibeaconBackgroundScan(enabled: Boolean, scanMode: BleScanMode)` 
 
Refer to [BleScanMode constructor](https://wingu-GmbH.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.blescanner.ble/-ble-scan-mode/-init-.html) for available options.

## Persistent background scanning

By default, when iBeacon background scanning is enabled, the SDK uses `JobScheduler`, so that when a device enters Doze mode, the scanning will not be performed in order to save the battery.

If you need the scanning to be working at all times, you can use a persistent scan service, that keeps running in the background using a "foreground" service (which displays a status bar notification).

To turn it on, set the `persistent` parameter of the method [ibeaconBackgroundScan](https://wingu-GmbH.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk/-wingu-s-d-k-builder/ibeacon-background-scan.html) to `true`.

## Autostart

Once you initialize wingu SDK (having enabled background scanning), your app will be started automatically after a reboot, so you will be able to listen for nearby channel events, or display notifications.
