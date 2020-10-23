---
title: Adicionar dicas de entrada a mensagens – Serviço de Bot
description: Saiba como adicionar dicas de entrada a mensagens de bot. Consulte como usar o serviço de conector de bot para especificar se um bot está aceitando, esperando ou ignorando a entrada do usuário.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: baa482cb2b25388fe01026f18f72e20cef7ffc65
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417108"
---
# <a name="add-input-hints-to-messages-with-the-bot-connector-api"></a>Adicionar dicas de entrada a mensagens com a API do conector de bot

Ao especificar uma dica de entrada para uma mensagem, você pode indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário após a entrega da mensagem ao cliente. Para canais compatíveis com esse campo, isso permite que os clientes definam o estado dos controles de entrada do usuário de acordo. Por exemplo, se a dica de entrada de uma mensagem indicar que o bot está ignorando a entrada do usuário, o cliente poderá fechar o microfone e desabilitar a caixa de entrada para evitar que o usuário forneça entrada.

## <a name="accepting-input"></a>Aceitar a entrada

Para indicar que seu bot está passivamente pronto para entrada mas não está aguardando uma resposta do usuário, defina a propriedade `inputHint` como **acceptingInput** no objeto [Atividade][] que representa sua mensagem. Em muitos canais, isso fará com que a caixa de entrada do cliente seja ativada e o microfone seja fechado, mas ainda assim acessível ao usuário. Por exemplo, a Cortana abrirá o microfone para aceitar a entrada do usuário se o usuário pressionar o botão do microfone. 

O exemplo a seguir mostra uma solicitação que envia uma mensagem e especifica que o bot está aceitando entrada. Nessa solicitação de exemplo, Direct Line representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

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

## <a name="expecting-input"></a>Esperando uma entrada

Para indicar que seu bot está ativamente aguardando uma resposta do usuário, defina a propriedade `inputHint` como **expectingInput** no objeto [Atividade][] que representa sua mensagem. Em canais compatíveis, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja aberto. 

O exemplo a seguir mostra uma solicitação que envia uma mensagem e especifica que o bot está esperando uma entrada. Nessa solicitação de exemplo, Direct Line representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

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

## <a name="ignoring-input"></a>Ignorando a entrada
 
Para indicar que seu bot não está pronto para receber a entrada do usuário, defina a propriedade `inputHint` como **ignoringInput** no objeto [Atividade][] que representa a mensagem. Em canais compatíveis, isso fará com que a caixa de entrada do cliente seja desabilitada e o microfone seja fechado. 

O exemplo a seguir mostra uma solicitação que envia uma mensagem e especifica que o bot está ignorando a entrada. Nessa solicitação de exemplo, Direct Line representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

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

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
