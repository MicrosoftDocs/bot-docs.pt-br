---
title: Reconectar-se a uma conversa | Microsoft Docs
description: Saiba como se reconectar a uma conversa usando a API de Linha Direta v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 2c6b3a7e9f0fdc7d5227fc8112cb6f3e330a2bcc
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296783"
---
# <a name="reconnect-to-a-conversation"></a>Reconectar-se a uma conversa

Se um cliente estiver usando a [interface do WebSocket](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket) para receber mensagens, mas perder a conexão, talvez ele precise se reconectar. Nesse cenário, o cliente precisa gerar uma nova URL de fluxo do WebSocket que ele pode usar para se reconectar à conversa.

## <a name="generate-a-new-websocket-stream-url"></a>Gerar uma nova URL de fluxo do WebSocket

Para gerar uma nova URL de fluxo do WebSocket que pode ser usada para se reconectar a uma conversa existente, emita esta solicitação: 

```http
GET https://directline.botframework.com/v3/directline/conversations/{conversationId}?watermark={watermark_value}
Authorization: Bearer SECRET_OR_TOKEN
```

Nesse URI de solicitação, substitua **{conversationId}** pela ID de conversa e **{watermark_value}** pelo valor de marca-d'água (caso o parâmetro `watermark` seja fornecido). O `watermark` é opcional. Se o parâmetro `watermark` for especificado no URI de solicitação, a conversa será reproduzida com base na marca-d'água, garantindo que nenhuma mensagem seja perdida. Se o parâmetro `watermark` for omitido do URI de solicitação, apenas as mensagens recebidas após a solicitação de reconexão serão reproduzidas.

Os trechos a seguir fornecem um exemplo da solicitação de Reconexão e da resposta.

### <a name="request"></a>Solicitação

```http
GET https://directline.botframework.com/v3/directline/conversations/abc123?watermark=0000a-42
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Response

Se a solicitação for bem-sucedida, a resposta conterá uma ID da conversa, um token e uma nova URL de fluxo do WebSocket.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "streamUrl": "https://directline.botframework.com/v3/directline/conversations/abc123/stream?watermark=000a-4&amp;t=RCurR_XV9ZA.cwA..."
}
```

## <a name="reconnect-to-the-conversation"></a>Reconectar-se à conversa

O cliente precisa usar a nova URL de fluxo do WebSocket para [se reconectar à conversa](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket) em até 60 segundos. Se a conexão não puder ser estabelecida durante esse tempo, o cliente precisará emitir outra solicitação de Reconexão para gerar uma nova URL de fluxo.

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md)
- [Receber atividades por meio do fluxo do WebSocket](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket)