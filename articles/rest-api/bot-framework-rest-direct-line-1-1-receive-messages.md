---
title: Receber mensagens do bot | Microsoft Docs
description: Saiba como receber mensagens do bot usando a API da Linha Direta v1.1.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 6679afa688917bdf3d558d5ed47717ee30d0e52e
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999353"
---
# <a name="receive-messages-from-the-bot"></a>Receber mensagens do bot

> [!IMPORTANT]
> Este artigo descreve como receber mensagens do bot usando a API da Linha Direta v1.1.1. Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use [API de Linha Direta 3.0](bot-framework-rest-direct-line-3-0-receive-activities.md).

Usando o protocolo de Linha Direta 1.1, os clientes devem sondar uma interface do `HTTP GET` para receber mensagens. 

## <a name="retrieve-messages-with-http-get"></a>Recuperar mensagens com HTTP GET

Para recuperar mensagens de uma conversa específica, emita uma solicitação `GET` para o ponto de extremidade `api/conversations/{conversationId}/messages`, especificando opcionalmente o parâmetro `watermark` para indicar a mensagem mais recente vista pelo cliente. Um valor `watermark` atualizado será retornado na resposta JSON, mesmo se nenhuma mensagem estiver incluída.

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Obter Mensagens. A resposta de Obter Mensagens contém `watermark` como uma propriedade de [MessageSet](bot-framework-rest-direct-line-1-1-api-reference.md#messageset-object). Os clientes devem percorrer as mensagens disponíveis aprimorando o valor `watermark`, até que nenhuma mensagem retorne. 

### <a name="request"></a>Solicitação

```http
GET https://directline.botframework.com/api/conversations/abc123/messages?watermark=0001a-94
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>Response

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
    "messages": [
        {
            "conversation": "abc123",
            "id": "abc123|0000",
            "text": "hello",
            "from": "user1"
        }, 
        {
            "conversation": "abc123",
            "id": "abc123|0001",
            "text": "Nice to see you, user1!",
            "from": "bot1"
        }
    ],
    "watermark": "0001a-95"
}
```

## <a name="timing-considerations"></a>Considerações sobre timing

Mesmo que a Linha Direta seja um protocolo de várias partes com potenciais intervalos de medição de tempo, o protocolo e o serviço foram projetados para facilitar a criação de um cliente confiável. A propriedade `watermark` enviada na resposta a Obter Mensagens é confiável. Um cliente não perderá qualquer mensagem, desde que repita a marca d'água textual.

Os clientes devem escolher um intervalo de sondagem que corresponda ao seu uso pretendido.

- Geralmente, os aplicativos de serviço a serviço usam um intervalo de sondagem de 5 ou 10 segundos.

- Os aplicativos voltados para o cliente geralmente usam um intervalo de sondagem de 1s e emitem uma solicitação adicional de cerca de 300 ms após cada mensagem que o cliente envia (para recuperar rapidamente a resposta de um bot). Esse atraso de 300 ms deve ser ajustado com base na velocidade e no tempo de trânsito do bot.

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-1-1-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-1-1-authentication.md)
- [Iniciar uma conversa](bot-framework-rest-direct-line-1-1-start-conversation.md)
- [Enviar uma mensagem para o bot](bot-framework-rest-direct-line-1-1-send-message.md)