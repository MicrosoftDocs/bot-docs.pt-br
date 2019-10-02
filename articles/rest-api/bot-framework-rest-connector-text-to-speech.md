---
title: Adicionar fala às mensagens | Microsoft Docs
description: Saiba como adicionar fala às mensagens usando o serviço Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: e02d71084690d95e0767a6b5a351f8dc86ffe7cf
ms.sourcegitcommit: e9cd857ee11945ef0b98a1ffb4792494dfaeb126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694528"
---
# <a name="add-speech-to-messages"></a>Adicionar fala a mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-text-to-speech.md)
> - [Node.js](../nodejs/bot-builder-nodejs-text-to-speech.md)
> - [REST](../rest-api/bot-framework-rest-connector-text-to-speech.md)

Se você estiver criando um bot para um canal habilitado para fala, como o Cortana, será possível criar mensagens que especifiquem o texto a ser falado pelo seu bot. Você também pode tentar influenciar o estado do microfone do cliente especificando uma [dica de entrada](bot-framework-rest-connector-add-input-hints.md) para indicar se o seu bot está aceitando, esperando ou ignorando a entrada do usuário.

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Especifique o texto a ser falado pelo seu bot

Para especificar o texto a ser falado pelo bot em um canal habilitado para fala, defina a propriedade `speak` no objeto [Atividade][Activity] que representa a mensagem. É possível definir a propriedade `speak` como uma cadeia de caracteres de texto sem formatação ou uma cadeia de caracteres formatada como <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup" target="_blank">SSML (Linguagem de Marcação de Sintetização de Voz)</a>, uma linguagem de marcação baseada em XML que permite controlar várias características de fala do bot como voz, velocidade, volume, pronúncia, tom e muito mais. 


A solicitação a seguir envia uma mensagem que especifica o texto a ser exibido e o texto a ser falado, e indica que o bot está [esperando a entrada do usuário](bot-framework-rest-connector-add-input-hints.md). Ele especifica a propriedade `speak` usando o formato <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup" target="_blank">SSML</a> para indicar que a palavra "certeza" deve ser falada com uma quantidade moderada de ênfase. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI de base, consulte [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "Are you sure that you want to cancel this transaction?",
    "speak": "<speak version=\"1.0\" xmlns=\"http://www.w3.org/2001/10/synthesis\" xml:lang=\"en-US\">Are you <emphasis level=\"moderate\">sure</emphasis> that you want to cancel this transaction?</speak>",
    "inputHint": "expectingInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="input-hints"></a>Dicas de entrada

Quando você envia uma mensagem em um canal habilitado para fala, é possível tentar influenciar o estado do microfone do cliente incluindo também uma dica de entrada para indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário. Para obter mais informações, consulte [adicionar dicas de entrada mensagens](bot-framework-rest-connector-add-input-hints.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)
- [Adicionar dicas de entrada às mensagens](bot-framework-rest-connector-add-input-hints.md)
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup" target="_blank">SSML (Linguagem de Marcação de Sintetização de Voz)</a>

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
