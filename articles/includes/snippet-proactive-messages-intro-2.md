---
ms.openlocfilehash: 4108f02a350d7e02d66db06cd7e65fa9c7a98e7d
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92438507"
---
Uma **mensagem proativa ad hoc** é o tipo mais simples de mensagem proativa.
O bot apenas injeta a mensagem na conversa sempre que ele é disparado, sem considerar se o usuário está envolvido em um tópico separado de conversa com o bot e não tentará alterar a conversa de modo algum.

Para lidar com notificações de forma mais suave, considere outras maneiras de integrar a notificação no fluxo de conversa, como definir um sinalizador no estado da conversa ou adicionar a notificação a uma fila.

<!--Snip
A **dialog-based proactive message** is more complex than an ad hoc proactive message. 
Before it can inject this type of proactive message into the conversation, 
the bot must identify the context of the existing conversation and decide how (or if)
it will resume that conversation after the message interrupts. 

For example, consider a bot that needs to initiate a survey at a given point in time. 
When that time arrives, the bot stops the existing conversation with the user and 
redirects the user to a `SurveyDialog`. 
The `SurveyDialog` is added to the top of the dialog stack and takes control of the conversation. 
When the user finishes all required tasks at the `SurveyDialog`, the `SurveyDialog` closes,
 returning control to the previous dialog, where the user can continue with the prior topic of conversation.

A dialog-based proactive message is more than just simple notification. 
In sending the notification, the bot changes the topic of the existing conversation. 
It then must decide whether to resume that conversation later, or to abandon that conversation altogether by resetting the dialog stack. 
/Snip-->