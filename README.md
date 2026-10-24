# Toshiba Fire TV — Blanche Platform Research

Research notes for the **Toshiba 43LF421U19 Fire TV**, covering device identification, ADB diagnostics, rooting feasibility, and the possibility of adding wireless display/casting receiver support.

> **Status:** Identification is well established; a compatible rooting method for the exact firmware has **not** been verified. Treat all firmware modification as high risk until a device-specific recovery path is documented.

## Device at a glance

| Field | Observed value |
|---|---|
| Retail model | Toshiba 43LF421U19 |
| Android product model | `AFTBAMR311` |
| Product / device codename | `blanche` |
| Product name | `blanche` |
| Build fingerprint | `Amazon/blanche/blanche:6.0/NS6711/5898N:user/amz-p,release-keys` |
| Build incremental | `0011644897924` |
| Android release property | `7.1.2` |
| Android SDK | `25` |
| Kernel | `3.14.29` |
| CPU ABI exposed to Android | `armeabi-v7a` |
| Board platform | `txlx` |
| Hardware vendor | Amlogic |
| Bootloader build tag | `BLANCHE_Uboot_AMZN_build-185241` |
| Device tree property | `blanche_cma_hvt2` |
| Reported model name | `PVT_FHD_43_T_1` |
| Reported memory type | `DDR4` |
| SELinux | Enforcing |
| Secure ADB | Enabled (`ro.adb.secure=1`) |
| Reported unlocked-kernel state | `false` |
| Reported verity mode | `disabled` |
| Reported slot suffix | `_a` |
| Anti-rollback property | `ro.boot.arb_enabled=1` |
| RPMB state property | `ro.boot.rpmb_state=1` |

Some identifiers are properties reported by the running OS, not independent proof of the underlying boot chain's state. See [DEVICE-IDENTIFIERS.md](DEVICE-IDENTIFIERS.md) for the detailed evidence and limitations.

## Current findings

### ADB and privilege level

- ADB over TCP was connected at `192.168.0.108:5555` during the diagnostic session. This is a local-network address and may change.
- The shell runs as Android's unprivileged `shell` user (UID 2000).
- `su -c id` returned `/system/bin/sh: su: not found`.
- SELinux reports `Enforcing`.
- Attempts to list `/dev/block/system`, `/dev/block/vendor`, `/dev/block/boot`, and `/dev/block/recovery` returned `Permission denied`. This is an access-control result, not proof those devices do not exist.
- `toybox`, `dmesg`, and `logcat` are available at `/system/bin/`.

### Storage and mounts

The observed block-device inventory reports `mmcblk0` with 7,634,944 1-KiB blocks, plus `mmcblk0boot0`, `mmcblk0boot1`, and `mmcblk0rpmb`. The partition table and mount output are recorded in [DEVICE-IDENTIFIERS.md](DEVICE-IDENTIFIERS.md).

Observed mounts include:
- `/system`, `/vendor`, and `/tvconfig`: ext4, read-only.
- `/data` and `/cache`: ext4, read-write.
- `/tee`: ext4, read-write in the captured mount output.

The expected by-name directory listings were not available from the tested paths. `/proc/cmdline` was denied to the ADB shell. These limitations do not establish that the bootloader is locked or unlocked.

### Rooting / bootloader status

**No verified rooting procedure has been established for this exact device/build combination.**

- `ro.boot.unlocked_kernel=false` suggests the currently booted kernel is reporting a locked/non-unlocked state; it is not a universal test for every possible privilege-escalation route.
- `ro.boot.veritymode=disabled` does **not** imply that the bootloader is unlocked, that arbitrary images can be flashed, or that partitions can be modified safely.
- `ro.boot.slot_suffix=_a` does not establish that a usable alternate slot or rollback path exists.
- Anti-rollback and RPMB-related properties make unverified bootloader/firmware experiments especially risky.
- Public information about the broader `blanche` platform has been described as not yet unlockable, but that is not proof that all possible software exploits are impossible on this exact build.
- Do not assume tools for other Amlogic Fire TV models apply. In particular, do not use a rooting/boot image solely because it targets a different Amlogic device or a newer kernel.

**Safety rule:** Do not write to `mmcblk0`, `mmcblk0boot0`, `mmcblk0boot1`, or RPMB, and do not flash boot/recovery/firmware images, unless a procedure is verified for this exact hardware and build and includes a tested recovery route.

### Wireless display / casting goal

The longer-term goal is to investigate whether the TV can act as a receiver for Miracast and/or Google Cast.

- Stock diagnostics reportedly showed a `P2pNotSupportedState` indication and some `WifiDisplaySource` symbols. These are not proof of Miracast sink/receiver support.
- No registered media-router providers were observed in the earlier diagnostics.
- Root alone would not add these features. Compatibility may depend on the Wi-Fi chipset/driver, vendor firmware, media pipeline, and receiver applications/services.
- A tentative Wi-Fi chipset identification based on a hardware/FCC reference has been discussed, but it is **not confirmed** and must not be treated as fact.

## Next research priorities

1. Find device-specific root/unlock research matching `blanche`, `AFTBAMR311`, the U-Boot build tag, kernel 3.14.29, and the `5898N` build fingerprint.
2. Verify the exact firmware lineage and whether the repository name `Toshiba-FireTV-6.7.1.1` corresponds to the installed software version; do not infer it solely from the repository name or fingerprint.
3. Identify a documented recovery/restore method before any write operation.
4. Continue read-only ADB investigation of Wi-Fi capabilities and media/casting services.
5. Keep confirmed observations separate from hypotheses and external reports.

## Keeping this repository current

Record new commands and outputs with the date, the exact device/build they came from, and whether a conclusion is **observed**, **inferred**, or **unverified**. Never commit Wi-Fi passwords, account tokens, or device serial numbers. The README should remain a concise entry point; put detailed raw observations and caveats in [DEVICE-IDENTIFIERS.md](DEVICE-IDENTIFIERS.md).


## Research notes

- [Research log](RESEARCH-LOG.md) — dated searches, assessment, and safety notes.
- [Wi-Fi driver diagnostics (2026-10-24)](WIFI-DIAGNOSTICS-2026-10-24.md) — read-only wireless-driver and casting evidence, with interpretation limits.
