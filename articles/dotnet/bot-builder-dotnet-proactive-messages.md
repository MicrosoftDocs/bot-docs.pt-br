---
title: Enviar mensagens proativas (C# v3) – Serviço de Bot
description: Saiba como usar os bots para enviar mensagens proativas. Consulte exemplos de código que usam a versão 3 do SDK do bot Framework para mensagens pró-ativas baseadas em caixa de diálogo e ad hoc.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 0aa7bdc03f072f1a195b81911b7badd69e8f1e06
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360799"
---
# <a name="send-proactive-messages"></a>Enviar mensagens proativas

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-proactive-messages.md)
> - [Node.js](../nodejs/bot-builder-nodejs-proactive-messages.md)

[!INCLUDE [Introduction to proactive messages - part 1](../includes/snippet-proactive-messages-intro-1.md)]

## <a name="types-of-proactive-messages"></a>Tipos de mensagens proativas 

[!INCLUDE [Introduction to proactive messages - part 2](../includes/snippet-proactive-messages-intro-2.md)]

## <a name="send-an-ad-hoc-proactive-message"></a>Enviar uma mensagem proativa ad hoc

Os exemplos de código a seguir mostram como enviar uma mensagem proativa ad hoc com o SDK do Bot Framework para .NET.

Para poder enviar uma mensagem ad hoc a um usuário, o bot deverá primeiro coletar e armazenar algumas informações sobre o usuário da conversa atual. 

```cs
public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
{
    var message = await result;
    
    // Extract data from the user's message that the bot will need later to send an ad hoc message to the user. 
    // Store the extracted data in a custom class "ConversationStarter" (not shown here).

    ConversationStarter.toId = message.From.Id;
    ConversationStarter.toName = message.From.Name;
    ConversationStarter.fromId = message.Recipient.Id;
    ConversationStarter.fromName = message.Recipient.Name;
    ConversationStarter.serviceUrl = message.ServiceUrl;
    ConversationStarter.channelId = message.ChannelId;
    ConversationStarter.conversationId = message.Conversation.Id;

    // (Save this information somewhere that it can be accessed later, such as in a database.)

    await context.PostAsync("Hello user, good to meet you! I now know your address and can send you notifications in the future.");
    context.Wait(MessageReceivedAsync);
}
```
> [!NOTE]
> Por simplicidade, este exemplo não especifica como armazenar os dados do usuário. Não importa como os dados são armazenados, contanto que o bot possa recuperá-los posteriormente.

Agora que os dados foram armazenados, o bot poderá simplesmente recuperar os dados, construir a mensagem proativa ad hoc e enviá-la. 

```cs
// Use the data stored previously to create the required objects.
var userAccount = new ChannelAccount(toId,toName);
var botAccount = new ChannelAccount(fromId, fromName);
var connector = new ConnectorClient(new Uri(serviceUrl));

// Create a new message.
IMessageActivity message = Activity.CreateMessageActivity();
if (!string.IsNullOrEmpty(conversationId) && !string.IsNullOrEmpty(channelId))  
{
    // If conversation ID and channel ID was stored previously, use it.
    message.ChannelId = channelId;
}
else
{
    // Conversation ID was not stored previously, so create a conversation. 
    // Note: If the user has an existing conversation in a channel, this will likely create a new conversation window.
    conversationId = (await connector.Conversations.CreateDirectConversationAsync( botAccount, userAccount)).Id;
}

// Set the address-related properties in the message and send the message.
message.From = botAccount;
message.Recipient = userAccount;
message.Conversation = new ConversationAccount(id: conversationId);
message.Text = "Hello, this is a notification";
message.Locale = "en-us";
await connector.Conversations.SendToConversationAsync((Activity)message);
```

> [!NOTE]
> Se o bot especificar uma ID de conversa armazenada anteriormente, a mensagem provavelmente será entregue ao usuário na janela de conversa existente no cliente. Se o bot gerar uma nova ID de conversa, a mensagem será entregue ao usuário em uma nova janela de conversa no cliente, desde que haja suporte para várias janelas de conversa. 

