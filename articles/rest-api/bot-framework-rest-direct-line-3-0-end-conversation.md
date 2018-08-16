---
title: Terminar uma conversa | Microsoft Docs
description: Saiba como encerrar uma conversa usando a API de linha direta v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: ac984609acfdd8f85088bd47ccded1f45e953b2c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297080"
---
# <a name="end-a-conversation"></a>Terminar uma conversa

Um cliente ou um bot pode sinalizar o final de uma conversa de linha direta, enviando uma **endOfConversation** [atividade](bot-framework-rest-connector-activities.md). 

## <a name="send-an-endofconversation-activity"></a>Enviar uma atividade endOfConversation

Uma atividade **endOfConversation** termina a comunicação entre o bot e o cliente. Depois que uma atividade **endOfConversation** foi enviada, o cliente ainda pode [recuperar mensagens](bot-framework-rest-direct-line-3-0-receive-activities.md#http-get) usando `HTTP GET`, mas nem o cliente nem o bot podem enviar mensagens adicionais para a conversa. 

Para encerrar uma conversa, basta emitir uma solicitação POST para enviar uma atividade **endOfConversation**.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
[other headers]
```

```json
{
    "type": "endOfConversation",
    "from": {
        "id": "user1"
    }
}
```

### <a name="response"></a>Response

Se a solicitação for bem-sucedida, a resposta conterá uma ID da atividade que foi enviado.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "id": "0004"
}
```

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md)
- [Enviar uma atividade para o bot](bot-framework-rest-direct-line-3-0-send-activity.md)
