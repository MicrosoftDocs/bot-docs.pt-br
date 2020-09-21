---
title: Adicionar ações sugeridas a mensagens (C# v3) – Serviço de Bot
description: Saiba como adicionar ações sugeridas às mensagens usando o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/13/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 17b788b974cf5ad5ca93750d75cbcb8d38368e19
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824156"
---
# <a name="add-suggested-actions-to-messages-the-v3-c-sdk"></a>Adicionar ações sugeridas a mensagens do SDK do v3 C \#

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
- [Classe de atividade](https://aka.ms/ActivityClass-dotnet-API)
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">Interface IMessageActivity</a>
- <a href="/dotnet/api/microsoft.bot.connector.cardaction" target="_blank">Classe CardAction</a>
- <a href="/dotnet/api/microsoft.bot.connector.suggestedactions" target="_blank">Classe SuggestedActions</a>

[cardAction]: /dotnet/api/microsoft.bot.connector.cardaction

[inspector]: ../bot-service-channel-inspector.md


