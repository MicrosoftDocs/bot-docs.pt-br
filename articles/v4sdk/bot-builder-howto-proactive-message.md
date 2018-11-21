---
title: Obter notificação de um bot | Microsoft Docs
description: Entenda como enviar mensagens de notificação
keywords: mensagem proativa, mensagem de notificação, notificação de bot,
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fac1e026ac92fcbe1b5c5bb9363c29e1d9e9b02a
ms.sourcegitcommit: b6327fa0b4547556d2d45d8910796e0c02948e43
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51681583"
---
# <a name="get-notification-from-a-bot"></a>Obter notificação de um bot

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Normalmente, cada mensagem que um bot envia ao usuário está diretamente relacionada à entrada anterior do usuário.
Em alguns casos, talvez o bot precise enviar ao usuário uma mensagem sem relação direta com o tópico atual da conversa ou com a última mensagem enviada pelo usuário. Esses tipos de mensagens são chamados _mensagens proativas_.

## <a name="uses"></a>Usos

Mensagens proativas podem ser útil em uma variedade de cenários. Se um bot definir um timer ou um lembrete, ele terá que notificar o usuário quando chegar a hora. Ou, se um bot receber uma notificação de um sistema externo, talvez precise informá-la imediatamente ao usuário. Por exemplo, se o usuário tiver solicitado ao bot o monitoramento do preço de um produto, se o preço do produto cair 20%, o bot poderá alertar o usuário. Ou, se o bot precisar de um tempo para compilar uma resposta para a pergunta do usuário, ele poderá informar ao usuário sobre o atraso e permitir que a conversa continue enquanto isso. Quando o bot terminar de compilar a resposta para a pergunta, compartilhará essas informações com o usuário.

Ao implementar mensagens proativas em seu bot:

- Não envie várias mensagens proativas em um período curto. Alguns canais impõem restrições sobre a frequência de envio de mensagens para o usuário de um bot, e desabilitarão o bot se ele violar essas restrições.
- Não envie mensagens proativas para usuários que ainda não interagiram com o bot ou que pediram contato com o bot por outros meios, como email ou SMS.

Uma **mensagem proativa ad hoc** é o tipo mais simples de mensagem proativa.
O bot apenas injeta a mensagem na conversa sempre que ele é disparado, sem considerar se o usuário está envolvido em um tópico separado de conversa com o bot e não tentará alterar a conversa de modo algum.

Para lidar com notificações de forma mais suave, considere outras maneiras de integrar a notificação no fluxo de conversa, como definir um sinalizador no estado da conversa ou adicionar a notificação a uma fila.

