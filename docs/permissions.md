# Permissions

Wingu SDK uses the following permissions:

- `ACCESS_COARSE_LOCATION` (runtime), `BLUETOOTH`, `BLUETOOTH_ADMIN` — scanning and discovering Bluetooth LE devices
- `ACCESS_FINE_LOCATION` (runtime) — discovering geofence channels
- `ACCESS_NETWORK_STATE` — displaying a notification (snackbar) when Internet connection is not available
- `INTERNET` — fetching content for channels
- `RECEIVE_BOOT_COMPLETED` - automatically starting scanning and displaying notifications after a reboot
- `VIBRATE` — feedback after pressing some buttons
- `WRITE_CONTACTS` (runtime) — adding new contacts (via the Contact component)
