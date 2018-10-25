---
title: Adicionar uma fala a mensagens | Microsoft Docs
description: Saiba como adicionar fala a mensagens usando o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: dc542c7e85b3a79e1071edebea65d93c99742beb
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000353"
---
# <a name="add-speech-to-messages"></a>Adicionar fala a mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-text-to-speech.md)
> - [Node.js](../nodejs/bot-builder-nodejs-text-to-speech.md)
> - [REST](../rest-api/bot-framework-rest-connector-text-to-speech.md)

Se você estiver criando um bot para um canal habilitado para fala, como o Cortana, será possível criar mensagens que especifiquem o texto a ser falado pelo seu bot. Você também pode tentar influenciar o estado do microfone do cliente especificando uma [dica de entrada](bot-builder-dotnet-add-input-hints.md) para indicar se o seu bot está aceitando, esperando ou ignorando a entrada do usuário.

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Especificar o texto a ser falado pelo seu bot

Usando o SDK do Construtor de Bot para .NET, há várias maneiras de especificar o texto a ser falado pelo seu bot em um canal habilitado para fala. É possível definir a propriedade `Speak` da [mensagem][IMessageActivity], chamar o método `IDialogContext.SayAsync()` ou especificar as opções de prompt `speak` e `retrySpeak` ao enviar uma mensagem usando um prompt interno.

### <a id="message-speak"></a> IMessageActivity.Speak

Se você estiver criando uma [mensagem][IMessageActivity] e definindo as propriedades individuais dela, será possível definir a propriedade `Speak` da mensagem para especificar o texto a ser falado pelo seu bot. O exemplo de código a seguir cria uma mensagem que especifica o texto a ser exibido e o texto a ser falado e indica que o bot está [aceitando a entrada do usuário](bot-builder-dotnet-add-input-hints.md).

[!code-csharp[Set speak property](../includes/code/dotnet-text-to-speech.cs#Speak1)]

### <a id="say-async"></a> IDialogContext.SayAsync()

Se você estiver usando [diálogos](bot-builder-dotnet-dialogs.md), será possível chamar o método `SayAsync()` para criar e enviar uma mensagem que especifique o texto a ser falado, além do texto a ser exibido e outras opções. O exemplo de código a seguir cria uma mensagem que especifica o texto a ser exibido e o texto a ser falado.

[!code-csharp[Call SayAsync()](../includes/code/dotnet-text-to-speech.cs#Speak2)]

### <a id="prompt-options"></a> Opções de prompt

Usando qualquer um dos prompts internos, você pode definir as opções `speak` e `retrySpeak` para especificar o texto a ser falado pelo seu bot. O exemplo de código a seguir cria um prompt que especifica o texto a ser exibido, o texto a ser falado inicialmente e o texto a ser falado após aguardar algum tempo pela entrada usuário. Ele usa a formatação [SSML](#ssml) para indicar que "com certeza" deve ser falado com uma quantidade moderada de ênfase.

[!code-csharp[Set Prompt options](../includes/code/dotnet-text-to-speech.cs#Speak3)]

## <a id="ssml"></a> Linguagem de Marcação de Sintetização de Fala (SSML)

Para especificar o texto a ser falado pelo seu bot, você pode usar uma string de texto simples ou uma string formatada como SSML (linguagem de marcação de síntese de fala), uma linguagem de marcação baseada em XML que permite controlar várias características do discurso do bot. como voz, taxa, volume, pronúncia, tom e muito mais. Confira mais detalhes sobre o SSML em <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Referência de Linguagem de Marcação de Sintetização de Fala</a>.

## <a name="input-hints"></a>Dicas de entrada

Quando você envia uma mensagem em um canal habilitado para fala, é possível tentar influenciar o estado do microfone do cliente incluindo também uma dica de entrada para indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário. Para obter mais informações, consulte [adicionar dicas de entrada mensagens](bot-builder-dotnet-add-input-hints.md).

## <a name="sample-code"></a>Exemplo de código 

Confira um exemplo completo que mostra como criar um bot habilitado para fala usando o SDK do Construtor de Bot para .NET em <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp" target="_blank">Exemplo de Habilidade de Rolagem</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- [Adicionar dicas de entrada às mensagens](bot-builder-dotnet-add-input-hints.md)
- <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Linguagem de Marcação de Sintetização de Fala (SSML)</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-RollerSkill" target="_blank">Exemplo de Habilidade de Rolagem (GitHub)</a>
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">Interface IMessageActivity</a>
- <a href="/dotnet/api/microsoft.bot.builder.dialogs.internals.dialogcontext" target="_blank">Classe DialogContext</a>
- <a href="/dotnet/api/microsoft.bot.builder.dialogs.internals.prompt-2" target="_blank">Classe Prompt</a>

[IMessageActivity]: /dotnet/api/microsoft.bot.connector.imessageactivity

