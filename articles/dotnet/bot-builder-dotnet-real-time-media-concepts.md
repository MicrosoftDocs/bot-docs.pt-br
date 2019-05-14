---
redirect_url: https://aka.ms/realTimeMediaCalling-repo
ms.openlocfilehash: f6568ed5d4f98addb19f452142a0a5d6d37e00c8
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65032970"
---
<a name="--"></a><!--
---
Título: Chamada de mídia em tempo real com o Skype | descrição do Microsoft Docs: Entenda os principais conceitos na criação de um bot capaz de conduzir chamadas de áudio e vídeo em tempo real com o Skype, usando o SDK do Bot Framework para .NET.
author: ssulzer ms.author: ssulzer manager: kamrani ms.topic: article ms.service: bot-service ms.subservice: sdk ms.date: 12/13/2017 monikerRange: 'azure-bot-service-3.0'
---

# <a name="real-time-media-calling-with-skype"></a>Chamada de mídia em tempo real com o Skype

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

A Plataforma de Mídia em Tempo Real para Bots adiciona uma nova dimensão para como os bots podem interagir com os usuários, permitindo as modalidades de voz, vídeo e compartilhamento de tela em tempo real. Isso fornece a capacidade de criar bots de entretenimento, educacionais e de assistência cativantes e interativos. Os usuários se comunicam com os bots de mídia em tempo real usando o Skype.

Isso é um recurso avançado que permite ao bot enviar e receber conteúdo de voz e vídeo *quadro a quadro*. O bot tem acesso “bruto” às modalidades de voz, vídeo e compartilhamento de tela em tempo real. Por exemplo, conforme o usuário fala, o bot receberá 50 quadros de áudio por segundo, com cada quadro contendo 20 milissegundos (ms) de áudio. O bot pode executar o reconhecimento de fala em tempo real conforme os quadros de áudio são recebidos, em vez de ter de esperar uma gravação depois que o usuário parou de falar. O bot também pode enviar vídeo de alta resolução e definição para o usuário a 30 quadros por segundo, junto com o áudio.

A Plataforma de Mídia em Tempo Real para Bots funciona com a Nuvem de Chamada do Skype para cuidar da configuração da chamada e do estabelecimento da sessão de mídia, permitindo ao bot participar de uma conversa de voz e vídeo (opcionalmente) com um chamador do Skype. A plataforma fornece uma API simples do tipo “soquete” para o bot enviar e receber mídia e lida com a codificação e decodificação de mídia em tempo real, usando codecs como SILK para áudio e H.264 para vídeo. Um bot de mídia em tempo real também pode participar de chamadas de vídeo de grupo do Skype com vários participantes.

Este artigo apresenta os conceitos principais relacionados à criação de um bot que pode realizar chamadas de áudio e vídeo em tempo real com o Skype e fornece links para recursos relevantes para desenvolvedores.

## <a name="media-session"></a>Sessão de mídia
Quando um bot de chamada em tempo real responde a uma chamada do Skype, ele decide se deve dar suporte a ambas as modalidades de áudio e vídeos ou áudio apenas. Para cada modalidade com suporte, o bot pode enviar e receber mídia, somente receber ou somente enviar. Por exemplo, um bot poderá tanto enviar como receber áudio, mas enviar apenas vídeo (já que não deseja receber o vídeo do chamador do Skype). O conjunto de modalidades de áudio e vídeos estabelecido entre o chamador do Skype e o bot é chamado de **sessão de mídia**.

Há duas modalidades de vídeos com suporte: “vídeo principal” e “compartilhamento de tela”. O vídeo principal transmite o vídeo que o bot gera, ou reproduz para o chamador e transmite o vídeo do chamador (normalmente de webcam do usuário) para o bot. A modalidade de compartilhamento de tela permite ao chamador compartilhar a tela dele ou dela (por exemplo, um vídeo) com o bot. O bot não pode enviar o vídeo de compartilhamento de tela ao usuário.

Quando incluído em uma **chamada de vídeo de grupo** com vários participantes do Skype, o bot de mídia em tempo real pode oferecer suporte ao recebimento de vários fluxos de vídeos principais simultaneamente. Isso permite que o bot “veja” mais de um participante na chamada de vídeo do grupo.

