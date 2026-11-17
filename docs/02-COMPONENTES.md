# Componentes e versões

O [inventário JSON](../manifests/kexts.json) registra cada entrada Kernel/Add, versão, identificação e faixa de kernel. As versões abaixo vieram dos arquivos locais, não de uma seleção automática das releases mais recentes.

| Componente | Versão local | Uso e origem |
|---|---|---|
| OpenCore | Versão exata do binário pendente; ocvalidate usado é 1.0.8 | [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg); não deduzir a versão do bootloader só pela ferramenta |
| Lilu | 1.7.2 | [acidanthera/Lilu](https://github.com/acidanthera/Lilu) |
| VirtualSMC / SMCBatteryManager | 1.3.8 | [acidanthera/VirtualSMC](https://github.com/acidanthera/VirtualSMC) |
| AMFIPass | 1.4.1 | Arquivo local; conferir variante/mod e origem exata antes de substituir |
| WhateverXe | 0.8.0, variante local chamada V12-Dell / ABI1 | Base [b00t0x/WhateverXe](https://github.com/b00t0x/WhateverXe); fork/patch exato ainda não localizado |
| WhateverXe Graphics | Pacote instalado 0.4.4 | Instalado no macOS, fora da EFI; recibo `org.b00t0x.WhateverXe.Sequoia` |
| AppleIntelTGLCompat | 0.8.1 no histórico carregado | Parte do conjunto gráfico instalado no sistema |
| CpuTopologyRebuild | 2.0.2 | [b00t0x/CpuTopologyRebuild](https://github.com/b00t0x/CpuTopologyRebuild) |
| NVMeFix | 1.1.3 | [acidanthera/NVMeFix](https://github.com/acidanthera/NVMeFix) |
| RestrictEvents | 1.1.6 | [acidanthera/RestrictEvents](https://github.com/acidanthera/RestrictEvents) |
| ECEnabler | 1.0.6 | [1Revenger1/ECEnabler](https://github.com/1Revenger1/ECEnabler) |
| VoodooI2C | 2.9.1 | [VoodooI2C/VoodooI2C](https://github.com/VoodooI2C/VoodooI2C), com VoodooInput, GPIO, Services e HID |
| VoodooPS2Controller | 2.3.7 | [acidanthera/VoodooPS2](https://github.com/acidanthera/VoodooPS2); teclado habilitado, mouse/trackpad PS2 desabilitados |
| USBToolBox / UTBMap | 1.2.0 / 1.1 | [USBToolBox/kext](https://github.com/USBToolBox/kext); mapa específico do meu Dell |
| HoRNDIS | 9.2 | [jwise/HoRNDIS](https://github.com/jwise/HoRNDIS); conexão USB Android usada como apoio nos testes |
| RTL88WiFi | 1.0.0 | [X1REN41L/RTL88WiFi](https://github.com/X1REN41L/RTL88WiFi), pacote salvo do repositório |
| IOSkywalkFamily | 1.0 | Dependência do pacote de Wi-Fi; identificação `com.apple.iokit.IOSkywalkFamily` |
| IO80211FamilyLegacy | 1200.12.2b1 | Dependência do pacote de Wi-Fi; identificação Apple; não incluída no repositório público |
| CS8409Audio | 1.1.0 | Identificação `local.kiro.CS8409Audio`; origem exata do build pendente |
| CS8409Speakers.driver | 1.1.0 | Plug-in Core Audio, instalado em `/Library/Audio/Plug-Ins/HAL/`; fora da EFI |
| RealtekBluetoothFirmware | Base 1.0.2 com modificações locais | [thegwchr/RealtekBluetoothFirmware](https://github.com/thegwchr/RealtekBluetoothFirmware/releases/tag/v1.0.2); patch cumulativo neste repositório |
| BlueToolFixup | 2.7.2 | [acidanthera/BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.7.2) |
| OpenRuntime / OpenHfsPlus | Binários do conjunto local | Drivers UEFI; não trocar isoladamente por versões aleatórias |

## Vídeo: a EFI não basta

O conjunto que apresentou resultado combina WhateverXe V12-Dell na EFI, Graphics 0.4.4 instalado no macOS e os ajustes presentes no config. Uma release upstream com o mesmo número 0.8.0 não é prova de equivalência ao build V12-Dell. O hash do executável local está em `manifests/EFI-SHA256.json`. O executável V12 tem SHA-256 `0a310c0838683b03810e04e00390efa27d0063977096becaeca1844735c6409f` no histórico; comparar com o inventário.

Conservar o conjunto compatível e localizar a origem/patch específico antes de ensinar uma reinstalação completa. Os boot-args do exemplo foram preservados, inclusive os de diagnóstico: `-wex-display13-trace`, `-wex-edp-keep`, `-wex-edp-blank`, `-wex-edp-diag` e `-wex-dell-wm2ppc`. Não foram removidos sem teste.

## Áudio: kext + HAL

O áudio confirmado usa CS8409Audio na EFI e CS8409Speakers.driver no macOS. Há um projeto público [nwrafael/CS8409Audio](https://github.com/nwrafael/CS8409Audio) para o mesmo codec/modelo, mas **não foi demonstrado que ele produz exatamente o build `local.kiro` usado aqui**. Ele é uma referência de arquitetura e possível origem a investigar, não um substituto já validado. O funcionamento do microfone e da saída P2 local continua pendente.

## Wi-Fi e Bluetooth

No Sequoia, as três entradas de Wi-Fi estão limitadas a Darwin `24.0.0–24.99.99`. A ordem é Lilu, AMFIPass, IOSkywalkFamily, IO80211FamilyLegacy e RTL88WiFi. Há Kernel/Block para excluir a IOSkywalkFamily nativa nessa mesma faixa. O AirportRTW e o patch antigo de coexistência foram desativados na migração, depois omitidos desta cópia organizada.

O [projeto RTL88WiFi](https://github.com/X1REN41L/RTL88WiFi) informa limitações de autenticação e de AWDL. A versão usada aqui é a 1.0.0 salva durante os testes; não assumir equivalência com atualizações do projeto.

No Bluetooth, a versão 1.0.2 do Info.plist não identifica por si só o binário: o meu foi modificado. Além de acrescentar o match `0bda:c829`, foram adicionados diagnóstico, recuperação do firmware persistente por comando `0xfc66` e HCI Reset. O hash final registrado é `abc5551548789f8755944881d76d11bbc0670da03cae3f6313a5dc4d625abd01`. O patch em `patches/` compara o código salvo original com o código do teste 5; não foi recompilado nesta preparação.

A configuração inclui `-btlfxboardid`, `bluetoothInternalControllerInfo` com **14 bytes zero**, `bluetoothExternalDongleFailed` com 1 byte zero e `bluetoothHostControllerSwitchBehavior=always`. As mesmas chaves constam em NVRAM/Delete para substituição no boot. Não são endereços pessoais. Esses ajustes foram testados nesse conjunto; não são uma receita universal.

## ACPI

As sete tabelas habilitadas são SSDT-EC, USBX, PLUG-12, RTCAWAC, PNLF, XOSI e HPET. Elas tratam compatibilidade do controlador embarcado, alimentação USB, descrição de CPU/plugin, RTC/AWAC, painel, chamadas de sistema e HPET. A tabela PLUG-ALT desativada foi omitida. Não copie essas tabelas para outra revisão sem conferir os caminhos ACPI.
