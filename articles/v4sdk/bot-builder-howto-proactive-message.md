---
title: Enviar mensagens proativas | Microsoft Docs
description: Saiba como enviar mensagens proativamente com o bot.
keywords: mensagem proativa
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/01/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c22ce6a35d4d49506360a78a439f15137c429d9d
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905130"
---
# <a name="send-proactive-messages"></a>Enviar mensagens proativas 

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]


Geralmente, os bots enviam _mensagens reativas_, mas há ocasiões em que também é preciso ser capaz de enviar uma [mensagem proativa](bot-builder-proactive-messages.md). 

Um caso comum do sistema de mensagens proativas surge quando nosso bot está executando uma tarefa que pode levar um tempo indeterminado. Nesse caso, é possível armazenar informações sobre a tarefa, informar ao usuário que o bot voltará a contatá-lo quando a tarefa for concluída e permitir que a conversa continue. Quando a tarefa for concluída, o bot pode retomar a conversa enviando a mensagem de confirmação de forma proativa.

# <a name="ctabcs"></a>[C#](#tab/cs)

## <a name="notes-about-this-sample"></a>Notas sobre este exemplo

Estamos modificando o exemplo de EchoBot básico.
- Estamos usando `Microsoft.Samples.Proactive` como o namespace.
- Estamos substituindo o arquivo de estado por um arquivo `JobData.cs`.
- Estamos substituindo o arquivo do bot por um arquivo `ProactiveBot.cs`.

> [!NOTE]
> Atualmente, mensagens proativas exigem que o bot tenha uma ApplicationID e uma senha válida.


## <a name="define-task-data"></a>Definir dados da tarefa

Nesse cenário, estamos acompanhando tarefas arbitrárias que podem ser criadas por vários usuários em diferentes conversas. Assim, estamos usando o middleware de estado do bot geral, em vez do middleware de estado do usuário ou da conversa.

A classe a seguir define a estrutura de dados que usaremos para trabalhos individuais.


```csharp
using Microsoft.Bot.Schema;
using System.Collections.Generic;

namespace Microsoft.Samples.Proactive
{
    /// <summary>
    /// Class for storing job state. 
    /// </summary>
    public class JobData
    {
        /// <summary>
        /// The name to use to read and write this bot state object to storage.
        /// </summary>
        public readonly static string PropertyName = $"BotState:{typeof(Dictionary<int, JobData>).FullName}";

        public int JobNumber { get; set; } = 0;
        public bool Completed { get; set; } = false;

        /// <summary>
        /// The conversation reference to which to send status updates.
        /// </summary>
        public ConversationReference Conversation { get; set; }
    }
}
```


Também precisamos adicionar o middleware de estado ao nosso código de inicialização.


No arquivo `StartUp.cs`, atualize o método `ConfigureServices` para adicionar um dicionário de trabalhos para o nosso estado do bot. No código a seguir, é a última chamada para `options.Middleware.Add`.
```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<ProactiveBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        // The CatchExceptionMiddleware provides a top-level exception handler for your bot. 
        // Any exceptions thrown by other Middleware, or by your OnTurn method, will be 
        // caught here. To facillitate debugging, the exception is sent out, via Trace, 
        // to the emulator. Trace activities are NOT displayed to users, so in addition
        // an "Ooops" message is sent. 
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity($"{nameof(ProactiveBot)} Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        // Using the base BotState here, since the job log is not necessarily tied to a
        // specific user or conversation.
        options.Middleware.Add(
            new BotState<Dictionary<int, JobData>>(
                dataStore, JobData.PropertyName, (context) => $"jobs/{typeof(Dictionary<int, JobData>)}"));
    });
}
```


## <a name="update-your-bot-to-create-and-run-jobs"></a>Atualizar o bot para criar e executar trabalhos

Em cada turno, vamos deixar um usuário criar um trabalho digitando `run` ou `run job`.

Como resposta, nosso bot executará as etapas a seguir nesse turno:
- Criar o trabalho.
- Registrar informações sobre a conversa atual, para podermos enviar a mensagem proativa mais tarde.
- Permitir ao usuário saber que estamos iniciando o trabalho dele e informar mais tarde quando for concluído.
- Iniciar o trabalho assíncrono.
- Deixar o turno sair.

