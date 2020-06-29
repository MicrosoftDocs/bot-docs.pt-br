---
title: Adicionar dicas de entrada a mensagens (JS v3) – Serviço de Bot
description: Aprenda como adicionar dicas de entrada às mensagens usando o SDK do Bot Framework para .NET.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 354adb42b4442fdc443192cd6b058a9f56d45eed
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790564"
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

Se você não definir a dica de entrada para uma mensagem, o SDK do Bot Framework a definirá automaticamente para você usando esta lógica: 

- Se seu bot enviar um prompt, a dica de entrada para a mensagem especificará que seu bot está **esperando entrada**.</li>
- Se seu bot enviar uma única mensagem, a dica de entrada para a mensagem especificará que seu bot está **aceitando a entrada**.</li>
- Se seu bot enviar uma série de mensagens consecutivas, a dica de entrada para todas as mensagens, exceto a final na série, especificará que seu bot está **ignorando a entrada**, e a dica de entrada para a mensagem final na série especificará que seu bot está **aceitando a entrada**.

## <a name="additional-resources"></a>Recursos adicionais

- [Adicionar fala a mensagens](bot-builder-nodejs-text-to-speech.md)
