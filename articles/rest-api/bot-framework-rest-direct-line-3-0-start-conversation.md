---
title: Iniciar uma conversa | Microsoft Docs
description: Saiba como iniciar uma conversa usando a API Direct Line v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: ead16c0ff41ae93daff8952ca135fa0771bbbe78
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296898"
---
# <a name="start-a-conversation"></a>Iniciar uma conversa

As conversas de Linha Direta são explicitamente abertas por clientes e podem ser executadas contanto que o bot e o cliente participem e tenham credenciais válidas. Enquanto a conversa estiver aberta, ambos o bot e o cliente podem enviar mensagens. Mais de um cliente pode conectar-se a uma determinada conversa; e cada cliente pode participar em nome de vários usuários.

## <a name="open-a-new-conversation"></a>Abrir uma nova conversa

Para abrir uma nova conversa com um bot, emita esta solicitação:

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer SECRET_OR_TOKEN
```

Os trechos de código a seguir fornecem um exemplo da solicitação Iniciar Conversa e da resposta.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Response

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

Normalmente, uma solicitação Iniciar Conversa é usada para abrir uma nova conversa e um código de status **HTTP 201** será retornado se a nova conversa for iniciada com êxito. No entanto, se um cliente envia uma solicitação Iniciar Conversa com um token de Linha Direta no cabeçalho `Authorization` que foi usado anteriormente para iniciar uma conversa usando a operação Iniciar Conversa, um código de status **HTTP 200** será retornado para indicar que a solicitação era aceitável, mas nenhuma conversa foi criada (pois ela já existia).

> [!TIP]
> Você tem 60 segundos para conectar-se à URL de fluxo do WebSocket. Se a conexão não puder ser estabelecida durante esse tempo, o cliente pode [reconectar-se à conversa](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) para gerar uma nova URL de fluxo.

## <a name="start-conversation-versus-generate-token"></a>Iniciar Conversa em comparação com Gerar Token

A operação Iniciar Conversa (`POST /v3/directline/conversations`) é semelhante à operação [Gerar Token](bot-framework-rest-direct-line-3-0-authentication.md#generate-token) (`POST /v3/directline/tokens/generate`), já que ambas retornam um `token` que pode ser usado para acessar uma única conversa. No entanto, a operação Iniciar Conversa também inicia a conversa, entra em contato com o bot e cria uma URL de fluxo do WebSocket, enquanto que a operação Gerar Token não faz nenhuma dessas coisas. 

Se você pretende iniciar a conversa imediatamente, use a operação Iniciar Conversa. Se você planeja distribuir o token para clientes e quer que eles iniciem a conversa, use a operação [Gerar Token](bot-framework-rest-direct-line-3-0-authentication.md#generate-token). 

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md)
- [Receber atividades por meio do fluxo do WebSocket](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket)
- [Reconectar-se a uma conversa](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)