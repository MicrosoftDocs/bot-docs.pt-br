---
title: Gerenciar fluxo da conversa com diálogos | Microsoft Docs
description: Saiba como modelar conversas e gerenciar um fluxo da conversa usando diálogos e o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 94690f58526e8ee322a73c68136d2b52667e1c89
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999943"
---
# <a name="manage-conversation-flow-with-dialogs"></a>Gerenciar fluxo de conversa com diálogos

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-manage-conversation-flow.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-manage-conversation-flow.md)

[!INCLUDE [Dialog flow example](../includes/snippet-dotnet-manage-conversation-flow-intro.md)]

Este artigo descreve como modelar este fluxo da conversa usando [diálogos](bot-builder-dotnet-dialogs.md) e o SDK do Construtor de Bot para .NET. 

## <a name="invoke-the-root-dialog"></a>Invocar o diálogo de raiz

Primeiro, o controlador de bot invoca o "diálogo de raiz". O exemplo a seguir mostra como conectar a chamada HTTP POST básica a um controlador e invocar o diálogo de raiz. 

```cs
public class MessagesController : ApiController
{
    public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
    {
            // Redirect to the root dialog.
            await Conversation.SendAsync(activity, () => new RootDialog()); 
            ...
    }
}
```

## <a name="invoke-the-new-order-dialog"></a>Invocar o diálogo ‘Novo Pedido’

Em seguida, o diálogo de raiz invoca o diálogo “Novo Pedido”. 

```cs
[Serializable]
public class RootDialog : IDialog<object>
{
    public async Task StartAsync(IDialogContext context)
    {
        // Root dialog initiates and waits for the next message from the user. 
        // When a message arrives, call MessageReceivedAsync.
        context.Wait(this.MessageReceivedAsync); 
    }

    public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
    {
        var message = await result; // We've got a message!
        if (message.Text.ToLower().Contains("order"))
        {
            // User said 'order', so invoke the New Order Dialog and wait for it to finish.
            // Then, call ResumeAfterNewOrderDialog.
            await context.Forward(new NewOrderDialog(), this.ResumeAfterNewOrderDialog, message, CancellationToken.None);
        }
        // User typed something else; for simplicity, ignore this input and wait for the next message.
        context.Wait(this.MessageReceivedAsync);
    }

    private async Task ResumeAfterNewOrderDialog(IDialogContext context, IAwaitable<string> result)
    {
        // Store the value that NewOrderDialog returned. 
        // (At this point, new order dialog has finished and returned some value to use within the root dialog.)
        var resultFromNewOrder = await result;

        await context.PostAsync($"New order dialog just told me this: {resultFromNewOrder}");

        // Again, wait for the next message from the user.
        context.Wait(this.MessageReceivedAsync);
    }
}
```

## <a id="dialog-lifecycle"></a> Ciclo de vida do diálogo

Quando um diálogo é invocado, ela assume o controle do fluxo da conversa. Cada nova mensagem estará sujeita ao processamento desse diálogo até ela fechar ou ser redirecionada a outro diálogo. 

No C#, é possível usar o `context.Wait()` para especificar o retorno de chamada a ser invocado da próxima vez que o usuário enviar uma mensagem. Para fechar um diálogo e removê-lo da pilha (enviando, assim, o usuário de volta para o diálogo anterior na pilha), use `context.Done()`. Encerre todos os métodos de diálogo com `context.Wait()`, `context.Fail()`, `context.Done()` ou alguma diretiva de redirecionamento, como `context.Forward()` ou `context.Call()`. Um método de diálogo que não encerrar com uma dessas opções resultará em um erro (porque a estrutura não saberá qual ação realizar da próxima vez que o usuário enviar uma mensagem).

## <a name="passing-state-between-dialogs"></a>Transmitir o estado entre diálogos

Embora seja possível armazenar o estado no estado do bot, também é possível transmitir dados entre diferentes diálogos sobrecarregando o seu construtor de classe de diálogo.

```cs
[Serializable]
public class AgeDialog : IDialog<int>
{
    private string name;

    public AgeDialog(string name)
    {
        this.name = name;
    }
}
 ```

Chamar o código do diálogo, transmitir o valor no nome do usuário.

```cs
private async Task NameDialogResumeAfter(IDialogContext context, IAwaitable<string> result)
{
    try
    {
        this.name = await result;
        context.Call(new AgeDialog(this.name), this.AgeDialogResumeAfter);
    }
    catch (TooManyAttemptsException)
    {
        await context.PostAsync("I'm sorry, I'm having issues understanding you. Let's try again.");
        await this.SendWelcomeMessageAsync(context);
    }
}
```

## <a name="sample-code"></a>Exemplo de código 

Confira um exemplo completo que mostra como gerenciar uma conversa usando diálogos no SDK do Construtor de Bot para .NET em <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-BasicMultiDialog" target="_blank">Exemplo de Diálogo Múltiplo Básico</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

- [Diálogos](bot-builder-dotnet-dialogs.md)
- [Projetar e controlar o fluxo da conversa](../bot-service-design-conversation-flow.md)
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-BasicMultiDialog" target="_blank">Exemplo de Diálogo Múltiplo Básico (GitHub)</a>
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Construtor de Bot para .NET</a>