O trabalho que estamos iniciando é um simples temporizador de 5 segundos que será concluído enviando a mensagem proativa.
- A chamada para o método de continuar a conversa do adaptador cria um novo turno iniciado pelo bot.
- Esse turno tem seu próprio [contexto de turno](bot-builder-concept-activity-processing.md#turn-context), do qual podemos recuperar as informações de estado.
- Usamos esse contexto para enviar a mensagem proativa ao usuário.



> [!NOTE]
> O método `GetAppId` é uma solução alternativa para habilitar mensagens proativas no SDK do .NET.

```csharp
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Connector.Authentication;
using Microsoft.Bot.Schema;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Claims;
using System.Security.Principal;
using System.Threading;
using System.Threading.Tasks;

namespace Microsoft.Samples.Proactive
{
    public class ProactiveBot : IBot
    {
        /// <summary>
        /// Random number generator for job numbers.
        /// </summary>
        private static Random NumberGenerator = new Random();

        /// <summary>
        /// Gets the job log from the bot state.
        /// </summary>
        /// <param name="context">The current turn context.</param>
        /// <returns>The job log.</returns>
        private static Dictionary<int, JobData> GetJobLog(ITurnContext context)
        {
            return context.Services.Get<Dictionary<int, JobData>>(JobData.PropertyName);
        }

        /// <summary>
        /// Workaround to get the bot's app ID.
        /// </summary>
        /// <param name="context">The current turn context.</param>
        /// <returns>The application ID for the bot.</returns>
        private static string GetAppId(ITurnContext context)
        {
            // The BotFrameworkAdapter sets the identity provider on the context object.
            var claimsIdentity = context.Services.Get<IIdentity>("BotIdentity") as ClaimsIdentity;

            // For requests from a channel, the app ID is in the Audience claim of the JWT token.
            // For requests from the emulator, it is in the AppId claim.
            // For unauthenticated requests, we have anonymouse identity provided auth is disabled.
            // For Activities coming from Emulator AppId claim contains the Bot's AAD AppId.
            var botAppIdClaim =
                (claimsIdentity.Claims?.SingleOrDefault(claim => claim.Type == AuthenticationConstants.AudienceClaim)
                ?? claimsIdentity.Claims?.SingleOrDefault(claim => claim.Type == AuthenticationConstants.AppIdClaim));

            return botAppIdClaim?.Value;
        }

        /// <summary>
        /// Every Conversation turn calls this method.
        /// When the user types "run" or "run job", the bot starts a "job".
        /// When the job finishes, the bot proactively notifies the user.
        /// </summary>
        /// <param name="context">The turn context.</param>
        /// <remarks>When our virtual job finishes, it sends a proactive message
        /// to notify the user that the job completed.</remarks>
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type is ActivityTypes.Message)
            {
                var text = context.Activity.AsMessageActivity()?.Text?.Trim().ToLower();
                switch (text)
                {
                    case "run":
                    case "run job":

                        var jobLog = GetJobLog(context);
                        var job = CreateJob(context, jobLog);
                        var appId = GetAppId(context);
                        var conversation = TurnContext.GetConversationReference(context.Activity);

                        await context.SendActivity($"We're starting job {job.JobNumber} for you. We'll notify you when it's complete.");

                        // Since the context is disposed at the end of the turn, extract and send the
                        // information we need to send the proactive message later.
                        var adapter = context.Adapter;
                        Task.Run(() =>
                        {
                            // Simulate a separate process to complete the user's job.
                            Thread.Sleep(5000);

                            // Perform bookkeeping and send the proactive message.
                            CompleteJob(adapter, appId, conversation, job.JobNumber);
                        });

                        break;

                    default:

                        await context.SendActivity("Type 'run' or 'run job' to start a new job.");

                        break;
                }
            }
        }

        /// <summary>
        /// Creates a simulated job and updates the job log.
        /// </summary>
        /// <param name="context">The current turn context.</param>
        /// <param name="jobLog">The job log.</param>
        /// <returns>A new job.</returns>
        private JobData CreateJob(ITurnContext context, Dictionary<int, JobData> jobLog)
        {
            // Generate a non-duplicate job number;
            int number;
            while (jobLog.ContainsKey(number = NumberGenerator.Next())) { }

            // Simulate creaing the job and logging it.
            var job = new JobData
            {
                JobNumber = number,
                Conversation = TurnContext.GetConversationReference(context.Activity)
            };
            jobLog.Add(job.JobNumber, job);

            // Return the created job.
            return job;
        }

        /// <summary>
        /// Performs bookkeeping and proactively notifies the user that their job completed.
        /// </summary>
        /// <param name="adapter">The bot adapter with which to send the message.</param>
        /// <param name="appId">The app ID of the bot to send the message from.</param>
        /// <param name="conversation">The conversation in which to put the message.</param>
        /// <param name="jobNumber">The number of the job that completed.</param>
        private async void CompleteJob(BotAdapter adapter, string appId, ConversationReference conversation, int jobNumber)
        {
            await adapter.ContinueConversation(appId, conversation, async context =>
            {
                // Get the job log from state, and retrieve the job.
                var jobLog = GetJobLog(context);
                var job = jobLog[jobNumber];

                // Perform bookkeeping.
                job.Completed = true;

                // Send the user a proactive confirmation message.
                await context.SendActivity($"Job {job.JobNumber} is complete.");
            });
        }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Para poder enviar uma mensagem proativa a um usuário, o usuário precisará enviar pelo menos uma mensagem de estilo reativo ao bot. 

É necessário enviar uma mensagem para o bot, já que ele precisa obter uma referência para o objeto de atividade e salvá-lo em algum local para uso futuro. Você pode imaginar o objeto de atividade como o endereço do usuário, pois contém informações sobre o canal no qual chegou, a ID de usuário, a ID da conversa e até mesmo o servidor que deve receber quaisquer mensagens futuras. Esse objeto é JSON simples e deve ser salvo por completo sem violação.

Vamos começar com um trecho de código curto que mostra como salvar a referência da conversa a qualquer momento que o usuário diz assinar:
```javascript
const { MemoryStorage } = require('botbuilder');

const storage = new MemoryStorage();

// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const utterances = (context.activity.text || '').trim().toLowerCase()
            if (utterances === 'subscribe') {
                var userId = await saveReference(TurnContext.getConversationReference(context.activity));
                await subscribeUser(userId)
                await context.sendActivity(`Thank You! We will message you shortly.`);
               
            } else{
                await context.sendActivity("Say 'subscribe' to start proactive message");
            }
    
        }
    });
});
```
Os trechos de código acima chamam a função `saveReference()` que salvará a referência do usuário com `MemoryStorage` e retornam `userId`. Depois que a referência é salva com êxito, nós então chamamos `subscribeUser()` que notificará o usuário que ele assinou. 

A função `subscribeUser()` é o que configura a assinatura real. Vamos conferir uma implementação simples que inicia um temporizador de 2 segundos e envia mensagens proativamente ao usuário depois que o temporizador transcorre:

```javascript
// Persist info to storage
async function saveReference(reference){
    const userId = reference.activityId
    const changes = {};
    changes['reference/' + userId] = reference;
    await storage.write(changes); // Write reference info to persisted storage
    return userId;
}

// Subscribe user to a proactive call. In this case, we are using a setTimeOut() to trigger the proactive call
async function subscribeUser(userId) {
    setTimeout(async () => {
        const reference = await findReference(userId);
        if (reference) {
            await adapter.continueConversation(reference, async (context) => {
                await context.sendActivity("You have been notified");
            });
            
        }
    }, 2000); // Trigger after 2 secs
}

// Read the stored reference info from storage
async function findReference(userId){
    const referenceKey = 'reference/' + userId;
    var rows = await storage.read([referenceKey])
    var reference = await rows[referenceKey]

    return reference;
}
```

A função `subscribeUser()` define um temporizador que localizará o objeto de referência lendo-o no armazenamento. Se o objeto de referência foi encontrado, poderemos continuar a conversa com o usuário. O método `continueConversation` permite que o bot envie mensagens proativamente para uma conversa ou usuário com o qual ele já tenha se comunicado.

---

## <a name="test-your-bot"></a>Testar o bot

Para testar o bot, implante-o no Azure como um bot somente de registro e teste-o no Webchat ou localmente usando o Emulador.
