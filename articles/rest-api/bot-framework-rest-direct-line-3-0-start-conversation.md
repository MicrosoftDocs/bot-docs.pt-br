---
title: Iniciar uma conversa – Serviço de Bot
description: Saiba como usar a versão 3,0 da API de linha direta para iniciar conversas com bots. Descubra como as operações iniciar conversa e gerar token são diferentes.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 5abf9125ef12fd4faa126c962f62fe52636fd208
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576718"
---
# <a name="start-a-conversation-in-direct-line-api-30"></a>Iniciar uma conversa na API de linha direta 3,0

As conversas de linha diretas explicitamente são abertas pelos clientes e podem ser executadas desde que o cliente e o bot participarem e tem credenciais válidas. Enquanto a conversa está aberta, o bot e o cliente podem enviar mensagens. Mais de um cliente pode se conectar a uma determinada conversa e cada cliente pode participar em nome de vários usuários.

## <a name="open-a-new-conversation"></a>Abra uma nova conversa

Para abrir uma nova conversa do cliente, emita POST para o ponto de extremidade /v3/directline/conversations.

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer SECRET_OR_TOKEN
```

Os trechos de código a seguir fornecem um exemplo da solicitação e resposta de início de conversa.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Resposta

Se a solicitação for bem-sucedida, a resposta conterá uma ID para a conversa, um token, um valor que indica o número de segundos até o token expirar e uma URL de fluxo que o cliente pode usar para [receber atividades por meio do fluxo de WebSocket](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket).

```http
HTTP/1.1 201 Created
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800,
  "streamUrl": "https://directline.botframework.com/v3/directline/conversations/abc123/stream?t=RCurR_XV9ZA.cwA..."
}
```

Normalmente, uma solicitação iniciar conversa é usada para abrir uma nova conversa e um código de status **HTTP 201** será retornado se a nova conversa for iniciada com êxito. No entanto, se um cliente enviar uma solicitação iniciar conversa com um token de linha direto no `Authorization` cabeçalho que foi usado anteriormente para iniciar uma conversa usando a operação iniciar conversa, um código de status **http 200** será retornado para indicar que a solicitação foi aceitável, mas nenhuma conversa foi criada (já que ela existia).

> [!TIP]
> Você tem 60 segundos para conectar-se à URL de fluxo do WebSocket. Se a conexão não puder ser estabelecida durante esse tempo, o cliente pode [reconectar-se à conversa](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) para gerar uma nova URL de fluxo.

## <a name="start-conversation-versus-generate-token"></a>Iniciar conversa versus gerar token

A operação iniciar conversa ( `POST /v3/directline/conversations` ) é semelhante à operação [gerar token](bot-framework-rest-direct-line-3-0-authentication.md#generate-token) ( `POST /v3/directline/tokens/generate` ) em que ambas as operações retornam um `token` que pode ser usado para acessar uma única conversa. No entanto, a operação iniciar conversa também inicia a conversa, entra em contato com o bot e cria uma URL de fluxo WebSocket, enquanto a operação gerar token não faz nenhuma dessas coisas.

Se você pretende iniciar a conversa imediatamente com seu cliente, use a operação iniciar conversa. Se você planeja distribuir o token para os clientes e desejar que eles iniciem a conversa, use a operação [gerar token](bot-framework-rest-direct-line-3-0-authentication.md#generate-token) em vez disso.

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md)
- [Receber atividades por meio do fluxo do WebSocket](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket)
- [Reconectar-se a uma conversa](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)
