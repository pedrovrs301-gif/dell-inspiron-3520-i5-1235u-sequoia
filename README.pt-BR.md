# Dell Inspiron 15 3520 — Hackintosh com macOS Sequoia

Intel Core i5-1235U · Intel Iris Xe · 16 GB de RAM · SSD de 500 GB

Registro da configuração e dos testes realizados nesta unidade até **16/11/2026**. O objetivo é ajudar quem tem o mesmo hardware a entender os componentes usados e reproduzir os resultados com critério.

**Estado: configuração experimental, com resultados reais e validação ainda incompleta.** O pacote público contém configuração anonimizada, ACPI, mapa USB, inventário, patch Bluetooth e documentação. A pasta `EFI` deste repositório **não é uma EFI completa para iniciar o computador**: os binários de terceiros devem ser obtidos separadamente. Há uma cópia completa para revisão local, fora deste repositório.

## O que foi confirmado

| Recurso | Resultado nesta unidade | Limite da evidência |
|---|---|---|
| macOS | Sequoia 15.8.3, build 24H241 | Outras versões não testadas aqui |
| Boot pelo SSD | Confirmado após V12 e após a última troca do Wi-Fi | Último boot pelo SSD confirmado pelo proprietário em 16/11/2026; cópia anonimizada não testada em boot |
| Vídeo interno | 1536 MB, Metal 2 e 1920 × 1080 a 120 Hz registrados | Depende de WhateverXe modificado + pacote Graphics no macOS |
| Reprodução de vídeo | YouTube relatado como fluido após V12 | Estabilidade prolongada e resets de GPU ainda pendentes |
| Memória do WindowServer | Aproximadamente 132–155 MB após V12, contra cerca de 2,7 GB antes | Amostras iniciais; não comprovam correção definitiva de vazamento |
| Alto-falantes internos | Som confirmado durante a migração V12 | Depende também do plug-in HAL instalado no macOS |
| Wi-Fi 5 GHz | RTL88WiFi 1.0.0; medições Apple de 104 Mb/s de download e 223 Mb/s de upload; outra rodada de 245 Mb/s de download | Testes pelo pendrive; um teste longo em outro servidor falhou |
| Bluetooth LE com Wi-Fi | Descoberta de anúncio BLE confirmada em repouso e durante download em 5 GHz | Não comprova pareamento, áudio de fone nem Bluetooth Classic |
| Teclado, trackpad, bateria e USB | Kexts e mapa presentes | Não há checklist funcional completo recuperado |
| HDMI, microfone, P2, suspensão e autonomia | Pendente | Não anunciar como funcionando |
| AirDrop, Handoff e iServices | Não validados nesta unidade | Não prometer suporte a partir da descoberta BLE |

## Como ler e usar

1. [Hardware e limites de compatibilidade](docs/01-HARDWARE.md).
2. [Componentes, versões, origens e dependências](docs/02-COMPONENTES.md).
3. [Preparação, instalação e recuperação](docs/03-INSTALACAO.md).
4. [Histórico dos ajustes e diagnóstico](docs/04-HISTORICO.md).
5. [Testes pendentes e preparação da publicação](docs/05-VALIDACAO-E-PUBLICACAO.md).
6. [Inventário dos kexts](manifests/kexts.json) e [SHA-256 da cópia local anonimizada](manifests/EFI-SHA256.json).

## Estrutura

```text
EFI/OC/config.example.plist    Configuração sem serial, MLB, UUID ou ROM pessoais
EFI/OC/ACPI/                   Somente as sete tabelas habilitadas nesta configuração
EFI/OC/Kexts/UTBMap.kext/      Mapa USB desta unidade; revisar em outro equipamento
patches/                      Alterações do firmware Bluetooth sobre a base v1.0.2
scripts/                      Auditoria da configuração e montagem a partir de arquivos locais
manifests/                    Versões e hashes para comparação
third-party/                  Aviso de licença da base do patch Bluetooth
```

A configuração conserva os ajustes de diagnóstico e segurança do conjunto testado, incluindo `csr-active-config=03080000`, `SecureBootModel=Disabled` e os boot-args gráficos. Isso faz parte da configuração experimental, e deve ser entendido antes de usá-la. Não é uma recomendação geral de desativação de proteções.

## Créditos

A aceleração experimental usa [WhateverXe](https://github.com/b00t0x/WhateverXe) e o pacote Graphics correspondente. O Wi-Fi usa [RTL88WiFi](https://github.com/X1REN41L/RTL88WiFi). O Bluetooth parte de [RealtekBluetoothFirmware](https://github.com/thegwchr/RealtekBluetoothFirmware) com alterações locais e [BlueToolFixup](https://github.com/acidanthera/BrcmPatchRAM). O boot usa [OpenCore](https://github.com/acidanthera/OpenCorePkg).

As versões locais de vídeo e áudio precisam ter sua origem exata registrada antes de uma release binária pública. Os detalhes conhecidos e essas lacunas estão no inventário de componentes. Os autores dos projetos mantêm os respectivos créditos e licenças.
