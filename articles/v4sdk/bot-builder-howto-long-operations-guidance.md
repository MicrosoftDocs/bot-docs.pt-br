---
title: Gerenciar um serviço de bot de operação de execução longa
description: Saiba como lidar com operações longas em um bot.
keywords: operações longas, tempo limite, 15 segundos
author: ericdahlvang
ms.author: erdahlva
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/30/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 75e1bfe8985ca29caad6415d74e04de11eac593b
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824306"
---
# <a name="manage-a-long-running-operation"></a>Gerenciar uma operação de execução prolongada

[!INCLUDE[applies-to](../includes/applies-to.md)]

A manipulação adequada de operações de longa execução é um aspecto importante de um bot robusto. Quando o serviço de bot do Azure envia uma atividade ao bot de um canal, espera-se que o bot processe a atividade rapidamente. Se o bot não concluir a operação dentro de 10 a 15 segundos, dependendo do canal, o serviço de bot do Azure atingirá o tempo limite e relatará para o cliente a `504:GatewayTimeout` , conforme descrito em [como os bots funcionam][concept-basics].

Este artigo descreve como usar um serviço externo para executar a operação e notificar o bot quando ele for concluído.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Familiaridade com [prompts em caixas de diálogo de cascata](bot-builder-concept-waterfall-dialogs.md#prompts) e [mensagens proativas](bot-builder-howto-proactive-message.md).
- Familiaridade com o [armazenamento de filas do Azure](/azure/storage/queues/storage-queues-introduction) e [Azure Functions script C#](/azure/azure-functions/functions-reference-csharp).
- Uma cópia do exemplo de **solicitação de múltipla ativação** em [**C#**](https://aka.ms/cs-multi-prompts-sample).

## <a name="about-this-sample"></a>Sobre este exemplo

Este artigo começa com o bot de exemplo de prompt de múltipla e adiciona o código para executar operações de longa execução. Ele também demonstra como responder a um usuário após a conclusão da operação. No exemplo atualizado:

- O bot pergunta ao usuário qual operação de longa execução executar.
- O bot recebe uma atividade do usuário e determina qual operação deve ser executada.
- O bot notifica o usuário de que a operação levará algum tempo e enviará a operação para uma função C#.
  - O bot salva o estado, indicando que há uma operação em andamento.
  - Enquanto a operação está em execução, o bot responde às mensagens do usuário, notificando-as de que a operação ainda está em andamento.
  - Azure Functions gerencia a operação de execução longa e envia uma `event` atividade para o bot, notificando-o de que a operação foi concluída.
- O bot retoma a conversa e envia uma mensagem proativa para notificar o usuário de que a operação foi concluída. Em seguida, o bot limpa o estado da operação mencionado anteriormente.

Este exemplo define uma `LongOperationPrompt` classe derivada da `ActivityPrompt` classe abstrata. Quando o `LongOperationPrompt` enfileira a atividade a ser processada, ela inclui uma opção do usuário na propriedade _valor_ da atividade. Essa atividade é consumida por Azure Functions, modificada e encapsulada em uma `event` atividade diferente antes de ser enviada de volta ao bot usando um cliente de linha direta. Dentro do bot, a atividade de evento é usada para retomar a conversa chamando o método _continue Conversation_ do adaptador. Em seguida, a pilha de diálogo é carregada e a `LongOperationPrompt` conclusão.

<!--The event activity is on a different conversation. Continue conversation is used to retrieve the bot's conversation with the user.-->

Este artigo aborda muitas tecnologias diferentes. Consulte a seção [recursos adicionais](#additional-resources) para obter links para artigos associados.

## <a name="create-an-azure-storage-account"></a>Criar uma conta do Armazenamento do Azure

Crie uma conta de armazenamento do Azure e recupere a cadeia de conexão. Será necessário adicionar a cadeia de conexão ao arquivo de configuração do bot.

Para obter mais informações, consulte [criar uma conta de armazenamento](/azure/storage/common/storage-account-create) e [copiar suas credenciais do portal do Azure](/azure/storage/queues/storage-dotnet-how-to-use-queues?tabs=dotnet#copy-your-credentials-from-the-azure-portal).

## <a name="create-a-bot-channels-registration"></a>Criar um Registro de Canais de Bot

1. Antes de criar o registro, configure ngrok e recupere uma URL a ser usada como o _ponto de extremidade do sistema de mensagens_ do bot durante a depuração local. O ponto de extremidade de mensagens será a URL de encaminhamento de HTTPS com `/api/messages/` anexado. Observe que a porta padrão para novos bots é 3978.

    Para obter mais informações, consulte como [depurar um bot usando o ngrok](https://docs.microsoft.com/azure/bot-service/bot-service-debug-channel-ngrok).

1. Crie um registro de canais de bot no portal do Azure ou com o CLI do Azure. Defina o ponto de extremidade do sistema de mensagens do bot para aquele que você criou com ngrok. Depois que o recurso de registro de canais de bot for criado, obtenha a ID e a senha do aplicativo Microsoft do bot. Habilite o canal de linha direta e recupere um segredo de linha direta. Você irá adicioná-los ao seu código de bot e à função C#.

    Para obter mais informações, consulte como [gerenciar um bot](../bot-service-manage-overview.md) e como [conectar um bot à linha direta](../bot-service-channel-connect-directline.md).

## <a name="create-the-c-function"></a>Criar a função C#

1. Crie um aplicativo Azure Functions com base na pilha .Net Core Runtime.

    Para obter mais informações, consulte como [criar um aplicativo de funções](/azure/azure-functions/functions-create-function-app-portal) e a [referência de script C# do Azure Functions](/azure/azure-functions/functions-reference-csharp).

1. Adicione uma `DirectLineSecret` configuração de aplicativo ao aplicativo de funções.

    Para obter mais informações, consulte como [gerenciar seu aplicativo de funções](/azure/azure-functions/functions-how-to-use-azure-function-app-settings).

1. Dentro do Aplicativo de funções, adicione uma função com base no [modelo de armazenamento de filas do Azure](/azure/azure-functions/functions-bindings-storage-queue-trigger).

    Defina o nome de fila desejado e escolha o `Azure Storage Account` criado em uma etapa anterior. Esse nome de fila também será colocado naappsettings.jsdo bot ** no** arquivo.

1. Adicione um arquivo **Function. proj** à função.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
        <PropertyGroup>
            <TargetFramework>netstandard2.0</TargetFramework>
        </PropertyGroup>

        <ItemGroup>
            <PackageReference Include="Microsoft.Bot.Connector.DirectLine" Version="3.0.2" />
            <PackageReference Include="Microsoft.Rest.ClientRuntime" Version="2.3.4" />
        </ItemGroup>
    </Project>
    ```

1. **Execute update. CSX** com o seguinte código:

    ```csharp
    #r "Newtonsoft.Json"

    using System;
    using System.Net.Http;
    using System.Text;
    using Newtonsoft.Json;
    using Microsoft.Bot.Connector.DirectLine;
    using System.Threading;

    public static async Task Run(string queueItem, ILogger log)
    {
        log.LogInformation($"C# Queue trigger function processing");

        JsonSerializerSettings jsonSettings = new JsonSerializerSettings() { NullValueHandling = NullValueHandling.Ignore };
        var originalActivity =  JsonConvert.DeserializeObject<Activity>(queueItem, jsonSettings);
        // Perform long operation here....
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(15));

        if(originalActivity.Value.ToString().Equals("option 1", CompareOptions.OrdinalIgnoreCase))
        {
            originalActivity.Value = " (Result for long operation one!)";
        }
        else if(originalActivity.Value.ToString().Equals("option 2", CompareOptions.OrdinalIgnoreCase))
        {
            originalActivity.Value = " (A different result for operation two!)";
        }

        originalActivity.Value = "LongOperationComplete:" + originalActivity.Value;
        var responseActivity =  new Activity("event");
        responseActivity.Value = originalActivity;
        responseActivity.Name = "LongOperationResponse";
        responseActivity.From = new ChannelAccount("GenerateReport", "AzureFunction");

        var directLineSecret = Environment.GetEnvironmentVariable("DirectLineSecret");
        using(DirectLineClient client = new DirectLineClient(directLineSecret))
        {
            var conversation = await client.Conversations.StartConversationAsync();
            await client.Conversations.PostActivityAsync(conversation.ConversationId, responseActivity);
        }

        log.LogInformation($"Done...");
    }
    ```

## <a name="create-the-bot"></a>Criar o bot

1. Comece com uma cópia do exemplo de prompt do C# com [vários folheios](https://aka.ms/cs-multi-prompts-sample) .
1. Adicione o pacote NuGet **Azure. Storage. Queues** ao seu projeto. <!--For more information, see [How to Use Queues](https://docs.microsoft.com/azure/storage/queues/storage-dotnet-how-to-use-queues).-->
1. Adicione a cadeia de conexão para a conta de armazenamento do Azure criada anteriormente e o nome da fila de armazenamento ao arquivo de configuração do bot.

    Verifique se o nome da fila é o mesmo usado para criar a função de gatilho de fila anteriormente. Além disso, adicione os valores para as `MicrosoftAppId` `MicrosoftAppPassword` Propriedades e que você gerou anteriormente ao criar o recurso de registro de canais de bot.

    **appsettings.json**

    ```json
    {
      "MicrosoftAppId": "<your-bot-app-id>",
      "MicrosoftAppPassword": "<your-bot-app-password>",
      "StorageQueueName": "<your-azure-storage-queue-name>",
      "QueueStorageConnection": "<your-storage-connection-string>"
    }
    ```

1. Adicione um `IConfiguration` parâmetro a **DialogBot.cs** para recuperar o `MicrsofotAppId` . Além disso, adicione um `OnEventActivityAsync` manipulador para o `LongOperationResponse` da função do Azure.

    **Bots\DialogBot.cs**

    ```csharp
    protected readonly IStatePropertyAccessor<DialogState> DialogState;
    protected readonly Dialog Dialog;
    protected readonly BotState ConversationState;
    protected readonly ILogger Logger;
    private readonly string _botId;

    /// <summary>
    /// Create an instance of <see cref="DialogBot{T}"/>.
    /// </summary>
    /// <param name="configuration"><see cref="IConfiguration"/> used to retrieve MicrosoftAppId
    /// which is used in ContinueConversationAsync.</param>
    /// <param name="conversationState"><see cref="ConversationState"/> used to store the DialogStack.</param>
    /// <param name="dialog">The RootDialog for this bot.</param>
    /// <param name="logger"><see cref="ILogger"/> to use.</param>
    public DialogBot(IConfiguration configuration, ConversationState conversationState, T dialog, ILogger<DialogBot<T>> logger)
    {
        _botId = configuration["MicrosoftAppId"] ?? Guid.NewGuid().ToString();
        ConversationState = conversationState;
        Dialog = dialog;
        Logger = logger;
        DialogState = ConversationState.CreateProperty<DialogState>(nameof(DialogState));
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default)
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        // Save any state changes that might have occurred during the turn.
        await ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
    }

    protected override async Task OnEventActivityAsync(ITurnContext<IEventActivity> turnContext, CancellationToken cancellationToken)
    {
        // The event from the Azure Function will have a name of 'LongOperationResponse'
        if (turnContext.Activity.ChannelId == Channels.Directline && turnContext.Activity.Name == "LongOperationResponse")
        {
            // The response will have the original conversation reference activity in the .Value
            // This original activity was sent to the Azure Function via Azure.Storage.Queues in AzureQueuesService.cs.
            var continueConversationActivity = (turnContext.Activity.Value as JObject)?.ToObject<Activity>();
            await turnContext.Adapter.ContinueConversationAsync(_botId, continueConversationActivity.GetConversationReference(), async (context, cancellation) =>
            {
                Logger.LogInformation("Running dialog with Activity from LongOperationResponse.");

                // ContinueConversationAsync resets the .Value of the event being continued to Null, 
                //so change it back before running the dialog stack. (The .Value contains the response 
                //from the Azure Function)
                context.Activity.Value = continueConversationActivity.Value;
                await Dialog.RunAsync(context, DialogState, cancellationToken);

                // Save any state changes that might have occurred during the inner turn.
                await ConversationState.SaveChangesAsync(context, false, cancellationToken);
            }, cancellationToken);
        }
        else
        {
            await base.OnEventActivityAsync(turnContext, cancellationToken);
        }
    }
    ```

1. Crie um serviço filas do Azure para enfileirar atividades que precisam ser processadas.

    **AzureQueuesService.cs**

    ```csharp
    /// <summary>
    /// Service used to queue messages to an Azure.Storage.Queues.
    /// </summary>
    public class AzureQueuesService
    {
        private static JsonSerializerSettings jsonSettings = new JsonSerializerSettings()
            {
                Formatting = Formatting.Indented,
                NullValueHandling = NullValueHandling.Ignore
            };

        private bool _createQueuIfNotExists = true;
        private readonly QueueClient _queueClient;

        /// <summary>
        /// Creates a new instance of <see cref="AzureQueuesService"/>.
        /// </summary>
        /// <param name="config"><see cref="IConfiguration"/> used to retrieve
        /// StorageQueueName and QueueStorageConnection from appsettings.json.</param>
        public AzureQueuesService(IConfiguration config)
        {
            var queueName = config["StorageQueueName"];
            var connectionString = config["QueueStorageConnection"];

            _queueClient = new QueueClient(connectionString, queueName);
        }

        /// <summary>
        /// Queue and Activity, with option in the Activity.Value to Azure.Storage.Queues
        ///
        /// <seealso cref="https://github.com/microsoft/botbuilder-dotnet/blob/master/libraries/Microsoft.Bot.Builder.Azure/Queues/ContinueConversationLater.cs"/>
        /// </summary>
        /// <param name="referenceActivity">Activity to queue after a call to GetContinuationActivity.</param>
        /// <param name="option">The option the user chose, which will be passed within the .Value of the activity queued.</param>
        /// <param name="cancellationToken">Cancellation token for the async operation.</param>
        /// <returns>Queued <see cref="Azure.Storage.Queues.Models.SendReceipt.MessageId"/>.</returns>
        public async Task<string> QueueActivityToProcess(Activity referenceActivity, string option, CancellationToken cancellationToken)
        {
            if (_createQueuIfNotExists)
            {
                _createQueuIfNotExists = false;
                await _queueClient.CreateIfNotExistsAsync().ConfigureAwait(false);
            }

            // create ContinuationActivity from the conversation reference.
            var activity = referenceActivity.GetConversationReference().GetContinuationActivity();
            // Pass the user's choice in the .Value
            activity.Value = option;

            var message = Convert.ToBase64String(Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(activity, jsonSettings)));

            // Aend ResumeConversation event, it will get posted back to us with a specific value, giving us 
            // the ability to process it and do the right thing.
            var reciept = await _queueClient.SendMessageAsync(message, cancellationToken).ConfigureAwait(false);
            return reciept.Value.MessageId;
        }
    }
    ```

## <a name="dialogs"></a>Diálogos

Remova a caixa de diálogo antiga e substitua-a por novas caixas de diálogo para dar suporte às operações.

1. Remova o arquivo **UserProfileDialog.cs** .
1. Adicione uma caixa de diálogo de prompt personalizada que pergunta ao usuário qual operação executar.

    **Dialogs\LongOperationPrompt.cs**

    ```csharp
    /// <summary>
    /// <see cref="ActivityPrompt"/> implementation which will queue an activity,
    /// along with the <see cref="LongOperationPromptOptions.LongOperationOption"/>,
    /// and wait for an <see cref="ActivityTypes.Event"/> with name of "ContinueConversation"
    /// and Value containing the text: "LongOperationComplete".
    ///
    /// The result of this prompt will be the received Event Activity, which is sent by
    /// the Azure Function after it finishes the long operation.
    /// </summary>
    public class LongOperationPrompt : ActivityPrompt
    {
        private readonly AzureQueuesService _queueService;

        /// <summary>
        /// Create a new instance of <see cref="LongOperationPrompt"/>.
        /// </summary>
        /// <param name="dialogId">Id of this <see cref="LongOperationPrompt"/>.</param>
        /// <param name="validator">Validator to use for this prompt.</param>
        /// <param name="queueService"><see cref="AzureQueuesService"/> to use for Enqueuing the activity to process.</param>
        public LongOperationPrompt(string dialogId, PromptValidator<Activity> validator, AzureQueuesService queueService) 
            : base(dialogId, validator)
        {
            _queueService = queueService;
        }

        public async override Task<DialogTurnResult> BeginDialogAsync(DialogContext dc, object options, CancellationToken cancellationToken = default)
        {
            // When the dialog begins, queue the option chosen within the Activity queued.
            await _queueService.QueueActivityToProcess(dc.Context.Activity, (options as LongOperationPromptOptions).LongOperationOption, cancellationToken);

            return await base.BeginDialogAsync(dc, options, cancellationToken);
        }

        protected override Task<PromptRecognizerResult<Activity>> OnRecognizeAsync(ITurnContext turnContext, IDictionary<string, object> state, PromptOptions options, CancellationToken cancellationToken = default)
        {
            var result = new PromptRecognizerResult<Activity>() { Succeeded = false };

            if(turnContext.Activity.Type == ActivityTypes.Event
                && turnContext.Activity.Name == "ContinueConversation"
                && turnContext.Activity.Value != null
                // Custom validation within LongOperationPrompt.  
                // 'LongOperationComplete' is added to the Activity.Value in the Queue consumer (See: Azure Function)
                && turnContext.Activity.Value.ToString().Contains("LongOperationComplete", System.StringComparison.InvariantCultureIgnoreCase))
            {
                result.Succeeded = true;
                result.Value = turnContext.Activity;
            }

            return Task.FromResult(result);
        }
    }
    ```

1. Adicione uma classe de opções de prompt para o prompt personalizado.

    **Dialogs\LongOperationPromptOptions.cs**

    ```csharp
    /// <summary>
    /// Options sent to <see cref="LongOperationPrompt"/> demonstrating how a value
    /// can be passed along with the queued activity.
    /// </summary>
    public class LongOperationPromptOptions : PromptOptions
    {
        /// <summary>
        /// This is a property sent through the Queue, and is used
        /// in the queue consumer (the Azure Function) to differentiate 
        /// between long operations chosen by the user.
        /// </summary>
        public string LongOperationOption { get; set; }
    }
    ```

1. Adicione a caixa de diálogo que usa o prompt personalizado para obter a opção do usuário e inicia a operação de execução longa.

    **Dialogs\LongOperationDialog.cs**

    ```csharp
    /// <summary>
    /// This dialog demonstrates how to use the <see cref="LongOperationPrompt"/>.
    ///
    /// The user is provided an option to perform any of three long operations.
    /// Their choice is then sent to the <see cref="LongOperationPrompt"/>.
    /// When the prompt completes, the result is received as an Activity in the
    /// final Waterfall step.
    /// </summary>
    public class LongOperationDialog : ComponentDialog
    {
        public LongOperationDialog(AzureQueuesService queueService)
            : base(nameof(LongOperationDialog))
        {
            // This array defines how the Waterfall will execute.
            var waterfallSteps = new WaterfallStep[]
            {
                OperationTimeStepAsync,
                LongOperationStepAsync,
                OperationCompleteStepAsync,
            };

            // Add named dialogs to the DialogSet. These names are saved in the dialog state.
            AddDialog(new WaterfallDialog(nameof(WaterfallDialog), waterfallSteps));
            AddDialog(new LongOperationPrompt(nameof(LongOperationPrompt), (vContext, token) =>
            {
                return Task.FromResult(vContext.Recognized.Succeeded);
            }, queueService));
            AddDialog(new ChoicePrompt(nameof(ChoicePrompt)));

            // The initial child Dialog to run.
            InitialDialogId = nameof(WaterfallDialog);
        }

        private static async Task<DialogTurnResult> OperationTimeStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
            // Running a prompt here means the next WaterfallStep will be run when the user's response is received.
            return await stepContext.PromptAsync(nameof(ChoicePrompt),
                new PromptOptions
                {
                    Prompt = MessageFactory.Text("Please select a long operation test option."),
                    Choices = ChoiceFactory.ToChoices(new List<string> { "option 1", "option 2", "option 3" }),
                }, cancellationToken);
        }

        private static async Task<DialogTurnResult> LongOperationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            var value = ((FoundChoice)stepContext.Result).Value;
            stepContext.Values["longOperationOption"] = value;

            var prompt = MessageFactory.Text("...one moment please....");
            // The reprompt will be shown if the user messages the bot while the long operation is being performed.
            var retryPrompt = MessageFactory.Text($"Still performing the long operation: {value} ... (is the Azure Function executing from the queue?)");
            return await stepContext.PromptAsync(nameof(LongOperationPrompt),
                                                        new LongOperationPromptOptions
                                                        {
                                                            Prompt = prompt,
                                                            RetryPrompt = retryPrompt,
                                                            LongOperationOption = value,
                                                        }, cancellationToken);
        }

        private static async Task<DialogTurnResult> OperationCompleteStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            stepContext.Values["longOperationResult"] = stepContext.Result;
            await stepContext.Context.SendActivityAsync(MessageFactory.Text($"Thanks for waiting. { (stepContext.Result as Activity).Value}"), cancellationToken);

            // Start over by replacing the dialog with itself.
            return await stepContext.ReplaceDialogAsync(nameof(WaterfallDialog), null, cancellationToken);
        }
    }
    ```

## <a name="register-services-and-dialog"></a>Registrar serviços e diálogo

No **Startup.cs**, atualize o `ConfigureServices` método para registrar o `LongOperationDialog` e adicionar o `AzureQueuesService` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers().AddNewtonsoftJson();

    // Create the Bot Framework Adapter with error handling enabled.
    services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();

    // In production, this should be a persistent storage provider.bot
    services.AddSingleton<IStorage>(new MemoryStorage());

    // Create the Conversation state. (Used by the Dialog system itself.)
    services.AddSingleton<ConversationState>();

    // The Dialog that will be run by the bot.
    services.AddSingleton<LongOperationDialog>();

    // Service used to queue into Azure.Storage.Queues
    services.AddSingleton<AzureQueuesService>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, DialogBot<LongOperationDialog>>();
}
```

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte ao seu bot e envie mensagens conforme mostrado a seguir.

    ![Exemplo de bot](./media/how-to-long-operations/long-operations-bot-example.png)

## <a name="additional-resources"></a>Recursos adicionais

| Ferramenta ou recurso | Recursos
| :--- | :---
| Funções do Azure | [Criar um aplicativo de funções](/azure/azure-functions/functions-create-function-app-portal)<br/>[Azure Functions script C#](/azure/azure-functions/functions-reference-csharp)<br/>[Gerenciar seu aplicativo de funções](/azure/azure-functions/functions-how-to-use-azure-function-app-settings)
| Portal do Azure | [Gerenciar um bot](../bot-service-manage-overview.md)<br/>[Conectar um bot à Linha Direta](../bot-service-channel-connect-directline.md)
| Armazenamento do Azure | [Armazenamento de Filas do Azure](/azure/storage/queues/storage-queues-introduction)<br/>[Criar uma conta de armazenamento](/azure/storage/common/storage-account-create)<br/>[Copiar suas credenciais no Portal do Azure](/azure/storage/queues/storage-dotnet-how-to-use-queues?tabs=dotnet#copy-your-credentials-from-the-azure-portal)<br/>[Como usar filas](https://docs.microsoft.com/azure/storage/queues/storage-dotnet-how-to-use-queues)
| Conceitos básicos do bot | [Como funcionam os bots][concept-basics]<br/>[Prompts em caixas de diálogo em cascata](bot-builder-concept-waterfall-dialogs.md#prompts)<br/>[Mensagens proativas](bot-builder-howto-proactive-message.md)
| ngrok | [Depurar um bot usando o ngrok](https://docs.microsoft.com/azure/bot-service/bot-service-debug-channel-ngrok)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-dialogs]: bot-builder-concept-dialog.md
