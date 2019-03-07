---
title: Usar botão para a entrada | Microsoft Docs
description: Saiba como enviar ações sugeridas em mensagens usando o SDK do Bot Framework para JavaScript.
keywords: ações sugeridas, botões, entrada extra
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 927d206c44d5809611871cfec7369e03e07837aa
ms.sourcegitcommit: cf3786c6e092adec5409d852849927dc1428e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57224784"
---
# <a name="use-button-for-input"></a>Usar o botão para a entrada

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Você pode habilitar seu bot para apresentar botões que o usuário pode tocar para fornecer a entrada. Os botões melhoram a experiência do usuário permitindo que ele responda a uma pergunta ou faça uma seleção com o simples toque de um botão, em vez de digitar uma resposta com um teclado. Ao contrário de botões exibidos em cartões avançados (que permanecem visíveis e acessíveis ao usuário, mesmo após serem tocados), os botões que aparecem no painel de ações sugeridas desaparecerão após a seleção do usuário. Isso impede que o usuário toque em botões obsoletos dentro de uma conversa e simplifica o desenvolvimento de bot (uma vez que não será necessário levar em conta esse cenário). 

## <a name="suggest-action-using-button"></a>Sugerir ação usando o botão

As *ações sugeridas* permitem que seu bot apresente botões. Você pode criar uma lista de ações sugeridas (também conhecidas como "respostas rápidas"), que será mostrada ao usuário para uma única rodada de conversa: 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Você pode acessar o código-fonte usado aqui no [GitHub](https://aka.ms/SuggestedActionsCSharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;

var reply = turnContext.Activity.CreateReply("What is your favorite color?");

reply.SuggestedActions = new SuggestedActions()
{
    Actions = new List<CardAction>()
    {
        new CardAction() { Title = "Red", Type = ActionTypes.ImBack, Value = "Red" },
        new CardAction() { Title = "Yellow", Type = ActionTypes.ImBack, Value = "Yellow" },
        new CardAction() { Title = "Blue", Type = ActionTypes.ImBack, Value = "Blue" },
    },

};
await turnContext.SendActivityAsync(reply, cancellationToken: cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Você pode acessar o código-fonte usado aqui no [GitHub](https://aka.ms/SuggestActionsJS).

```javascript
const { ActivityTypes, MessageFactory, TurnContext } = require('botbuilder');

async sendSuggestedActions(turnContext) {
    var reply = MessageFactory.suggestedActions(['Red', 'Yellow', 'Blue'], 'What is the best color?');
    await turnContext.sendActivity(reply);
}
```

---

## <a name="additional-resources"></a>Recursos adicionais

Você pode acessar o código-fonte usado aqui no GitHub [[C#](https://aka.ms/SuggestedActionsCSharp) | [JS](https://aka.ms/SuggestActionsJS)].
