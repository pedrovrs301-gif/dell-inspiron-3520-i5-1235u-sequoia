# Graphics acceleration on this Dell

## What actually worked

The recovered successful combination was:

| Layer | Recorded version / value |
|---|---|
| OS | Sequoia 15.8.3 (24H241) |
| Lilu | 1.7.2 |
| AMFIPass | 1.4.1; earlier report identifies the Mod variant; exact saved package origin still needs confirmation |
| EFI graphics plug-in | WhateverXe 0.8.0, locally called **V12-Dell / ABI1** |
| Installed Graphics package | 0.4.4, receipt `org.b00t0x.WhateverXe.Sequoia` |
| Loaded compatibility component | AppleIntelTGLCompat 0.8.1, recorded in the successful boot |
| Runtime contract | `WhateverXeGraphicsABI = <01000000>` |
| Display result | 1920 × 1080 at 120 Hz, 1536 MB reported VRAM, Metal 2 |

The saved V12 executable's SHA-256 is:

```text
0a310c0838683b03810e04e00390efa27d0063977096becaeca1844735c6409f
```

The inventory independently matches that local file. **0.8.0 alone is not enough to identify this build.** A stock release, an old local experiment and V12 can all have the same bundle version. Source/commit recovery for the final Dell-specific build is still pending, so this page does not pretend to be a complete source-level reproduction of its modifications.

## Why copying only the EFI is insufficient

There is a kernel-side part in the EFI and a graphics package installed in the macOS volume. In this build they were validated as a combination. A copied EFI without the compatible system-side drivers can reach a fallback desktop without acceleration or fail at the display handoff.

The successful history records Graphics **0.4.4**. Earlier unsuccessful tests used Graphics **0.4.1**. Do not assume a current release from the upstream project reproduces the same combination, and do not mix the separate Tahoe/NootedGreen stack into this Sequoia installation.

Our earlier [Sequoia compatibility discussion](https://github.com/JulioNectar/Hackintosh-XPS13Plus-IrisXe-Tahoe/issues/33) concerned another project's Tahoe stack. That discussion did not establish a working Sequoia port of that stack. The working result documented here is WhateverXe-based.

## Boot arguments preserved in the current template

```text
-v keepsyms=1 debug=0x100 -vi2c-force-polling -allow3d -disablegfxfirmware -wex-display13-trace -wex-edp-keep -wex-edp-blank -wex-edp-diag -wex-dell-wm2ppc -btlfxboardid
```

| Argument | Role in the recorded configuration |
|---|---|
| `-v`, `keepsyms=1`, `debug=0x100` | Boot/panic diagnostics; not an acceleration fix by themselves |
| `-vi2c-force-polling` | Input-device configuration, retained while graphics was changed |
| `-allow3d`, `-disablegfxfirmware` | Present in the tested graphics setup; not independently toggled in this preparation |
| `-wex-display13-trace`, `-wex-edp-diag` | Display13/eDP diagnostic flags used in local experiments |
| `-wex-edp-keep` | Selects the keep-path experiment for the firmware-trained internal panel link |
| `-wex-edp-blank` | Related panel blanking experiment; not proof that system sleep works |
| `-wex-dell-wm2ppc` | Dell-specific flag present in the local build; do not assume the official binary implements it |
| `-btlfxboardid` | BlueToolFixup configuration, unrelated to GPU acceleration |

Binary inspection finds the literal Dell flag, watermark-related function symbols and `WEXV11WM2PPC*` diagnostic keys. This supports identifying the local watermark-related path, **but does not recover its source or prove the precise arithmetic change**. The V11 marker in the executable is not a reason to discard the independently matched V12 hash/ABI1 result. No new source-level explanation is invented from the flag's name.

`DeviceProperties/Add` is empty in the final saved config. There is no GPU device-ID spoof/framebuffer injection in that section. Do not copy another laptop's IGPU properties or assume they were what made this build work.

The saved security settings are `csr-active-config` Data `03080000` and OpenCore `SecureBootModel=Disabled`, with AMFIPass in the loading sequence. The current template preserves these requirements of the experimental combination; changing them requires retesting.

## Earlier failure modes and what they taught us

The original Dell issue described physical GPU 46A8/80 EU and an NCP005F panel with a preferred timing near 120.035 Hz and a 335.47 MHz pixel clock. That report also recorded a 4-lane firmware-trained eDP path. These are **this unit's historical measurements**, not values to force on a different panel.

The initial upstream setup reached a desktop with 7 MB and no Metal. Later local experiments reached the native display transition but sometimes produced a black panel while the kernel/network continued running. Earlier reports distinguish failures during full link training from a later Partial → Complete modeset transition returning NotReady. They do not establish that every black-screen experiment had the same cause.

The [eDP discussion](https://github.com/b00t0x/WhateverXe/issues/43) records attempts based on `display13-edp-keep-080`, including local diagnostics and the difference between the branch and our local adaptations. Passing a build or CPU-only native checks was not treated as proof that physical eDP training worked.

V11 eventually produced acceleration but showed high WindowServer memory use, around 2.7 GB in an observation. V12 was tested on a separate macOS test volume before migration. After V12 + Graphics 0.4.4, initial WindowServer observations were around 132–155 MB and YouTube was reported smooth. This is a substantial observed improvement, **not proof that a long-session leak or every GPU reset was eliminated**.

## Checks that identified the successful combination

Inspect locally:

```bash
sw_vers
pkgutil --pkg-info org.b00t0x.WhateverXe.Sequoia
kmutil showloaded | rg 'WhateverXe|AppleIntelTGLCompat|CS8409Audio'
system_profiler SPDisplaysDataType
ioreg -p IOService -l -w0 | rg -m 1 'WhateverXeGraphicsABI'
```

If `rg` is unavailable, use equivalent `grep` filters. Expected historical values are Graphics 0.4.4, WhateverXe 0.8.0, AppleIntelTGLCompat 0.8.1, ABI1 data, 1536 MB and Metal 2. Version strings alone are incomplete evidence; compare the executable hash too.

Smooth YouTube is not a measurement of H.264/HEVC/AV1 hardware decode. Metal support, rendering acceleration, codec-specific media acceleration and DRM are different checks.

## Migration and recovery

The successful migration replaced the graphics kext after backing up the EFI and verifying the files. The config and CS8409 audio components were preserved. The former V11 executable was kept outside the active kext path for rollback. The public organized copy omits that backup.

Before testing another graphics build, preserve both the EFI and the macOS-side Graphics version. A bootable USB using an incompatible system-side graphics installation may not be enough to restore acceleration. Keep the previous package/source and follow its removal/reinstall procedure.

Still needed: a long-session test log, GPU-reset monitoring, external display tests, display sleep/system sleep, and the exact source diff/release for the final V12-Dell executable.
