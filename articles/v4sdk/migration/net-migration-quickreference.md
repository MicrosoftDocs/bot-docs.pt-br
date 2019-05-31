---
title: Referência rápida da migração do .NET v3 para v4 | Microsoft Docs
description: Uma descrição das principais diferenças no SDK v3 e v4 do Bot Framework para .NET.
keywords: .net, migração de bot, caixas de diálogo, bot v3
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 93f660820407d6caf4c29efb3c128851059f3b02
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215571"
---
# <a name="net-migration-quick-reference"></a>Referência rápida da migração do .NET

O SDK v4 do BotBuilder para .NET introduz várias mudanças fundamentais que afetam como os bots são criados. O objetivo deste guia é fornecer uma referência rápida para destacar as diferenças comuns na realização de tarefas nos SDKs v3 e v4.

- A forma como as informações passam entre canais e um bot foi alterada. No V3, você usava o objeto _Conversation_ e o método _SendAsync_ para processar uma mensagem. O Autofac era extensivamente usado para carregar várias dependências. No v4, você usa os objetos_Adapter_ e _TurnContext_ para processar uma mensagem, e pode usar a biblioteca de injeção de dependência de sua escolha.

- Além disso, as instâncias de caixa de diálogo e bot foram ainda mais separadas. No v3, as caixas de diálogo foram desenvolvidas dentro do núcleo do SDK e a pilha foi manipulada internamente. As caixas de diálogo filho foram carregadas com os métodos _Call_ e _Forward_. Agora, no v4, você passa as caixas de diálogo para as instâncias de bot como argumentos, fornecendo mais flexibilidade de composição e controle do desenvolvedor sobre a pilha de caixas de diálogo. As caixas de diálogo filho são carregadas com os métodos _BeginDialogAsync_ e _ReplaceDialogAsync_.

- Além disso, o v4 fornece uma classe `ActivityHandler`, que ajuda a automatizar o manuseio de tipos diferentes de atividades, como _message_, _conversation update_ e _event_.

Essas melhorias resultam em mudanças na sintaxe para desenvolvimento de bots no .NET, especialmente com relação à criação de objetos de bot, definição de caixas de diálogo e lógica de manuseio de evento de codificação.

O restante deste tópico compara as construções do SDK v3 do Bot Framework para .NET com seu equivalente no v4.

## <a name="to-process-incoming-messages"></a>Para processar mensagens de entrada

### <a name="v3"></a>v3

```cs
[BotAuthentication]
public class MessagesController : ApiController
{
    public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
    {
        if (activity.GetActivityType() == ActivityTypes.Message)
        {
            await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }
}
```

### <a name="v4"></a>v4

```cs
[Route("api/messages")]
[ApiController]
public class BotController : ControllerBase
{
    private readonly IBotFrameworkHttpAdapter Adapter;
    private readonly IBot Bot;

    public BotController(IBotFrameworkHttpAdapter adapter, IBot bot)
    {
        Adapter = adapter;
        Bot = bot;
    }

    [HttpPost]
    public async Task PostAsync()
    {
        await Adapter.ProcessAsync(Request, Response, Bot);
    }
}
```

## <a name="to-send-a-message-to-a-user"></a>Para enviar uma mensagem para um usuário

### <a name="v3"></a>v3

```cs
await context.PostAsync("Hello and welcome to the help desk bot.");
```

### <a name="v4"></a>v4

```cs
await turnContext.SendActivityAsync("Hello and welcome to the help desk bot.");
```

## <a name="to-load-a-root-dialog"></a>Para carregar uma caixa de diálogo raiz

### <a name="v3"></a>v3

```cs
await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
```

### <a name="v4"></a>v4

```cs
// Create a DialogExtensions class with a Run method.
public static class DialogExtensions
{
    public static async Task Run(
        this Dialog dialog,
        ITurnContext turnContext,
        IStatePropertyAccessor<DialogState> accessor,
        CancellationToken cancellationToken)
    {
        var dialogSet = new DialogSet(accessor);
        dialogSet.Add(dialog);

        var dialogContext = await dialogSet.CreateContextAsync(turnContext, cancellationToken);

        var results = await dialogContext.ContinueDialogAsync(cancellationToken);
        if (results.Status == DialogTurnStatus.Empty)
        {
            await dialogContext.BeginDialogAsync(dialog.Id, null, cancellationToken);
        }
    }
}

// Call it from the ActivityHandler's OnMessageActivityAsync override
protected override async Task OnMessageActivityAsync(
    ITurnContext<IMessageActivity> turnContext,
    CancellationToken cancellationToken)
{
    // Run the Dialog with the new message Activity.
    await Dialog.Run(
        turnContext,
        ConversationState.CreateProperty<DialogState>("DialogState"),
        cancellationToken);
}
```

