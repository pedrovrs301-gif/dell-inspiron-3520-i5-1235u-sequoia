# Dell Inspiron 15 3520 — Hackintosh com macOS Sequoia

Intel Core i5-1235U · Intel Iris Xe · 16 GB de RAM · SSD de 500 GB

Registrei aqui a configuração e os testes que fiz no meu Dell até **18/11/2026**. O objetivo é ajudar quem tem o mesmo hardware a entender os componentes usados e reproduzir os resultados com critério.

## Baixe minha EFI V12 completa

Publiquei a **[EFI V12 sem meu SMBIOS pessoal nesta release](https://github.com/pedrovrs301-gif/dell-inspiron-3520-i5-1235u-sequoia/releases/tag/v12-dell-sequoia-2026.11.18)**, com OpenCore, ACPI, drivers, kexts e o complemento de áudio. Leia o **[passo a passo em português](COMECE-AQUI.pt-BR.md)** e copie o **[texto pronto para seu ChatGPT](PARA-COLAR-NO-CHATGPT.txt)**. Organizei os arquivos para você fornecer à sua IA o contexto exato do meu conjunto e seguir uma etapa por vez.

No painel **Assets** da release, escolha `Dell-Inspiron-3520-i5-1235U-EFI-V12-Sem-SMBIOS-Pessoal.zip`. O ZIP antigo `Guia-GitHub.zip` contém documentação/modelo; não é a EFI completa. Os arquivos automáticos “Source code” também não são o pacote de boot.

Você precisa gerar a sua identidade para `MacBookPro16,2` e testar pelo pendrive antes de alterar a EFI interna. **Copiar a EFI não instala o WhateverXe Graphics 0.4.4 nem o HAL de áudio dentro do macOS.** Expliquei esses componentes no [guia técnico do pacote](LEIA-ME.pt-BR.md).

**Estado: experimental.** Meu conjunto foi usado no Sequoia 15.8.3; não testei macOS 26. A cópia anonimizada passou em validação de estrutura, mas precisa ser personalizada e testada no seu hardware.

## O que foi confirmado

| Recurso | Resultado no meu Dell | Limite da evidência |
|---|---|---|
| macOS | Sequoia 15.8.3, build 24H241 | Outras versões não testadas aqui |
| Boot pelo SSD | Confirmado após V12 e após a última troca do Wi-Fi | Confirmei o último boot pelo SSD em 16/11/2026; cópia anonimizada não testada em boot |
| Vídeo interno | 1536 MB, Metal 2 e 1920 × 1080 a 120 Hz registrados | Depende de WhateverXe modificado + pacote Graphics no macOS |
| Reprodução de vídeo | YouTube relatado como fluido após V12 | Estabilidade prolongada e resets de GPU ainda pendentes |
| Memória do WindowServer | Aproximadamente 132–155 MB após V12, contra cerca de 2,7 GB antes | Amostras iniciais; não comprovam correção definitiva de vazamento |
| Alto-falantes internos | Som confirmado durante a migração V12 | Depende também do plug-in HAL instalado no macOS |
| Wi-Fi 5 GHz | RTL88WiFi 1.0.0; medições Apple de 104 Mb/s de download e 223 Mb/s de upload; outra rodada de 245 Mb/s de download | Testes pelo pendrive; um teste longo em outro servidor falhou |
| Bluetooth LE com Wi-Fi | Descoberta de anúncio BLE confirmada em repouso e durante download em 5 GHz | Não comprova pareamento, áudio de fone nem Bluetooth Classic |
| Teclado, trackpad, bateria e USB | Kexts e mapa presentes | Não há checklist funcional completo recuperado |
| HDMI, microfone, P2, suspensão e autonomia | Pendente | Não anunciar como funcionando |
| AirDrop, Handoff e iServices | Não validados no meu Dell | Não prometer suporte a partir da descoberta BLE |

## Como ler e usar

1. [Comece aqui: passo a passo em português](COMECE-AQUI.pt-BR.md).
2. [Texto para colar no seu ChatGPT](PARA-COLAR-NO-CHATGPT.txt).
3. [Guia técnico do pacote V12](LEIA-ME.pt-BR.md).
4. [Inventário do pacote completo](COMPONENTS.json).
5. [Hardware](docs/01-HARDWARE.md), [componentes](docs/02-COMPONENTES.md) e [histórico dos ajustes](docs/04-HISTORICO.md).

As notas antigas registram a fase em que eu só havia publicado o modelo sem binários. Para a entrega completa de 18/11, siga os quatro primeiros links e o ZIP da release. Dentro do ZIP estão `EFI/`, `macOS/Audio/`, documentação, inventário, patches de fonte, créditos/licenças e `SHA256SUMS.txt`.

A configuração conserva os ajustes de diagnóstico e segurança do conjunto testado, incluindo `csr-active-config=03080000`, `SecureBootModel=Disabled` e os boot-args gráficos. Isso faz parte da configuração experimental, e deve ser entendido antes de usá-la. Não é uma recomendação geral de desativação de proteções.

## Créditos

A aceleração experimental usa [WhateverXe](https://github.com/b00t0x/WhateverXe) e o pacote Graphics correspondente. O Wi-Fi usa [RTL88WiFi](https://github.com/X1REN41L/RTL88WiFi). O Bluetooth parte de [RealtekBluetoothFirmware](https://github.com/thegwchr/RealtekBluetoothFirmware) com alterações locais e [BlueToolFixup](https://github.com/acidanthera/BrcmPatchRAM). O boot usa [OpenCore](https://github.com/acidanthera/OpenCorePkg).

Incluí os patches locais de vídeo e Bluetooth e preservei os créditos disponíveis. A origem exata do build local de áudio ainda não foi estabelecida; não trate o projeto relacionado como substituto byte a byte. As versões e lacunas estão no inventário do pacote. Os autores dos projetos mantêm os respectivos créditos e licenças.
