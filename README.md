# LineageOS Recovery for the Galaxy View SM-T670 (`gvwifi`)

The stock LineageOS 23.2 recovery, built from the
[`lineage-gvwifi`](https://github.com/gvwifi-los23/lineage-gvwifi) tree. The recovery code itself is unmodified; what makes it
work on this tablet is in that repo's kernel and device patches, which it shares with the ROM.

## Build
From a synced and patched `lineage-gvwifi` tree:
```
scripts/3-build.sh recovery      # recovery.img + lineage-recovery-gvwifi.tar (Odin)
```
Odin wants a ustar tar with the image named as the partition:
`tar -H ustar -cf lineage-recovery-gvwifi.tar recovery.img`.
The script prints the image size against the 39,845,888-byte RECOVERY partition.

## Patches that matter for recovery (in `lineage-gvwifi/patches/kernel/samsung/universal7580/`)
| Patch | Effect in recovery |
|---|---|
| 0005 s3c_udc: drop lock around suspend/resume | no hard lockup on USB plug/unplug (sideload) |
| 0010 bootwatch + SELinux recovery/charger-aware | recovery keeps SELinux permissive; the boot watchdog is not armed in recovery (`bootmode=2`) |
| 0011 usb: reconnect gadget after UDC rebind | USB comes back after recovery switches adb and sideload |
| 0012 fscrypt: implement GET_ENCRYPTION_POLICY_EX | lets userspace read v2 encryption policies |

## Flashing
Download mode (Power + Volume Down + Home, then Volume Up), Odin 3.10.7, AP = the tar,
**Auto Reboot off**. After PASS, hold Power + Volume Down until the screen is off, then
Power + Volume Up to enter recovery.

## Known quirks
- `adb reboot recovery` (a warm reboot into recovery) sometimes stops at a lit, black screen;
  the key combo from power-off always works.
- Large `adb push` transfers to this recovery have frozen it; push to the ROM's storage instead.
- Images over ~31.4 MB don't boot on this bootloader (see the ROM README).
