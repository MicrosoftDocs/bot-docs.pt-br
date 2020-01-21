---
title: Reconhecer a intenção do conteúdo da mensagem – Serviço de Bot
description: Saiba como reconhecer a intenção do usuário usando expressões regulares ou verificando o conteúdo da mensagem.
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: cab45fa2eca7fad94ce8a66e2a17495a529935c3
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790679"
---
# <a name="recognize-user-intent-from-message-content"></a>Reconhecer a intenção do usuário do conteúdo da mensagem

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Quando o bot receber uma mensagem de um usuário, o bot poderá usar um **reconhecedor** para examinar a mensagem e determinar a intenção. A intenção fornece um mapeamento de mensagens aos diálogos para invocar. Este artigo explica como reconhecer a intenção usando expressões regulares ou inspecionando o conteúdo de uma mensagem. Por exemplo, um bot pode usar expressões regulares para verificar se uma mensagem contém a palavra "ajuda" e invocar um diálogo de ajuda. Um bot também pode verificar as propriedades da mensagem do usuário, por exemplo, para ver se o usuário enviou uma imagem em vez de um texto e invocar um diálogo de processamento de imagem.

> [!NOTE]
> Para obter informações sobre como reconhecer a intenção usando LUIS, consulte [Reconhecer intenções e entidades com LUIS](bot-builder-nodejs-recognize-intent-luis.md)

## <a name="use-the-built-in-regular-expression-recognizer"></a>Use o reconhecedor de expressão regular interno

Use [RegExpRecognizer][RegExpRecognizer] para detectar a intenção do usuário usando uma expressão regular. É possível passar várias expressões para o reconhecedor para dar suporte a vários idiomas.

> [!TIP]
> Consulte [Suporte de localização](bot-builder-nodejs-localization.md) para obter mais informações sobre como localizar o bot.

O código a seguir cria um reconhecedor de expressão regular nomeado `CancelIntent` e adiciona ao bot. O reconhecedor neste exemplo fornece expressões regulares para as localidades `en_us` e `ja_jp`.

[!code-js[Add a regular expression recognizer (JavaScript)](../includes/code/node-regex-recognizer.js#addRegexRecognizer)]

Depois que o reconhecedor for adicionado ao bot, anexe um [triggerAction][triggerAction] à caixa de diálogo que você quer que o bot invoque quando o reconhecedor detectar a intenção. Use a opção `matches` para especificar o nome da intenção, conforme mostrado no código a seguir:

[!code-js[Map the CancelIntent recognizer to a cancel dialog (JavaScript)](../includes/code/node-regex-recognizer.js#bindCancelDialogToRegexRecognizer)]

Reconhecedores de intenção são globais, o que significa que o reconhecedor será executado para cada mensagem recebida do usuário. Se um reconhecedor detectar uma intenção associada a um diálogo usando um `triggerAction`, ele poderá disparar a interrupção do diálogo ativa no momento. Permitir e tratar interrupções é um design flexível que explica o que os usuários realmente fazem.

> [!TIP]
> Para saber como um `triggerAction` funciona com diálogos, consulte [Gerenciar fluxo de conversa](bot-builder-nodejs-manage-conversation-flow.md). Para saber mais sobre as várias ações que podem ser associadas a uma intenção reconhecida, consulte [Tratar ações do usuário](bot-builder-nodejs-dialog-actions.md).

## <a name="register-a-custom-intent-recognizer"></a>Registrar um reconhecedor de intenção personalizado

Também é possível implementar um reconhecedor personalizado. Esse exemplo adiciona um reconhecedor simples que procura que o usuário diga 'ajuda' ou 'adeus', mas você pode facilmente adicionar um reconhecedor que faz um processamento mais complexo, como aquele que reconhece quando o usuário envia uma imagem.

[!code-js[Add a custom recognizer (JavaScript)](../includes/code/node-howto-recognize-intent.js#addCustomRecognizer)]

Após registrar um reconhecedor, você poderá associar o reconhecedor a uma ação usando uma cláusula `matches`.

[!code-js[Bind intents to actions (JavaScript)](../includes/code/node-howto-recognize-intent.js#bindIntentsToActions)]

## <a name="disambiguate-between-multiple-intents"></a>Remover ambiguidade entre várias intenções

O bot pode registrar mais de um reconhecedor. Observe que o exemplo do reconhecedor personalizado envolve a atribuição de uma pontuação numérica para cada intenção. Isso é feito já que o bot pode ter mais de um reconhecedor e o SDK do Bot Framework fornece lógica interna para remover ambiguidades entre intenções retornadas por vários reconhecedores. A pontuação atribuída a uma intenção normalmente é entre 0,0 e 1,0, mas um reconhecedor personalizado pode definir uma intenção maior que 1,1 para garantir que essa intenção sempre seja escolhida pela lógica de desambiguação do SDK do Bot Framework.

Por padrão, os reconhecedores são executados em paralelo, mas é possível definir recognizeOrder em [IIntentRecognizerSetOptions][IIntentRecognizerSetOptions], de modo que o processo encerre assim que o bot encontrar um que dê uma pontuação de 1.0.

O SDK do Bot Framework inclui um [exemplo][DisambiguationSample] que demonstra como fornecer lógica de desambiguação personalizada no bot, implementando [IDisambiguateRouteHandler][IDisambiguateRouteHandler].

## <a name="next-steps"></a>Próximas etapas

A lógica para usar expressões regulares e inspecionar o conteúdo das mensagens pode tornar-se complexa, especialmente se o fluxo de conversação do bot for em aberto. Para ajudar o bot a tratar uma ampla variedade de entradas de fala e textuais dos usuários, é possível usar um serviço de reconhecimento de intenção como [LUIS][LUIS] para adicionar reconhecimento vocal natural ao bot.

> [!div class="nextstepaction"]
> [Reconhecer intenções e entidades com o LUIS](bot-builder-nodejs-recognize-intent-luis.md)

[LUIS]: https://www.luis.ai/

[IDisambiguateRouteHandler]:   https://docs.microsoft.com/javascript/api/botbuilder/idisambiguateroutehandler?view=botbuilder-ts-3.0
[IIntentRecognizerSetOptions]: https://docs.microsoft.com/javascript/api/botbuilder/iintentrecognizersetoptions?view=botbuilder-ts-3.0
[RegExpRecognizer]:            https://docs.microsoft.com/javascript/api/botbuilder/regexprecognizer?view=botbuilder-ts-3.0
[triggerAction]:               https://docs.microsoft.com/javascript/api/botbuilder/dialog?view=botbuilder-ts-3.0#triggeraction-itriggeractionoptions-

[DisambiguationSample]: https://aka.ms/v3-js-onDisambiguateRoute
