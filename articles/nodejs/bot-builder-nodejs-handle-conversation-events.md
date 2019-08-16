---
title: Manipular eventos de usuário e de conversa | Microsoft Docs
description: Saiba como lidar com eventos como um usuário ingressando em uma conversa usando o SDK do Bot Framework para Node.js.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a6149b750a4432f00268571df6d12b611114181f
ms.sourcegitcommit: 6a83b2c8ab2902121e8ee9531a7aa2d85b827396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67404917"
---
# <a name="handle-user-and-conversation-events"></a>Manipular eventos de usuário e de conversa

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Este artigo demonstra como seu bot pode lidar com eventos como um usuário ingressando em uma conversa, a adição de um bot a uma lista de contatos ou dizendo **Adeus** quando um bot estiver sendo removido de uma conversa.


## <a name="greet-a-user-on-conversation-join"></a>Saudar a um usuário ao ingressar em conversa
O Bot Framework fornece o evento [conversationUpdate][conversationUpdate] para notificar seu bot sempre que um membro entra ou sai de uma conversa. Um membro da conversa pode ser um usuário ou um bot.

O snippet de código a seguir permite que o bot dê as boas-vindas aos novos membros de uma conversa ou diga **Adeus** se eles estiverem sendo removidos da conversa.

[!INCLUDE [conversationUpdate sample Node.js](../includes/snippet-code-node-conversationupdate-1.md)]

## <a name="acknowledge-add-to-contacts-list"></a>Confirmar adição à lista de contatos

O evento [contactRelationUpdate][contactRelationUpdate] notifica seu bot de que um usuário adicionou você à lista de contatos dele.

[!INCLUDE [contactRelationUpdate sample Node.js](../includes/snippet-code-node-contactrelationupdate-1.md)]

## <a name="add-a-first-run-dialog"></a>Adicionar uma caixa de diálogo inicial

Uma vez que os eventos **conversationUpdate** e **contactRelationUpdate** não têm suporte de todos os canais, uma maneira universal para saudar um usuário que participa de uma conversa é adicionar uma caixa de diálogo inicial.

No exemplo a seguir, adicionamos uma função que dispara o diálogo a qualquer momento com um usuário que nunca vimos antes. Você pode personalizar a maneira como uma ação é disparada, fornecendo um manipulador [onFindAction][onFindAction] para a ação. 

[!INCLUDE [first-run sample Node.js](../includes/snippet-code-node-first-run-dialog-1.md)]

Você também pode personalizar o que uma ação faz depois de disparada fornecendo um manipulador [onSelectAction][onSelectAction]. Para disparar ações, você pode fornecer um manipulador [onInterrupted][onInterrupted] para interceptar uma interrupção antes que ela ocorra. Para saber mais, veja [Lidar com ações de usuário](bot-builder-nodejs-dialog-actions.md)

## <a name="additional-resources"></a>Recursos adicionais

* [conversationUpdate][conversationUpdate]
* [contactRelationUpdate][contactRelationUpdate]

[conversationUpdate]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iconversationupdate.html
[contactRelationUpdate]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.icontactrelationupdate.html

[onFindAction]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#onfindaction
[onSelectAction]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#onselectaction
[onInterrupted]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#oninterrupted

[SendTyping]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping
[IMessage]: http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
[ChatConnector]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata
