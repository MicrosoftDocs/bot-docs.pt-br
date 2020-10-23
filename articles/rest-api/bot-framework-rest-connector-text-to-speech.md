---
title: Adicionar fala a mensagens – Serviço de Bot
description: Saiba como adicionar fala a mensagens de bot. Consulte como especificar e formatar o texto que os bots usam em canais habilitados para fala e como incluir dicas de entrada.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
ms.openlocfilehash: a0d70958c28d921ac9c9e4be8ec06303b97f8117
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417068"
---
# <a name="add-speech-to-messages-with-the-bot-connector-api"></a>Adicionar fala a mensagens com a API do conector de bot

Se você estiver criando um bot para um canal habilitado para fala, como o Cortana, será possível criar mensagens que especifiquem o texto a ser falado pelo seu bot. Você também pode tentar influenciar o estado do microfone do cliente especificando uma [dica de entrada](bot-framework-rest-connector-add-input-hints.md) para indicar se o seu bot está aceitando, esperando ou ignorando a entrada do usuário.

Você pode configurar seu bot para permitir que aplicativos cliente se comuniquem com ele por meio do [canal do Direct Line Speech](../bot-service-channel-connect-directlinespeech.md).

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Especifique o texto a ser falado pelo seu bot

Para especificar o texto a ser falado pelo bot em um canal habilitado para fala, defina a propriedade `speak` no objeto [Atividade][Activity] que representa a mensagem. É possível definir a propriedade `speak` como uma cadeia de caracteres de texto sem formatação ou uma cadeia de caracteres formatada como <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup" target="_blank">SSML (Linguagem de Marcação de Sintetização de Voz)</a>, uma linguagem de marcação baseada em XML que permite controlar várias características de fala do bot como voz, velocidade, volume, pronúncia, tom e muito mais. Se o canal não for compatível, a mensagem será entregue como texto.


A solicitação a seguir envia uma mensagem que especifica o texto a ser exibido e o texto a ser falado, e indica que o bot está [esperando a entrada do usuário](bot-framework-rest-connector-add-input-hints.md). Ele especifica a propriedade `speak` usando o formato <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup" target="_blank">SSML</a> para indicar que a palavra "certeza" deve ser falada com uma quantidade moderada de ênfase. Nessa solicitação de exemplo, Direct Line representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

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

Ao enviar uma mensagem em um canal habilitado para fala, você pode expressar o estado pretendido do microfone do cliente, incluindo também uma dica de entrada para indicar se o bot está aceitando, esperando ou ignorando a entrada do usuário. Para obter mais informações, consulte [adicionar dicas de entrada mensagens](bot-framework-rest-connector-add-input-hints.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)
- [Adicionar dicas de entrada às mensagens](bot-framework-rest-connector-add-input-hints.md)
- [Linguagem de marcação de síntese de fala (SSML)](/azure/cognitive-services/speech-service/speech-synthesis-markup)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
