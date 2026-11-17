# Trackpad, keyboard, ACPI, USB and CPU

## Trackpad and keyboard

In my earlier Dell hardware report, I confirmed that my trackpad and keyboard worked. The final graphics/network changes preserved the input configuration. The precise trackpad vendor/device ID and a gesture-by-gesture test were not recovered, so no unsupported model name is assigned here.

| Kernel/Add entry | State |
|---|---|
| VoodooI2C.kext/Contents/PlugIns/VoodooInput.kext | Enabled, version 1.1.7 |
| VoodooI2C.kext/Contents/PlugIns/VoodooGPIO.kext | Enabled |
| VoodooI2C.kext/Contents/PlugIns/VoodooI2CServices.kext | Enabled |
| VoodooI2C.kext | Enabled, version 2.9.1 |
| VoodooI2CHID.kext | Enabled |
| VoodooPS2Controller.kext | Enabled, version 2.3.7 |
| VoodooPS2Keyboard.kext | Enabled |
| VoodooPS2Mouse / VoodooPS2Trackpad plug-ins | Disabled in config |

`-vi2c-force-polling` is present in boot-args. This configuration uses that polling choice; do not describe it as an interrupt-driven trackpad fix. It was retained during the graphics work. SSDT-XOSI and the associated ACPI renames are also present, but the available evidence does not isolate which change was decisive for initial input bring-up.

BrightnessKeys.kext was disabled in the original config and omitted from the organized copy. Do not enable it and list brightness keys as tested. Similarly, a trackpad being usable does not establish all gestures, palm rejection, physical click or wake behavior.

## ACPI tables and renames

| Enabled table | Purpose of this configuration component |
|---|---|
| SSDT-EC.aml | EC compatibility |
| SSDT-USBX.aml | USB power properties |
| SSDT-PLUG-12.aml | CPU/plugin description for the saved Dell setup |
| SSDT-RTCAWAC.aml | RTC/AWAC compatibility |
| SSDT-PNLF.aml | Panel/backlight device declaration |
| SSDT-XOSI.aml | OS-interface compatibility |
| SSDT-HPET.aml | HPET/resource compatibility |

The unused SSDT-PLUG-ALT table is not included. The template also preserves these enabled patches:

- PNLF → XNLF.
- OSID → XSID **before** _OSI → XOSI.
- HPET _STA → XSTA and _CRS → XCRS.
- The saved IPIC IRQ 2, RTC IRQ 8 and TIMR IRQ 0 resource patches.

These are copied from the working unit's configuration, not generic ACPI replacements for every Dell. Confirm ACPI paths, device names and firmware revision before reuse. The complete Find/Replace bytes are in the example plist.

## USB map

USBToolBox 1.2.0 loads the provided UTBMap 1.1. Seven entries are declared:

| Name | Port bytes interpreted as integer | UsbConnector |
|---|---:|---:|
| HS01 | 1 | 3 |
| HS02 | 2 | 3 |
| HS03 | 3 | 0 |
| HS04 | 6 | 255 (internal) |
| HS05 | 10 | 255 (internal) |
| SS01 | 13 | 3 |
| SS02 | 14 | 3 |

`port-count` is 14, indicating the upper port index, not 14 enabled connectors. I have not yet recovered a physical-port/device assignment proving which internal entry is Bluetooth and which is the other internal device. Preserve the map for my laptop, but remap and test another hardware revision rather than assuming its wiring.

## CPU, storage and battery

CpuTopologyRebuild 2.0.2, NVMeFix 1.1.3, VirtualSMC/SMCBatteryManager 1.3.8 and ECEnabler 1.0.6 are present. Their configured presence is not a substitute for CPU power, battery accuracy or SSD health measurements.

The saved CPU emulation bytes are:

```text
Cpuid1Data: 55060a00000080000000000000000000
Cpuid1Mask: ffffffff0000ff000000000000000000
DummyPowerManagement: false
```

These are configuration bytes for this saved setup, not personal identifiers. Do not transplant them into a different CPU configuration without understanding the topology and matching OpenCore settings.

HoRNDIS 9.2 provided an Android USB-network fallback during Wi-Fi troubleshooting. It is included in the saved loading sequence; normal Wi-Fi operation does not require keeping a phone attached.

Still to record: exact trackpad ID, physical USB assignments, gestures, brightness behavior, sleep/wake, battery percentages/runtime, CPU frequency/thermals and storage model.
