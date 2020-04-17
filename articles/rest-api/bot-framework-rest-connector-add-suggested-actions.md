---
title: Adicionar ações sugeridas a mensagens – Serviço de Bot
description: Saiba como adicionar ações sugeridas a mensagens usando o serviço Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: ea8c76df736678754db95d7306605f5ef10b6a11
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790077"
---
# <a name="add-suggested-actions-to-messages"></a>Adicionar ações sugeridas a mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

## <a name="send-suggested-actions"></a>Enviar ações sugeridas

Para adicionar ações sugeridas a uma mensagem, defina a propriedade `suggestedActions` do objeto [Atividade][] para especificar a lista de objetos [CardAction][] que representam os botões a serem apresentados ao usuário. 

A solicitação a seguir envia uma mensagem que apresenta três ações sugeridas para o usuário. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "text": "I have colors in mind, but need your help to choose the best one.",
    "inputHint": "expectingInput",
    "suggestedActions": {
        "actions": [
            {
                "type": "imBack",
                "title": "Blue",
                "value": "Blue"
            },
            {
                "type": "imBack",
                "title": "Red",
                "value": "Red"
            },
            {
                "type": "imBack",
                "title": "Green",
                "value": "Green"
            }
        ]
    },
    "replyToId": "5d5cdc723"
}
```

Quando o usuário tocar em uma das ações sugeridas, o bot receberá uma mensagem do usuário que contém o `value` da ação correspondente.

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)

[channelInspector]: ../bot-service-channel-inspector.md

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
[CardAction]: bot-framework-rest-connector-api-reference.md#cardaction-object
