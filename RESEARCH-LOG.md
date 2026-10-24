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

## 2026-10-24 — Focused root / bootloader-unlock research update

### Exact platform cross-reference
A public postmarketOS device entry for the Insignia Full HD Fire TV Edition (`amazon-blanche`) identifies an Amlogic T950Z, Cortex-A53, and kernel 3.14.29. It marks the device as not yet unlockable and records a contributor's Insignia unit on Fire OS 6.2.8.9 as not unlockable. A contributor profile independently lists that same `amazon-blanche` device and Fire OS version as not unlockable. This is a close platform match to this Toshiba's `blanche` device/codename and kernel, but is not proof that every firmware build is unexploitable.

Sources:
- https://wiki.postmarketos.org/wiki/Insignia_Fire_TV_Edition_%28Full_HD%29_%28amazon-blanche%29
- https://wiki.postmarketos.org/wiki/User%3ABenTheTechGuy

### Searches and compatibility assessment
Searches were run for `AFTBAMR311`, `BLANCHE_Uboot_AMZN_build-185241`, `blanche Fire TV root`, `txlx Fire TV unlock`, and Toshiba HD Fire TV bootloader unlocking. No public exploit or documented unlock procedure matching the exact U-Boot tag or build fingerprint was found in these searches.

Known exploit families found in the search are not presently validated for this target:
- FireFU documentation targets particular Fire TV Cube/Pendant hardware and boot chains. No evidence found that it supports this `blanche` television.
- GhostLock-based Fire OS projects surfaced for newer MediaTek-based devices; no evidence found that it supports this Amlogic T950Z / kernel 3.14.29 target.
- Amlogic boot-ROM exploits documented for specific Cube SoCs cannot be assumed to work merely because this device also uses an Amlogic platform.

### Current conclusion
No verified root or bootloader-unlock method has been found for the exact Toshiba `AFTBAMR311` / `blanche` device and reported firmware. The closest public platform reference currently points to an unlockability limitation, so the next productive avenue is deeper boot-chain/firmware-specific research (exact Fire OS build mapping, U-Boot/BL2/boot-ROM identification, and any device-specific security research), not trying unrelated exploit scripts.

Do not attempt FireFU, GhostLock, Cube payloads, or generic Amlogic flashing procedures on this TV without independent proof of SoC/boot-ROM and firmware compatibility. No device modifications have been performed.

### Firmware-family cross-reference (2026-10-24)
The exact incremental `0011644897924` and fingerprint `Amazon/blanche/blanche:6.0/NS6711/5898N:user/amz-p,release-keys` were also searched publicly. Searches did not find an exact match for the incremental or U-Boot tag. Public firmware catalogs show `NS6711` / Fire OS 6.7.1.1 on other Fire TV hardware (for example, the AFTMM/mantis Stick 4K), but that does not establish shared bootloader, kernel, or exploit compatibility. The `NS6711` portion is a firmware family/build label, not a sufficient hardware identifier; never apply an AFTMM/mantis root procedure to `blanche` based on that match.

The next safe data-gathering step is to query additional boot-state properties through read-only ADB (`ro.boot.flash.locked`, `ro.boot.verifiedbootstate`, `ro.boot.secureboot`, `ro.bootloader`, `ro.build.version.incremental`, and `ro.build.display.id`). These values may clarify the reported lock/secure-boot state but still will not themselves unlock the device. Do not reboot into bootloader or flash anything until recovery/transport behavior is understood.

### Boot-state property results (2026-10-24)
The user queried the following properties through read-only ADB:
- `ro.boot.flash.locked`: empty / not exposed
- `ro.boot.verifiedbootstate`: empty / not exposed
- `ro.boot.secureboot`: empty / not exposed
- `ro.boot.secure_cpu=1`
- `ro.boot.arb_enabled=1`
- `ro.boot.rpmb_state=1`
- `ro.bootloader=unknown`
- `ro.build.version.incremental=0011644897924`
- `ro.build.display.id=NS6711`
- `ro.build.fingerprint=Amazon/blanche/blanche:6.0/NS6711/5898N:user/amz-p,release-keys`

