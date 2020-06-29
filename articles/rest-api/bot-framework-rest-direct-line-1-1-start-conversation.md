---
title: Iniciar uma conversa usando a Direct Line API 1.1 – Serviço de Bot
description: Saiba como iniciar uma conversa usando a API de linha direta v1.1.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 6aa107dc54f6a960aabc4f03b5e9acb49a9c6cb0
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75789587"
---
# <a name="start-a-conversation"></a>Iniciar uma conversa

> [!IMPORTANT]
> Este artigo descreve como iniciar uma conversa usando a API de linha direta 1.1. Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use a [API de Linha Direta 3.0](bot-framework-rest-direct-line-3-0-start-conversation.md).

As conversas de linha diretas explicitamente são abertas pelos clientes e podem ser executadas desde que o cliente e o bot participarem e tem credenciais válidas. Enquanto a conversa está aberta, o bot e o cliente podem enviar mensagens. Mais de um cliente pode se conectar a uma determinada conversa e cada cliente pode participar em nome de vários usuários.

## <a name="open-a-new-conversation"></a>Abra uma nova conversa

Para abrir uma nova conversa com um bot, execute esta solicitação:

```http
POST https://directline.botframework.com/api/conversations
Authorization: Bearer SECRET_OR_TOKEN
```

Os snippets de código a seguir fornecem um exemplo de conversa Iniciar solicitação e resposta.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/api/conversations
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Resposta

Se a solicitação for bem-sucedida, a resposta conterá um ID para a conversa, um token e um valor que indica o número de segundos até que o token expire.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800
}
```

## <a name="start-conversation-versus-generate-token"></a>Iniciar conversa em vez de gerar o Token

A operação Iniciar Conversa (`POST /api/conversations`) é semelhante à operação [Gerar Token](bot-framework-rest-direct-line-1-1-authentication.md#generate-token) (`POST /api/tokens/conversation`) em que ambas as operações retornam um `token` que pode ser usado para acessar uma única conversa. No entanto, a operação Iniciar Conversa também inicia a conversa e entra em contato com o bot, enquanto a operação Gerar Token não faz nada disso. 

Se você pretende iniciar a conversa imediatamente, use a operação Iniciar Conversa. Se você planeja distribuir o token para clientes e deseja que eles iniciem a conversa, use a operação [Gerar Token](bot-framework-rest-direct-line-1-1-authentication.md#generate-token). 

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-1-1-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-1-1-authentication.md)