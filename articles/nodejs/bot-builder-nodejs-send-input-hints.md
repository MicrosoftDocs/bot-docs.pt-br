---
title: Adicionar sugestões de entrada às mensagens | Microsoft Docs
description: Aprenda como adicionar dicas de entrada para mensagens usando o Bot Builder SDK for .NET.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 2bbf75f166a90d2e0a905bd269f51cef4398a2ef
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904488"
---
# <a name="add-input-hints-to-messages"></a>Adicionar dicas de entrada às mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

Ao especificar uma dica de entrada para uma mensagem, você pode indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário após a entrega da mensagem ao cliente. Para muitos canais, isso permite que os clientes definam o estado dos controles de entrada do usuário de acordo. Por exemplo, se a dica de entrada de uma mensagem indicar que o bot está ignorando a entrada do usuário, o cliente poderá fechar o microfone e desabilitar a caixa de entrada para evitar que o usuário forneça entrada.

## <a name="accepting-input"></a>Aceitar a entrada

Para indicar que seu bot está passivamente pronto para entrada, mas não está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como `builder.InputHint.acceptingInput`. Em muitos canais, isso fará com que a caixa de entrada do cliente seja ativada e o microfone seja fechado, mas ainda assim acessível ao usuário. Por exemplo, a Cortana abrirá o microfone para aceitar a entrada do usuário se o usuário pressionar o botão do microfone. O exemplo de código a seguir cria uma mensagem que indica que o bot está aceitando a entrada do usuário.

[!code-javascript[IMessage.speak](../includes/code/node-input-hints.js#InputHintAcceptingInput)]

## <a name="expecting-input"></a>Esperando uma entrada

Para indicar que seu bot está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como `builder.InputHint.expectingInput`. Em muitos canais, isso fará com que a caixa de entrada do cliente seja ativada e o microfone esteja aberto. O exemplo de código a seguir cria um prompt que indica que o bot está esperando a entrada do usuário.

[!code-javascript[Prompt](../includes/code/node-input-hints.js#InputHintExpectingInput)]

## <a name="ignoring-input"></a>Ignorando a entrada

Para indicar que seu bot não está pronto para receber entrada do usuário, defina a dica de entrada da mensagem como `builder.InputHint.ignoringInput`. Em muitos canais, isso fará com que a caixa de entrada do cliente seja desativada e o microfone seja fechado. O exemplo de código a seguir usa o método `session.say()` para enviar uma mensagem que indica que o bot está ignorando a entrada do usuário.

[!code-javascript[Session.say()](../includes/code/node-input-hints.js#InputHintIgnoringInput)]

## <a name="default-values-for-input-hint"></a>Valores padrão para a dica de entrada

Se você não definir a dica de entrada para uma mensagem, o SDK do Construtor de Bot definirá automaticamente para você usando essa lógica: 

- Se seu bot enviar um prompt, a dica de entrada para a mensagem especificará que seu bot está **esperando entrada**.</li>
- Se seu bot enviar uma única mensagem, a dica de entrada para a mensagem especificará que seu bot está **aceitando a entrada**.</li>
- Se seu bot enviar uma série de mensagens consecutivas, a dica de entrada para todas as mensagens, exceto a final na série, especificará que seu bot está **ignorando a entrada**, e a dica de entrada para a mensagem final na série especificará que seu bot está **aceitando a entrada**.

## <a name="additional-resources"></a>Recursos adicionais

- [Adicionar fala a mensagens](bot-builder-nodejs-text-to-speech.md)
- [Construtor de bot do SDK para referência do Node. js][SDKReference]

[SDKReference]: https://docs.botframework.com/en-us/node/builder/chat-reference/modules/_botbuilder_d_.html