### <a name="prerequisites"></a>Pré-requisitos
- Uma cópia do **Exemplo de mensagens proativas** no [C#](https://aka.ms/proactive-sample-cs) ou no [JS](https://aka.ms/proactive-sample-js).
- Para o JS, instale o [Construtor de Bot](https://www.npmjs.com/package/botbuilder) para o Node.js


### <a name="about-the-sample-code"></a>Sobre o código de exemplo

Este exemplo de mensagens proativas modela as tarefas do usuário, o que pode demorar um período de tempo indeterminado. O bot armazena informações sobre a tarefa, informa ao usuário que voltará a contatá-lo quando a tarefa for concluída e permite que a conversa continue. Quando a tarefa for concluída, o bot enviará a mensagem de confirmação de forma proativa na conversa original.

#### <a name="define-job-data-and-state"></a>Definir o estado e os dados do trabalho

Nesse cenário, estamos acompanhando trabalhos arbitrários que podem ser criados por vários usuários em diferentes conversas. Precisaremos armazenar informações sobre cada trabalho, incluindo a referência da conversa e um identificador de trabalho. O que será necessário:
- A referência da conversa para podermos enviar a mensagem proativa para a conversa correta.
- Uma maneira de identificar os trabalhos. Neste exemplo, usamos um carimbo de data/hora simples.
- Armazenar o estado do trabalho independente da conversa ou do estado do usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

É necessário definir as classes de dados de trabalho e o estado do trabalho. Também precisamos registrar nosso bot e configurar um acessador de propriedade de estado para o log do trabalho.

#### <a name="define-a-class-for-job-data"></a>Definir uma classe para os dados de trabalho

A classe `JobLog` controla os dados de trabalho, indexados pelo número do trabalho (o carimbo de data/hora). A classe `JobLog` controla todos os trabalhos pendentes.  Cada trabalho é identificado por uma chave exclusiva. `Job data` descreve o estado de um trabalho e é definido como uma classe interna de um dicionário.

```csharp
public class JobLog : Dictionary<long, JobLog.JobData>
{
    public class JobData
    {
        // Gets or sets the time-stamp for the job.
        public long TimeStamp { get; set; } = 0;

        // Gets or sets a value indicating whether indicates whether the job has completed.
        public bool Completed { get; set; } = false;

        // Gets or sets the conversation reference to which to send status updates.
        public ConversationReference Conversation { get; set; }
    }
}
```

#### <a name="define-a-state-middleware-class"></a>Definir uma classe de middleware de estado

A classe **JobState** gerencia o estado do trabalho de forma independente do estado da conversa ou do usuário.

```csharp
using Microsoft.Bot.Builder;

/// A BotState for managing bot state for "bot jobs".
public class JobState : BotState
{
    // The key used to cache the state information in the turn context.
    private const string StorageKey = "ProactiveBot.JobState";

    // Initializes a new instance of the JobState class.
    public JobState(IStorage storage)
        : base(storage, StorageKey)
    {
    }

    // Gets the storage key for caching state information.
    protected override string GetStorageKey(ITurnContext turnContext) => StorageKey;
}
```

### <a name="register-the-bot-and-required-services"></a>Registrar o bot e os serviços necessários

O arquivo **Startup.cs** registra o bot e os serviços associados.

1. O método `ConfigureServices` registra o bot, incluindo o tratamento de erros e o gerenciamento de estado. Ele também registra o serviço de ponto de extremidade do bot e o acessador de estado do trabalho.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, everything stored in memory will be gone.
        IStorage dataStore = new MemoryStorage();

        // ...

        // Create Job State object.
        // The Job State object is where we persist anything at the job-scope.
        // Note: It's independent of any user or conversation.
        var jobState = new JobState(dataStore);

        // Make it available to our bot
        services.AddSingleton(sp => jobState);

        // Register the proactive bot.
        services.AddBot<ProactiveBot>(options =>
        {
            var secretKey = Configuration.GetSection("botFileSecret")?.Value;
            var botFilePath = Configuration.GetSection("botFilePath")?.Value;

            // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
            var botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
            services.AddSingleton(sp => botConfig ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

            // Retrieve current endpoint.
            var environment = _isProduction ? "production" : "development";
            var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
            if (!(service is EndpointService endpointService))
            {
                throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
            }

            options.CredentialProvider = new SimpleCredentialProvider(endpointService.AppId, endpointService.AppPassword);

            // Creates a logger for the application to use.
            ILogger logger = _loggerFactory.CreateLogger<ProactiveBot>();

            // Catches any errors that occur during a conversation turn and logs them.
            options.OnTurnError = async (context, exception) =>
            {
                logger.LogError($"Exception caught : {exception}");
                await context.SendActivityAsync("Sorry, it looks like something went wrong.");
            };

        });

        services.AddSingleton(sp =>
        {
            var config = BotConfiguration.Load(@".\BotConfiguration.bot");
            var endpointService = (EndpointService)config.Services.First(s => s.Type == "endpoint")
                                    ?? throw new InvalidOperationException(".bot file 'endpoint' must be configured prior to running.");

            return endpointService;
        });
    }
    ```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código no arquivo **index.js** faz o seguinte:
- Faz referência à classe do bot e ao arquivo **.bot**
- Cria o servidor HTTP, o adaptador do bot e os objetos de armazenamento
- Cria o bot e inicia o servidor, passando as atividades para o bot

```javascript
const restify = require('restify');
const path = require('path');

// Import required bot services. See https://aka.ms/bot-services to learn more about the different part of a bot.
const { BotFrameworkAdapter, BotState, MemoryStorage } = require('botbuilder');
const { BotConfiguration } = require('botframework-config');

const { ProactiveBot } = require('./bot');

// Read botFilePath and botFileSecret from .env file.
// Note: Ensure you have a .env file and include botFilePath and botFileSecret.
const ENV_FILE = path.join(__dirname, '.env');
require('dotenv').config({ path: ENV_FILE });

// Create HTTP server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }.`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator.`);
    console.log(`\nTo talk to your bot, open proactive-messages.bot file in the Emulator.`);
});

