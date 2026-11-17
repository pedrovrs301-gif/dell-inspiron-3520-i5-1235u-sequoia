# Dell Inspiron 15 3520 — macOS Sequoia build log

**Intel Core i5-1235U · Intel Iris Xe · 16 GB RAM · 500 GB SSD**

An experimental, hardware-tested Hackintosh build documented to help owners of the same configuration understand what was changed, which combinations worked, and what still needs testing. Last updated **November 16, 2026**.

[Português](README.pt-BR.md)

## Current result

The owner confirmed a successful SSD boot after the latest Wi-Fi migration. Earlier graphics tests recorded **Metal 2, 1536 MB reported VRAM, and the internal 1920 × 1080 display at 120 Hz**. Internal speakers were confirmed. Wi-Fi performance improved substantially, and Bluetooth LE discovery passed while 5 GHz Wi-Fi was transferring data.

**This is a documented experimental configuration, not a claim that every feature works.** The public `EFI/` directory is a sanitized template, not a complete boot-ready EFI. Exact provenance of the local graphics/audio builds is still being recovered. Installing only an EFI does not reproduce the graphics and speaker stack: both need components installed in macOS.

## Hardware

| Item | This unit |
|---|---|
| Laptop | Dell Inspiron 15 3520 |
| CPU | Intel Core i5-1235U, Alder Lake-U |
| GPU | Intel Iris Xe; physical `8086:46A8` revision `0C`, 80 EU, recorded in the earlier hardware report |
| Memory / storage | 16 GB RAM / 500 GB SSD; SSD manufacturer/model not yet recorded |
| Internal display | NCP005F eDP, 1920 × 1080, approximately 120 Hz; panel identification from the earlier hardware report |
| Wi-Fi | Realtek RTL8821CE PCIe; final tested driver RTL88WiFi 1.0.0 |
| Bluetooth | Realtek internal USB `0bda:c829` |
| Audio | Cirrus Logic CS8409; CS8409Audio + CS8409Speakers HAL |
| Input | VoodooI2C/HID trackpad and VoodooPS2 keyboard; exact trackpad device ID not yet recovered |
| macOS | Sequoia 15.8.3, build 24H241; earlier experiments used 15.8.2 |
| SMBIOS | `MacBookPro16,2` — emulated identity, not the physical laptop model |
| Bootloader | OpenCore 1.0.8 reported during earlier tests; exact saved binary revision remains to be independently confirmed |
| BIOS | Version/settings not yet recovered; do not infer Dell firmware version from spoofed Apple firmware fields |

Another 3520 may have a different CPU, panel, wireless card or board revision. Match the hardware before reusing the tables or USB map.

## Confirmed, partially tested, and still open

| Feature | Evidence | Remaining work |
|---|---|---|
| SSD boot | Owner confirmed the latest SSD boot on November 16 | The separately prepared anonymous template has not been boot-tested |
| Internal graphics | Metal 2, 1536 MB, Full HD at 120 Hz recorded with the local V12-Dell combination | Long sessions, GPU resets, sleep and external displays |
| WindowServer memory | Initial snapshots around 132–155 MB after V12, versus about 2.7 GB with V11 | This does not establish a permanent memory-leak fix |
| Video playback | YouTube reported smooth after V12 | Codec-specific hardware decode and DRM not measured |
| Internal speakers | Audible playback confirmed during V12 migration | Microphone, headphone jack, reconnect after sleep |
| Trackpad and keyboard | Working in the earlier report; current config preserves the input stack | Exact trackpad ID, full gesture/palm rejection and sleep checklist |
| Wi-Fi | USB-boot Apple measurements: 104 Mb/s down, 223 Mb/s up; a separate run reached 245 Mb/s down | Long transfers and post-SSD measurements; one Cloudflare transfer timed out |
| Bluetooth LE | Advertisement discovery passed both idle and during 5 GHz download | Pairing, headset audio, Classic profiles, 2.4 GHz coexistence |
| USB / battery | USBToolBox map and SMCBatteryManager configured | Port-by-port, battery accuracy and runtime tests |
| HDMI, brightness keys, webcam, sleep/wake, P2, microphone, iServices | No complete local validation recovered | Do not list as working without further tests |

