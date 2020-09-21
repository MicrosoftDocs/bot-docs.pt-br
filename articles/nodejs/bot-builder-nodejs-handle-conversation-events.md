---
title: Manipular eventos de usuário e de conversa – Serviço de Bot
description: Saiba como lidar com eventos como um usuário ingressando em uma conversa usando o SDK do Bot Framework para Node.js.
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/19/2020
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 519f85e1ab500b02fc11ed42960cd17103c6089c
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824796"
---
# <a name="handle-user-and-conversation-events-in-the-v3-javascript-sdk"></a>Manipular eventos de usuário e de conversa no SDK do JavaScript v3

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

Você também pode personalizar o que uma ação faz depois de disparada fornecendo um manipulador [onSelectAction][onSelectAction]. Para disparar ações, você pode fornecer um manipulador [onInterrupted][onInterrupted] para interceptar uma interrupção antes que ela ocorra. Para saber mais, veja [Lidar com ações de usuário](bot-builder-nodejs-dialog-actions.md).

## <a name="additional-resources"></a>Recursos adicionais

* [conversationUpdate][conversationUpdate]
* [contactRelationUpdate][contactRelationUpdate]

[conversationUpdate]: https://docs.microsoft.com/javascript/api/botbuilder/iconversationupdate?view=botbuilder-ts-3.0
[contactRelationUpdate]: https://docs.microsoft.com/javascript/api/botbuilder/icontactrelationupdate?view=botbuilder-ts-3.0

[onFindAction]: https://docs.microsoft.com/javascript/api/botbuilder/itriggeractionoptions?view=botbuilder-ts-3.0#onfindaction
[onSelectAction]: https://docs.microsoft.com/javascript/api/botbuilder/itriggeractionoptions?view=botbuilder-ts-3.0#onselectaction
[onInterrupted]: https://docs.microsoft.com/javascript/api/botbuilder/itriggeractionoptions?view=botbuilder-ts-3.0#oninterrupted

[SendTyping]: https://docs.microsoft.com/javascript/api/botbuilder/session?view=botbuilder-ts-3.0#sendtyping--
[IMessage]: https://docs.microsoft.com/javascript/api/botbuilder/imessage?view=botbuilder-ts-3.0
[ChatConnector]: https://docs.microsoft.com/javascript/api/botbuilder/chatconnector?view=botbuilder-ts-3.0
[session_userData]: https://docs.microsoft.com/javascript/api/botbuilder/session?view=botbuilder-ts-3.0#userdata
