# Device Identifiers and Diagnostic Evidence

This file records the current ADB observations for the Toshiba 43LF421U19 Fire TV. Values are transcribed from the device's reported Android properties and shell output; they are not all independently verified against hardware.

## Identification

- Retail model: Toshiba 43LF421U19
- `ro.product.model`: `AFTBAMR311`
- `ro.product.device`: `blanche`
- `ro.product.name`: `blanche`
- `ro.build.fingerprint`: `Amazon/blanche/blanche:6.0/NS6711/5898N:user/amz-p,release-keys`
- `ro.build.version.incremental`: `0011644897924`
- `ro.build.version.release`: `7.1.2`
- `ro.build.version.sdk`: `25`
- `uname -r`: `3.14.29`
- `ro.product.cpu.abi`: `armeabi-v7a`
- `ro.board.platform`: `txlx`
- `ro.boot.hardware`: `amlogic`
- `ro.boot.UbootBuildTag`: `BLANCHE_Uboot_AMZN_build-185241`
- `ro.boot.dts`: `blanche_cma_hvt2`
- `ro.boot.model_name`: `PVT_FHD_43_T_1`
- `ro.boot.memory_type`: `DDR4`
- `ro.boot.hwid`: `1111`
- `ro.boot.prod`: `1`
- `ro.boot.secure_cpu`: `1`
- `ro.boot.arb_enabled`: `1`
- `ro.boot.rpmb_state`: `1`
- `ro.boot.slot_suffix`: `_a`
- `ro.boot.unlocked_kernel`: `false`
- `ro.boot.veritymode`: `disabled`
- `ro.boot.selinux`: `enforcing`
- `ro.secure`: `1`
- `ro.adb.secure`: `1`
- `ro.bootloader`: `unknown`
- `ro.bootmode`: `unknown`
- `ro.boot.data_mounted`: `true`
- `ro.boot.firstboot`: `1`
- `ro.bootimage.build.fingerprint`: `Amazon/blanche/blanche:6.0/NS6711/5898N:user/amz-p,release-keys`
- `ro.bootimage.build.date`: `Thu Dec 4 00:26:52 UTC 2025`
- `ro.bootimage.build.date.utc`: `1764808012`
- `ro.expect.recovery_id`: `0xe34db92211ecae456771c1d78d7262d15b197aca000000000000000000000000`

**Privacy note:** The device serial number was intentionally omitted from this public repository. Preserve it locally if it is needed for support or ownership records; it is not needed for general platform research.

## CPU observations

The reported `/proc/cpuinfo` output identified:
- Architecture family: AArch64-class CPU
- CPU implementer: `0x41`
- CPU part: `0xd03` (Cortex-A53 class)
- Four processors were reported
- Hardware: Amlogic

Android's exposed primary ABI is 32-bit `armeabi-v7a`, even though the CPU identifier is AArch64-class. This does not by itself establish that a usable 64-bit Android userspace is installed.

## Block devices

Captured `/proc/partitions` output:

```text
major minor  #blocks  name
7 0 10240 loop0
253 0 262144 zram0
179 0 7634944 mmcblk0
179 1 4096 mmcblk0p1
179 2 8192 mmcblk0p2
179 3 4096 mmcblk0p4
179 4 8192 mmcblk0p5
179 5 122880 mmcblk0p6
179 6 512 mmcblk0p7
179 7 16384 mmcblk0p8
179 8 16384 mmcblk0p9
179 9 1433600 mmcblk0p10
179 10 1228800 mmcblk0p11
179 11 262144 mmcblk0p12
179 12 4477440 mmcblk0p13
179 48 512 mmcblk0rpmb
179 32 4096 mmcblk0boot1
179 16 4096 mmcblk0boot0
```

The table above preserves the captured output's rows as provided in the diagnostic transcript; partition minor-number/name mapping should be re-captured from the TV before using it for any analysis or operation. **Do not use this transcription to target a partition for writing.**

## Mounts observed

Relevant lines from `/proc/mounts`:

```text
/dev/block/system /system ext4 ro,seclabel,relatime,data=ordered
/dev/block/vendor /vendor ext4 ro,seclabel,relatime,data=ordered
/dev/block/tvconfig /tvconfig ext4 ro,seclabel,relatime,data=ordered
/dev/block/cache /cache ext4 rw,seclabel,nosuid,nodev,noatime,nodelalloc,data=ordered
/dev/block/data /data ext4 rw,seclabel,nosuid,nodev,noatime,nodelalloc,data=ordered
/dev/block/tee /tee ext4 rw,seclabel,relatime,errors=panic
```

The root filesystem is reported as read-only `rootfs`. Other observed mounts included `/proc`, `/sys`, `/sys/fs/selinux`, `/sys/kernel/debug`, `/sys/kernel/config`, `/acct`, `/config`, `/dev/cpuctl`, `/dev/cpuset`, `/sys/fs/pstore`, `/tmp`, `/model_dep_configs`, `/dev/logd`, and emulated storage via FUSE.

## ADB shell and access results

- ADB TCP endpoint observed during the session: `192.168.0.108:5555` (local-network address; may change).
- `id` reported UID 2000 (`shell`).
- `su -c id` returned `/system/bin/sh: su: not found`.
- Attempts to read `/proc/cmdline` were denied.
- Listing `/dev/block/platform/*/by-name` and `/dev/block/by-name` did not produce a usable listing in the tested shell.
- `ls -l /dev/block/system /dev/block/vendor /dev/block/boot /dev/block/recovery` returned `Permission denied` for each path.
- Available commands: `/system/bin/toybox`, `/system/bin/dmesg`, `/system/bin/logcat`.
- SELinux mode: enforcing.

## Interpretation caveats

- A property is evidence of what the running OS reports; it may not be a complete or authoritative statement about the bootloader, fuse state, exploitability, or flash policy.
- `ro.boot.unlocked_kernel=false` is not proof that no privilege-escalation exploit exists.
- `ro.boot.veritymode=disabled` is not proof that arbitrary firmware can be flashed.
- A reported slot suffix does not establish a safe rollback route.
- `Permission denied` means the current shell could not access the requested path; it does not prove the path or device is absent.
- Device-specific rooting claims must match this platform and firmware. Do not substitute instructions for a different Fire TV/Amlogic model.
- No confirmed root method for the exact build is currently recorded.

## Casting / wireless display notes

- Earlier diagnostics reportedly showed `P2pNotSupportedState`.
- Some `WifiDisplaySource` symbols were present, but this does not establish Miracast receiver/sink support.
- No registered media-router providers were observed in the earlier diagnostics.
- A possible Wi-Fi chipset estimate was discussed from external hardware/FCC clues, but it is unconfirmed and is deliberately not recorded as a fact.
- Root access alone would not guarantee Miracast or Google Cast support; driver, vendor firmware, receiver-service, and media-pipeline compatibility all matter.

## Safe next steps

1. Continue read-only identification and logs.
2. Seek a published procedure matching the exact `blanche` / `AFTBAMR311` platform, build fingerprint, and U-Boot build.
3. Confirm a recovery method before any firmware/partition write.
4. Capture future diagnostic outputs with date, command, and build fingerprint.