Speeds are **Mb/s**, not MB/s. Results from different tools and servers are separate observations, not a sustained benchmark average.

## Detailed documentation

- [Graphics acceleration: exact recorded combination, eDP history, boot arguments and checks](docs/GRAPHICS.md)
- [Audio, Bluetooth and Wi-Fi: dependencies, modifications and limits](docs/AUDIO-AND-NETWORK.md)
- [Trackpad, keyboard, ACPI, USB, CPU and battery configuration](docs/INPUT-ACPI-AND-USB.md)
- [Reproduction, recovery and validation](docs/REPRODUCTION.md)
- [Component versions](manifests/kexts.json) and [anonymous local EFI file hashes](manifests/EFI-SHA256.json)
- [Longer original notes in Portuguese](docs/02-COMPONENTES.md)

## What is included

```text
EFI/OC/config.example.plist       Tested settings with machine identity removed
EFI/OC/ACPI/                      The seven enabled SSDTs saved from this unit
EFI/OC/Kexts/UTBMap.kext/          This unit's USB map
patches/                         Cumulative local Bluetooth source patch
scripts/                         Local-only auditing and EFI reconstruction tools
manifests/                       Versions, SHA-256 hashes and preparation checks
docs/                            Hardware, installation and troubleshooting notes
```

Serial number and MLB are blank, UUID is zero and ROM is six zero bytes. Generate your own identity in a private copy before booting or using Apple services. Never publish a personalized config, Dell telemetry or unreviewed logs.

The template preserves the experimental security configuration, including `csr-active-config=03080000` and `SecureBootModel=Disabled`. These are documented properties of this tested stack, not general recommendations for other builds. See the graphics dependency notes before changing them.

## Desktop screenshot

A screenshot of this actual Dell running macOS will be added later. No stock image or generated screenshot is used as proof. Before uploading a capture, remove serial numbers, private account details, network identifiers and unrelated windows.

## Community report

An English report is being prepared for r/hackintosh. It will describe the tested result and remaining issues; this repository is the longer technical record. A screenshot and any further results can be added as the hardware is tested.

## Credits and source status

- [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg), [Lilu](https://github.com/acidanthera/Lilu), [VirtualSMC](https://github.com/acidanthera/VirtualSMC), NVMeFix, RestrictEvents and VoodooPS2 by acidanthera and contributors.
- [WhateverXe](https://github.com/b00t0x/WhateverXe) by b00t0x and contributors; [nickk19's eDP work](https://github.com/b00t0x/WhateverXe/issues/43) was part of the investigation. The final local V12-Dell build must not be confused with an unmodified upstream binary having the same 0.8.0 version string.
- [RTL88WiFi](https://github.com/X1REN41L/RTL88WiFi), [RealtekBluetoothFirmware](https://github.com/thegwchr/RealtekBluetoothFirmware), and [BlueToolFixup/BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM).
- [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C), [USBToolBox](https://github.com/USBToolBox/kext), [ECEnabler](https://github.com/1Revenger1/ECEnabler), [CpuTopologyRebuild](https://github.com/b00t0x/CpuTopologyRebuild), and [HoRNDIS](https://github.com/jwise/HoRNDIS).
- The exact origin of the `local.kiro` CS8409Audio/HAL build is not yet established. [nwrafael/CS8409Audio](https://github.com/nwrafael/CS8409Audio) is a related reference, not a demonstrated byte-identical replacement.

The Bluetooth patch retains the original project's GPL-2.0-or-later notice. Other components retain their own licenses. Apple driver binaries and the complete local EFI are not bundled in this public repository. Documentation was assembled with AI assistance from the owner's reports, saved configs, source changes and test results; unknowns are labeled rather than filled in.