// .bot file path
const BOT_FILE = path.join(__dirname, (process.env.botFilePath || ''));

// Read the bot's configuration from a .bot file identified by BOT_FILE.
// This includes information about the bot's endpoints and configuration.
let botConfig;
try {
    botConfig = BotConfiguration.loadSync(BOT_FILE, process.env.botFileSecret);
} catch (err) {
    console.error(`\nError reading bot file. Please ensure you have valid botFilePath and botFileSecret set for your environment.`);
    console.error(`\n - The botFileSecret is available under appsettings for your Azure Bot Service bot.`);
    console.error(`\n - If you are running this bot locally, consider adding a .env file with botFilePath and botFileSecret.\n\n`);
    process.exit();
}

const DEV_ENVIRONMENT = 'development';

// Define the name of the bot, as specified in .bot file.
// See https://aka.ms/about-bot-file to learn more about .bot files.
const BOT_CONFIGURATION = (process.env.NODE_ENV || DEV_ENVIRONMENT);

// Load the configuration profile specific to this bot identity.
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);

// Create the adapter. See https://aka.ms/about-bot-adapter to learn more about using information from
// the .bot file when configuring your adapter.
const adapter = new BotFrameworkAdapter({
    appId: endpointConfig.appId || process.env.MicrosoftAppId,
    appPassword: endpointConfig.appPassword || process.env.MicrosoftAppPassword
});

// Define the state store for your bot. See https://aka.ms/about-bot-state to learn more about using MemoryStorage.
// A bot requires a state storage system to persist the dialog and user state between messages.
const memoryStorage = new MemoryStorage();

// Create state manager with in-memory storage provider.
const botState = new BotState(memoryStorage, () => 'proactiveBot.botState');

// Create the main dialog, which serves as the bot's main handler.
const bot = new ProactiveBot(botState, adapter);

// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (turnContext) => {
        // Route the message to the bot's main handler.
        await bot.onTurn(turnContext);
    });
});

// Catch-all for errors.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${ error }`);
    // Send a message to the user
    context.sendActivity(`Oops. Something went wrong!`);
};
```

---

### <a name="define-the-bot"></a>Define o bot

O usuário pode solicitar ao bot que crie e execute um trabalho para ele. Um serviço de trabalho separado poderia notificar o bot quando um trabalho é concluído. O bot é projetado para:

- Criar um trabalho em resposta a uma mensagem `run` ou `run job` do usuário.
- Mostrar todos os trabalhos registrados em resposta a uma mensagem `show` ou `show jobs` do usuário.
- Concluir um trabalho em resposta a um evento de _trabalho concluído_ que identifica o trabalho concluído.
- Simular um evento de trabalho concluído em resposta a uma mensagem `done <jobIdentifier>`.
- Quando o trabalho for concluído, enviar uma mensagem proativa para o usuário usando a conversa original.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O bot possui alguns aspectos:

- código de inicialização
- um manipulador de turnos
- métodos para criar e concluir os trabalhos

#### <a name="declare-the-class"></a>Declarar a classe

```csharp
namespace Microsoft.BotBuilderSamples
{
    // For each interaction from the user, an instance of this class is called.
    // This is a Transient lifetime service.  Transient lifetime services are created
    // each time they're requested. For each Activity received, a new instance of this
    // class is created. Objects that are expensive to construct, or have a lifetime
    // beyond the single Turn, should be carefully managed.
    public class ProactiveBot : IBot
    {
        // The name of events that signal that a job has completed.
        public const string JobCompleteEventName = "jobComplete";

