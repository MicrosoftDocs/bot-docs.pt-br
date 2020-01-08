---
title: Enviar e receber mensagens de texto | Microsoft Docs
description: Saiba mais sobre como enviar e receber mensagens de texto dentro do SDK do Bot Framework.
keywords: enviar mensagens, atividades de mensagem, mensagem de texto simples, mensagem, mensagem de texto, receber mensagens
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d8aa120e37d13cc5710915d2362fdeed5b9d10bf
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491489"
---
# <a name="send-and-receive-text-message"></a>Enviar e receber mensagens de texto

[!INCLUDE[applies-to](../includes/applies-to.md)]

A principal maneira que o bot se comunicará com usuários e, da mesma forma, receberá comunicação, é por meio de atividades de **mensagem**. Algumas mensagens simplesmente podem conter texto sem formatação, enquanto outras podem ter conteúdo mais elaborado, como cartões ou anexos. O manipulador do turno do bot recebe mensagens do usuário e, a partir daí, é possível enviar respostas ao usuário. O objeto de contexto do turno fornece métodos para enviar mensagens de volta ao usuário. Este artigo descreve como enviar mensagens de texto simples.

O markdown tem suporte na maioria dos campos de texto, mas o suporte pode variar por canal.

Para um bot em execução que envia e recebe mensagens, siga os guias de início rápido na parte superior do sumário ou confira o [artigo sobre o funcionamento de bots](bot-builder-basics.md#bot-structure), que também fornece links para exemplos simples disponíveis para execução por conta própria.

## <a name="send-a-text-message"></a>Enviar uma mensagem de texto

Para enviar uma mensagem de texto simples, especifique a cadeia de caracteres que você deseja enviar como a atividade:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nos manipuladores de atividade do bot, use o método `SendActivityAsync` de mudança de contexto do objeto para enviar uma resposta de mensagem individual. Também é possível usar o método `SendActivitiesAsync` do objeto para enviar várias respostas ao mesmo tempo.

```cs
await turnContext.SendActivityAsync($"Welcome!");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Nos manipuladores de atividade do bot, use o método `sendActivity` de mudança de contexto do objeto para enviar uma resposta de mensagem individual. Também é possível usar o método `sendActivities` do objeto para enviar várias respostas ao mesmo tempo.

```javascript
await context.sendActivity("Welcome!");
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Nos manipuladores de atividade do bot, use o método `send_activity` de mudança de contexto do objeto para enviar uma resposta de mensagem individual.

```python
await turn_context.send_activity("Welcome!")
```

---
## <a name="receive-a-text-message"></a>Receber uma mensagem de texto

Para receber uma mensagem de texto simples, use a propriedade de *texto* do objeto de *atividade*. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nos manipulares de atividade do bot, use o código a seguir para receber uma mensagem. 

```cs
var responseMessage = turnContext.Activity.Text;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Nos manipulares de atividade do bot, use o código a seguir para receber uma mensagem.

```javascript
let text = turnContext.activity.text;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Nos manipulares de atividade do bot, use o código a seguir para receber uma mensagem.

```python
response = context.activity.text
```

---

## <a name="additional-resources"></a>Recursos adicionais

- Para obter mais informações sobre o processamento de atividade, confira [processamento de atividade](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack).
- Para obter mais informações sobre a formatação, confira a [seção de atividades de mensagem](https://aka.ms/botSpecs-activitySchema#message-activity) do esquema de Atividade do Bot Framework.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar mídia às mensagens](./bot-builder-howto-add-media-attachments.md)
