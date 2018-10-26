---
title: Terminar uma conversa | Microsoft Docs
description: Saiba como encerrar uma conversa usando a API de linha direta v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: f0985f28fd1744bcfb6bf5cea1c2230254670e01
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000203"
---
# <a name="end-a-conversation"></a>Terminar uma conversa

Um cliente ou um bot pode sinalizar o final de uma conversa de linha direta, enviando uma **endOfConversation** [atividade](bot-framework-rest-connector-activities.md). 

> [!NOTE] 
> O evento endOfConversation tem suporte apenas no canal da Cortana, outros canais não implementam essa funcionalidade. Cada canal determina como reagir a uma atividade endOfConversation. Se você estiver criando um cliente DirectLine, atualize o cliente para ele se comportar adequadamente, por exemplo, gerar um erro se o bot enviar uma atividade para uma conversa que já terminou.

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