Assessment: the empty lock / verified-boot properties and `ro.bootloader=unknown` do not establish whether the bootloader is locked or unlockable; this build may simply not expose these Android properties. `secure_cpu=1`, `arb_enabled=1`, and `rpmb_state=1` are security-related indicators, but are not by themselves proof of a particular exploitable or non-exploitable boot chain. The incremental and fingerprint reconfirm the exact software build. Continue seeking device-specific boot ROM, BL2/U-Boot, recovery, and firmware evidence; do not treat generic Amlogic exploit compatibility as established.

No reboot, write, or firmware change was performed.

### Additional public exploit and firmware research (2026-10-24)

Additional web research did not uncover a root/unlock exploit for `AFTBAMR311` / `blanche` on incremental `0011644897924`.

Relevant findings:
- The postmarketOS `amazon-blanche` page identifies Amlogic T950Z / Cortex-A53 / kernel 3.14.29, marks the device “not yet unlockable,” and records a user's Fire OS 6.2.8.9 unit as not unlockable: https://wiki.postmarketos.org/wiki/Insignia_Fire_TV_Edition_%28Full_HD%29_%28amazon-blanche%29
- `aftv-full-unlock` is for the original Fire TV bootloader `51.1.0.2` or earlier and requires an already-rooted device. It is not a candidate for this Fire OS 6 `blanche` build: https://github.com/androidrbox/aftv-full-unlock
- A current community unlock overview describes Amlogic download-mode methods for particular older Fire TV Cubes/Pendant and states those depend on specific device revisions / firmware cutoffs. No `blanche` support is listed: https://github.com/zenfyrdev/bootloader-unlock-wall-of-shame/blob/main/brands/amazon/README.md
- A public FTVDB record confirms that Fire OS `6.7.1.1 (NS6711/5908)` also exists for the separate AFTMM / Fire TV Stick 4K (1st gen) family: https://ftvdb.com/firetv/firmware/com.amazon.mantis.android.os/. This is a different device and does not establish exploit or firmware compatibility with `blanche`; shared `NS6711` alone is not enough to transfer a root method.
- A recent CVE-2026-43499/GhostLock reproduction report is specifically for Fire TV Stick 3rd Gen `sheldonp` / `AFTSSS`, Fire OS 7.7.1.6, Android 9, kernel 4.4.162+. It is not compatible evidence for this Amlogic `blanche` / Android 7.1.2 / kernel 3.14.29 device: https://github.com/accessmodifier364/cve-2026-43499-firetv-sheldonp-writeup

### Assessment after these searches
No verified public root, temporary-root, or bootloader-unlock method has been found for this exact television. The queried `ro.boot.flash.locked`, `ro.boot.verifiedbootstate`, and `ro.boot.secureboot` properties were empty, and `ro.bootloader=unknown`; this build does not expose useful standard lock-state values through those properties. `ro.boot.secure_cpu=1`, `ro.boot.arb_enabled=1`, and `ro.boot.rpmb_state=1` are security-related signals, but do not identify a specific exploit or prove the bootloader's state.

Research should now prioritize the device's actual boot path and hardware revision: identify the exact T950Z/TXLX board and boot ROM / BL2 / U-Boot lineage; look for a matching stock update package or publicly documented service/flash mode; and compare the vendor kernel source/config with known vulnerabilities affecting kernel 3.14.29. Do not run a kernel exploit simply because it is old, and do not enter a write-capable burn/flash mode without a device-specific recovery plan. No modifications have been made.

### Follow-up: FireFU and older Android CVE leads (2026-10-24)
A focused search for FireFU against `T950Z` / `amazon-blanche` returned no evidence of compatibility. The postmarketOS `amazon-blanche` page continues to be the closest exact-platform reference and marks the device not yet unlockable. FireFU's public documentation describes the older `amazon-needle` / Fire TV 3 path and explicit Fire OS version cutoffs; it should not be attempted on this television without proof of a shared vulnerable BootROM and matching bootloader path. Sources: https://wiki.postmarketos.org/wiki/Amazon_Fire_TV_3_%28amazon-needle%29 and https://wiki.postmarketos.org/wiki/Insignia_Fire_TV_Edition_%28Full_HD%29_%28amazon-blanche%29.

