---
title: SDK do Bot Builder para Node.js | Microsoft Docs
description: Explore o SDK do Bot Builder para Node.js, um avançado framework de construção de bots fácil de usar.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 67acd4d8b24b88ac705f997357166a3fdb9f26c0
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997173"
---
# <a name="bot-builder-sdk-for-nodejs"></a>SDK do Bot Builder para Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

O SDK do Bot Builder para Node.js é um avançado framework fácil de usar que fornece uma maneira familiar para desenvolvedores do Node.js gravarem bots.
É possível utilizá-lo para compilar uma ampla variedade de interfaces de usuário de conversa, desde simples solicitações até conversas de forma livre.

A lógica conversacional do bot é hospedada como um serviço Wb. O SDK do Bot Builder usa <a href="http://restify.com">restify</a>, um framework popular para construir serviços Web, para criar o servidor Web do bot. O SDK também é compatível com <a href="http://expressjs.com/">Express</a> e o uso de outros frameworks de aplicativos Web é possível com alguma adaptação. 

Usando o SDK, é possível aproveitar os seguintes recursos do SDK: 

- Sistema avançado de construção de diálogos para encapsular lógica conversacional.
- Prompts internos para condições simples, como Sim/Não, cadeia de caracteres, números e enumerações, bem como suporte para mensagens contendo imagens e anexos e cartões avançados contendo botões.
- Suporte interno para frameworks de IA avançados como <a href="http://luis.ai" target="_blank">LUIS</a>.
- Manipuladores de eventos e reconhecedores internos que orientam o usuário através da conversa fornecendo ajuda, navegação, esclarecimento e confirmação, conforme necessário.

## <a name="get-started"></a>Introdução

Se você for um iniciante na gravação de bots, [crie seu primeiro bot com Node.js](bot-builder-nodejs-quickstart.md) usando as instruções passo a passo para ajudá-lo a configurar o projeto, instalar o SDK e executar seu primeiro bot. 

Se for iniciante no SDK do Bot Builder para Node.js, você poderá começar com os principais conceitos que ajudarão a reconhecer os principais componentes do SDK do Bot Builder, consulte [Principais conceitos](bot-builder-nodejs-concepts.md).

Para garantir que o bot atenda os principais cenários do usuário, revise os [princípios de design](../bot-service-design-principles.md) e [explorar padrões](../bot-service-design-pattern-task-automation.md) para obter mais diretrizes.

## <a name="get-samples"></a>Obter exemplos

Os [exemplos do SDK do Bot Builder SDK para Node.js](bot-builder-nodejs-samples.md) demonstram bots com tarefa focalizada que mostram como aproveitar os recursos do SDK do Bot Builder para Node.js. Você pode usar as amostras para ajudá-lo a começar rapidamente a criar grandes bots com recursos avançados.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Principais conceitos](bot-builder-nodejs-concepts.md)

## <a name="additional-resources"></a>Recursos adicionais

Os seguintes guias de instruções com tarefa focalizada demonstram vários recursos do SDK do Bot Builder para Node.js.

* [Responder às mensagens](bot-builder-nodejs-use-default-message-handler.md)
* [Manipular ações de usuário](bot-builder-nodejs-dialog-actions.md)
* [Reconhecer intenção de usuário](bot-builder-nodejs-recognize-intent-messages.md)
* [Enviar um cartão avançado](bot-builder-nodejs-send-rich-cards.md)
* [Enviar anexos](bot-builder-nodejs-send-receive-attachments.md)
* [Salvar dados de usuário](bot-builder-nodejs-save-user-data.md)


Se você enfrentar problemas ou tiver sugestões sobre o SDK do Bot Builder para Node.js, consulte o [Suporte](../bot-service-resources-links-help.md) para obter uma lista dos recursos disponíveis. 


[DesignGuide]: ../bot-service-design-principles.md 
[DesignPatterns]: ../bot-service-design-pattern-task-automation.md 
[HowTo]: bot-builder-nodejs-use-default-message-handler.md 
