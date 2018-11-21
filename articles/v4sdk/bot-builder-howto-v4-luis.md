---
title: Adicionar reconhecimento de idioma natural ao seu bot | Microsoft Docs
description: Aprenda a usar o LUIS para entender a linguagem natural com o SDK do Bot Builder.
keywords: Entendimento de Linguagem, LUIS, intenção, reconhecedor, entidades, middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 11/08/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: eab8e2f9d437748d0bb0fefd31c03c8fb350c6b1
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645696"
---
# <a name="add-natural-language-understanding-to-your-bot"></a>Adicionar reconhecimento de idioma natural ao seu bot

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A capacidade de entender o que seu usuário quer dizer contextualmente e em conversas pode ser uma tarefa difícil, mas pode dar ao seu bot uma sensação de conversa mais natural. O Language Understanding, chamado LUIS, permite que você faça exatamente isso para que seu bot possa reconhecer a intenção das mensagens do usuário, permitir uma linguagem mais natural do seu usuário e direcionar melhor o fluxo de conversação. Se você precisar de mais informações sobre o LUIS, veja o [reconhecimento de idioma](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/what-is-luis) para bots.

## <a name="prerequisites"></a>Pré-requisitos
Este tópico fornece orientação pela configuração de um bot simples que usa o LUIS para reconhecer algumas intenções diferentes. O código neste artigo se baseia no exemplo de NLP com LUIS em [C#](https://aka.ms/cs-luis-sample) e [JavaScript](https://aka.ms/js-luis-sample).

## <a name="create-a-luis-app-in-the-luis-portal"></a>Criar um aplicativo LUIS no portal do LUIS

Primeiro, inscreva-se para ter uma conta no [luis.ai](https://www.luis.ai) e crie um aplicativo LUIS no portal do LUIS seguindo [estas](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-start-new-app) instruções. Se você quiser criar sua própria versão do aplicativo de exemplo do LUIS usado neste artigo, no portal do LUIS, [importe](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app) este arquivo `LUIS.Reminders.json` ([C#](https://github.com/Microsoft/BotBuilder-Samples/blob/v4/samples/csharp_dotnetcore/12.nlp-with-luis/CognitiveModels/LUIS-Reminders.json) | [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/12.nlp-with-luis/cognitiveModels/reminders.json)) para compilar o aplicativo, [treine-o](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) e [publique-o](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp).

### <a name="obtain-values-to-connect-to-your-luis-app"></a>Obter valores para conectar ao seu aplicativo LUIS

Após a publicação de seu aplicativo LUIS, acesse-o no bot. Será necessário registrar vários valores para acessar seu aplicativo LUIS no bot. Você pode recuperar essas informações usando o portal do LUIS ou as ferramentas da CLI.

#### <a name="using-luis-portal"></a>Usar o portal do LUIS
- Marque seu aplicativo LUIS publicado em [luis.ai](https://www.luis.ai).
- Com o aplicativo LUIS publicado aberto, escolha a guia **GERENCIAR**.
- Escolha a guia **Informações do Aplicativo** no lado esquerdo, registre o valor mostrado para _ID do Aplicativo_ como <ID_DO_SEU_APLICATIVO>.
- Escolha a guia **Chaves e Pontos de Extremidade** no lado esquerdo, registre o valor mostrado para _Chave de Criação_ como <SUA_CHAVE_DE_CRIAÇÃO>. Observe <SUA_CHAVE_DE_ASSINATURA> é a mesma que <SUA_CHAVE_DE_CRIAÇÃO>. Role a tela para baixo até o final da página, registre o valor mostrado para _Região_ como <SUA_REGIÃO> e registre o valor mostrado para _Ponto de Extremidade_ como <SEU_PONTO_DE_EXTREMIDADE>.

#### <a name="using-cli-tools"></a>Usar as ferramentas de CLI

Você pode usar as ferramentas de CLI do BotBuilder [luis](https://aka.ms/botbuilder-tools-luis) e [msbot](https://aka.ms/botbuilder-tools-msbot-readme) para obter metadados sobre seu aplicativo LUIS e adicioná-lo ao seu arquivo **.bot**.

1. Abra um terminal ou prompt de comando e navegue até o diretório raiz do seu projeto de bot.
2. Verifique se as ferramentas `luis` e `msbot` estão instaladas.

    ```shell
    npm install luis msbot
    ```

3. Execute `luis init` para criar um arquivo de recursos do LUIS (**.luisrc**). Forneça sua chave de criação do LUIS e sua região quando receber a solicitação. Não é necessário inserir sua ID do aplicativo desta vez.
4. Execute o comando a seguir para baixar seus metadados e adicioná-los ao arquivo de configuração do bot.
    Se você tiver criptografado o arquivo de configuração, precisará fornecer sua chave secreta para atualizar o arquivo.

    ```shell
    luis get application --appId <your-app-id> --msbot | msbot connect luis --stdin [--secret <YOUR-SECRET>]
    ```

## <a name="configure-your-bot-to-use-your-luis-app"></a>Configure seu bot para usar seu aplicativo LUIS

Uma referência ao aplicativo LUIS é adicionada ao inicializar o bot. Depois, podemos chamá-la dentro de nossa lógica do bot.

### <a name="prerequisite"></a>Pré-requisito

Antes de começarmos a codificar, verifique se você tem os pacotes necessários para o aplicativo LUIS.

# <a name="ctabcs"></a>[C#](#tab/cs)

Adicione o seguinte [pacote NuGet](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) ao bot.

* `Microsoft.Bot.Builder.AI.Luis`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Os recursos do LUIS estão no pacote `botbuilder-ai`. Adicione este pacote ao projeto por meio do npm:

```shell
npm install --save botbuilder-ai
```

---

# <a name="ctabcs"></a>[C#](#tab/cs)

Baixe e abra o [código de exemplo de NLP LUIS](https://aka.ms/cs-luis-sample) encontrado aqui. Modificaremos o código conforme necessário. 

Primeiro, adicione as informações necessárias para acessar seu aplicativo LUIS incluindo a id do aplicativo, a chave de criação, a chave de assinatura, o ponto de extremidade e a região ao arquivo `BotConfiguration.bot`. Estes são os valores que você salvou anteriormente de seu aplicativo LUIS publicado.

```csharp
{
  "name": "LuisBot",
  "services": [
    {
      "type": "endpoint",
      "name": "development",
      "endpoint": "http://localhost:3978/api/messages",
      "appId": "",
      "appPassword": "",
      "id": "1"
    },
    {
      "type": "luis",
      "name": "LuisBot",
      "AppId": "<YOUR_APP_ID>",
      "SubscriptionKey": "<YOUR_SUBSCRIPTION_KEY>",
      "AuthoringKey": "<YOUR_AUTHORING_KEY>",
      "GetEndpoint": "<YOUR_ENDPOINT>",
      "Region": "<YOUR_REGION>"
    }
  ],
  "padlock": "",
  "version": "2.0"
}
```

Em seguida, inicializamos uma nova instância da classe BotService `BotServices.cs`, que captura as informações do arquivo `.bot`. Adicione o seguinte código ao arquivo `BotServices.cs`.

```csharp
public class BotServices
{
    /// Initializes a new instance of the BotServices class
    public BotServices(BotConfiguration botConfiguration)
    {
        foreach (var service in botConfiguration.Services)
        {
            switch (service.Type)
            {
                case ServiceTypes.Luis:
                {
                    var luis = (LuisService)service;
                    if (luis == null)
                    {
                        throw new InvalidOperationException("The LUIS service is not configured correctly in your '.bot' file.");
                    }

                    var app = new LuisApplication(luis.AppId, luis.AuthoringKey, luis.GetEndpoint());
                    var recognizer = new LuisRecognizer(app);
                    this.LuisServices.Add(luis.Name, recognizer);
                    break;
                    }
                }
            }
        }

    /// Gets the set of LUIS Services used.
    /// LuisServices is represented as a dictionary.  
    public Dictionary<string, LuisRecognizer> LuisServices { get; } = new Dictionary<string, LuisRecognizer>();
}
```

Em seguida, registramos o aplicativo LUIS como um singleton no arquivo `Startup.cs` adicionando o seguinte código dentro de `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var secretKey = Configuration.GetSection("botFileSecret")?.Value;
    var botFilePath = Configuration.GetSection("botFilePath")?.Value;

    // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
    services.AddSingleton(sp => botConfig ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

    // Initialize Bot Connected Services clients.
    var connectedServices = new BotServices(botConfig);
    services.AddSingleton(sp => connectedServices);
    services.AddSingleton(sp => botConfig);

    services.AddBot<LuisBot>(options =>
    {
        // Retrieve current endpoint.
        var environment = _isProduction ? "production" : "development";
        var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
        if (!(service is EndpointService endpointService))
        {
            throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
        }

        options.CredentialProvider = new SimpleCredentialProvider(endpointService.AppId, endpointService.AppPassword);

        // Creates a logger for the application to use.
        ILogger logger = _loggerFactory.CreateLogger<LuisBot>();

        // Catches any errors that occur during a conversation turn and logs them.
        options.OnTurnError = async (context, exception) =>
        {
            logger.LogError($"Exception caught : {exception}");
            await context.SendActivityAsync("Sorry, it looks like something went wrong.");
        };
        /// ...
    });
}
```

Em seguida, precisamos dar ao bot esta instância do LUIS. Abra `LuisBot.cs` e adicione o seguinte código na parte superior do arquivo.

```csharp
public class LuisBot : IBot
{
    public static readonly string LuisKey = "LuisBot";
    private const string WelcomeText = "This bot will introduce you to natural language processing with LUIS. Type an utterance to get started";

    /// Services configured from the ".bot" file.
    private readonly BotServices _services;

    /// Initializes a new instance of the LuisBot class.
    public LuisBot(BotServices services)
    {
        _services = services ?? throw new System.ArgumentNullException(nameof(services));
        if (!_services.LuisServices.ContainsKey(LuisKey))
        {
            throw new System.ArgumentException($"Invalid configuration. Please check your '.bot' file for a LUIS service named '{LuisKey}'.");
        }
    }
    /// ...
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Em nosso exemplo, o código de inicialização está em um arquivo **index.js**, o código para a lógica do bot está em um arquivo **bot.js** e informações adicionais de configuração estão no arquivo **nlp-with-luis.bot**.

Depois de seguir as instruções para a criação de seu aplicativo LUIS e para atualizar seu arquivo **.bot**, seu arquivo **nlp-with-luis.bot** deve incluir uma entrada de serviço para seu aplicativo LUIS.

```json
{
    "name": "YOUR_LUIS_APP_NAME",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "endpoint": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "id": "35"
        },
        {
            "type": "luis",
            "name": "YOUR_LUIS_APP_NAME",
            "appId": "<YOUR_APP_ID>",
            "version": "0.1",
            "authoringKey": "<YOUR_AUTHORING_KEY>",
            "subscriptionKey": "<YOUR_SUBSCRIPTION_KEY>>",
            "region": "<YOUR_REGION>",
            "id": "83"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

No arquivo **bot.js**, lemos as informações de configuração para gerar o serviço LUIS e inicializar o bot.
Atualize o valor de `LUIS_CONFIGURATION` com o nome de seu aplicativo LUIS, como ele aparece no arquivo de configuração.

```javascript
// Language Understanding (LUIS) service name as defined in the .bot file.YOUR_LUIS_APP_NAME is "LuisBot" in the C# code.
const LUIS_CONFIGURATION = '<YOUR_LUIS_APP_NAME>';

// Get endpoint and LUIS configurations by service name.
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);
const luisConfig = botConfig.findServiceByNameOrId(LUIS_CONFIGURATION);

// Map the contents to the required format for `LuisRecognizer`.
const luisApplication = {
    applicationId: luisConfig.appId,
    endpointKey: luisConfig.subscriptionKey || luisConfig.authoringKey,
    azureRegion: luisConfig.region
};

// Create configuration for LuisRecognizer's runtime behavior.
const luisPredictionOptions = {
    includeAllIntents: true,
    log: true,
    staging: false
};

// Create adapter...

// Create the LuisBot.
let bot;
try {
    bot = new LuisBot(luisApplication, luisPredictionOptions);
} catch (err) {
    console.error(`[botInitializationError]: ${ err }`);
    process.exit();
}
```

Em seguida, criamos o servidor HTTP e escutamos as solicitações de entrada, as quais gerarão chamadas para a lógica do bot.

```javascript
// Create HTTP server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }.`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator.`);
    console.log(`\nTo talk to your bot, open nlp-with-luis.bot file in the emulator.`);
});

// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async(turnContext) => {
        await bot.onTurn(turnContext);
    });
});
```

---

Agora, o LUIS está configurado para seu bot. Em seguida, vamos ver como obter a intenção do LUIS.

## <a name="get-the-intent-by-calling-luis"></a>Obter a intenção chamando o LUIS

O bot recebe resultados do LUIS chamando o reconhecedor do LUIS.

# <a name="ctabcs"></a>[C#](#tab/cs)

Para que seu bot apenas envie uma resposta com base na intenção detectada pelo aplicativo LUIS, chame o `LuisRecognizer` para receber um `RecognizerResult`. Isso pode ser feito no código sempre que você precisar da intenção do LUIS.

```cs
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))

{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Check LUIS model
        var recognizerResult = await _services.LuisServices[LuisKey].RecognizeAsync(turnContext, cancellationToken);
        var topIntent = recognizerResult?.GetTopScoringIntent();
        if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
        {
            await turnContext.SendActivityAsync($"==>LUIS Top Scoring Intent: {topIntent.Value.intent}, Score: {topIntent.Value.score}\n");
        }
        else
        {
            var msg = @"No LUIS intents were found.
                        This sample is about identifying two user intents:
                        'Calendar.Add'
                        'Calendar.Find'
                        Try typing 'Add Event' or 'Show me tomorrow'.";
            await turnContext.SendActivityAsync(msg);
        }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
}
```

Qualquer intenções reconhecidas na declaração serão retornadas como um mapa de nomes de intenção de pontuações e podem ser acessadas de `recognizerResult.Intents`. Um método estático `recognizerResult?.GetTopScoringIntent()` é fornecido para ajudar a simplificar a localização da intenção de pontuação superior para um conjunto de resultados.

Quaisquer entidades reconhecidas serão retornadas como um mapa de nomes de entidades para valores e acessadas via `recognizerResults.entities`. Metadados de entidade adicionais podem ser retornados, passando um `verbose=true` definir ao criar o LuisRecognizer. Os metadados adicionados podem ser acessados via `recognizerResults.entities.$instance`.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

No arquivo **bot.js**, passamos a entrada do usuário para o método `recognize` do reconhecedor do LUIS para obter respostas do aplicativo LUIS.

```javascript
const { ActivityTypes } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');

/**
 * A simple bot that responds to utterances with answers from the Language Understanding (LUIS) service.
 * If an answer is not found for an utterance, the bot responds with help.
 */
class LuisBot {
    /**
     * The LuisBot constructor requires one argument (`application`) which is used to create an instance of `LuisRecognizer`.
     * @param {LuisApplication} luisApplication The basic configuration needed to call LUIS. In this sample the configuration is retrieved from the .bot file.
     * @param {LuisPredictionOptions} luisPredictionOptions (Optional) Contains additional settings for configuring calls to LUIS.
     */
    constructor(application, luisPredictionOptions, includeApiResults) {
        this.luisRecognizer = new LuisRecognizer(application, luisPredictionOptions, true);
    }

    /**
     * Every conversation turn calls this method.
     * @param {TurnContext} turnContext Contains all the data needed for processing the conversation turn.
     */
    async onTurn(turnContext) {
        // By checking the incoming Activity type, the bot only calls LUIS in appropriate cases.
        if (turnContext.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the user's message.
            const results = await this.luisRecognizer.recognize(turnContext);

            // Since the LuisRecognizer was configured to include the raw results, get the `topScoringIntent` as specified by LUIS.
            const topIntent = results.luisResult.topScoringIntent;

            if (topIntent.intent !== 'None') {
                await turnContext.sendActivity(`LUIS Top Scoring Intent: ${ topIntent.intent }, Score: ${ topIntent.score }`);
            } else {
                // If the top scoring intent was "None" tell the user no valid intents were found and provide help.
                await turnContext.sendActivity(`No LUIS intents were found.
                                                \nThis sample is about identifying two user intents:
                                                \n - 'Calendar.Add'
                                                \n - 'Calendar.Find'
                                                \nTry typing 'Add Event' or 'Show me tomorrow'.`);
            }
        } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate &&
            turnContext.activity.recipient.id !== turnContext.activity.membersAdded[0].id) {
            // If the Activity is a ConversationUpdate, send a greeting message to the user.
            await turnContext.sendActivity('Welcome to the NLP with LUIS sample! Send me a message and I will try to predict your intent.');
        } else if (turnContext.activity.type !== ActivityTypes.ConversationUpdate) {
            // Respond to all other Activity types.
            await turnContext.sendActivity(`[${ turnContext.activity.type }]-type activity detected.`);
        }
    }
}

module.exports.LuisBot = LuisBot;
```

O reconhecedor do LUIS retorna informações sobre o quanto as declarações corresponderam às intenções disponíveis. A propriedade `luisResult.intents` do objeto resultante contém uma matriz das intenções pontuadas. A propriedade `luisResult.topScoringIntent` do objeto resultante contém as principais intenções e a pontuação.

---

## <a name="extract-entities"></a>Extrair entidades

Além de reconhecer a intenção, um aplicativo LUIS também pode extrair entidades, que são palavras importantes para atender a solicitação de um usuário. Por exemplo, no bot de previsão do tempo, o aplicativo LUIS pode conseguir extrair o local do relatório meteorológico da mensagem do usuário.

Uma maneira comum de estruturar sua conversa é identificar qualquer entidade na mensagem do usuário e solicitar qualquer uma das entidades necessárias que não foram encontradas. Em seguida, as etapas subsequentes manipula a resposta para o prompt.

<!--Snip
# [C#](#tab/cs)

Let's say the message from the user was "What's the weather in Seattle"? The [LuisRecognizer](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.luisrecognizer) gives you a [RecognizerResult](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult) with an [`Entities` property](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult#properties-) that has this structure:

```json
{
"$instance": {
    "Weather_Location": [
        {
            "startIndex": 22,
            "endIndex": 29,
            "text": "seattle",
            "score": 0.8073087
        }
    ]
},
"Weather_Location": [
        "seattle"
    ]
}
```

The following helper function can be added to your bot to get entities out of the `RecognizerResult` from LUIS. It will require the use of the `Newtonsoft.Json.Linq` library, which you'll have to add to your **using** statements.

```cs
// Get entities from LUIS result
private T GetEntity<T>(RecognizerResult luisResult, string entityKey)
{
    var data = luisResult.Entities as IDictionary<string, JToken>;
    if (data.TryGetValue(entityKey, out JToken value))
    {
        return value.First.Value<T>();
    }
    return default(T);
}
```

When gathering information like entities from multiple steps in a conversation, it can be helpful to save the information you need in your state. If an entity is found, it can be added to the appropriate state field. In your conversation if the current step already has the associated field completed, the step to prompt for that information can be skipped.

# [JavaScript](#tab/js)

Let's say the message from the user was "What's the weather in Seattle"? The [LuisRecognizer](https://docs.microsoft.com/en-us/javascript/api/botbuilder-ai/luisrecognizer) gives you a [RecognizerResult](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core-extensions/recognizerresult) with an `entities` property that has this structure:

```json
{
"$instance": {
    "Weather_Location": [
        {
            "startIndex": 22,
            "endIndex": 29,
            "text": "seattle",
            "score": 0.8073087
        }
    ]
},
"Weather_Location": [
        "seattle"
    ]
}
```

This `findEntities` function looks for any entities recognized by the LUIS app that match the incoming `entityName`.

```javascript
// Helper function for finding a specified entity
// entityResults are the results from LuisRecognizer.get(context)
function findEntities(entityName, entityResults) {
    let entities = []
    if (entityName in entityResults) {
        entityResults[entityName].forEach(entity => {
            entities.push(entity);
        });
    }
    return entities.length > 0 ? entities : undefined;
}
```
/Snip-->

Ao coletar informações como entidades de várias etapas em uma conversa, pode ser útil salvar as informações necessárias no estado. Se uma entidade for encontrada, ela poderá ser adicionada ao campo de estado apropriado. Na conversa, se a etapa atual já tiver o campo associado concluído, a etapa para solicitar informações poderá ser ignorada.

## <a name="additional-resources"></a>Recursos adicionais

Para obter um exemplo usando o LUIS, confira os projetos para [[C#](https://aka.ms/cs-luis-sample)] ou [[JavaScript](https://aka.ms/js-luis-sample)].

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Combinar o LUIS e os serviços QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)
