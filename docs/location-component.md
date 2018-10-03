# Enabling location (maps) component

Location component is not enabled by default, since it requires a Google API key.

There are two ways in which the location component is rendered, so there are two ways it can be initialized.

## Option 1 - static image maps

By default, the map is rendered using "Google Static Maps API" without Google API key. This is fine for development, but for production you should provide your own Google API key:

```
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
  .registerComponent(LocationWinguComponent.specStaticMaps(new PlainString(your_google_api_key)))
  //...
```

You can also provide a URL signing secret which tags requests to Google Static Maps API with a higher degree of security and is required if you exceed the free daily quota.

However, when using static maps, it's not really possible to restrict the usage of your Google API key.

[Static Maps API Quickstart](https://developers.google.com/maps/documentation/static-maps/get-api-key)

## Option 2 - map view

If you want to limit the usage of your Google API key to your app's package ID ([more about API key restrictions](https://developers.google.com/maps/documentation/android-api/signup#restrict-key)), use the "Maps Android API" variant:

```
WinguSDKBuilder.with(this, YOUR_WINGU_API_KEY)
  .registerComponent(LocationWinguComponent.specMapView())
  //...
```

Make sure that Google API key is added to your `AndroidManifest.xml` file, otherwise the map will not be displayed.

[Maps Android API Quickstart](https://developers.google.com/maps/documentation/android-api/signup).
