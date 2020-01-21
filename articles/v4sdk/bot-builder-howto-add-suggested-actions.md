---
title: Usar o botão para a entrada – Serviço de Bot
description: Saiba como enviar ações sugeridas em mensagens usando o SDK do Bot Framework para JavaScript.
keywords: ações sugeridas, botões, entrada extra
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/10/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7a8ec7dbe7d8079967d66d9461b67fb3d2152d94
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75798407"
---
# <a name="use-button-for-input"></a>Usar o botão para a entrada

[!INCLUDE[applies-to](../includes/applies-to.md)]

Você pode habilitar seu bot para apresentar botões que o usuário pode tocar para fornecer a entrada. Os botões melhoram a experiência do usuário permitindo que ele responda a uma pergunta ou faça uma seleção com o simples toque de um botão, em vez de digitar uma resposta com um teclado. Ao contrário de botões exibidos em cartões avançados (que permanecem visíveis e acessíveis ao usuário, mesmo após serem tocados), os botões que aparecem no painel de ações sugeridas desaparecerão após a seleção do usuário. Isso impede que o usuário toque em botões obsoletos dentro de uma conversa e simplifica o desenvolvimento de bot (uma vez que não será necessário levar em conta esse cenário). 

## <a name="suggest-action-using-button"></a>Sugerir ação usando o botão

As *ações sugeridas* permitem que seu bot apresente botões. Você pode criar uma lista de ações sugeridas (também conhecidas como "respostas rápidas"), que será mostrada ao usuário para uma única rodada de conversa: 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O código-fonte mostrado aqui se baseia no [exemplo de ações sugeridas](https://aka.ms/SuggestedActionsCSharp).

[!code-csharp[suggested actions](~/../botbuilder-samples/samples/csharp_dotnetcore/08.suggested-actions/Bots/SuggestedActionsBot.cs?range=87-101)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código-fonte mostrado aqui se baseia no [exemplo de ações sugeridas](https://aka.ms/SuggestActionsJS).

[!code-javascript[suggested actions](~/../botbuilder-samples/samples/javascript_nodejs/08.suggested-actions/bots/suggestedActionsBot.js?range=61-64)]


# <a name="pythontabpython"></a>[Python](#tab/python)

O código-fonte mostrado aqui se baseia no [exemplo de ações sugeridas](https://aka.ms/SuggestActionsPython).

[!code-python[suggested actions](~/../botbuilder-python/samples/python/08.suggested-actions/bots/suggested_actions_bot.py?range=63-81)]


---

## <a name="additional-resources"></a>Recursos adicionais

Você pode acessar o código-fonte completo mostrado aqui:
- [Exemplo de C#](https://aka.ms/SuggestedActionsCSharp)
- [Exemplo de JavaScript](https://aka.ms/SuggestActionsJS)
- [Exemplo de Python](https://aka.ms/SuggestActionsPython)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Salvar dados de usuário e de conversa](./bot-builder-howto-v4-state.md)
