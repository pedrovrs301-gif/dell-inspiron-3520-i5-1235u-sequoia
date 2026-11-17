# Audio, Wi-Fi and Bluetooth

## Internal speakers: two required parts

| Part | Version | Location / purpose |
|---|---|---|
| CS8409Audio.kext | 1.1.0, identifier `local.kiro.CS8409Audio` | Injected by OpenCore; codec/controller side |
| CS8409Speakers.driver | 1.1.0, identifier `local.kiro.CS8409Speakers` | `/Library/Audio/Plug-Ins/HAL/`; Core Audio device side |

The owner confirmed audible internal-speaker output during the V12 migration. Merely seeing a loaded kext is weaker evidence. A fresh macOS installation needs the HAL component as well as the EFI kext.

The exact source/release for these `local.kiro` builds has not yet been recovered. A [related CS8409 project](https://github.com/nwrafael/CS8409Audio) uses the same two-part architecture, but its current release has not been shown to be interchangeable with our local build. Do not replace the working files based only on their names. This build did not use an `alcid` value as the documented speaker solution.

Keep a backup of the HAL before any replacement, preserve permissions required by its actual installer, and check the selected Core Audio output after restarting. We are not providing a speculative install command for an unverified package. Microphone, P2/headphone jack, codec details and behavior after sleep still need local validation.

## Wi-Fi: what changed

The card is RTL8821CE. AirportRTW 2.3.1 was the baseline. Feixiao 1.0.1 with Starskiff 1.0.0 and later AirportRTW diagnostic/2.3.3 experiments were also tried. Neither became the final choice. The final tested combination is:

1. Existing Lilu 1.7.2 and AMFIPass 1.4.1.
2. IOSkywalkFamily.kext 1.0.
3. IO80211FamilyLegacy.kext 1200.12.2b1.
4. RTL88WiFi.kext 1.0.0.

These three Wi-Fi additions are restricted to Darwin `24.0.0–24.99.99` in Kernel/Add. Kernel/Block excludes the native `com.apple.iokit.IOSkywalkFamily` in the same range. This matters: adding only the Realtek driver is not the recorded Sequoia setup. Do not enable AirportRTW, Feixiao/rtw88 and RTL88WiFi together.

The old AirportRTW coexistence patch is disabled in the migrated SSD config and omitted from the organized template. The RTL88WiFi dependency binaries are not distributed in this public repository. The exact files used are identified by SHA-256.

### Observations, including the failure

| Measurement | Result |
|---|---|
| Earlier AirportRTW download rounds | Approximately 11–18 Mb/s; large latency variation |
| RTL88WiFi short transfers | Approximately 126 Mb/s down, 107 Mb/s up |
| Apple measurement tool | Approximately 104 Mb/s down, 223 Mb/s up |
| Separate BLE/coexistence download run | Approximately 245 Mb/s down while the target BLE advertisement was found |
| Larger Cloudflare transfer | About 8.16 MB received in 25 seconds, timeout; about 2.61 Mb/s effective |

These were USB-boot tests. The owner subsequently confirmed the migrated SSD configuration booted, but a repeated SSD speed/BLE test is not yet recorded. A larger upload also failed in one test. Different servers and tools later behaved differently; the cause was not isolated.

Turning Bluetooth off was not a consistent cure: an on/off comparison had variable results. In another earlier comparison 2.4 GHz latency was much lower than 5 GHz, but that was with an older driver. Those results do not establish a universal bad band or defective router.

The [RTL88WiFi project](https://github.com/X1REN41L/RTL88WiFi) describes authentication and AWDL limitations. Do not infer AirDrop or WPA3-only support from a successful WPA2-style connection. Keep the saved 1.0.0 package and compare any replacement separately.

## Bluetooth Realtek USB 0bda:c829

Wi-Fi is PCIe and Bluetooth is a separate internal USB device. Getting Wi-Fi online does not establish a working Bluetooth controller.

The local solution uses RealtekBluetoothFirmware base 1.0.2 **with code changes**, plus BlueToolFixup 2.7.2. Final saved firmware-kext executable:

```text
abc5551548789f8755944881d76d11bbc0670da03cae3f6313a5dc4d625abd01
```

The cumulative [source patch](../patches/RealtekBluetoothFirmware-v1.0.2-dell-c829.patch) includes the USB match and later diagnostic/recovery changes. Its application was checked against the saved original v1.0.2 source; it was not recompiled during this documentation pass.

### Why a USB match alone was insufficient

The investigation progressed beyond adding the device ID. A previously loaded rtl8821c firmware identity could survive a reboot, so the ROM-only identification table did not match it. The later recovery path uses vendor command `0xfc66`, waits 200 ms, reads the ROM identity again, and performs the standard HCI reset after firmware upload. The recovery is restricted to the observed USB/firmware identity; it is not a generic reset for arbitrary controllers.

Selecting the controller also required the BlueToolFixup board-ID path (`-btlfxboardid`) in this SMBIOS/OS combination and the following NVRAM values under `7C436110-AB2A-4BBB-A880-FE41995C9F82`:

| Key | Value in the tested config |
|---|---|
| `bluetoothInternalControllerInfo` | Data, **14 zero bytes** |
| `bluetoothExternalDongleFailed` | Data, 1 zero byte |
| `bluetoothHostControllerSwitchBehavior` | String, `always` |

The same keys appear in NVRAM/Delete so the saved values can be replaced at boot. One earlier configuration used **13 bytes** for internal controller info and produced an explicit service length error; the final config corrects it to 14. These zero fields are configuration values, not the owner's actual Bluetooth address.

### What passed

A known BLE advertisement was discovered with Wi-Fi on 5 GHz, and again during a download. This validates **discovery in those scenarios**. It does not validate headset pairing/audio, Bluetooth Classic, HID reconnect, 2.4 GHz coexistence or Apple Continuity. Those remain separate tests.
