---
title: Adicionar dicas de entrada a mensagens | Microsoft Docs
description: Saiba como adicionar dicas de entrada a mensagens usando o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 2f56a855990675ccae4845c13541150ab205379a
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297338"
---
# <a name="add-input-hints-to-messages"></a>Adicionar dicas de entrada a mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

Ao especificar uma dica de entrada para uma mensagem, você pode indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário depois que a mensagem é entregue ao cliente. Para vários canais, isso permite que os clientes definam o estado dos controles de entrada do usuário adequadamente. Por exemplo, se a dica de entrada da mensagem indicar que o bot está ignorando a entrada do usuário, o cliente poderá fechar o microfone e desabilitar a caixa de entrada para impedir que o usuário forneça uma entrada.

## <a name="accepting-input"></a>Aceitação da entrada

Para indicar que seu bot está passivamente pronto para entrada mas não está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como `InputHints.AcceptingInput`. Em vários canais, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja fechado, mas ainda acessível ao usuário. Por exemplo, a Cortana abrirá o microfone para aceitar a entrada do usuário se o usuário mantiver pressionado o botão de microfone. O código a seguir cria um exemplo que indica que o bot está aceitando a entrada do usuário.

[!code-csharp[Accepting input](../includes/code/dotnet-input-hints.cs#InputHintAcceptingInput)]

## <a name="expecting-input"></a>Espera por uma entrada

Para indicar que seu bot está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como `InputHints.ExpectingInput`. Em vários canais, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja aberto. O código a seguir cria um exemplo que indica que o bot está esperando a entrada do usuário.

[!code-csharp[Expecting input](../includes/code/dotnet-input-hints.cs#InputHintExpectingInput)]

## <a name="ignoring-input"></a>Ignorar a entrada
 
Para indicar que seu bot não está pronto para receber a entrada do usuário, defina a dica de entrada da mensagem como `InputHints.IgnorningInput`. Em vários canais, isso fará com que a caixa de entrada do cliente seja desabilitada e o microfone seja fechado. O código a seguir cria um exemplo que indica que o bot está ignorando a entrada do usuário.

[!code-csharp[Ignoring input](../includes/code/dotnet-input-hints.cs#InputHintIgnoringInput)]

## <a name="default-values-for-input-hint"></a>Valores padrão para a dica de entrada

Se você não definir a dica de entrada para uma mensagem, o SDK do Construtor de Bot definirá automaticamente para você usando essa lógica: 

- Se seu bot enviar um prompt, a dica de entrada para a mensagem especificará que seu bot está **esperando entrada**.</li>
- Se seu bot enviar uma única mensagem, a dica de entrada para a mensagem especificará que seu bot está **aceitando a entrada**.</li>
- Se seu bot enviar uma série de mensagens consecutivas, a dica de entrada para todas as mensagens, exceto a final na série, especificará que seu bot está **ignorando a entrada**, e a dica de entrada para a mensagem final na série especificará que seu bot está **aceitando a entrada**.

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- [Adicionar fala a mensagens](bot-builder-dotnet-text-to-speech.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
- <a href="/dotnet/api/microsoft.bot.connector.inputhints" target="_blank">Classe InputHints</a>