        public const string WelcomeText = "Type 'run' or 'run job' to start a new job.\r\n" +
                                          "Type 'show' or 'show jobs' to display the job log.\r\n" +
                                          "Type 'done <jobNumber>' to complete a job.";
    }
}
```

#### <a name="add-initialization-code"></a>Adicionar código de inicialização

```csharp
private readonly JobState _jobState;
private readonly IStatePropertyAccessor<JobLog> _jobLogPropertyAccessor;

public ProactiveBot(JobState jobState, EndpointService endpointService)
{
    _jobState = jobState ?? throw new ArgumentNullException(nameof(jobState));
    _jobLogPropertyAccessor = _jobState.CreateProperty<JobLog>(nameof(JobLog));

    // Validate AppId.
    // Note: For local testing, .bot AppId is empty for the Bot Framework Emulator.
    AppId = string.IsNullOrWhiteSpace(endpointService.AppId) ? "1" : endpointService.AppId;
}

private string AppId { get; }
```

#### <a name="add-a-turn-handler"></a>Adicionar um manipulador de turnos

Cada bot deve implementar um manipulador de turnos. O adaptador encaminha as atividades para esse método.

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type != ActivityTypes.Message)
    {
        // Handle non-message activities.
        await OnSystemActivityAsync(turnContext);
    }
    else
    {
        // Get the job log.
        // The job log is a dictionary of all outstanding jobs in the system.
        JobLog jobLog = await _jobLogPropertyAccessor.GetAsync(turnContext, () => new JobLog());

        // Get the user's text input for the message.
        var text = turnContext.Activity.Text.Trim().ToLowerInvariant();
        switch (text)
        {
            case "run":
            case "run job":

                // Start a virtual job for the user.
                JobLog.JobData job = CreateJob(turnContext, jobLog);

                // Set the new property
                await _jobLogPropertyAccessor.SetAsync(turnContext, jobLog);

                // Now save it into the JobState
                await _jobState.SaveChangesAsync(turnContext);

                await turnContext.SendActivityAsync(
                    $"We're starting job {job.TimeStamp} for you. We'll notify you when it's complete.");

                break;

            case "show":
            case "show jobs":

                // Display information for all jobs in the log.
                if (jobLog.Count > 0)
                {
                    await turnContext.SendActivityAsync(
                        "| Job number &nbsp; | Conversation ID &nbsp; | Completed |<br>" +
                        "| :--- | :---: | :---: |<br>" +
                        string.Join("<br>", jobLog.Values.Select(j =>
                            $"| {j.TimeStamp} &nbsp; | {j.Conversation.Conversation.Id.Split('|')[0]} &nbsp; | {j.Completed} |")));
                }
                else
                {
                    await turnContext.SendActivityAsync("The job log is empty.");
                }

                break;

            default:
                // Check whether this is simulating a job completed event.
                string[] parts = text?.Split(' ', StringSplitOptions.RemoveEmptyEntries);
                if (parts != null && parts.Length == 2
                    && parts[0].Equals("done", StringComparison.InvariantCultureIgnoreCase)
                    && long.TryParse(parts[1], out long jobNumber))
                {
                    if (!jobLog.TryGetValue(jobNumber, out JobLog.JobData jobInfo))
                    {
                        await turnContext.SendActivityAsync($"The log does not contain a job {jobInfo.TimeStamp}.");
                    }
                    else if (jobInfo.Completed)
                    {
                        await turnContext.SendActivityAsync($"Job {jobInfo.TimeStamp} is already complete.");
                    }
                    else
                    {
                        await turnContext.SendActivityAsync($"Completing job {jobInfo.TimeStamp}.");

                        // Send the proactive message.
                        await CompleteJobAsync(turnContext.Adapter, AppId, jobInfo);
                    }
                }

                break;
        }

        if (!turnContext.Responded)
        {
            await turnContext.SendActivityAsync(WelcomeText);
        }
    }
}

private static async Task SendWelcomeMessageAsync(ITurnContext turnContext)
{
    foreach (var member in turnContext.Activity.MembersAdded)
    {
        if (member.Id != turnContext.Activity.Recipient.Id)
        {
            await turnContext.SendActivityAsync($"Welcome to SuggestedActionsBot {member.Name}.\r\n{WelcomeText}");
        }
    }
}

// Handles non-message activities.
private async Task OnSystemActivityAsync(ITurnContext turnContext)
{
    // On a job completed event, mark the job as complete and notify the user.
    if (turnContext.Activity.Type is ActivityTypes.Event)
    {
        var jobLog = await _jobLogPropertyAccessor.GetAsync(turnContext, () => new JobLog());
        var activity = turnContext.Activity.AsEventActivity();
        if (activity.Name == JobCompleteEventName
            && activity.Value is long timestamp
            && jobLog.ContainsKey(timestamp)
            && !jobLog[timestamp].Completed)
        {
            await CompleteJobAsync(turnContext.Adapter, AppId, jobLog[timestamp]);
        }
    }
    else if (turnContext.Activity.Type is ActivityTypes.ConversationUpdate)
    {
        if (turnContext.Activity.MembersAdded.Any())
        {
            await SendWelcomeMessageAsync(turnContext);
        }
    }
}
```

