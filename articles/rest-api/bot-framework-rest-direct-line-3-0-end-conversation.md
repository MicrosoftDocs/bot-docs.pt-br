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
ms.openlocfilehash: 438558995f83ade38404856d61ba66ee77480a27
ms.sourcegitcommit: 32615b88e4758004c8c99e9d564658a700c7d61f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55711930"
---
# <a name="end-a-conversation"></a>Terminar uma conversa

A [atividade](bot-framework-rest-connector-activities.md) **endOfConversation** significa que o canal ou o bot encerrou a conversa. 

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
