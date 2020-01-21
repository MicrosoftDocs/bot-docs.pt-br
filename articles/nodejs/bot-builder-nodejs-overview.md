---
title: SDK do Bot Framework para Node.js – Serviço de Bot
description: Explore o SDK do Bot Framework para Node.js, um framework avançado e simples para a criação de bots.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 047293c00e8ade5445f75218be6d4dfbe332934c
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790821"
---
# <a name="bot-framework-sdk-for-nodejs"></a>SDK do Bot Framework para Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

O SDK do Bot Framework para Node.js é um framework avançado e simples que fornece uma maneira familiar para desenvolvedores do Node.js gravarem bots.
Use-o para criar uma ampla variedade de interfaces do usuário de conversa, de prompts simples a conversas de forma livre.

A lógica conversacional do bot é hospedada como um serviço Wb. O SDK do Bot Framework usa <a href="http://restify.com">restify</a>, um conhecido framework de criação de serviços Web, para criar o servidor Web do bot. O SDK também é compatível com <a href="http://expressjs.com/">Express</a> e o uso de outros frameworks de aplicativos Web é possível com alguma adaptação. 

Usando o SDK, é possível aproveitar os seguintes recursos do SDK: 

- Sistema avançado de construção de diálogos para encapsular lógica conversacional.
- Prompts internos para condições simples, como Sim/Não, cadeia de caracteres, números e enumerações, bem como suporte para mensagens contendo imagens e anexos e cartões avançados contendo botões.
- Suporte interno para frameworks de IA avançados como <a href="http://luis.ai" target="_blank">LUIS</a>.
- Manipuladores de eventos e reconhecedores internos que orientam o usuário através da conversa fornecendo ajuda, navegação, esclarecimento e confirmação, conforme necessário.

## <a name="get-started"></a>Introdução

Se você for um iniciante na gravação de bots, [crie seu primeiro bot com Node.js](bot-builder-nodejs-quickstart.md) usando as instruções passo a passo para ajudá-lo a configurar o projeto, instalar o SDK e executar seu primeiro bot. 

Se for iniciante no SDK do Bot Framework para Node.js, você poderá começar com os principais conceitos que ajudarão a reconhecer os principais componentes do SDK do Bot Framework. Consulte [Principais conceitos](bot-builder-nodejs-concepts.md) para obter mais informações.

Para garantir que o bot atenda os principais cenários do usuário, revise os [princípios de design](../bot-service-design-principles.md) e [explorar padrões](../bot-service-design-pattern-task-automation.md) para obter mais diretrizes.

## <a name="get-samples"></a>Obter exemplos

Os [exemplos do SDK do Bot Framework para Node.js](bot-builder-nodejs-samples.md) demonstram bots com tarefa focalizada que mostram como aproveitar os recursos do SDK do Bot Framework para Node.js. Você pode usar as amostras para ajudá-lo a começar rapidamente a criar grandes bots com recursos avançados.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Principais conceitos](bot-builder-nodejs-concepts.md)

## <a name="additional-resources"></a>Recursos adicionais

Os seguintes guias de instruções com tarefa focalizada demonstram vários recursos do SDK do Bot Framework para Node.js.

* [Responder às mensagens](bot-builder-nodejs-use-default-message-handler.md)
* [Manipular ações de usuário](bot-builder-nodejs-dialog-actions.md)
* [Reconhecer intenção de usuário](bot-builder-nodejs-recognize-intent-messages.md)
* [Enviar um cartão avançado](bot-builder-nodejs-send-rich-cards.md)
* [Enviar anexos](bot-builder-nodejs-send-receive-attachments.md)
* [Salvar dados de usuário](bot-builder-nodejs-save-user-data.md)


Se você encontrar problemas ou tiver sugestões sobre o SDK do Bot Framework para Node.js, consulte a lista de recursos disponíveis em [Suporte](../bot-service-resources-links-help.md). 


[DesignGuide]: ../bot-service-design-principles.md 
[DesignPatterns]: ../bot-service-design-pattern-task-automation.md 
[HowTo]: bot-builder-nodejs-use-default-message-handler.md 
