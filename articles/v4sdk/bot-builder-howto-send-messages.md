---
title: Enviar mensagens com o SDK do Bot Builder | Microsoft Docs
description: Saiba mais sobre como enviar mensagens dentro do SDK do Bot Builder.
keywords: enviar mensagens, atividades de mensagem, mensagem de texto simples, fala, mensagem falada
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/04/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b854eeb4ecb45a875ab3be5d867333cd63babffd
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297073"
---
# <a name="sending-messages"></a>Enviar mensagens

A principal maneira que o bot se comunicará com usuários e, da mesma forma, receberá comunicação, é por meio de atividades de **mensagem**. Algumas mensagens simplesmente podem conter texto sem formatação, enquanto outras podem ter conteúdo mais elaborado, como cartões ou anexos. O manipulador do turno do bot recebe mensagens do usuário e, a partir daí, é possível enviar respostas ao usuário. O objeto de contexto do turno fornece métodos para enviar mensagens de volta ao usuário. Para obter mais informações sobre o processamento de atividade, consulte [Processamento de atividade](bot-builder-concept-activity-processing.md).

Este artigo descreve como enviar mensagens de texto simples e de fala. Para enviar o conteúdo mais avançado, saiba como [adicionar anexos de mídia avançada](bot-builder-howto-add-media-attachments.md). Para obter informações sobre como usar objetos de solicitação, saiba como [solicitar entrada dos usuários](bot-builder-prompts.md).

## <a name="send-a-simple-text-message"></a>Enviar uma mensagem de texto simples

Para enviar uma mensagem de texto simples, especifique a cadeia de caracteres que você deseja enviar como a atividade:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No método **OnTurn** do bot, use o método **SendActivity** do objeto de contexto de turno para enviar uma única resposta de mensagem. Também é possível usar o método **SendActivities** do objeto para enviar várias respostas ao mesmo tempo.

```cs
await context.SendActivity("Greetings from sample message.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No manipulador de turno do bot, use o método **sendActivity** do objeto de contexto de turno para enviar uma única resposta de mensagem. Também é possível usar o método **sendActivities** do objeto para enviar várias respostas ao mesmo tempo.

```javascript
await context.sendActivity("Greetings from sample message.");
```

---

## <a name="send-a-spoken-message"></a>Enviar uma mensagem falada

Determinados canais dão suporte à bots habilitados para fala, permitindo que eles falem com o usuário. Uma mensagem pode ter conteúdo escrito e falado.

> [!NOTE]
> Para esses canais que não dão suporte à fala, o conteúdo de fala é ignorado.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Use o parâmetro **speak** opcional para fornecer o texto a ser falado como parte da resposta.

```cs
await context.SendActivity(
    "This is the text to be displayed.",
    "This is the text to be spoken.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para adicionar fala, será necessário o `Microsoft.Bot.Builder.MessageFactory` para criar a mensagem. `MessageFactory` é usado mais com [mídia avançada](bot-builder-howto-add-media-attachments.md), onde é explicado um pouco mais, mas por enquanto será apenas exigido e usado aqui.

```javascript
// Require MessageFactory from botbuilder
const {MessageFactory} = require('botbuilder');

const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.');
await context.sendActivity(basicMessage);
```

---
