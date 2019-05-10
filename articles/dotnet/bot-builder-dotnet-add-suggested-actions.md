---
title: Adicionar ações sugeridas às mensagens | Microsoft Docs
description: Saiba como adicionar ações sugeridas às mensagens usando o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/13/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ad9b791cf74c4a67515fdf8d60eab29c51f93bc2
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563902"
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

## <a name="send-suggested-actions"></a>Enviar ações sugeridas

Para adicionar ações sugeridas a uma mensagem, defina a propriedade `SuggestedActions` da atividade como uma lista de objetos [CardAction][cardAction] que representam os botões a serem apresentados ao usuário. 

Este exemplo de código mostra como criar uma mensagem que apresenta três ações sugeridas ao usuário:

[!code-csharp[Add suggested actions](../includes/code/dotnet-add-suggested-actions.cs#addSuggestedActions)]

Quando o usuário tocar em uma das ações sugeridas, o bot receberá uma mensagem do usuário que contém o `Value` da ação correspondente.

## <a name="additional-resources"></a>Recursos adicionais

- [Visualizar recursos com o Inspetor de Canal][inspector]
- [Visão geral das atividades](bot-builder-dotnet-activities.md)
- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">Interface IMessageActivity</a>
- <a href="/dotnet/api/microsoft.bot.connector.cardaction" target="_blank">Classe CardAction</a>
- <a href="/dotnet/api/microsoft.bot.connector.suggestedactions" target="_blank">Classe SuggestedActions</a>

[cardAction]: /dotnet/api/microsoft.bot.connector.cardaction

[inspector]: ../bot-service-channel-inspector.md

[channelInspector]: ../bot-service-channel-inspector.md