#### <a name="add-job-creation-and-completion-methods"></a>Adicionar métodos de criação e conclusão do trabalho

Para iniciar um trabalho, o bot cria o trabalho e registra as informações sobre ele, e sobre a conversa atual, no log de trabalho. Quando o bot recebe um evento de trabalho concluído em qualquer conversa, ele valida a ID do trabalho antes de chamar o código para concluir o trabalho.

O código para concluir o trabalho obtém o log de trabalho do estado e, em seguida, marca o trabalho como concluído e envia uma mensagem proativa, usando o método _continuar a conversa_ do adaptador.

- A chamada desse método solicita que o canal inicie um turno independente do usuário.
- O adaptador executa o retorno de chamada associado no lugar do manipulador de turnos normal do bot. Esse turno tem seu próprio contexto de turno, do qual podemos recuperar as informações de estado e enviar a mensagem proativa ao usuário.

```csharp
// Creates and "starts" a new job.
private JobLog.JobData CreateJob(ITurnContext turnContext, JobLog jobLog)
{
    JobLog.JobData jobInfo = new JobLog.JobData
    {
        TimeStamp = DateTime.Now.ToBinary(),
        Conversation = turnContext.Activity.GetConversationReference(),
    };

    jobLog[jobInfo.TimeStamp] = jobInfo;

    return jobInfo;
}

// Sends a proactive message to the user.
private async Task CompleteJobAsync(
    BotAdapter adapter,
    string botId,
    JobLog.JobData jobInfo,
    CancellationToken cancellationToken = default(CancellationToken))
{
    await adapter.ContinueConversationAsync(botId, jobInfo.Conversation, CreateCallback(jobInfo), cancellationToken);
}

// Creates the turn logic to use for the proactive message.
private BotCallbackHandler CreateCallback(JobLog.JobData jobInfo)
{
    return async (turnContext, token) =>
    {
        // Get the job log from state, and retrieve the job.
        JobLog jobLog = await _jobLogPropertyAccessor.GetAsync(turnContext, () => new JobLog());

        // Perform bookkeeping.
        jobLog[jobInfo.TimeStamp].Completed = true;

        // Set the new property
        await _jobLogPropertyAccessor.SetAsync(turnContext, jobLog);

        // Now save it into the JobState
        await _jobState.SaveChangesAsync(turnContext);

        // Send the user a proactive confirmation message.
        await turnContext.SendActivityAsync($"Job {jobInfo.TimeStamp} is complete.");
    };
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O bot é definido no **bot.js** e possui alguns aspectos:

- código de inicialização
- um manipulador de turnos
- métodos para criar e concluir os trabalhos

#### <a name="declare-the-class-and-add-initialization-code"></a>Declarar a classe e adicionar o código de inicialização

```javascript
const { ActivityTypes, TurnContext } = require('botbuilder');

