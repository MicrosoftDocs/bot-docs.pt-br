---
title: Terminar uma conversa – Serviço de Bot
description: Saiba como encerrar uma conversa usando a API de linha direta v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 4b8a3046ee53d90fe2abdc97a3c931a4f67c051f
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75789428"
---
# <a name="end-a-conversation"></a>Terminar uma conversa

A [atividade](https://aka.ms/botSpecs-activitySchema) **endOfConversation** significa que o canal ou o bot encerrou a conversa. 

> [!NOTE] 
> Embora o evento **endOfConversation** só seja enviado por pouquíssimos canais, o canal da Cortana é o único que o aceita. Outros canais, incluindo o Direct Line, não implementam essa funcionalidade e, em vez disso, descartam ou encaminham a atividade; cada canal determina como reagir a uma atividade endOfConversation. Se você estiver criando um cliente DirectLine, atualize o cliente para ele se comportar adequadamente, por exemplo, gerar um erro se o bot enviar uma atividade para uma conversa que já terminou.

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

### <a name="response"></a>Resposta

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