## <a name="to-start-a-child-dialog"></a>Para iniciar uma caixa de diálogo filho

### <a name="v3"></a>v3

```cs
context.Call(new NextDialog(), this.ResumeAfterNextDialog);
```

ou o

```cs
await context.Forward(new NextDialog(), this.ResumeAfterNextDialog, message);
```

### <a name="v4"></a>v4

```cs
dialogContext.BeginDialogAsync("<child-dialog-id>", options);
```

ou o

```cs
dialogContext.ReplaceDialogAsync("<child-dialog-id>", options);
```

## <a name="to-end-a-dialog"></a>Para encerrar uma caixa de diálogo

### <a name="v3"></a>v3

```cs
context.Done(ReturnValue);
```

### <a name="v4"></a>v4

```cs
await context.EndDialogAsync(ReturnValue);
```

## <a name="to-prompt-a-user-for-input"></a>Para solicitar a entrada a um usuário

### <a name="v3"></a>v3

```cs
PromptDialog.Choice(
    context,
    this.OnOptionSelected,
    Options, PromptMessage,
    ErrorMessage,
    3,
    PromptStyle.PerLine);
```

### <a name="v4"></a>v4

```cs
// In the dialog's constructor, register the prompt, and waterfall steps.
AddDialog(new TextPrompt(nameof(TextPrompt)));
AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
{
    FirstStepAsync,
    SecondStepAsync,
}));

// The initial child Dialog to run.
InitialDialogId = nameof(WaterfallDialog);

// ...

// In the first step, invoke the prompt.
private async Task<DialogTurnResult> FirstStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    return await stepContext.PromptAsync(
        nameof(TextPrompt),
        new PromptOptions { Prompt = MessageFactory.Text("Please enter your destination.") },
        cancellationToken);
}

// In the second step, retrieve the Result from the stepContext.
private async Task<DialogTurnResult> SecondStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    var destination = (string)stepContext.Result;
}
```

## <a name="to-save-information-to-dialog-state"></a>Para salvar as informações para o estado da caixa de diálogo

### <a name="v3"></a>v3

Todas as caixas de diálogo e seus campos foram serializados automaticamente no v3.

### <a name="v4"></a>v4

```cs
// StepContext values are auto-serialized in V4, and scoped to the dialog.
stepContext.values.destination = destination;
```

## <a name="to-write-changes-in-state-to-the-persistance-layer"></a>Para gravar as alterações no estado para a camada de persistência

### <a name="v3"></a>v3

Os dados de estado são salvos automaticamente por padrão no final do turno.

### <a name="v4"></a>v4

```cs
// You now must explicitly save state changes before the end of the turn.
await this.conversationState.saveChanges(context, false);
await this.userState.saveChanges(context, false);
```

## <a name="to-create-and-register-state-storage"></a>Para criar e registrar o armazenamento do estado

### <a name="v3"></a>v3

```cs
// Autofac was used internally by the sdk, and state was automatic.
Conversation.UpdateContainer(
builder =>
{
    builder.RegisterModule(new AzureModule(Assembly.GetExecutingAssembly()));
    var store = new InMemoryDataStore();
    builder.Register(c => store)
        .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
        .AsSelf()
        .SingleInstance();
});
```

### <a name="v4"></a>v4

```cs
// Create the storage we'll be using for User and Conversation state.
// In-memory storage is great for testing purposes.
services.AddSingleton<IStorage, MemoryStorage>();

// Create the user state (used in this bot's Dialog implementation).
services.AddSingleton<UserState>();

// Create the conversation state (used by the Dialog system itself).
services.AddSingleton<ConversationState>();

// The dialog that will be run by the bot.
services.AddSingleton<MainDialog>();

// Create the bot as a transient. In this case the ASP.NET controller is expecting an IBot.
services.AddTransient<IBot, DialogBot>();

// In the bot's ActivityHandler implementation, call SaveChangesAsync after the OnTurnAsync completes.
public class DialogBot : ActivityHandler
{
    protected readonly Dialog Dialog;
    protected readonly BotState ConversationState;
    protected readonly BotState UserState;

    public DialogBot(ConversationState conversationState, UserState userState, Dialog dialog)
    {
        ConversationState = conversationState;
        UserState = userState;
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        // Save any state changes that might have ocurred during the turn.
        await ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
        await UserState.SaveChangesAsync(turnContext, false, cancellationToken);
    }

    protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
    {
        // Run the dialog, passing in the message activity for this turn.
        await Dialog.Run(turnContext, ConversationState.CreateProperty<DialogState>("DialogState"), cancellationToken);
    }
}
```