const JOBS_LIST = 'jobs';

class ProactiveBot {
    constructor(botState, adapter) {
        this.botState = botState;
        this.adapter = adapter;

        this.jobsList = this.botState.createProperty(JOBS_LIST);
    }

    // ...
};

// Helper function to check if object is empty.
function isEmpty(obj) {
    for (var key in obj) {
        if (obj.hasOwnProperty(key)) {
            return false;
        }
    }
    return true;
};

module.exports.ProactiveBot = ProactiveBot;
```

#### <a name="the-turn-handler"></a>O manipulador de turnos

Os métodos `onTurn` e `showJobs` são definidos dentro da classe `ProactiveBot`. O `onTurn` manipula a entrada de usuários. Ele também recebe atividades de eventos do sistema hipotético de realização do trabalho. O `showJobs` formata e envia o log do trabalho.

```javascript
/**
    *
    * @param {TurnContext} turnContext A TurnContext object representing an incoming message to be handled by the bot.
    */
async onTurn(turnContext) {
    // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
    if (turnContext.activity.type === ActivityTypes.Message) {
        const utterance = (turnContext.activity.text || '').trim().toLowerCase();
        var jobIdNumber;

        // If user types in run, create a new job.
        if (utterance === 'run') {
            await this.createJob(turnContext);
        } else if (utterance === 'show') {
            await this.showJobs(turnContext);
        } else {
            const words = utterance.split(' ');

            // If the user types done and a Job Id Number,
            // we check if the second word input is a number.
            if (words[0] === 'done' && !isNaN(parseInt(words[1]))) {
                jobIdNumber = words[1];
                await this.completeJob(turnContext, jobIdNumber);
            } else if (words[0] === 'done' && (words.length < 2 || isNaN(parseInt(words[1])))) {
                await turnContext.sendActivity('Enter the job ID number after "done".');
            }
        }

        if (!turnContext.responded) {
            await turnContext.sendActivity(`Say "run" to start a job, or "done <job>" to complete one.`);
        }
    } else if (turnContext.activity.type === ActivityTypes.Event && turnContext.activity.name === 'jobCompleted') {
        jobIdNumber = turnContext.activity.value;
        if (!isNaN(parseInt(jobIdNumber))) {
            await this.completeJob(turnContext, jobIdNumber);
        }
    }

    await this.botState.saveChanges(turnContext);
}

