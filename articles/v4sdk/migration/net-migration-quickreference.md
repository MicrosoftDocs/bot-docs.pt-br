---
title: Referência rápida da migração do .NET v3 para v4 – Serviço de Bot
description: Uma descrição das principais diferenças no SDK v3 e v4 do Bot Framework para .NET.
keywords: .net, migração de bot, caixas de diálogo, bot v3
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f8569aa3d4e2e227e7b348675429e5f347b13329
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75791018"
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

ou

```cs
await context.Forward(new NextDialog(), this.ResumeAfterNextDialog, message);
```

### <a name="v4"></a>v4

```cs
dialogContext.BeginDialogAsync("<child-dialog-id>", options);
```

ou

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

## <a name="to-log-all-activities"></a>Para registrar todas as atividades

### <a name="v3"></a>v3

[IActivityLogger](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.history.iactivitylogger) foi usado.

```csharp
builder.RegisterType<ActivityLoggerImplementation>().AsImplementedInterfaces().InstancePerDependency(); 

public class ActivityLoggerImplementation : IActivityLogger
{
    async Task IActivityLogger.LogAsync(IActivity activity)
    {
        // Store the activity.
    }
}
```

### <a name="v4"></a>v4

Use [ITranscriptLogger](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.itranscriptlogger).

```csharp
var transcriptMiddleware = new TranscriptLoggerMiddleware(new TranscriptLoggerImplementation(Configuration.GetSection("StorageConnectionString").Value));
adapter.Use(transcriptMiddleware);

public class TranscriptLoggerImplementation : ITranscriptLogger
{
    async Task ITranscriptLogger.LogActivityAsync(IActivity activity)
    {
        // Store the activity.
    }
}
```

## <a name="to-add-bot-state-storage"></a>Para adicionar armazenamento de estado do bot

A interface para armazenar _dados de usuário_, _dados de conversas_ e _dados de conversas particulares_ foi alterada.

### <a name="v3"></a>v3

