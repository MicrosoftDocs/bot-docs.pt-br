---
title: Adicionar ações sugeridas a mensagens | Microsoft Docs
description: Saiba como adicionar ações sugeridas a mensagens usando o serviço Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 346c522a1e1cd3d5904b74ad6e6092a1457a001c
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65032595"
---
# <a name="add-suggested-actions-to-messages"></a>Adicionar ações sugeridas a mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

## <a name="send-suggested-actions"></a>Enviar ações sugeridas

Para adicionar ações sugeridas a uma mensagem, defina a propriedade `suggestedActions` da [Atividade][Activity] para especificar a lista de objetos [CardAction][CardAction] que representam os botões a serem apresentados ao usuário. 

A solicitação a seguir envia uma mensagem que apresenta três ações sugeridas para o usuário. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI de base; o URI de base para solicitações em que os seus problemas de bot podem ser diferentes. Para obter detalhes sobre como definir o URI de base, veja [Referência da API](bot-framework-rest-connector-api-reference.md#base-uri).

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

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object

[CardAction]: bot-framework-rest-connector-api-reference.md#cardaction-object

[SuggestedAction]: bot-framework-rest-connector-api-reference.md#suggestedactions-object
