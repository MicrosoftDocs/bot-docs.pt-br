---
title: Configurar a desobstrução de fala – Serviço de Bot
description: Saiba como configurar a desobstrução de fala para o serviço de bot usando o portal do Azure.
keywords: desobstrução de fala, reconhecimento de fala, LUIS
author: v-royhar
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 6801af211b22e2c603455b7ad4cf496cacc89c70
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456360"
---
# <a name="configure-speech-priming"></a>Configurar a desobstrução de fala

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

A desobstrução de fala melhora o reconhecimento de palavras faladas e frases que são comumente usadas em um bot.

Para bots habilitados para fala que usam os canais [Web Chat](bot-service-channel-connect-webchat.md) e [Cortana](~/bot-service-channel-connect-cortana.md) , a desativação de fala usa exemplos especificados em aplicativos reconhecimento vocal ([Luis](https://www.luis.ai/)) para melhorar a precisão do reconhecimento de fala para palavras importantes.

O bot já pode estar integrado a um aplicativo LUIS, ou você pode optar por criar um aplicativo LUIS para associá-lo ao bot para a desobstrução de fala. O aplicativo LUIS contém exemplos do que você espera que os usuários digam ao bot. As palavras importantes que você deseja que o bot reconheça devem ser rotuladas como entidades. Por exemplo, em um bot de xadrez, você deseja certificar-se de que quando o usuário disser "mover Knight", ele não será interpretado como "mover à noite". O aplicativo LUIS deve incluir exemplos em que "cavalo" é rotulado como uma entidade.

> [!IMPORTANT]
> - Para usar a desobstrução de fala com o canal de Webchat, use o serviço de Fala do Bing. Confira [Habilitar a fala no canal de Webchat](bot-service-channel-connect-webchat-speech.md) para obter uma explicação de como usar o serviço de Fala do Bing.
> - A desobstrução de fala só se aplica aos bots configurados para o canal da Cortana ou de Webchat. Não há suporte para a desobstrução de aplicativos LUIS que não sejam das regiões dos EUA incluindo: eu.luis.ai e au.luis.ai

## <a name="change-the-list-of-luis-apps-your-bot-uses"></a>Alterar a lista de aplicativos LUIS usados pelo bot

Para alterar a lista de aplicativos LUIS usados pela Fala do Bing com o bot, faça o seguinte:

1. Clique em **Desobstrução de fala** na folha do serviço de bot. A lista de aplicativos LUIS disponíveis para você será exibida.
1. Selecione os aplicativos LUIS que deseja que sejam usados pela Fala do Bing.
    1. Para selecionar um aplicativo LUIS na lista, passe o mouse sobre o modelo LUIS até que uma caixa de seleção seja exibida e marque a caixa de seleção.
    1. Para selecionar um aplicativo LUIS que não está na lista, role a tela para baixo e insira o GUID da ID do Aplicativo LUIS na caixa de texto.
1. Clique em **Salvar** para salvar a lista de aplicativos LUIS associados à Fala do Bing para o bot.

![O painel de desobstrução de fala](~/media/bot-service-manage-speech-priming/speech-priming.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Habilitar a fala no canal de chat da Web](~/bot-service-channel-connect-webchat-speech.md)
- [Suporte a fala no bot Framework – chat da Web para direcionar para a Cortana](https://blog.botframework.com/2017/06/26/Speech-To-Text/)
- [Serviço Inteligente de Reconhecimento Vocal](https://www.luis.ai)
