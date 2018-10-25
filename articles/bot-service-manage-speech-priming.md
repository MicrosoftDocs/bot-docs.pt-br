---
title: Configurar a desobstrução de fala | Microsoft Docs
description: Saiba como configurar a desobstrução de fala para o serviço de bot usando o portal do Azure.
keywords: desobstrução de fala, reconhecimento de fala, LUIS
author: v-royhar
ms.author: v-royhar
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 12/13/2017
ms.openlocfilehash: 5cb47be530f9f82d83272684e6405730c72f3cb7
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997143"
---
# <a name="configure-speech-priming"></a>Configurar a desobstrução de fala

A desobstrução de fala melhora o reconhecimento de palavras e frases faladas que costumam ser usadas no bot. Para bots habilitados para fala que usam os canais de Webchat e da Cortana, a desobstrução de fala usa exemplos especificados em aplicativos LUIS (Reconhecimento vocal) para melhorar a precisão do reconhecimento de fala para palavras importantes.

O bot já pode estar integrado a um aplicativo LUIS, ou você pode optar por criar um aplicativo LUIS para associá-lo ao bot para a desobstrução de fala. O aplicativo LUIS contém exemplos do que você espera que os usuários digam ao bot. As palavras importantes que você deseja que o bot reconheça devem ser rotuladas como entidades. Por exemplo, em um bot de xadrez, você deseja garantir que quando o usuário disser "Mover cavalo", isso não seja interpretado como "Mover intervalo". O aplicativo LUIS deve incluir exemplos em que "cavalo" é rotulado como uma entidade.

> [!NOTE]
> Para usar a desobstrução de fala com o canal de Webchat, use o serviço de Fala do Bing. Confira [Habilitar a fala no canal de Webchat](~/bot-service-channel-connect-webchat-speech.md) para obter uma explicação de como usar o serviço de Fala do Bing.

> [!IMPORTANT]
> A desobstrução de fala só se aplica aos bots configurados para o canal da Cortana ou de Webchat.

> [!IMPORTANT]
> Não há suporte para a desobstrução de aplicativos LUIS que não sejam das regiões dos EUA incluindo: eu.luis.ai e au.luis.ai

## <a name="change-the-list-of-luis-apps-your-bot-uses"></a>Alterar a lista de aplicativos LUIS usados pelo bot

Para alterar a lista de aplicativos LUIS usados pela Fala do Bing com o bot, faça o seguinte:

1. Clique em **Desobstrução de fala** na folha do serviço de bot. A lista de aplicativos LUIS disponíveis para você será exibida.
2. Selecione os aplicativos LUIS que deseja que sejam usados pela Fala do Bing.
 
    a. Para selecionar um aplicativo LUIS na lista, focalize o modelo LUIS até que uma caixa de seleção seja exibida e, em seguida, marque a caixa de seleção.
     
    b. Para selecionar um aplicativo LUIS que não está na lista, role a tela para baixo e insira o GUID da ID do Aplicativo LUIS na caixa de texto.
     
3. Clique em **Salvar** para salvar a lista de aplicativos LUIS associados à Fala do Bing para o bot.

![O painel de desobstrução de fala](~/media/bot-service-manage-speech-priming/speech-priming.png)

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre como habilitar a fala no Webchat, confira [Habilitar a fala no canal de Webchat](~/bot-service-channel-connect-webchat-speech.md).
- Para saber mais sobre a desobstrução de fala, confira [Suporte de fala no Bot Framework – Webchat, Linha Direta e Cortana](https://blog.botframework.com/2017/06/26/Speech-To-Text/).
- Para saber mais sobre os aplicativos LUIS, confira [Serviço Inteligente de Reconhecimento Vocal](https://www.luis.ai).