A broad Android 7.1.2 CVE list includes vulnerabilities across Android components, but version overlap alone does not prove the relevant component is present, unpatched, reachable, or capable of yielding local root on this vendor build. No specific Android CVE has yet been validated against the TV's patched kernel/framework. The next avenue is to map the vendor kernel and security patch level, then audit concrete local privilege-escalation candidates against those exact sources rather than running a generic exploit.

## 2026-10-24 — CVE-2026-43499 (GhostLock) assessed as a possible research lead

### New finding
- Linux kernel CVE-2026-43499 concerns a use-after-free hazard in RT-mutex priority-inheritance handling: `remove_waiter()` incorrectly uses `current` rather than `waiter->task` during proxy-lock rollback from `futex_requeue()`.
- The upstream CVE notice says the bug was introduced in 2.6.39 and fixed in upstream 7.1-rc1; stable backports exist, so kernel version alone cannot prove the target is vulnerable.
- The target reports kernel `3.14.29` and security patch level `2025-11-01`. The reported patch level predates the public CVE disclosure (2026-05-21), making this a credible audit candidate, but neither property proves the vulnerable code is present or exploitable.
- A public Fire TV exploit port exists at [dorlow/hazel-cve-2026-43499](https://github.com/dorlow/hazel-cve-2026-43499). Its documented scope is the `hazel` platform, ARM32 Linux 4.9 firmware (tested on Toshiba AFTHA004 / `PS7716.5665N`) with exact firmware/kernel-specific profiles. It is **not a drop-in method** for this `blanche` / `AFTBAMR311` target.
- The public exploit's strict identity/profile matching and kernel-layout dependence explicitly warn against reusing addresses or bypassing checks on another device. Porting would require independent inspection of the target kernel image, RT-mutex/futex code, architecture/ABI, kernel configuration, allocator/layout differences, and recovery plan.

### Current assessment
- This is the strongest newly found **potential temporary-root research lead**, not a verified root method for this TV.
- Next read-only evidence to collect: `uname -a`, `getprop ro.product.cpu.abi`, `getprop ro.product.cpu.abi2`, `getprop ro.zygote`, and whether `/proc/config.gz` or a matching kernel image/config is accessible. These establish ABI and help assess whether the relevant futex PI and supporting kernel features are present.
- Do not run the Hazel exploit, copy its profiles, force a profile match, or attempt a kernel exploit on this TV as-is. A wrong heap/layout assumption can panic or reboot the TV, and a crash would not demonstrate a safe path to root.
- No device changes, exploit execution, partition writes, or flashing were performed.

### Sources
- Linux kernel CVE announcement: https://lists.openwall.net/linux-cve-announce/2026/05/21/7
- Ubuntu CVE details: https://ubuntu.com/security/CVE-2026-43499
- Firmware-specific Hazel port and its scope/warnings: https://github.com/dorlow/hazel-cve-2026-43499
- Exact-device platform reference (marked not yet unlockable): https://wiki.postmarketos.org/wiki/Insignia_Fire_TV_Edition_%28Full_HD%29_%28amazon-blanche%29


## 2026-10-24 — Runtime ABI and kernel config access

### User-supplied read-only output
- `uname -a`: `Linux localhost 3.14.29 #1 SMP PREEMPT Thu Dec 4 00:28:54 UTC 2025 armv8l`
- `ro.product.cpu.abi=armeabi-v7a`
- `ro.product.cpu.abi2=armeabi`
- `ro.zygote=zygote32`
- `ro.product.cpu.abilist=armeabi-v7a,armeabi`
- The shell test reports `/proc/config.gz` readable, but the attempted `toybox zcat ... | grep ...` produced no matching lines. The config has **not yet been inspected successfully**; do not infer that options are disabled from this output.

### Assessment / next check
The running kernel reports `armv8l`, while Android userspace is 32-bit ARM. This makes an ARM32 build a plausible userspace target, but does not establish kernel exploit compatibility or prove that the processor/kernel can execute a particular exploit binary. Obtain the compressed config through ADB to the Ubuntu host and inspect it there, rather than relying on the device's toybox `zcat` implementation.

CVE-2026-43499's upstream notice lists stable fixes as well as the mainline fix; the target's old kernel version and reported patch level are only clues. No exact source/config or vulnerable-code confirmation for the Blanche kernel has been found yet.

No exploit was executed and no device changes were made.
