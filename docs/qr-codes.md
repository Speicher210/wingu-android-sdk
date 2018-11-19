# QR codes

The included QR code scanner can be used to discover and display wingu content.

You need to add additional `qrcodescanner` dependecy to your `build.gradle` (see [installation](./#installation)).

[QrCodeScanningActivity](https://wingu-GmbH.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.qrcodescanner/-qr-code-scanner-activity/index.html) can be used to scan for QR codes. When a proper wingu QR code channel is recognized, its contents are displayed via [ChannelDetailsActivity](https://wingu-GmbH.github.io/wingu-android-sdk/dokka/wingu-android-sdk/de.wingu.sdk.ui.details/-channel-details-activity/index.html).

If you wish to customize this behaviour, extend this activity and override methods: `openDetailsActivity(chanelKey: ChannelKey)` or `showError(qrCodeError: QrCodeError))`.
