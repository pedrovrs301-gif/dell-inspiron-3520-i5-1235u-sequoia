# Preparação e reprodução

Este documento descreve a preparação da configuração recuperada. **Ainda não é um tutorial de instalação limpa integralmente reproduzível**: falta localizar a distribuição exata da variante V12-Dell e do build CS8409 local, registrar a BIOS e completar os testes de rede/BLE após o último boot pelo SSD. Confirmei em 16/11/2026 que esse boot deu certo.

## 1. Preparar recuperação e conferir o hardware

Manter um pendrive com a EFI que já inicia a própria máquina e um backup integral da EFI interna fora do disco de destino. Identificar os discos por capacidade, tipo e partições; os números `disk0`, `disk3` etc. mudam. Não copiar identificadores de disco dos testes antigos. Se houver Windows, guardar a chave de recuperação do BitLocker antes de alterações de firmware/boot.

Para o instalador do macOS e os conceitos do OpenCore, consultar o [guia Dortania](https://dortania.github.io/OpenCore-Install-Guide/). Usar o guia como base, complementado pelas dependências experimentais deste equipamento. Confirmar as opções reais da BIOS antes de registrar uma receita para o 3520.

## 2. Obter os componentes

Ler `02-COMPONENTES.md` e o inventário. Reunir o bootloader, drivers, kexts, tabelas ACPI e mapa USB. Preservar as versões e a ordem de carregamento. As dependências Apple de Wi-Fi e os pacotes gráficos/HAL não estão incorporados ao repositório público.

Para reconstruir uma cópia usando uma pasta EFI completa que já contenha os arquivos exatos do inventário:

```bash
python3 scripts/montar_efi.py /caminho/para/EFI-completa ./build/EFI
```

O script não baixa arquivos, não acessa partições, não usa sudo e não sobrescreve um destino existente. Confere os hashes antes de copiar. O resultado ainda é um modelo anonimizado.

## 3. Gerar identidade própria

Copiar `config.example.plist` para `config.plist` no conjunto que será testado. Gerar serial, MLB e UUID próprios para o SMBIOS `MacBookPro16,2`, por exemplo com [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), e preencher `PlatformInfo/Generic`. Definir ROM conforme a orientação do OpenCore para a identidade da própria máquina. Nunca publicar esses quatro campos.

O exemplo tem serial e MLB vazios, UUID zero e ROM com seis bytes zero. Isso é intencional; não iniciar nem configurar serviços Apple com essa identidade de exemplo.

## 4. Validar e testar pelo pendrive

```bash
python3 scripts/auditar_efi.py ./build/EFI --personal
/caminho/para/ocvalidate ./build/EFI/OC/config.plist
```

Usar o ocvalidate correspondente à versão real do OpenCore. A ferramenta local recuperada é compatível com 1.0.8, mas ainda falta confirmar o número exato do binário OpenCore salvo. A auditoria verifica arquivos, tamanhos dos campos e ausência de identidade pessoal; o ocvalidate verifica a estrutura. Nenhum deles comprova boot, vídeo, áudio ou estabilidade.

Selecionar a entrada UEFI do pendrive no menu F12 do Dell, sem substituir a EFI interna nesse primeiro teste. O modelo conserva `ShowPicker=true`, `Timeout=0` e `AllowSetDefault=true`: o menu fica visível e aguarda seleção. A conversa antiga discutiu ocultar o picker, mas o último config recuperado mantém o menu.

## 5. Instalar os componentes que ficam no macOS

Vídeo exige o pacote Graphics compatível e áudio exige o HAL CS8409Speakers. **Não há pacote instalador desses componentes nesta entrega.** Localizar a origem exata e seguir seu procedimento, incluindo os requisitos de assinatura/proteções. Não usar uma versão encontrada por nome como se fosse o mesmo build testado.

Conferir versões com:

```bash
sw_vers
pkgutil --pkg-info org.b00t0x.WhateverXe.Sequoia
system_profiler SPDisplaysDataType SPAudioDataType
kmutil showloaded
```

Inspecionar localmente e compartilhar somente os trechos necessários, retirando serial, MAC, SSID e nomes pessoais.

## 6. Levar ao SSD e recuperar

Depois de preencher a identidade e testar o conjunto completo pelo pendrive, fazer novo backup da EFI interna e transferir **somente os arquivos do OpenCore necessários**, preservando bootloaders do Windows e arquivos OEM. Comparar a cópia e selecionar a entrada UEFI correta. Não formatar o SSD como parte da instalação da EFI.

Se não iniciar, selecionar a EFI anterior pelo F12. Restaurar o backup da EFI correta depois de confirmar a partição. Se a mudança incluiu Graphics ou HAL no macOS, restaurar também esses componentes: voltar só a EFI pode não desfazer alterações no sistema. Não fazer reset de NVRAM como passo automático em máquina com múltiplos sistemas.
