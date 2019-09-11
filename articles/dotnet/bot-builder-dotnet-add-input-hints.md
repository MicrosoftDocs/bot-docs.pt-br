---
title: Adicionar sugestões de entrada às mensagens | Microsoft Docs
description: Aprenda como adicionar dicas de entrada às mensagens usando o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: d9d98c217afcf3eb1759284bcb0d46d9001bf383
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298466"
---
# <a name="add-input-hints-to-messages"></a>Adicionar dicas de entrada às mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

Ao especificar uma dica de entrada para uma mensagem, você pode indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário após a entrega da mensagem ao cliente. Para muitos canais, isso permite que os clientes definam o estado dos controles de entrada do usuário de acordo. Por exemplo, se a dica de entrada de uma mensagem indicar que o bot está ignorando a entrada do usuário, o cliente poderá fechar o microfone e desabilitar a caixa de entrada para evitar que o usuário forneça entrada.

## <a name="accepting-input"></a>Aceitar a entrada

Para indicar que seu bot está passivamente pronto para entrada, mas não está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como `InputHints.AcceptingInput`. Em muitos canais, isso fará com que a caixa de entrada do cliente seja ativada e o microfone seja fechado, mas ainda assim acessível ao usuário. Por exemplo, a Cortana abrirá o microfone para aceitar a entrada do usuário se o usuário pressionar o botão do microfone. O exemplo de código a seguir cria uma mensagem que indica que o bot está aceitando a entrada do usuário.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.AcceptingInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="expecting-input"></a>Esperando uma entrada

Para indicar que seu bot está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como `InputHints.ExpectingInput`. Em vários canais, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja aberto. O código a seguir cria um exemplo que indica que o bot está esperando a entrada do usuário.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.ExpectingInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="ignoring-input"></a>Ignorar a entrada

Para indicar que seu bot não está pronto para receber entrada do usuário, defina a dica de entrada da mensagem como `InputHints.IgnorningInput`. Em vários canais, isso fará com que a caixa de entrada do cliente seja desabilitada e o microfone seja fechado. O código a seguir cria um exemplo que indica que o bot está ignorando a entrada do usuário.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.IgnoringInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="default-values-for-input-hint"></a>Valores padrão para a dica de entrada

Se você não definir a dica de entrada para uma mensagem, o SDK do Bot Framework a definirá automaticamente para você usando esta lógica:

- Se seu bot enviar um prompt, a dica de entrada para a mensagem especificará que seu bot está **esperando entrada**.</li>
- Se seu bot enviar uma única mensagem, a dica de entrada para a mensagem especificará que seu bot está **aceitando a entrada**.</li>
- Se seu bot enviar uma série de mensagens consecutivas, a dica de entrada para todas as mensagens, exceto a final na série, especificará que seu bot está **ignorando a entrada**, e a dica de entrada para a mensagem final na série especificará que seu bot está **aceitando a entrada**.

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- [Adicionar fala a mensagens](bot-builder-dotnet-text-to-speech.md)
- <a href="https://docs.botframework.com/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
- <a href="/dotnet/api/microsoft.bot.connector.inputhints" target="_blank">Classe InputHints</a>