## <a name="send-a-dialog-based-proactive-message"></a>Enviar uma mensagem proativa baseada em diálogo

Os exemplos de código a seguir mostram como enviar uma mensagem proativa baseada em diálogo usando o SDK do Bot Framework para .NET.

Para poder enviar uma mensagem proativa baseada em diálogo a um usuário, o bot deverá primeiro coletar e salvar as informações da conversa atual. 

```cs
public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
{
    var message = await result;
    
    // Store information about this specific point the conversation, so that the bot can resume this conversation later.
    var conversationReference = message.ToConversationReference();
    ConversationStarter.conversationReference = JsonConvert.SerializeObject(conversationReference);

    await context.PostAsync("Greetings, user! I now know how to start a proactive message to you."); 
    context.Wait(MessageReceivedAsync);
}
```

Quando for a hora de enviar a mensagem, o bot criará um novo diálogo e o adicionará ao topo da pilha de diálogos. O novo diálogo assume o controle da conversa, entrega a mensagem proativa, fecha e, em seguida, retorna o controle para o diálogo anterior na pilha. 

```cs
// This will interrupt the conversation and send the user to SurveyDialog, then wait until that's done 
public static async Task Resume() 
{
    // Recreate the message from the conversation reference that was saved previously.
    var message = JsonConvert.DeserializeObject<ConversationReference>(conversationReference).GetPostToBotMessage(); 
    var client = new ConnectorClient(new Uri(message.ServiceUrl));

    // Create a scope that can be used to work with state from bot framework.
    using (var scope = DialogModule.BeginLifetimeScope(Conversation.Container, message))
    {
        var botData = scope.Resolve<IBotData>();
        await botData.LoadAsync(CancellationToken.None);

        // This is our dialog stack.
        var task = scope.Resolve<IDialogTask>();

        // Create the new dialog and add it to the stack.
        var dialog = new SurveyDialog();
        // interrupt the stack. This means that we're stopping whatever conversation that is currently happening with the user
        // Then adding this stack to run and once it's finished, we will be back to the original conversation
        task.Call(dialog.Void<object, IMessageActivity>(), null);
        
        await task.PollAsync(CancellationToken.None);

        // Flush the dialog stack back to its state store.
        await botData.FlushAsync(CancellationToken.None);        
    }
}
```
O `SurveyDialog` controla a conversa até que termine. Quando a tarefa é finalizada, ele chama `context.Done` e fecha, retornando o controle para o diálogo anterior. 

```cs
[Serializable]
public class SurveyDialog : IDialog<object>
{
    public async Task StartAsync(IDialogContext context)
    {
        await context.PostAsync("Hello, I'm the survey dialog. I'm interrupting your conversation to ask you a question. Type \"done\" to resume");

        context.Wait(this.MessageReceivedAsync);
    }
    public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
    {
        if ((await result).Text == "done")
        {
            await context.PostAsync("Great, back to the original conversation!");
            context.Done(String.Empty); // Finish this dialog.
        }
        else
        {
            await context.PostAsync("I'm still on the survey until you type \"done\"");
            context.Wait(MessageReceivedAsync); // Not done yet.
        }
    }
}
```

## <a name="sample-code"></a>Código de exemplo

Veja um exemplo completo mostrando como enviar mensagens proativas usando o SDK do Bot Framework para .NET em <a href="https://aka.ms/proactive-messaging-cs-v3 " target="_blank">Exemplo de mensagens proativas</a> no GitHub. No exemplo de Mensagens Proativas, <a href="https://aka.ms/proactive-sendmessage-cs-v3 " target="_blank">simpleSendMessage</a> mostra como enviar uma mensagem proativa ad-hoc e <a href="https://aka.ms/proactive-newdialog-cs-v3 " target="_blank">startNewDialog</a> mostra como enviar uma mensagem proativa baseada em diálogo. 

## <a name="additional-resources"></a>Recursos adicionais

- [Design e controle de fluxo da conversa](../bot-service-design-conversation-flow.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-proactiveMessages" target="_blank">Exemplo de mensagens proativas (GitHub)</a>

