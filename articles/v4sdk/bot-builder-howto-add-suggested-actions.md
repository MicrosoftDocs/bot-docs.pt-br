---
title: Adicionar ações sugeridas a mensagens | Microsoft Docs
description: Saiba como enviar ações sugeridas a mensagens usando o SDK do Bot Builder para JavaScript.
keywords: ações sugeridas, botões, entrada extra
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 802d245112baa6d5fb10f4e992d9f6722b70a224
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297332"
---
# <a name="add-suggested-actions-to-messages"></a>Adicionar ações sugeridas a mensagens

[!include[Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)] 

## <a name="send-suggested-actions"></a>Enviar ações sugeridas

Você pode criar uma lista de ações sugeridas (também conhecidas como "respostas rápidas"), que será mostrada ao usuário para uma única rodada de conversa:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and add suggested actions.
var activity = MessageFactory.SuggestedActions(
    new CardAction[]
    {
        new CardAction(title: "red", type: ActionTypes.ImBack, value: "red"),
        new CardAction( title: "green", type: ActionTypes.ImBack, value: "green"),
        new CardAction(title: "blue", type: ActionTypes.ImBack, value: "blue")
    }, text: "Choose a color");

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Require MessageFactory from botbuilder.
const {MessageFactory} = require('botbuilder');

//  Initialize the message object.
const basicMessage = MessageFactory.suggestedActions(['red', 'green', 'blue'], 'Choose a color');

await context.sendActivity(basicMessage);
```

---

## <a name="additional-resources"></a>Recursos adicionais

Para visualizar os recursos, você pode usar o [Inspetor de Canal](../bot-service-channel-inspector.md)
