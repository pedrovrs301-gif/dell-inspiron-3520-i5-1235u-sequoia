# Hardware e compatibilidade

| Item | Configuração registrada | Origem |
|---|---|---|
| Notebook | Dell Inspiron 15 3520 | Meu notebook Dell 3520 |
| CPU | Intel Core i5-1235U, Alder Lake-U | Minha configuração |
| RAM | 16 GB | Minha configuração e meu histórico de testes |
| SSD | 500 GB | Minha configuração; fabricante/modelo não recuperados |
| iGPU | Intel Iris Xe, identificador reportado `8086:46a8` | Consulta atual ao macOS; um identificador reportado pode sofrer spoof |
| Tela | 1920 × 1080, 120 Hz | Saída de diagnóstico registrada no histórico V12 |
| Wi-Fi | Realtek RTL8821CE PCIe | Registros dos drivers e investigação local |
| Bluetooth | Realtek USB `0bda:c829`, associado ao combo | Registros de identificação USB e firmware |
| Áudio | Cirrus Logic CS8409 | Driver e HAL registrados; demais IDs do codec não recuperados |
| SMBIOS usado | `MacBookPro16,2` | Configuração OpenCore; é uma identidade emulada, não o modelo físico |
| BIOS | Versão e opções efetivamente usadas ainda não recuperadas | Não deduzir pela identidade Apple emulada |

O mesmo nome comercial pode acompanhar outra placa Wi-Fi, painel, codec, SSD ou revisão de placa-mãe. Compare os IDs antes de copiar ACPI ou UTBMap. O mapa USB aqui tem sete portas declaradas, incluindo duas internas (`UsbConnector=255`); não houve identificação recuperada de qual delas atende cada dispositivo interno.

Não há captura confiável de todas as opções da BIOS usada. Antes de publicar instruções específicas, registrar UEFI, Secure Boot, modo do armazenamento, Fast Boot e demais opções existentes, preservando a configuração necessária ao Windows. Não alterar RAID/AHCI em um Windows existente sem preparar essa migração.

O funcionamento deste Alder Lake depende de componentes gráficos experimentais. A documentação atual do [WhateverXe](https://github.com/b00t0x/WhateverXe) descreve suporte que varia por GPU e caminho de display; o resultado local não demonstra suporte universal ao 3520 ou a toda a família Alder Lake-U.