## <a name="frames-and-frame-rate"></a>Quadros e taxa de quadros
Um bot de mídia em tempo real interage diretamente com as modalidades de áudio e vídeo de uma chamada do Skype. Isso significa que o bot está enviando e/ou recebendo mídia como uma sequência de **quadros**, onde cada quadro representa uma unidade de conteúdo. Um segundo de áudio pode ser transmitido como uma sequência de quadros de 50, com cada quadro contendo 20 milissegundos (ms) (1/50 de segundo) de conteúdo. Um segundo de vídeo pode ser segmentado como uma sequência de 30 imagens fixas, cada um destinado a ser exibido por apenas 33 ms (1/30 de segundo) antes que o próximo quadro de vídeo seja exibido. O número de quadros transmitidos ou renderizados por segundo é chamado de **taxa de quadros**. “30fps” indica 30 quadros por segundo.

## <a name="audio-format"></a>Formato de áudio
Cada segundo de áudio é representado como 16.000 **amostras**, com cada exemplo armazenando 16 bits de dados. Um quadro de áudio de 20 ms contém 320 amostras (640 bytes de dados).

## <a name="video-format"></a>Formato de vídeo
Há vários formatos com suporte para vídeo. Duas propriedades principais de um formato de vídeo são seu **tamanho de quadro** e **formato de cor**. Os tamanhos de quadro com suporte incluem 640 x 360 (“360p”), 1280 x 720 (“720p”) e 1920 x 1080 (“1080p”). Os formatos de cor com suporte incluem NV12 (12 bits por pixel) e RGB24 (24 bits por pixel).

Um quadro de vídeo de “720p” contém 921.600 pixels (1280 vezes 720). No formato de cor RGB24, cada pixel é representado como 3 bytes (24 bits) compostos de um byte cada dos componentes de cor vermelho, verde e azul. Portanto, um único quadro de vídeo de 720p RGB24 requer 2.764.800 bytes de dados (921.600 pixels vezes 3 bytes/pixel). Uma taxa de quadros de 30fps, enviando quadros de vídeo de 720p RGB24 significa aproximadamente 80 MB/s de conteúdo (que é substancialmente compactado pelo codec de vídeo H.264 antes da transmissão de rede) de processamento.

## <a name="active-and-dominant-speakers"></a>Alto-falantes ativos e dominantes
Quando associado a uma chamada de vídeo de grupo do Skype consistindo de vários participantes, o bot pode identificar quais participantes estão falando no momento. **Alto-falantes ativos** identificam quais participantes estão sendo ouvidos em cada quadro de áudio recebido. **Alto-falantes dominantes** identificam quais participantes estão mais ativos no momento (ou “dominante”) na conversa de grupo, mesmo que sua voz não possa ser ouvida em cada quadro de áudio. O conjunto de alto-falantes dominantes pode alterar conforme os diferentes participantes começam e param de falar.

## <a name="video-subscription"></a>Assinatura de vídeo
Em uma chamada com um único chamador do Skype, o bot receberá automaticamente o vídeo do chamador (se o bot estiver habilitado para receber o vídeo principal). Em uma chamada de vídeo de grupo com vários participantes do Skype, o bot deve indicar à plataforma de mídia em tempo real quais participantes ele deseja ver. Uma assinatura de vídeo é uma solicitação pelo bot para receber o vídeo de um participante. Conforme os participantes em uma chamada de vídeo de grupo de mantêm uma conversa, um bot pode modificar suas assinaturas de vídeos desejadas com base nas atualizações do conjunto de alto-falante dominante.

## <a name="developer-resources"></a>Recursos para desenvolvedores 

### <a name="sdks"></a>SDKs

Para desenvolver um bot de mídia em tempo real, você deve instalar esses pacotes do NuGet no projeto do Visual Studio:

- [SDK do Bot Framework para .NET](bot-builder-dotnet-overview.md)
- [Chamada de Mídia em Tempo Real do Bot Builder para .NET](https://www.nuget.org/packages?q=Bot.Builder.RealTimeMediaCalling)
- [Biblioteca Microsoft.Skype.Bots.Media .NET](https://www.nuget.org/packages?q=Microsoft.Skype.Bots.Media)

### <a name="code-samples"></a>Exemplos de código

O repositório [BotBuilder RealTimeMediaCalling](https://github.com/Microsoft/BotBuilder-RealTimeMediaCalling) do GitHub contém exemplos que mostram como criar bots de mídia em tempo real para o Skype.
-->