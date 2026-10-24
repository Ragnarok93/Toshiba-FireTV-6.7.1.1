# Wi-Fi Driver Diagnostics — 2026-10-24

Target: Toshiba 43LF421U19 Fire TV (`AFTBAMR311`, `blanche`), build fingerprint `Amazon/blanche/blanche:6.0/NS6711/5898N:user/amz-p,release-keys`, Android 7.1.2 / SDK 25, kernel 3.14.29.

## Observed via read-only ADB

- `ip link` listed `wlan0` with flags `UP,LOWER_UP`, MTU 1500.
- `/sys/module` contained `wlan_mt76x8_usb`, `wlan_mt76x8_usb_prealloc`, `cfg80211`, and `btmtk_usb`.
- Android services included `wifip2p` (`android.net.wifi.p2p.IWifiP2pManager`), `wifi`, `wifiscanner`, and Amazon Wi-Fi services.
- Properties included `wifi.interface=wlan0` and `wlan.driver.status=ok`.
- Listing `/sys/class/net` entries returned permission-denied messages to the shell user.

## Interpretation and limits

The loaded `wlan_mt76x8_usb` module is strong evidence that this firmware uses a MediaTek MT76x8-family USB Wi-Fi driver. It does not uniquely identify the physical chipset; the specific MT7668AU identification remains unconfirmed. The loaded `btmtk_usb` module does not prove a functional or populated Bluetooth radio. The presence of Android's `wifip2p` service does not prove end-to-end Wi-Fi Direct support. Earlier diagnostics reportedly showed `P2pNotSupportedState`. An active `wlan0` interface does not establish Miracast sink support.

## Next read-only diagnostic

Collect `dumpsys wifi`, `dumpsys wifip2p`, and filtered wireless-related `logcat` output to identify the chipset/driver more precisely and investigate why P2P may be unsupported. Do not unload modules, write partitions, or flash firmware as part of this investigation.

## Root / casting status

No verified root method for the exact build is currently established. Root access would not itself guarantee Miracast or Google Cast receiver capability; Wi-Fi driver/firmware, Android framework support, receiver services, and media pipeline compatibility must be assessed separately.
