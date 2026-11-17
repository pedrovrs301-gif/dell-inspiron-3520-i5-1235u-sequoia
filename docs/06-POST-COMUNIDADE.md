# Minuta para divulgação

## Português

**Título:** Dell Inspiron 15 3520 / i5-1235U: Sequoia, Iris Xe experimental, áudio CS8409 e Wi-Fi RTL8821CE

Reuni a documentação do meu Hackintosh no Dell Inspiron 15 3520 com i5-1235U, 16 GB de RAM e SSD de 500 GB, rodando macOS Sequoia 15.8.3 (24H241).

O último boot direto pelo SSD, após a troca do driver Wi-Fi, funcionou. O conjunto gráfico experimental usa WhateverXe V12-Dell + Graphics 0.4.4; registrei Metal 2, 1536 MB e tela interna 1920 × 1080 a 120 Hz. Os alto-falantes usam CS8409Audio + o plug-in HAL CS8409Speakers.

Nos testes anteriores pelo pendrive, o RTL88WiFi 1.0.0 apresentou um ganho grande sobre o AirportRTW: o medidor Apple registrou 104 Mb/s de download e 223 Mb/s de upload, e outra rodada chegou a 245 Mb/s de download. Também confirmei descoberta Bluetooth LE durante transferência em Wi-Fi 5 GHz, usando RealtekBluetoothFirmware modificado para o dispositivo USB 0bda:c829 e BlueToolFixup.

O guia inclui componentes e versões, configuração anonimizada, ACPI, mapa USB desta unidade, patch Bluetooth e histórico das tentativas. Ainda faltam testes longos, fone Bluetooth, suspensão e outras funções. Um download longo em outro servidor falhou, então não estou anunciando estabilidade total. A aceleração Iris Xe é experimental e depende de componentes instalados no macOS além da EFI.

**Documentação:** [inserir URL real do repositório antes de publicar]

Publiquei como relato experimental. A pasta pública é um modelo documentado, sem os binários completos; não é só copiar e iniciar. Ainda estou identificando a origem exata de alguns builds locais antes de oferecer uma release completa reproduzível.

Créditos aos autores de OpenCore, WhateverXe, RTL88WiFi, RealtekBluetoothFirmware, BlueToolFixup e demais componentes citados no guia. Se alguém tiver a mesma configuração, relatos de suspensão, áudio Bluetooth e estabilidade prolongada podem ajudar na comparação.

## English

**Title:** Dell Inspiron 15 3520 / i5-1235U — Sequoia with experimental Iris Xe graphics, CS8409 audio and RTL8821CE Wi-Fi

I documented my Dell Inspiron 15 3520 Hackintosh: Core i5-1235U, 16 GB RAM, 500 GB SSD, Intel Iris Xe and macOS Sequoia 15.8.3 (24H241).

The latest SSD boot after the Wi-Fi driver change succeeded. The experimental graphics setup uses a local WhateverXe V12-Dell build with Graphics 0.4.4. Recorded results include Metal 2, 1536 MB reported VRAM and the internal 1920 × 1080 display at 120 Hz. Internal speakers were confirmed with CS8409Audio and the CS8409Speakers HAL plug-in.

Earlier USB-boot tests with RTL88WiFi 1.0.0 showed a substantial improvement over AirportRTW: Apple's measurement tool recorded 104 Mb/s down and 223 Mb/s up, and a separate download run reached 245 Mb/s. BLE advertisement discovery also passed while transferring over 5 GHz Wi-Fi, using locally modified RealtekBluetoothFirmware for USB 0bda:c829 plus BlueToolFixup.

This remains experimental. A longer transfer from another server timed out, and long-term graphics stability, sleep/wake, Bluetooth headset audio and several other features remain unverified. SSD boot success is confirmed, but the speed/BLE numbers above came from USB-boot testing. Graphics and speaker audio require components installed in macOS in addition to the EFI.

**Documentation:** [insert actual repository URL before posting]

The repository includes a sanitized config template, the saved ACPI tables, this unit's USB map, component versions, a cumulative Bluetooth patch and troubleshooting history. It does not contain a complete boot-ready EFI. Exact provenance of some local graphics/audio builds is still being established before a reproducible binary release.

Thanks to the authors of OpenCore, WhateverXe, RTL88WiFi, RealtekBluetoothFirmware, BlueToolFixup and the other projects credited in the guide. Reports from identical hardware, especially sleep/wake, Bluetooth audio and long-term stability, would be useful.
