# Comece aqui — minha EFI V12 para o Dell 3520

Separei a EFI completa que usei no meu Dell Inspiron 15 3520 com i5-1235U, 16 GB de RAM e SSD de 500 GB. Preparei este passo a passo e o arquivo `PARA-COLAR-NO-CHATGPT.txt` para você conseguir continuar com ajuda do seu ChatGPT. Os componentes, versões e limites estão escritos no pacote para ele ter contexto concreto.

**Meu resultado foi no macOS Sequoia 15.8.3 (24H241). Não testei este conjunto no macOS 26.** Retirei meu serial, MLB, UUID e ROM: você precisa preencher os seus antes de testar. A cópia sem identidade passou na validação de estrutura; isso não substitui um teste no seu notebook.

## 1. Baixe o pacote certo

Na [release da minha EFI V12](https://github.com/pedrovrs301-gif/dell-inspiron-3520-i5-1235u-sequoia/releases/tag/v12-dell-sequoia-2026.11.18), abra **Assets** e baixe **Dell-Inspiron-3520-i5-1235U-EFI-V12-Sem-SMBIOS-Pessoal.zip**. Não escolha os ZIPs automáticos chamados “Source code” nem o antigo `Dell-Inspiron-3520-Guia-GitHub.zip`: aquele pacote antigo era documentação/modelo, sem todos os binários.

Extraia o ZIP. Dentro da pasta `Compartilhar-Dell-3520-V12`, você encontrará:

| Arquivo/pasta | Para que serve |
|---|---|
| `EFI/` | OpenCore, config.plist, ACPI, drivers e kexts do meu conjunto V12 |
| `macOS/Audio/CS8409Speakers.driver` | Complemento dos alto-falantes, instalado dentro do macOS |
| `LEIA-ME.pt-BR.md` | Detalhes do vídeo, áudio, rede, Bluetooth e touchpad |
| `COMPONENTS.json` | Versões, identificação do hardware e hashes de referência |
| `PARA-COLAR-NO-CHATGPT.txt` | Texto pronto para começar o atendimento com seu ChatGPT |
| `Sources/`, `CREDITS.md`, `LICENSES/` | Alterações locais, origens e créditos |
| `SHA256SUMS.txt` | Verificação de integridade dos arquivos |

## 2. Comece pelo ChatGPT, antes de mudar o notebook

Abra `PARA-COLAR-NO-CHATGPT.txt`, copie o texto inteiro e cole no seu chat. Anexe este guia, `LEIA-ME.pt-BR.md` e `COMPONENTS.json`; se seu chat aceitar arquivos ZIP, pode anexar o pacote completo. Se ele não conseguir abrir, envie os arquivos separadamente. Ele precisa ler o material, não apenas o link do GitHub.

Conte se você está no Windows ou no macOS, se o Sequoia já está instalado e em que ponto trava. Não é necessário repetir uma instalação limpa se o problema está só no boot ou nos drivers. O ChatGPT só terá acesso ao que você compartilhar; anexar o pacote não dá acesso ao seu computador.

Peça uma etapa por vez e envie o resultado antes de avançar. Assim fica mais fácil localizar uma diferença sem alterar várias coisas ao mesmo tempo.

## 3. Confira o hardware e prepare uma volta segura

No meu Dell usei Iris Xe `8086:46A8`/80 EU, painel NCP005F Full HD a 120 Hz, áudio CS8409, Wi-Fi RTL8821CE e Bluetooth USB `0bda:c829`. Compare com o seu. O mesmo nome “3520” pode vir com painel, rede ou revisão diferentes; o mapa USB também precisa ser conferido.

Faça backup da sua EFI atual fora do disco que será alterado. Tenha um pendrive de recuperação com a configuração que já inicia seu sistema. Se houver BitLocker no Windows, tenha sua chave de recuperação antes de mudar o boot/firmware. Não publique essa chave.

Não registrei a versão e todos os ajustes reais da minha BIOS. Por isso, não inventei uma receita de opções da BIOS. Registre as opções atuais e confirme o que seu equipamento apresenta antes de mudar alguma coisa.

## 4. Preencha sua própria identidade na cópia extraída

Use um editor de plist, como [ProperTree](https://github.com/corpnewt/ProperTree), para abrir `EFI/OC/config.plist`. O modelo usado no meu conjunto é `MacBookPro16,2`. Com [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), gere uma identidade própria para esse modelo e preencha:

| Caminho em `PlatformInfo > Generic` | O que preencher |
|---|---|
| `SystemSerialNumber` | Seu serial gerado; no pacote está vazio |
| `MLB` | Seu MLB gerado; no pacote está vazio |
| `SystemUUID` | Seu UUID gerado; no pacote está zerado |
| `ROM` | Seis bytes da identidade da sua máquina, no tipo **Data**, conforme a orientação do OpenCore; no pacote são seis zeros |

Não use meu serial, o serial Dell/Service Tag nem valores de uma captura de tela. Não mande a versão personalizada para um comentário público. Se precisar compartilhar o config, faça outra cópia com esses quatro campos removidos/zerados.

Salve o arquivo preservando os tipos de cada campo. Peça ao seu ChatGPT para explicar a edição antes de aplicar. Não faça um “Clean Snapshot” automático nem atualize todos os kexts: isso pode mudar a ordem e habilitar componentes que eu deixei desativados.

## 5. Teste a EFI pelo pendrive

Se já tem um pendrive OpenCore que funciona, faça backup dele antes de usar uma cópia para teste. A raiz da partição EFI do pendrive deve ficar assim:

```text
EFI/
  BOOT/
    BOOTx64.efi
  OC/
    OpenCore.efi
    config.plist
    ACPI/
    Drivers/
    Kexts/
```

Evite criar `EFI/EFI/BOOT`. Confirme qual disco é o pendrive por capacidade e partições; números como `disk0` mudam. Não execute um comando de formatação sugerido sem conferir o destino.

Valide o `config.plist` com o `ocvalidate` correspondente à versão do OpenCore. Meu arquivo passou na ferramenta recuperada para OpenCore 1.0.8; a revisão exata do binário salvo ainda precisa de confirmação independente. Validação de plist não garante boot.

No Dell, abra o menu de boot **F12**, selecione a entrada **UEFI do pendrive** e depois o seu Sequoia no menu OpenCore. No meu config, o menu aguarda a escolha. Não substitua ainda a EFI interna. Não faça reset de NVRAM como tentativa automática, principalmente se usa mais de um sistema.

Se o macOS ainda não está instalado, combine com seu ChatGPT a preparação do instalador oficial usando o [guia Dortania](https://dortania.github.io/OpenCore-Install-Guide/), sem apagar um disco por suposição. Este ZIP não contém instalador do macOS e este guia não documenta uma instalação limpa completa testada desde o zero.

## 6. Complete o vídeo dentro do macOS

**Copiar só a EFI não instala a aceleração.** Meu conjunto usa WhateverXe **V12-Dell/ABI1**, que exibe a versão 0.8.0, mais o **WhateverXe Graphics 0.4.4** instalado no macOS. O kext local já está nesta EFI; o instalador Graphics é separado.

Baixe `WhateverXe-Graphics-0.4.4.pkg` na [release oficial v0.4.4](https://github.com/b00t0x/WhateverXe-Graphics/releases/tag/v0.4.4). Antes de instalar, leia com seu ChatGPT as instruções dessa versão e confirme que seu sistema é Sequoia e que existe forma de recuperação. O hash e o recibo do pacote estão no `LEIA-ME.pt-BR.md`. Esse pacote faz alterações no macOS; voltar apenas a EFI pode não desfazê-las.

Preserve o WhateverXe V12 incluído, Lilu 1.7.2, AMFIPass 1.4.1 e os boot-args desse conjunto. Não troque pelo 0.8.3 nem misture AMFIAnchor/instruções novas com meu conjunto antigo. Os ajustes de segurança e diagnóstico preservados no config pertencem ao meu teste experimental e precisam ser compreendidos antes do uso.

Depois da instalação correspondente e do reinício, confira localmente:

```bash
sw_vers
pkgutil --pkg-info org.b00t0x.WhateverXe.Sequoia
system_profiler SPDisplaysDataType
```

No meu Dell registrei Metal 2, 1536 MB e 1920 × 1080 a 120 Hz. Compare também a fluidez e a estabilidade. Apenas aparecer “Iris Xe” ou “1536 MB” não prova sozinho a aceleração completa; eu não validei DRM nem todos os codecs.

## 7. Complete o áudio

O `CS8409Audio.kext` já está na EFI. O complemento `CS8409Speakers.driver` precisa ficar no macOS. Faça backup de um plug-in de mesmo nome caso já exista.

No Terminal, digite `cd ` (com espaço), arraste a pasta extraída `Compartilhar-Dell-3520-V12` para a janela e pressione Enter. Confira com `pwd` e `ls` que aparecem as pastas `EFI` e `macOS`. Então, se o seu áudio for CS8409 e o backup estiver feito:

```bash
sudo ditto macOS/Audio/CS8409Speakers.driver /Library/Audio/Plug-Ins/HAL/CS8409Speakers.driver
sudo chown -R root:wheel /Library/Audio/Plug-Ins/HAL/CS8409Speakers.driver
```

O `sudo` pede a senha local e não mostra os caracteres digitados. Não envie a senha para ninguém. Reinicie e selecione a saída de som nos Ajustes. Confirmei meus alto-falantes internos; microfone e P2 continuam sem validação completa.

## 8. Confira rede, Bluetooth e touchpad

- **Wi-Fi:** mantenha RTL88WiFi com IOSkywalkFamily/IO80211FamilyLegacy do pacote para Darwin 24. Não ative AirportRTW ou Feixiao junto. Teste primeiro em 5 GHz e confira se sua placa é RTL8821CE.
- **Bluetooth:** mantive RealtekBluetoothFirmware modificado, BlueToolFixup e os ajustes de NVRAM do config. Confirmei descoberta BLE junto com transferência em 5 GHz. Não prometi áudio de fone, Bluetooth Classic, AirDrop ou Handoff.
- **Touchpad/teclado:** mantive VoodooI2C/HID, VoodooPS2Keyboard e `-vi2c-force-polling`. Se falhar, confira identificação do touchpad e carregamento antes de trocar kexts ao acaso. Um mouse/teclado USB pode ajudar no diagnóstico.

## 9. Só depois pense no SSD

Teste alguns reinícios pelo pendrive e seus recursos básicos. Quando tudo que você precisa estiver validado, faça novo backup da EFI interna e transfira os arquivos necessários do OpenCore para a partição correta, preservando bootloaders Windows e arquivos OEM. Não formate o SSD para copiar uma EFI.

Se falhar, volte pelo F12 ao pendrive/EFI anterior. Anote a última mudança. Se alterou Graphics ou HAL, a recuperação pode exigir restaurar também os componentes do macOS. Deixe HDMI, suspensão, webcam, autonomia, microfone e todas as portas como testes pendentes até você confirmar no seu equipamento.

## Se travar: mande um relato que ajude

Copie este formulário e preencha, sem dados pessoais:

```text
Meu modelo/CPU/painel/Wi-Fi/Bluetooth:
Versão do macOS e build:
Estado: instalador / já instalado / boot pelo USB / boot pelo SSD:
Etapa deste guia em que parei:
Última mudança que fiz:
O que eu esperava e o que aconteceu:
Erro exato ou foto do ponto onde para:
Pacote Graphics 0.4.4 instalado? Qual recibo apareceu?
Minha EFI anterior ainda inicia?
```

Retire serial, MLB, UUID, ROM/MAC, Service Tag, SSID, senhas e nomes pessoais das imagens/saídas compartilhadas. Mande só o trecho necessário. Com esse contexto, seu ChatGPT consegue comparar a sua situação com o conjunto que eu realmente usei, sem precisar adivinhar os detalhes.
