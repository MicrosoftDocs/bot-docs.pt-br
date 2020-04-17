---
title: Terminar uma conversa – Serviço de Bot
description: Saiba como encerrar uma conversa usando a API de linha direta v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 67c74bc3f51328370e4d6ba207855b20e4d79497
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77519995"
---
# <a name="end-a-conversation"></a>Terminar uma conversa

A [atividade](https://aka.ms/botSpecs-activitySchema) **endOfConversation** significa que o canal ou o bot encerrou a conversa. 

> [!NOTE] 
> Embora o evento **endOfConversation** só seja enviado por pouquíssimos canais, o canal da Cortana é o único que o aceita. Outros canais, incluindo o Direct Line, não implementam essa funcionalidade e, em vez disso, descartam ou encaminham a atividade; cada canal determina como reagir a uma atividade endOfConversation.

## <a name="send-an-endofconversation-activity"></a>Enviar uma atividade endOfConversation

Para solicitar a finalização de uma conversa com o canal da Cortana, emita o POST de Fim da Atividade de Conversa para o ponto de extremidade de mensagens do canal.

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