## <a name="to-catch-an-error-thrown-from-a-dialog"></a>Para capturar um erro lançado de uma caixa de diálogo

### <a name="v3"></a>v3

```cs
// Create a custom IPostToBot implementation to catch exceptions.
public sealed class CustomPostUnhandledExceptionToUser : IPostToBot
{
    private readonly IPostToBot inner;
    private readonly IBotToUser botToUser;
    private readonly ResourceManager resources;
    private readonly System.Diagnostics.TraceListener trace;

    public CustomPostUnhandledExceptionToUser(IPostToBot inner, IBotToUser botToUser, ResourceManager resources, System.Diagnostics.TraceListener trace)
    {
        SetField.NotNull(out this.inner, nameof(inner), inner);
        SetField.NotNull(out this.botToUser, nameof(botToUser), botToUser);
        SetField.NotNull(out this.resources, nameof(resources), resources);
        SetField.NotNull(out this.trace, nameof(trace), trace);
    }

    async Task IPostToBot.PostAsync(IActivity activity, CancellationToken token)
    {
        try
        {
            await this.inner.PostAsync(activity, token);
        }
        catch (Exception ex)
        {
            try
            {
                // Log exception and send custom error message here.
                await this.botToUser.PostAsync("custom error message");
            }
            catch (Exception inner)
            {
                this.trace.WriteLine(inner);
            }

            throw;
        }
    }
}

// Register this using AutoFac, replacing the default PostUnhandledExceptionToUser.
builder
  .RegisterType<CustomPostUnhandledExceptionToUser>()
  .Keyed<IPostToBot>(typeof(PostUnhandledExceptionToUser));
```

### <a name="v4"></a>v4

```cs
// Provide an error handler in your implementation of the BotFrameworkHttpAdapter.
public class AdapterWithErrorHandler : BotFrameworkHttpAdapter
{
    public AdapterWithErrorHandler(
        ICredentialProvider credentialProvider,
        ILogger<BotFrameworkHttpAdapter> logger,
        ConversationState conversationState = null)
        : base(credentialProvider)
    {
        OnTurnError = async (turnContext, exception) =>
        {
            // Log any leaked exception from the application.
            logger.LogError($"Exception caught : {exception.Message}");

            // Send a catch-all apology to the user.
            await turnContext.SendActivityAsync("Sorry, it looks like something went wrong.");

            if (conversationState != null)
            {
                try
                {
                    // Delete the conversation state for the current conversation, to prevent the
                    // bot from getting stuck in a error-loop caused by being in a bad state.
                    // Conversation state is similar to "cookie-state" in a web page.
                    await conversationState.DeleteAsync(turnContext);
                }
                catch (Exception e)
                {
                    logger.LogError(
                        $"Exception caught on attempting to Delete ConversationState : {e.Message}");
                }
            }
        };
    }
}
```

## <a name="to-process-different-activity-types"></a>Para processar tipos diferentes de atividade

### <a name="v3"></a>v3

```cs
// Within your MessageController, check the message type.
string messageType = activity.GetActivityType();
if (messageType == ActivityTypes.Message)
{
    await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
}
else if (messageType == ActivityTypes.DeleteUserData)
{
}
else if (messageType == ActivityTypes.ConversationUpdate)
{
}
else if (messageType == ActivityTypes.ContactRelationUpdate)
{
}
else if (messageType == ActivityTypes.Typing)
{
}
```

### <a name="v4"></a>v4

```cs
// In the bot's ActivityHandler implementation, override relevant methods.

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    // Handle message activities here.
}

protected override Task OnConversationUpdateActivityAsync(ITurnContext<IConversationUpdateActivity> turnContext, CancellationToken cancellationToken)
{
    // Handle conversation update activities in general here.
}

protected override Task OnEventActivityAsync(ITurnContext<IEventActivity> turnContext, CancellationToken cancellationToken)
{
    // Handle event activities in general here.
}
```