---
title: Adicionar fala a mensagens (JS v3) – Serviço de Bot
description: Saiba como adicionar fala a mensagens de bot. Consulte como usar o SDK do bot Framework para Node.js Formatar e especificar o texto que os bots usam em canais habilitados para fala.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 5521ccd7110f39b30d8ff8c3839dc460760bf962
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824626"
---
# <a name="add-speech-to-messages-in-the-v3-javascript-sdk"></a>Adicionar fala a mensagens no SDK do JavaScript v3

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-text-to-speech.md)
> - [Node.js](../nodejs/bot-builder-nodejs-text-to-speech.md)
> - [REST](../rest-api/bot-framework-rest-connector-text-to-speech.md)

Se você estiver criando um bot para um canal habilitado para fala, como o Cortana, será possível criar mensagens que especifiquem o texto a ser falado pelo seu bot. Você também pode tentar influenciar o estado do microfone do cliente especificando uma [dica de entrada](bot-builder-nodejs-send-input-hints.md) para indicar se o seu bot está aceitando, esperando ou ignorando a entrada do usuário.

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Especifique o texto a ser falado pelo seu bot

Usando o SDK do Bot Framework para Node.js, há várias maneiras de especificar o texto a ser falado pelo seu bot em um canal habilitado para fala. Você pode definir a propriedade `IMessage.speak` e enviar a mensagem usando o método `session.send()`, enviar a mensagem usando o método `session.say()` (passando parâmetros que especificam texto de exibição, texto da fala e opções), ou enviar a mensagem usando um -in prompt (especificando as opções `speak` e `retrySpeak`).

### <a name="imessagespeak"></a><a id="message-speak"></a> IMessage.speak

Se você estiver criando uma mensagem que será enviada usando o método `session.send()`, defina a propriedade `speak` para especificar o texto a ser falado pelo seu bot. O exemplo de código a seguir cria uma mensagem que especifica o texto a ser falado e indica que o bot está [aceitando a entrada do usuário](bot-builder-nodejs-send-input-hints.md).

[!code-javascript[IMessage.speak](../includes/code/node-text-to-speech.js#IMessageSpeak)]

### <a name="sessionsay"></a><a id="session-say"></a> Session.say()

Como alternativa ao uso de `session.send()`, você pode chamar o método `session.say()` para criar e enviar uma mensagem que especifique o texto a ser falado, além do texto a ser exibido e outras opções. O método é definido da seguinte maneira:

`session.say(displayText: string, speechText: string, options?: object)`

| Parâmetro | Descrição |
|----|----|
| `displayText` | O texto a ser exibido. |
| `speechText` | O texto (em texto simples ou no formato <a href="https://msdn.microsoft.com/library/hh378377(v=office.14).aspx" target="_blank">SSML</a>) deve ser falado. |
| `options` | Um objeto `IMessage` que pode conter um anexo ou [dica de entrada](bot-builder-nodejs-send-input-hints.md). |

O exemplo de código a seguir cria uma mensagem que especifica o texto a ser exibido e o texto a ser falado e indica que o bot está [aceitando a entrada do usuário](bot-builder-nodejs-send-input-hints.md).

[!code-javascript[Session.say()](../includes/code/node-text-to-speech.js#SessionSay)]

### <a name="prompt-options"></a><a id="prompt-options"></a> Opções de prompts

Usando qualquer um dos prompts internos, você pode definir as opções `speak` e `retrySpeak` para especificar o texto a ser falado pelo seu bot. O exemplo de código a seguir cria um prompt que especifica o texto a ser exibido, o texto a ser falado inicialmente e o texto a ser falado após aguardar algum tempo pela entrada do usuário. Isso indica que o bot está [esperando a entrada do usuário](bot-builder-nodejs-send-input-hints.md) e usa a formatação [SSML](#ssml) para especificar que a palavra "sure" deve ser falada com uma quantidade moderada de ênfase.

[!code-javascript[Prompt](../includes/code/node-text-to-speech.js#Prompt)]

## <a name="speech-synthesis-markup-language-ssml"></a><a id="ssml"></a> Linguagem de marcação de síntese de fala (SSML)

Para especificar o texto a ser falado pelo seu bot, você pode usar uma string de texto simples ou uma string formatada como SSML (linguagem de marcação de síntese de fala), uma linguagem de marcação baseada em XML que permite controlar várias características do discurso do bot. como voz, taxa, volume, pronúncia, tom e muito mais. Para obter detalhes sobre SSML, consulte <a href="https://msdn.microsoft.com/library/hh378377(v=office.14).aspx" target="_blank">Referência da Linguagem de Marcação de Síntese de Fala</a>.

> [!TIP]
> Use uma <a href="https://www.npmjs.com/search?q=ssml" target="_blank">biblioteca SSML</a> para criar um SSML bem formatado.

## <a name="input-hints"></a>Dicas de entrada

Quando você envia uma mensagem em um canal habilitado para fala, é possível tentar influenciar o estado do microfone do cliente incluindo também uma dica de entrada para indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário. Para obter mais informações, consulte [adicionar dicas de entrada mensagens](bot-builder-nodejs-send-input-hints.md).

## <a name="sample-code"></a>Código de exemplo 

Veja um exemplo completo que mostra como criar um bot habilitado para fala usando o SDK do Bot Framework para Node.js em <a href="https://github.com/microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/demo-RollerSkill" target="_blank">Exemplo de rolagem</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

- <a href="https://msdn.microsoft.com/library/hh378377(v=office.14).aspx" target="_blank">Linguagem de marcação de síntese de fala (SSML)</a>
- <a href="https://github.com/microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/demo-RollerSkill" target="_blank">Exemplo de rolo de pintura (GitHub)</a>
