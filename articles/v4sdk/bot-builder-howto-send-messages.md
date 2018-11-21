---
title: Enviar e receber mensagens de texto | Microsoft Docs
description: Saiba mais sobre como enviar e receber mensagens de texto dentro do SDK do Bot Builder.
keywords: enviar mensagens, atividades de mensagem, mensagem de texto simples, mensagem, mensagem de texto, receber mensagens
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a01a64e032acfde2b3711e3efbb3886439888c42
ms.sourcegitcommit: 5c40e2e21adb3a779022d45704c29cf11ed7f4a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51506191"
---
# <a name="send-and-receive-text-message"></a>Enviar e receber mensagens de texto 

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A principal maneira que o bot se comunicará com usuários e, da mesma forma, receberá comunicação, é por meio de atividades de **mensagem**. Algumas mensagens simplesmente podem conter texto sem formatação, enquanto outras podem ter conteúdo mais elaborado, como cartões ou anexos. O manipulador do turno do bot recebe mensagens do usuário e, a partir daí, é possível enviar respostas ao usuário. O objeto de contexto do turno fornece métodos para enviar mensagens de volta ao usuário. Este artigo descreve como enviar mensagens de texto simples.

## <a name="send-a-text-message"></a>Enviar uma mensagem de texto

Para enviar uma mensagem de texto simples, especifique a cadeia de caracteres que você deseja enviar como a atividade:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No método `OnTurnAsync` do bot, use o método `SendActivityAsync` do objeto de contexto de turno para enviar uma única resposta de mensagem. Também é possível usar o método `SendActivitiesAsync` do objeto para enviar várias respostas ao mesmo tempo.

```cs
await turnContext.SendActivityAsync($"Welcome!");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No manipulador `onTurn` do bot, use o método `sendActivity` do objeto de contexto de turno para enviar uma única resposta de mensagem. Também é possível usar o método `sendActivities` do objeto para enviar várias respostas ao mesmo tempo.

```javascript
await context.sendActivity("Welcome!");
```
---
## <a name="receive-a-text-message"></a>Receber uma mensagem de texto

Para receber uma mensagem de texto simples, use a propriedade de *texto* do objeto de *atividade*. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No método `OnTurnAsync` do bot, use o código a seguir para receber uma mensagem. 

```cs
var responseMessage = turnContext.Activity.Text;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No método `OnTurnAsync` do bot, use o código a seguir para receber uma mensagem. 
```javascript
let text = turnContext.activity.text;
```
---


## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre o processamento de atividade, confira [processamento de atividade](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack). Para enviar o conteúdo mais avançado, saiba como adicionar anexos de [mídia avançada](bot-builder-howto-add-media-attachments.md).
