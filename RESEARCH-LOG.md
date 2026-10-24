# Research Log

## 2026-10-24 — Initial device-specific repository search

### Target
- Retail model: Toshiba 43LF421U19
- Android model/codename: `AFTBAMR311` / `blanche`
- Build fingerprint: `Amazon/blanche/blanche:6.0/NS6711/5898N:user/amz-p,release-keys`
- Kernel: `3.14.29`
- U-Boot tag: `BLANCHE_Uboot_AMZN_build-185241`

### Searches performed
Searched GitHub repository/code indexes for:
- `blanche Fire TV root`
- `blanche AFTBAMR311`
- `BLANCHE_Uboot_AMZN_build-185241`
- `Amlogic txlx Fire TV root 3.14.29`

### Result
- No direct GitHub repository/code result matched the exact product model, codename, or U-Boot tag.
- Broader searches returned generic Amlogic Linux 3.14.29 kernel configuration repositories. These are not evidence of a compatible rooting method.
- This is a limited search result, not proof that no exploit or private/unindexed method exists.

### Current assessment
- No verified root/unlock procedure for this exact firmware has been found in the searches above.
- `ro.boot.veritymode=disabled` must not be treated as evidence that arbitrary images can be flashed.
- The next useful research step is to inspect device-specific documentation and exploit reports for the `blanche` platform, then independently verify any claimed method against the exact build and recovery requirements.
- Continue read-only ADB work in parallel to establish wireless-display / casting capabilities without assuming root will be obtainable.

### Safety
No device modifications were performed. No partition writes, firmware flashing, or bootloader changes were attempted.


## 2026-10-24 — Wi-Fi / wireless-display capability evidence

### Newly recorded observations
A separate diagnostic note was committed as [WIFI-DIAGNOSTICS-2026-10-24.md](WIFI-DIAGNOSTICS-2026-10-24.md). Read-only ADB output showed:
- `wlan0` is up and has carrier (`UP,LOWER_UP`).
- Loaded modules include `wlan_mt76x8_usb`, `wlan_mt76x8_usb_prealloc`, `cfg80211`, and `btmtk_usb`.
- Android exposes a `wifip2p` service, but an earlier diagnostic reportedly entered `P2pNotSupportedState`.
- `wifi.interface=wlan0` and `wlan.driver.status=ok` are present.

### Assessment
The module name points to the MediaTek MT76x8 USB driver family; it does not conclusively identify the physical Wi-Fi chipset. Neither a loaded Bluetooth driver nor an Android Wi-Fi Direct service proves that the corresponding radio/functionality is usable. These observations do not establish Miracast sink support or Google Cast receiver support.

### Next step
Collect the outputs of `dumpsys wifi`, `dumpsys wifip2p`, and filtered wireless-related `logcat` using the read-only command shared in the project conversation. Use those results to narrow down the P2P failure and wireless-display capabilities before proposing any system changes.

No device modifications, partition writes, firmware flashing, or bootloader changes were performed.


### Follow-up diagnostic result (2026-10-24)
The user supplied output from `dumpsys wifi` and `dumpsys wifip2p`; the wireless-related `logcat` tail was too large to paste and is not yet available for review.

Observed:
- The Wi-Fi framework reports a completed supplicant connection and `CONNECTED/CONNECTED` network state on `wlan0`; the connection uses 5 GHz Wi-Fi. Routine scanning has successful results.
- The Wi-Fi framework's chipset-information block reports blank firmware and driver version strings and `Supported Feature set: 0`.
- `dumpsys wifip2p` reports `curState=P2pNotSupportedState`, no formed group, no peer discovery, and P2P network availability `false` / state `IDLE`.

Assessment:
- Normal Wi-Fi client connectivity is working, but the currently running Android/driver stack is reporting Wi-Fi Direct as unsupported. This is a strong practical blocker for conventional Miracast, which typically uses Wi-Fi Direct.
- This does not prove the physical radio is inherently incapable of P2P; the cause could be in the driver, firmware, platform configuration, or framework integration. Blank version strings and feature set zero may reflect unavailable chipset reporting and should not be interpreted as proof that the radio has no capabilities.
- These results do not establish Google Cast receiver support either way. Google Cast can use the existing LAN, but requires compatible receiver software and media integration.
- The filtered wireless `logcat` output has not been reviewed, so no specific underlying driver or firmware error has been established.

Next: inspect the relevant `logcat` lines if available and continue read-only investigation. Do not infer a root method or recommend partition/firmware changes from this output alone.