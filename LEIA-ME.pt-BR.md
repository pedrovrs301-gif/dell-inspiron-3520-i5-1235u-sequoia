# Minha EFI — Dell Inspiron 15 3520 / i5-1235U

Separei a configuração V12 que usei no meu Dell com macOS Sequoia 15.8.3 (24H241), 16 GB de RAM, SSD de 500 GB, Iris Xe 8086:46A8/80 EU, painel NCP005F Full HD a 120 Hz, Wi-Fi RTL8821CE e Bluetooth USB 0bda:c829.

Incluí a pasta EFI completa, o plug-in de áudio CS8409Speakers e os patches de fonte dos builds locais de vídeo e Bluetooth. Não incluí meus identificadores pessoais, logs, capturas, históricos de conversa, scripts de automação ou instalador do macOS. Esta é minha configuração V12; não misturei o teste mais recente da 0.8.3.

**Primeira vez aqui? Comece pelo [passo a passo em português](COMECE-AQUI.pt-BR.md) e use o [texto pronto para seu ChatGPT](PARA-COLAR-NO-CHATGPT.txt).**

## Antes de iniciar

1. Faça backup da sua EFI atual e mantenha um pendrive que já consiga iniciar seu sistema. Compare CPU, iGPU, painel, áudio, placa de rede e mapa USB: o nome 3520 sozinho não garante a mesma revisão de hardware.
2. No seu editor de plist, preencha `PlatformInfo > Generic > SystemSerialNumber`, `MLB`, `SystemUUID` e `ROM` com valores próprios. Eu deixei serial/MLB vazios e UUID/ROM zerados de propósito. O modelo `MacBookPro16,2` permanece porque faz parte da configuração. Não use a identidade vazia com serviços Apple. [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS).
3. Teste a pasta `EFI` pelo pendrive primeiro. Ela contém `BOOT` e `OC`. Preserve bootloaders do Windows e arquivos OEM; não formate seu SSD para copiá-la. A cópia anonimizada exige sua identidade e ainda não foi testada no seu computador.

## Vídeo: a EFI não instala o Graphics

Meu resultado de aceleração usou **WhateverXe V12-Dell/ABI1 (0.8.0)** na EFI, **WhateverXe Graphics 0.4.4** instalado no macOS, Lilu 1.7.2 e AMFIPass 1.4.1. Registrei Metal 2, 1536 MB e 1920 × 1080 a 120 Hz. O recibo do pacote é `org.b00t0x.WhateverXe.Sequoia`; no boot bem-sucedido, AppleIntelTGLCompat aparecia como 0.8.1.

O instalador Graphics não está dentro deste ZIP. Obtenha a versão exata **WhateverXe-Graphics-0.4.4.pkg** na [release oficial v0.4.4](https://github.com/b00t0x/WhateverXe-Graphics/releases/tag/v0.4.4) e siga as instruções do projeto para a instalação correspondente ao Sequoia. O SHA-256 do instalador que guardei é:

```text
f4c2d680a7c52c33a3f038ce6945da0aa2cc044e62b935a52ea046c7c473cfec
```

Não substitua o kext local por outro só porque tem o mesmo número 0.8.0. Meu executável V12 tem SHA-256:

```text
0a310c0838683b03810e04e00390efa27d0063977096becaeca1844735c6409f
```

Mantive os boot-args, a ordem dos kexts, `csr-active-config=03080000` e `SecureBootModel=Disabled` do meu conjunto testado. Eles pertencem a essa configuração experimental. As instruções atuais do upstream podem ter mudado; não misture novos componentes AMFI ou outros stacks gráficos com este conjunto sem um teste separado.

## Áudio: dois componentes

Usei `CS8409Audio.kext` 1.1.0 na EFI e `CS8409Speakers.driver` 1.1.0 no macOS. O segundo está em `macOS/Audio/` neste ZIP. Antes de instalar, faça backup de qualquer plug-in de áudio de mesmo nome que você já tenha. Para instalar essa cópia, a partir da pasta extraída:

```bash
sudo ditto macOS/Audio/CS8409Speakers.driver /Library/Audio/Plug-Ins/HAL/CS8409Speakers.driver
sudo chown -R root:wheel /Library/Audio/Plug-Ins/HAL/CS8409Speakers.driver
```

Depois de iniciar com o kext correspondente e reiniciar o macOS, confira a saída de som nos Ajustes. Confirmei som nos meus alto-falantes internos; ainda não confirmei microfone, P2 ou áudio após suspensão.

## Wi-Fi, Bluetooth e touchpad

- Usei RTL88WiFi 1.0.0 com IOSkywalkFamily e IO80211FamilyLegacy. A exclusão da Skywalk nativa e as três entradas de Wi-Fi estão limitadas a Darwin 24 no config. Não ative AirportRTW ou Feixiao junto.
- Meu Bluetooth usa RealtekBluetoothFirmware 1.0.2 modificado e BlueToolFixup 2.7.2, com `-btlfxboardid` e a variável de controlador com 14 bytes zero. Confirmei descoberta BLE junto com transferência Wi-Fi em 5 GHz; fone/áudio Bluetooth e coexistência em 2,4 GHz continuam sem validação completa.
- Meu touchpad usa VoodooI2C/VoodooI2CHID e o teclado usa VoodooPS2Keyboard. Mantive `-vi2c-force-polling`. O UTBMap corresponde ao meu equipamento; revise antes de usar em outra revisão.

## Limites dos meus testes

Confirmei o boot pelo SSD e a aceleração no meu Dell. Isso não garante que outro 3520 seja idêntico. Ainda faltam testes completos de suspensão, HDMI, microfone/P2, webcam, autonomia, todas as portas USB e estabilidade gráfica prolongada. Um teste longo de rede falhou, embora outros tenham mostrado melhora; não trato a velocidade como garantida.

O `SHA256SUMS.txt` serve apenas para conferir os arquivos deste pacote. Os hashes identificam arquivos, não conversas ou pessoas. O texto técnico e os créditos estão neste pacote; não há acesso remoto ou rotina de publicação embutida.
