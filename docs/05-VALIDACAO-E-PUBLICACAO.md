# Validação e publicação

## Preparação já feita

- Configuração derivada da última preparação registrada para o SSD.
- Serial, MLB, UUID e ROM pessoais removidos.
- Somente sete ACPI habilitadas; tabela alternativa e backups omitidos.
- AirportRTW e BrightnessKeys desabilitados omitidos; patch antigo de coexistência removido.
- Arquivos Dell, telemetria, SysReport, logs e cópia V11 não incluídos.
- Configuração funcional preservada nos demais campos; menu de boot e diagnósticos mantidos.
- Inventário por versão e SHA-256, documentação dos resultados e patch cumulativo Bluetooth.
- EFI completa montada para revisão **local**, fora da pasta pública, com identidade vazia.

## Antes de anunciar uma release completa para o mesmo modelo

- [x] Confirmar boot pelo SSD depois da última troca do Wi-Fi — confirmei em 16/11/2026.
- [ ] Registrar versão e opções reais da BIOS, SSD e IDs completos do Wi-Fi/áudio.
- [ ] Identificar a revisão exata de OpenCore, além da versão do validador.
- [ ] Localizar fonte/release/patch da variante WhateverXe V12-Dell e do CS8409Audio/HAL local.
- [ ] Documentar instalação limpa dos componentes fora da EFI.
- [ ] Recompilar o patch Bluetooth e comparar resultado/funcionamento; fonte foi recuperada, mas não recompilada nesta etapa.
- [ ] Repetir downloads longos e uploads, distinguindo servidor, tamanho e duração.
- [ ] Testar BLE e fone/áudio Bluetooth, reconexão e coexistência em 2,4 e 5 GHz.
- [ ] Testar suspensão, despertar, brilho, HDMI, microfone, P2, USB, webcam, bateria e autonomia.
- [ ] Acompanhar WindowServer e resets de GPU por várias horas.
- [ ] Incluir licenças, fontes correspondentes e créditos necessários ao distribuir binários.

Pode-se publicar o guia como **relato experimental** com essas lacunas explícitas. Não chamar a configuração de 100% funcional ou pronta para qualquer 3520.

## Divulgação na comunidade

Há uma minuta em [06-POST-COMUNIDADE.md](06-POST-COMUNIDADE.md). Substituir o marcador de URL pelo repositório publicado antes de postar. O texto separa o boot pelo SSD confirmado dos testes de velocidade/BLE feitos pelo pendrive.

## O que subir

Apenas o conteúdo da pasta `GitHub-Dell-Inspiron-3520`. Não iniciar o Git na pasta geral de trabalho, que contém backups pessoais e diagnósticos. A pasta irmã `EFI-Dell-3520-LOCAL` reúne binários apenas para revisão; não faz parte do pacote público preparado. Não redistribuir automaticamente binários Apple, Graphics, HAL e builds modificados sem a origem e os avisos apropriados.

No GitHub, um nome possível é `dell-inspiron-3520-i5-1235u-sequoia`. Descrição sugerida: “Configuração experimental do macOS Sequoia no Dell Inspiron 15 3520 i5-1235U: Iris Xe, CS8409, RTL8821CE e Bluetooth Realtek.”

Antes do upload:

```bash
python3 scripts/auditar_efi.py EFI
```

Esse comando audita o modelo público; avisa que ele está incompleto para boot. O `.gitignore` evita inclusão acidental de `config.plist`, pacotes, logs e diretório build, mas não substitui a revisão de arquivos e imagens. A auditoria procura campos de identidade e referências de arquivos; não é um detector universal de segredos.

Não há uma licença única declarada para todos os materiais. O patch Bluetooth deriva da base GPL-2.0-or-later e acompanha o aviso original em `third-party/`. O conteúdo específico desta documentação, as ACPI recuperadas e os demais componentes devem ter sua autoria/licença definida antes de uma distribuição com licença única. O guia não transfere direitos sobre componentes de terceiros.