O estado foi mantido usando uma implementação `IBotDataStore` e injetando-a no sistema de estado do diálogo do SDK, usando o Autofac.  A Microsoft forneceu as classes `MemoryStorage`, `DocumentDbBotDataStore`, `TableBotDataStore` e `SqlBotDataStore` no [Microsoft.Bot.Builder.Azure](https://github.com/Microsoft/BotBuilder-Azure/).

[IBotDataStore<BotData>](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.dialogs.internals.ibotdatastore-1?view=botbuilder-dotnet-3.0) foi usado para manter os dados.

```csharp
Task<bool> FlushAsync(IAddress key, CancellationToken cancellationToken);
Task<T> LoadAsync(IAddress key, BotStoreType botStoreType, CancellationToken cancellationToken);
Task SaveAsync(IAddress key, BotStoreType botStoreType, T data, CancellationToken cancellationToken);
```

```csharp
var dbPath = ConfigurationManager.AppSettings["DocDbPath"];
var dbKey = ConfigurationManager.AppSettings["DocDbKey"];
var docDbUri = new Uri(dbPath);
var storage = new DocumentDbBotDataStore(docDbUri, dbKey);
builder.Register(c => storage)
                .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
                .AsSelf()
                .SingleInstance();
```

### <a name="v4"></a>v4

A camada de armazenamento utiliza a interface `IStorage`. Especifique o objeto de camada de armazenamento ao criar cada objeto de gerenciamento de estado para seu bot, como `UserState`, `ConversationState` ou `PrivateConversationState`. O objeto de gerenciamento de estado fornece chaves à camada de armazenamento subjacente e também atua como um gerenciador de propriedades. Por exemplo, utilize `IPropertyManager.CreateProperty<T>(string name)` para criar um acessador de propriedade de estado.  Esses acessadores de propriedade são usados para recuperar e armazenar os valores dentro e fora do armazenamento subjacente do bot.

Use [IStorage](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.istorage?view=botbuilder-dotnet-stable) para manter os dados.

```csharp
Task DeleteAsync(string[] keys, CancellationToken cancellationToken = default(CancellationToken));
Task<IDictionary<string, object>> ReadAsync(string[] keys, CancellationToken cancellationToken = default(CancellationToken));
Task WriteAsync(IDictionary<string, object> changes, CancellationToken cancellationToken = default(CancellationToken));
```

```csharp
var storageOptions = new CosmosDbPartitionedStorageOptions()
{
    AuthKey = configuration["cosmosKey"],
    ContainerId = configuration["cosmosContainer"],
    CosmosDbEndpoint = configuration["cosmosPath"],
    DatabaseId = configuration["cosmosDatabase"]
};

IStorage dataStore = new CosmosDbPartitionedStorage(storageOptions);
var conversationState = new ConversationState(dataStore);
services.AddSingleton(conversationState);

```

> [!NOTE]
> Ao usar `CosmosDbPartitionedStorage`, você é responsável por criar um banco de dados e fornecer o ponto de extremidade do Cosmos DB, a chave de autorização e a ID do banco de dados, conforme mostrado acima. Você deve apenas especificar uma ID para o contêiner. O bot o criará para você, garantindo que ele esteja configurado corretamente para armazenar o estado do bot. Se você criar o contêiner por conta própria, verifique se a chave de partição está definida para **/id** e defina a propriedade `CosmosDbPartitionedStorageOptions.ContainerId`.

## <a name="to-use-form-flow"></a>Para usar Form Flow

### <a name="v3"></a>v3

O [Microsoft.Bot.Builder.FormFlow](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.formflow?view=botbuilder-dotnet-3.0) foi incluído no núcleo do SDK do Bot Builder.

### <a name="v4"></a>v4

O [Bot.Builder.Community.Dialogs.FormFlow](https://www.nuget.org/packages/Bot.Builder.Community.Dialogs.FormFlow/) agora é uma biblioteca do Bot Builder Community.  A fonte está disponível no [repositório](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Dialogs.FormFlow) da comunidade.

## <a name="to-use-luisdialog"></a>Para usar LuisDialog

### <a name="v3"></a>v3

O [Microsoft.Bot.Builder.Dialogs.LuisDialog](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.dialogs.luisdialog-1?view=botbuilder-dotnet-3.0) foi incluído no núcleo do SDK do Bot Builder.

### <a name="v4"></a>v4

O [Bot.Builder.Community.Dialogs.Luis](https://www.nuget.org/packages/Bot.Builder.Community.Dialogs.Luis/) agora é uma biblioteca do Bot Builder Community.  A fonte está disponível no [repositório](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Dialogs.Luis) da comunidade.

## <a name="to-use-qna-maker"></a>Como usar o QnA Maker

### <a name="v3"></a>v3

```csharp
[Serializable]
[QnAMaker("QnAEndpointKey", "QnAKnowledgebaseId", <ScoreThreshold>, <TotalResults>, "QnAEndpointHostName")]
public class SimpleQnADialog : QnAMakerDialog
{
}
```

### <a name="v4"></a>v4

```csharp
public class QnABot : ActivityHandler
{
  private readonly IConfiguration _configuration;
  private readonly ILogger<QnABot> _logger;
  private readonly IHttpClientFactory _httpClientFactory;

  public QnABot(IConfiguration configuration, ILogger<QnABot> logger, IHttpClientFactory httpClientFactory)
  {
    _configuration = configuration;
    _logger = logger;
    _httpClientFactory = httpClientFactory;
  }

  protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
  {
    var httpClient = _httpClientFactory.CreateClient();

    var qnaMaker = new QnAMaker(new QnAMakerEndpoint
    {
      KnowledgeBaseId = _configuration["QnAKnowledgebaseId"],
      EndpointKey = _configuration["QnAEndpointKey"],
      Host = _configuration["QnAEndpointHostName"]
    },
    null,
    httpClient);

    _logger.LogInformation("Calling QnA Maker");

    // The actual call to the QnA Maker service.
    var response = await qnaMaker.GetAnswersAsync(turnContext);
    if (response != null && response.Length > 0)
    {
      await turnContext.SendActivityAsync(MessageFactory.Text(response[0].Answer), cancellationToken);
    }
    else
    {
      await turnContext.SendActivityAsync(MessageFactory.Text("No QnA Maker answers were found."), cancellationToken);
    }
  }
}
```
