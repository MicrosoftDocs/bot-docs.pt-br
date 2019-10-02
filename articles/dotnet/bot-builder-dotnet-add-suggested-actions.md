---
title: Adicionar ações sugeridas às mensagens | Microsoft Docs
description: Saiba como adicionar ações sugeridas às mensagens usando o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/13/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 26a253ed46f8ed0d23f2bd046de132f44cd19019
ms.sourcegitcommit: d493caf74b87b790c99bcdaddb30682251e3fdd4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71278957"
---
# <a name="add-suggested-actions-to-messages"></a>Adicionar ações sugeridas a mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

## <a name="send-suggested-actions"></a>Enviar ações sugeridas

Para adicionar ações sugeridas a uma mensagem, defina a propriedade `SuggestedActions` da atividade como uma lista de objetos [CardAction][cardAction] que representam os botões a serem apresentados ao usuário. 

Este exemplo de código mostra como criar uma mensagem que apresenta três ações sugeridas ao usuário:

[!code-csharp[Add suggested actions](../includes/code/dotnet-add-suggested-actions.cs#addSuggestedActions)]

Quando o usuário tocar em uma das ações sugeridas, o bot receberá uma mensagem do usuário que contém o `Value` da ação correspondente.

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral das atividades](bot-builder-dotnet-activities.md)
- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- [Classe Activity](https://aka.ms/ActivityClass-dotnet-API)
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">Interface IMessageActivity</a>
- <a href="/dotnet/api/microsoft.bot.connector.cardaction" target="_blank">Classe CardAction</a>
- <a href="/dotnet/api/microsoft.bot.connector.suggestedactions" target="_blank">Classe SuggestedActions</a>

[cardAction]: /dotnet/api/microsoft.bot.connector.cardaction

[inspector]: ../bot-service-channel-inspector.md


