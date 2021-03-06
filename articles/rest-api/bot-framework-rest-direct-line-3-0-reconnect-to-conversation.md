---
title: Reconectar-se a uma conversa – Serviço de Bot
description: Saiba como reconectar-se a conversas com bots após perder o contato. Consulte como usar a versão 3,0 da API de linha direta para gerar novas URLs de fluxo WebSocket.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/20/2020
ms.openlocfilehash: ca7045553a16c5e31396648abecb414db0cdf796
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824406"
---
# <a name="reconnect-to-a-conversation-in-direct-line-api-30"></a>Reconectar-se a uma conversa na API de linha direta 3,0

Se um cliente estiver usando a [interface do WebSocket](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket) para receber mensagens, mas perder a conexão, talvez ele precise se reconectar. Nesse cenário, o cliente precisa gerar uma nova URL de fluxo do WebSocket que ele pode usar para se reconectar à conversa.

## <a name="generate-a-new-websocket-stream-url"></a>Gerar uma nova URL de fluxo do WebSocket

Para gerar uma nova URL de fluxo do WebSocket que pode ser usada para se reconectar a uma conversa existente, emita esta solicitação: 

```http
GET https://directline.botframework.com/v3/directline/conversations/{conversationId}?watermark={watermark_value}
Authorization: Bearer SECRET_OR_TOKEN
```

Nesse URI de solicitação, substitua **{conversationId}** pela ID de conversa e **{watermark_value}** pelo valor de marca-d'água (caso o parâmetro `watermark` esteja disponível). O `watermark` é opcional. Se o parâmetro `watermark` for especificado no URI de solicitação, a conversa será reproduzida com base na marca-d'água, garantindo que nenhuma mensagem seja perdida. Se o parâmetro `watermark` for omitido do URI de solicitação, apenas as mensagens recebidas após a solicitação de reconexão serão reproduzidas.

Os snippets a seguir fornecem um exemplo da solicitação de Reconexão e da resposta.

### <a name="request"></a>Solicitação

```http
GET https://directline.botframework.com/v3/directline/conversations/abc123?watermark=0000a-42
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Resposta

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

Se você tiver a "Opção de autenticação avançada" habilitada nas configurações do Direct Line, poderá receber um erro 400 "MissingProperty" se não tiver um token configurado corretamente anexado à solicitação. 

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md)
- [Receber atividades por meio do fluxo do WebSocket](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket)
