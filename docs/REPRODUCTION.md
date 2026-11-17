# Reproduction, verification and recovery

## Scope

The owner confirmed the latest SSD boot after the RTL88WiFi migration on November 16, 2026. The complete anonymous copy prepared for local review has not been booted with its blank identity. This is not yet an end-to-end clean-install recipe: the precise source/release of the final local V12-Dell and CS8409Audio/HAL builds still needs to be identified.

## Prepare a recovery path

Keep the current working EFI and macOS-side graphics/audio components backed up, plus a known bootable USB. Identify disks by capacity and partitions; `disk0` or `disk3` from another boot is not a safe target identifier. Preserve Windows/OEM boot files and any BitLocker recovery key before firmware changes.

The exact working BIOS options have not yet been recovered. Record the actual settings before changing them. Use the [Dortania guide](https://dortania.github.io/OpenCore-Install-Guide/) for OpenCore fundamentals and obtaining an installer, with the experimental dependencies in this repository as additional requirements, not an officially supported Alder Lake recipe.

## Template and identity

`EFI/OC/config.example.plist` has blank serial/MLB, a zero UUID and a six-byte zero ROM. Copy it to `config.plist` only in a private EFI. Generate your own SMBIOS identity for MacBookPro16,2 with a tool such as [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), and follow OpenCore guidance for ROM. Do not publish the resulting file.

The organized copy removes only disabled obsolete top-level AirportRTW/BrightnessKeys entries, the disabled old Wi-Fi coexistence patch and unused backup/OEM material. Other tested settings and diagnostics are preserved. It keeps `ShowPicker=true`, `Timeout=0` and `AllowSetDefault=true`: the picker is visible and waits for selection. An older conversation discussed hiding it, but that is not the latest recovered setting.

## Reconstruct from exact local files

To assemble a template from a complete local EFI containing the exact recorded binaries:

```bash
python3 scripts/montar_efi.py /path/to/complete/EFI ./build/EFI
```

The script checks SHA-256 before copying, writes only into a new destination and does not mount disks, download packages or run sudo. It intentionally rejects arbitrary newer binaries. This source EFI is not included in the public repository; obtain the required projects/packages and resolve the explicitly noted provenance gaps first.

The public model can be checked with:

```bash
python3 scripts/auditar_efi.py EFI
```

After completing a private EFI and filling its own identity:

```bash
python3 scripts/auditar_efi.py ./build/EFI --personal
/path/to/matching/ocvalidate ./build/EFI/OC/config.plist
```

Python 3.9 or newer is required. The tools currently print Portuguese status messages. `--personal` is only for private validation; it never prints the identity values. The recovered ocvalidate is for OpenCore 1.0.8 and returned no issues on the anonymous organized copy. Earlier tests reported OpenCore 1.0.8, but the saved bootloader's exact revision was not independently established from the generic embedded build string.

## Test before migrating

Use the Dell F12 boot menu to select the USB EFI. Keep graphics/audio system-side versions compatible; an EFI swap alone does not install or roll back Graphics or the audio HAL. Once the system is running, check actual loaded versions, the graphics ABI and real playback rather than relying only on plist validity.

After a successful USB test, back up the correct internal EFI, copy only the required OpenCore files while preserving other bootloaders, compare the files, and test without the USB. If it fails, select the recovery EFI through F12. Restore system-side components too if those were changed. Do not format the SSD or reset NVRAM as an automatic troubleshooting step.

## Remaining validation

- Repeat speed and BLE checks after the confirmed SSD boot.
- Compare short and long transfers against more than one server.
- Test Bluetooth headset pairing/audio and 2.4 GHz coexistence.
- Test internal microphone, P2, gestures, USB ports, brightness and webcam.
- Test display sleep and system sleep separately.
- Monitor WindowServer and GPU errors over several hours; record conditions and duration.
- Record the real BIOS version/settings, SSD model and exact input-device IDs.
- Recover the graphics/audio source revisions before offering a reproducible binary release.

Do not publish raw sysdiagnose, SysReport, firmware telemetry or unreviewed IORegistry dumps. Share only the excerpts needed, after removing serials, MAC addresses, SSIDs, private account information and unrelated application data.