// Show a list of the pending jobs
async showJobs(turnContext) {
    const jobs = await this.jobsList.get(turnContext, {});
    if (Object.keys(jobs).length) {
        await turnContext.sendActivity(
            '| Job number &nbsp; | Conversation ID &nbsp; | Completed |<br>' +
            '| :--- | :---: | :---: |<br>' +
            Object.keys(jobs).map((key) => {
                return `${ key } &nbsp; | ${ jobs[key].reference.conversation.id.split('|')[0] } &nbsp; | ${ jobs[key].completed }`;
            }).join('<br>'));
    } else {
        await turnContext.sendActivity('The job log is empty.');
    }
}
```

#### <a name="logic-to-start-a-job"></a>Lógica para iniciar um trabalho

O método `createJob` é definido dentro da classe `ProactiveBot`. Ele cria e registra novos trabalhos para o usuário. Em teoria, ele também encaminharia essas informações para o sistema de realização do trabalho.

```javascript
// Save job ID and conversation reference.
async createJob(turnContext) {
    // Create a unique job ID.
    var date = new Date();
    var jobIdNumber = date.getTime();

    // Get the conversation reference.
    const reference = TurnContext.getConversationReference(turnContext.activity);

    // Get the list of jobs. Default it to {} if it is empty.
    const jobs = await this.jobsList.get(turnContext, {});

    // Try to find previous information about the saved job.
    const jobInfo = jobs[jobIdNumber];

    try {
        if (isEmpty(jobInfo)) {
            // Job object is empty so we have to create it
            await turnContext.sendActivity(`Need to create new job ID: ${ jobIdNumber }`);

            // Update jobInfo with new info
            jobs[jobIdNumber] = { completed: false, reference: reference };

            try {
                // Save to storage
                await this.jobsList.set(turnContext, jobs);
                // Notify the user that the job has been processed
                await turnContext.sendActivity('Successful write to log.');
            } catch (err) {
                await turnContext.sendActivity(`Write failed: ${ err.message }`);
            }
        }
    } catch (err) {
        await turnContext.sendActivity(`Read rejected. ${ err.message }`);
    }
}
```

#### <a name="logic-to-complete-a-job"></a>Lógica para concluir um trabalho

O método `completeJob` é definido dentro da classe `ProactiveBot`. Ele executa algumas atividades contábeis e envia a mensagem proativa para o usuário (na conversa original do usuário) que seu trabalho concluiu.

```javascript
async completeJob(turnContext, jobIdNumber) {
    // Get the list of jobs from the bot's state property accessor.
    const jobs = await this.jobsList.get(turnContext, {});

    // Find the appropriate job in the list of jobs.
    let jobInfo = jobs[jobIdNumber];

    // If no job was found, notify the user of this error state.
    if (isEmpty(jobInfo)) {
        await turnContext.sendActivity(`Sorry no job with ID ${ jobIdNumber }.`);
    } else {
        // Found a job with the ID passed in.
        const reference = jobInfo.reference;
        const completed = jobInfo.completed;

        // If the job is not yet completed and conversation reference exists,
        // use the adapter to continue the conversation with the job's original creator.
        if (reference && !completed) {
            // Since we are going to proactively send a message to the user who started the job,
            // we need to create the turnContext based on the stored reference value.
            await this.adapter.continueConversation(reference, async (proactiveTurnContext) => {
                // Complete the job.
                jobInfo.completed = true;
                // Save the updated job.
                await this.jobsList.set(turnContext, jobs);
                // Notify the user that the job is complete.
                await proactiveTurnContext.sendActivity(`Your queued job ${ jobIdNumber } just completed.`);
            });

            // Send a message to the person who completed the job.
            await turnContext.sendActivity('Job completed. Notification sent.');
        } else if (completed) { // The job has already been completed.
            await turnContext.sendActivity('This job is already completed, please start a new job.');
        };
    };
};
```

---

### <a name="test-your-bot"></a>Testar seu bot

Crie e execute seu bot localmente e abra duas janelas do emulador.

1. Observe que a ID da conversa é diferente nas duas janelas.
1. Na primeira janela, digite `run` duas vezes para iniciar alguns trabalhos.
1. Na segunda janela, digite `show` para ver uma lista dos trabalhos no log.
1. Na segunda janela, digite `done <jobNumber>`, onde `<jobNumber>` é um dos números de trabalho do log, sem os colchetes angulares. (O código do bot é projetado para interpretar isso como se fosse um evento jobComplete).
1. Observe que o bot envia uma mensagem proativa para o usuário na primeira janela.

<!--TODO: Recreate the screen shots once we're happy with both the C# and JS versions of the code.-->

Sua conversa pode parecer com isto do ponto de vista do usuário:

![Sessão de emulador do usuário](~/v4sdk/media/how-to-proactive/user.png)

E ter essa aparência da perspectiva do sistema do trabalho simulado:

![Sessão de emulador do sistema do trabalho](~/v4sdk/media/how-to-proactive/job-system.png)

<!-- Add a next steps section. -->
