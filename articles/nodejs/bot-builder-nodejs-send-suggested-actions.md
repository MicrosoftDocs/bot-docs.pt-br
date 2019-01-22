---
title: Adicionar ações sugeridas às mensagens | Microsoft Docs
description: Saiba como enviar ações sugeridas em mensagens usando o SDK do Bot Framework para Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 06/06/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 975496786e66a4d9b1de5c6ead6d8257687f23b7
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224521"
---
# <a name="add-suggested-actions-to-messages"></a>Adicionar ações sugeridas a mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

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
