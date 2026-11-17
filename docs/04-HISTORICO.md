# Histórico das decisões e testes

Reconstituição a partir das conversas e arquivos salvos, sem publicar logs brutos ou nomes de dispositivos/redes. As confirmações posteriores do proprietário foram incorporadas; os testes sem confirmação permanecem pendentes.

## Vídeo e boot

A configuração inicial teve Iris Xe sem aceleração adequada, com relato de 7 MB. O conjunto chamado V11 posteriormente apresentou consumo de aproximadamente 2,7 GB no WindowServer e relatos de resets/engasgos. O teste V12-Dell combinou WhateverXe 0.8.0 modificado, Graphics 0.4.4 e ABI1. Foram registrados Metal 2, 1536 MB, tela Full HD a 120 Hz, vídeo fluido e WindowServer na faixa inicial de 132–155 MB.

A migração ao NVMe preservou config e áudio e manteve backup da V11. Houve confirmação posterior de boot pelo SSD. Isso precede a última troca do Wi-Fi. A redução inicial do consumo de memória não prova estabilidade por horas ou correção definitiva de GPU resets.

## Áudio

Os alto-falantes foram ouvidos e confirmados durante a migração V12. A solução usa CS8409Audio 1.1.0 e CS8409Speakers.driver 1.1.0 no HAL. Não há confirmação recuperada de microfone, jack P2 ou áudio após suspensão.

## Bluetooth Realtek

O dispositivo USB `0bda:c829` aparecia no barramento sem se tornar um controlador funcional no macOS. Os testes progrediram por match USB, diagnóstico de carga de firmware, HCI Reset, recuperação de firmware que persistia entre reinícios, seleção do controlador e correção das variáveis NVRAM.

Um erro objetivo no teste 7 foi `bluetoothInternalControllerInfo` com 13 bytes: o serviço esperava 14. A correção passou a usar 14 bytes zero. O ajuste `-btlfxboardid` habilitou o caminho de patch pertinente no BlueToolFixup. A cópia final usa o binário modificado do teste 5, preservado na migração.

A descoberta BLE de um telefone foi confirmada com Wi-Fi 5 GHz ligado e durante transferência. Isso demonstra descoberta simultânea nesse cenário. Fone, pareamento duradouro e perfis de Bluetooth Classic não foram validados.

## Wi-Fi: alternativas comparadas

| Etapa | Observação | Conclusão limitada |
|---|---|---|
| AirportRTW 2.3.1 | Lentidão, grandes oscilações de latência e downloads na faixa de 11–18 Mb/s em diferentes rodadas | O link nominal não descrevia a experiência real |
| Bluetooth ligado/desligado | Resultados variáveis, inclusive melhora com Bluetooth ligado em uma rodada | Não houve relação consistente que atribuísse a lentidão ao estado do Bluetooth |
| Feixiao 1.0.1 / Starskiff 1.0.0 | Alternativa testada no pendrive; 2,4 GHz chegou a ter latência baixa, 5 GHz permaneceu problemático | Não resolveu de forma convincente; foi abandonada na configuração final |
| 2,4 GHz contra 5 GHz | Em uma comparação IPv4, cerca de 4,9 ms contra 266 ms até o roteador | A banda/canal e a interação com o driver importavam; resultado dependente da rodada |
| AirportRTW 2.3.3 e builds de diagnóstico | Investigação de agregação, taxa de transmissão e atualizações periódicas | Algumas hipóteses puderam ser examinadas, mas não consolidaram a solução final |
| RTL88WiFi 1.0.0 | 126/107 Mb/s em testes curtos; Apple mediu 104/223 Mb/s; outra rodada simultânea ao BLE registrou 245 Mb/s de download | Ganho real no cenário testado, com estabilidade prolongada ainda pendente |

**Falha que precisa constar:** uma transferência Cloudflare de 50 MB recebeu aproximadamente 8,16 MB em 25 s e expirou, com taxa efetiva de 2,61 Mb/s; um upload maior também falhou. Testes posteriores em outro serviço passaram. A causa não foi isolada, e o melhor pico não deve ser apresentado como velocidade sustentada garantida.

Os números são Mb/s (megabits por segundo), não MB/s. Não são médias de um benchmark controlado nem garantias para outro roteador.

## Último estado recuperado — 16/11/2026

A configuração RTL88WiFi testada pelo pendrive foi copiada para a EFI do SSD com backup verificado. Entradas Kernel/Add, Kernel/Block e o patch obsoleto foram ajustados; os arquivos e variáveis do Bluetooth foram preservados. O registro termina pedindo desligar, retirar o pendrive, iniciar pelo SSD e repetir rede/BLE. Em 16/11/2026, o proprietário confirmou que o último boot pelo SSD deu certo. Essa confirmação encerra a pendência de inicialização; não substitui novas medições de rede e BLE após esse boot.
