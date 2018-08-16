---
title: Adicionar ações sugeridas às mensagens | Microsoft Docs
description: Saiba como enviar ações sugeridas a mensagens usando o SDK do Construtor de Bot para Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 06/06/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 510a340506416e61f906228ccd26c0bdd6e5d4d3
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296787"
---
# <a name="add-suggested-actions-to-messages"></a>Adicionar ações sugeridas a mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

> [!TIP]
> Use o [Inspetor de Canal][channelInspector] para ver a aparência e o funcionamento das ações sugeridas em vários canais.

## <a name="suggested-actions-example"></a>Exemplo de ações sugeridas

Para adicionar ações sugeridas a uma mensagem, defina a propriedade `suggestedActions` da mensagem como uma lista de [ações de cartão][ICardAction] que representam os botões a serem apresentados ao usuário.

Este exemplo de código mostra como enviar uma mensagem que apresenta três ações sugeridas ao usuário:

[!code-javascript[Send suggested actions](../includes/code/node-send-suggested-actions.js#sendSuggestedActions)]

Quando o usuário tocar em uma das ações sugeridas, o bot receberá uma mensagem do usuário que contém o `value` da ação correspondente.

Lembre-se de que o método `imBack` postará o `value` para a janela de chat do canal que você está usando. Se isso não for o efeito desejado, você poderá usar o método `postBack`, que ainda lançará a seleção de volta para o seu bot, mas não exibirá a seleção na janela de bate-papo. Alguns canais não oferecem suporte a `postBack`, no entanto, e nesses casos, o método se comportará como `imBack`.

## <a name="additional-resources"></a>Recursos adicionais

* [Visualizar recursos com o Inspetor de Canal][inspector]
* [IMessage][IMessage]
* [ICardAction][ICardAction]
* [session.send][SessionSend]

[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage

[SessionSend]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#send

[ICardAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.icardaction.html

[inspector]: ../bot-service-channel-inspector.md

[channelInspector]: ../bot-service-channel-inspector.md
