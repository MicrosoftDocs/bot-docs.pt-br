---
title: Adicionar dicas de entrada a mensagens | Microsoft Docs
description: Saiba como adicionar dicas de entrada a mensagens usando o serviço Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 66c6bc20013ff2de82e29af76e9c99898c8b13d9
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297178"
---
# <a name="add-input-hints-to-messages"></a>Adicionar dicas de entrada a mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

Ao especificar uma dica de entrada para uma mensagem, você pode indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário depois que a mensagem é entregue ao cliente. Para vários canais, isso permite que os clientes definam o estado dos controles de entrada do usuário adequadamente. Por exemplo, se a dica de entrada da mensagem indicar que o bot está ignorando a entrada do usuário, o cliente poderá fechar o microfone e desabilitar a caixa de entrada para impedir que o usuário forneça uma entrada.

## <a name="accepting-input"></a>Aceitação da entrada

Para indicar que seu bot está passivamente pronto para entrada mas não está aguardando uma resposta do usuário, defina a propriedade `inputHint` como **acceptingInput** no objeto [Activity][Activity] que representa sua mensagem. Em vários canais, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja fechado, mas ainda acessível ao usuário. Por exemplo, a Cortana abrirá o microfone para aceitar a entrada do usuário se o usuário mantiver pressionado o botão de microfone. 

O exemplo a seguir mostra uma solicitação que envia uma mensagem e especifica que o bot está aceitando entrada. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI de base; o URI de base para solicitações em que os seus problemas de bot podem ser diferentes. Para obter detalhes sobre como definir o URI de base, veja [Referência da API](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "Here's a picture of the house I was telling you about.",
    "inputHint": "acceptingInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="expecting-input"></a>Espera por uma entrada

Para indicar que seu bot está aguardando uma resposta do usuário, defina a propriedade `inputHint` como **expectingInput** no objeto [Activity][Activity] que representa sua mensagem. Em vários canais, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja aberto. 

O exemplo a seguir mostra uma solicitação que envia uma mensagem e especifica que o bot está esperando uma entrada. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI de base; o URI de base para solicitações em que os seus problemas de bot podem ser diferentes. Para obter detalhes sobre como definir o URI de base, veja [Referência da API](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "What is your favorite color?",
    "inputHint": "expectingInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="ignoring-input"></a>Como ignorar a entrada
 
Para indicar que seu bot não está pronto para receber a entrada do usuário, defina a propriedade `inputHint` como **ignoringInput** no objeto [Activity][Activity] representa a mensagem. Em vários canais, isso fará com que a caixa de entrada do cliente seja desabilitada e o microfone seja fechado. 

O exemplo a seguir mostra uma solicitação que envia uma mensagem e especifica que o bot está ignorando a entrada. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI de base; o URI de base para solicitações em que os seus problemas de bot podem ser diferentes. Para obter detalhes sobre como definir o URI de base, veja [Referência da API](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "Please hold while I perform the calculation.",
    "inputHint": "ignoringInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object