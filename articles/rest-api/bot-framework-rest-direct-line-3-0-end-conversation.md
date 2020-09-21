---
title: Terminar uma conversa – Serviço de Bot
description: Saiba como usar a versão 3,0 da API de linha direta para encerrar conversas entre bots e canais Cortana. Consulte como configurar e enviar eventos do endOfConversation.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 6f214b0b903f5c39437efe760b0c953e9ed9cb8a
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824426"
---
# <a name="end-a-conversation-in-direct-line-api-30"></a>Encerrar uma conversa na API de linha direta 3,0

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
