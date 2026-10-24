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


## Follow-up framework output (2026-10-24)

The user supplied excerpts from `dumpsys wifi` and `dumpsys wifip2p`. The filtered wireless `logcat` output was too long to paste and has not yet been reviewed.

### Observed

- Wi-Fi client mode is connected: supplicant state `COMPLETED`, network state `CONNECTED/CONNECTED`, interface `wlan0`; the reported connection is on 5 GHz. Scan counters show successful scans.
- The framework chipset-information block shows blank firmware and driver version values and `Supported Feature set: 0`.
- `dumpsys wifip2p` reports `curState=P2pNotSupportedState`; no P2P group is formed, peer discovery is not started, and the P2P network is unavailable in `IDLE` state.

### Interpretation and limits

The current software/driver stack supports ordinary Wi-Fi client connectivity but reports Wi-Fi Direct as unsupported. This is a significant obstacle to conventional Miracast, which typically uses Wi-Fi Direct. It does not prove the physical radio can never support P2P; driver support, firmware, board configuration, and Android framework integration remain possible causes. Blank version strings and feature-set value zero should not be treated as proof of zero hardware capability.

Google Cast receiver support remains undetermined. It can use the existing local network, but requires compatible receiver software and media integration. No underlying driver or firmware error has been confirmed because the filtered wireless `logcat` output was not included. Do not modify partitions or flash firmware based on these observations alone.