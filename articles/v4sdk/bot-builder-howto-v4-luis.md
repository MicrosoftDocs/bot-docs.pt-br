---
title: Adicionar reconhecimento de idioma natural ao seu bot | Microsoft Docs
description: Aprenda a usar o LUIS para reconhecimento de linguagem natural com o SDK do Bot Framework.
keywords: Entendimento de Linguagem, LUIS, intenção, reconhecedor, entidades, middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 11/28/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a30a3f5dfe4693d67a4cd42a50d35893f8888e07
ms.sourcegitcommit: 05ddade244874b7d6e2fc91745131b99cc58b0d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591034"
---
# <a name="add-natural-language-understanding-to-your-bot"></a>Adicionar reconhecimento de idioma natural ao seu bot

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A capacidade de entender o que seu usuário quer dizer contextualmente e em conversas pode ser uma tarefa difícil, mas pode dar ao seu bot uma sensação de conversa mais natural. O Language Understanding, chamado LUIS, permite que você faça exatamente isso para que seu bot possa reconhecer a intenção das mensagens do usuário, permitir uma linguagem mais natural do seu usuário e direcionar melhor o fluxo de conversação. Este tópico fornece orientação pela configuração de um bot simples que usa o LUIS para reconhecer algumas intenções diferentes. 
## <a name="prerequisites"></a>Pré-requisitos
- conta [luis.ai](https://www.luis.ai)
- [Emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- O código neste artigo se baseia no exemplo de **NLP com LUIS**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/cs-luis-sample) ou [JS](https://aka.ms/js-luis-sample). 
- Conhecimento das [Noções básicas do bot](bot-builder-basics.md), [processamento de idioma natural](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/what-is-luis) e do arquivo [.bot](bot-file-basics.md).

## <a name="create-a-luis-app-in-the-luis-portal"></a>Criar um aplicativo LUIS no portal do LUIS
Entrar no portal do LUIS para criar sua própria versão do exemplo de aplicativo LUIS. Você pode criar e gerenciar seus aplicativos em **Meus Aplicativos**. 

1. Selecione **Importar novos aplicativos**. 
1. Clique em **Escolher arquivo do aplicativo (formato JSON)...** 
1. Selecione o arquivo `reminders.json` localizado na parta `CognitiveModels` do exemplo. No **Nome opcional**, insira **LuisBot**. Esse arquivo contém três intenções: Calendar_Add, Calendar_Find e None. Vamos usar essas intenções para entender o que o usuário pretende ao enviar uma mensagem ao bot. Se você quiser incluir entidades, consulte a [seção opcional](#optional---extract-entities) ao final deste artigo.
1. [Treine](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) o aplicativo.
1. [Publique](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp) o aplicativo no ambiente de *produção*.

### <a name="obtain-values-to-connect-to-your-luis-app"></a>Obter valores para conectar ao seu aplicativo LUIS

Após a publicação de seu aplicativo LUIS, acesse-o no bot. Será necessário registrar vários valores para acessar seu aplicativo LUIS no bot. Você pode recuperar essas informações usando o portal do LUIS.

#### <a name="retrieve-application-information-from-the-luisai-portal"></a>Recupere as informações do aplicativo no portal LUIS.ai
O arquivo .bot atua como o lugar para reunir todas as referências de serviço em um único local. As informações que você recupera serão adicionadas ao arquivo .bot na próxima seção. 
1. Marque seu aplicativo LUIS publicado em [luis.ai](https://www.luis.ai).
1. Com o aplicativo LUIS publicado aberto, escolha a guia **GERENCIAR**.
1. Escolha a guia **Informações do Aplicativo** no lado esquerdo, registre o valor mostrado para _ID do Aplicativo_ como <ID_DO_SEU_APLICATIVO>.
1. Escolha a guia **Chaves e Pontos de Extremidade** no lado esquerdo, registre o valor mostrado para _Chave de Criação_ como <SUA_CHAVE_DE_CRIAÇÃO>. Observe que *sua chave de assinatura* é a mesma que *sua chave de criação*. 
1. Role para baixo até o final da página, registre o valor mostrado para _Região_ como <YOUR_REGION>.
1. Registre o valor mostrado para _Ponto de extremidade_ como <YOUR_ENDPOINT>.

#### <a name="update-the-bot-file"></a>Atualize o arquivo de bot
Adicione as informações necessárias para acessar seu aplicativo LUIS incluindo a id do aplicativo, a chave de criação, a chave de assinatura, o ponto de extremidade e a região ao arquivo `nlp-with-luis.bot`. Estes são os valores que você salvou anteriormente de seu aplicativo LUIS publicado.

```json
{
    "name": "LuisBot",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "endpoint": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "id": "166"
        },
        {
            "type": "luis",
            "name": "LuisBot",
            "appId": "<luis appid>",
            "version": "0.1",
            "authoringKey": "<luis authoring key>",
            "subscriptionKey": "<luis subscription key>",
            "region": "<luis region>",
            "id": "158"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```
# <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="configure-your-bot-to-use-your-luis-app"></a>Configure seu bot para usar seu aplicativo LUIS
Certifique-se de que o pacote do nuget **Microsoft.Bot.Builder.AI.Luis** está instalado para seu projeto.

Em seguida, inicializamos uma nova instância da classe BotService em `BotServices.cs`, que captura as informações acima de seu arquivo `.bot`. O serviço externo é configurado usando a classe `BotConfiguration`.

```csharp
using Microsoft.Bot.Builder.AI.Luis;
using Microsoft.Bot.Configuration;

public class BotServices
{
    // Initializes a new instance of the BotServices class
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

    // Gets the set of LUIS Services used. LuisServices is represented as a dictionary.  
    public Dictionary<string, LuisRecognizer> LuisServices { get; } = new Dictionary<string, LuisRecognizer>();
}
```

Registramos o aplicativo LUIS como um singleton no arquivo `Startup.cs` usando o seguinte código dentro do método `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var secretKey = Configuration.GetSection("botFileSecret")?.Value;
    var botFilePath = Configuration.GetSection("botFilePath")?.Value;

    // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\nlp-with-luis.bot", secretKey);
    services.AddSingleton(sp => botConfig ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

    // Initialize Bot Connected Services client.
    var connectedServices = new BotServices(botConfig);
    services.AddSingleton(sp => connectedServices);

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

        // ...
    });
}
```

Em seguida, no arquivo `LuisBot.cs`, o bot obtém essa instância do LUIS.

```csharp
public class LuisBot : IBot
{
    public static readonly string LuisKey = "LuisBot";
    private const string WelcomeText = "This bot will introduce you to natural language processing with LUIS. Type an utterance to get started";

    // Services configured from the ".bot" file.
    private readonly BotServices _services;

    // Initializes a new instance of the LuisBot class.
    public LuisBot(BotServices services)
    {
        _services = services ?? throw new System.ArgumentNullException(nameof(services));
        if (!_services.LuisServices.ContainsKey(LuisKey))
        {
            throw new System.ArgumentException($"Invalid configuration....");
        }
    }
    // ...
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Em nosso exemplo, o código de inicialização está em um arquivo **index.js**, o código para a lógica do bot está em um arquivo **bot.js** e informações adicionais de configuração estão no arquivo **nlp-with-luis.bot**.

No arquivo **bot.js**, lemos as informações de configuração para gerar o serviço LUIS e inicializar o bot.
Atualize o valor de `LUIS_CONFIGURATION` com o nome de seu aplicativo LUIS, como ele aparece no arquivo de configuração.

```javascript
// Language Understanding (LUIS) service name as defined in the .bot file.YOUR_LUIS_APP_NAME is "LuisBot" in the JavaScript code.
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

### <a name="get-the-intent-by-calling-luis"></a>Obter a intenção chamando o LUIS

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

### <a name="test-the-bot"></a>Testar o bot

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo Leiame para o exemplo em [C#](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/12.nlp-with-luis/README.md) ou no [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/12.nlp-with-luis/README.md).

1. No emulador, digite uma mensagem, conforme mostrado abaixo. 

![testar entrada de exemplo de nlp](./media/nlp-luis-sample-message.png)

O bot responderá com a intenção de pontuação mais alta, que nesse caso é a intenção "Calendar_Add". Lembre-se de que o arquivo `reminders.json` importado no portal luis.ai definiu as intenções "Calendar_Add", "Calendar_Find" e "None". 

![testar resposta de exemplo de nlp](./media/nlp-luis-sample-response.png) 

Uma pontuação de previsão indica o grau de confiança que o LUIS tem para resultados de previsão. Uma pontuação de previsão é entre zero (0) e um (1). Um exemplo de uma pontuação com alta confiança do LUIS é 0,99. Um exemplo de uma pontuação com baixa confiança é 0,01. 

## <a name="optional---extract-entities"></a>Opcional - Extrair entidades

Além de reconhecer a intenção do usuário, um aplicativo LUIS também pode retornar entidades. As entidades são palavras importantes relacionadas à intenção e podem, às vezes, ser essenciais para atender a uma solicitação do usuário ou permitir que seu bot se comporte de maneira mais inteligente. 

### <a name="why-use-entities"></a>Por que usar entidades?

As entidades LUIS permitem que seu bot entenda determinadas coisas ou eventos diferentes das intenções padrão. Assim, você pode coletar mais informações do usuário, o que permite que seu bot responda de forma mais inteligente ou ignore algumas perguntas nas quais ele pede essas informações ao usuário. Por exemplo, em um bot meteorológico, o aplicativo LUIS pode usar uma entidade _Location_ para extrair o local da previsão meteorológica solicitada de dentro da mensagem do usuário. Assim, seu bot poderia ignorar a pergunta sobre o local do usuário e proporcionar ao usuário uma conversa mais inteligente e mais concisa.

### <a name="prerequisites"></a>Pré-requisitos

Para usar entidades com este exemplo, você precisará criar um aplicativo LUIS que inclui entidades. Siga as etapas na seção acima para [criar seu aplicativo LUIS](#create-a-luis-app-in-the-luis-portal), mas em vez de usar o arquivo `reminders.json`, use o arquivo [reminders-with-entities.json](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/dotnet_core/nlp-with-luis) para compilar seu aplicativo LUIS. Esse arquivo fornece as mesmas intenções, além de três entidades adicionais: Compromisso, Reunião e Agenda. Essas entidades ajudam o LUIS a determinar a intenção da mensagem do usuário. 

### <a name="extract-and-display-entities"></a>Extrair e exibir entidades
O código opcional a seguir pode ser adicionado ao exemplo de aplicativo para extrair e exibir as informações de entidade, quando uma entidade for usada pelo LUIS para ajudar a identificar a intenção do usuário. 

# <a name="ctabcs"></a>[C#](#tab/cs)

A função auxiliar seguinte pode ser adicionada ao bot para obter entidades fora do `RecognizerResult` no LUIS. Ela exigirá o uso da biblioteca `Newtonsoft.Json.Linq` que você terá que adicionar às instruções **using**. Se as informações de entidade forem encontradas ao analisar o JSON retornado do LUIS, a função _DeserializeObject_ da Newtonsoft converterá esse JSON em um objeto dinâmico, fornecendo acesso às informações de entidade.

```cs
using Newtonsoft.Json.Linq;

private string ParseLuisForEntities(RecognizerResult recognizerResult)
{
   var result = string.Empty;

   // recognizerResult.Entities returns type JObject.
   foreach (var entity in recognizerResult.Entities)
   {
       // Parse JObject for a known entity types: Appointment, Meeting, and Schedule.
       var appointFound = JObject.Parse(entity.Value.ToString())["Appointment"];
       var meetingFound = JObject.Parse(entity.Value.ToString())["Meeting"];
       var schedFound = JObject.Parse(entity.Value.ToString())["Schedule"];

       // We will return info on the first entity found.
       if (appointFound != null)
       {
           // use JsonConvert to convert entity.Value to a dynamic object.
           dynamic o = JsonConvert.DeserializeObject<dynamic>(entity.Value.ToString());
           if (o.Appointment[0] != null)
           {
              // Find and return the entity type and score.
              var entType = o.Appointment[0].type;
              var entScore = o.Appointment[0].score;
              result = "Entity: " + entType + ", Score: " + entScore + ".";
              
              return result;
            }
        }

        if (meetingFound != null)
        {
            // use JsonConvert to convert entity.Value to a dynamic object.
            dynamic o = JsonConvert.DeserializeObject<dynamic>(entity.Value.ToString());
            if (o.Meeting[0] != null)
            {
                // Find and return the entity type and score.
                var entType = o.Meeting[0].type;
                var entScore = o.Meeting[0].score;
                result = "Entity: " + entType + ", Score: " + entScore + ".";
                
                return result;
            }
        }

        if (schedFound != null)
        {
            // use JsonConvert to convert entity.Value to a dynamic object.
            dynamic o = JsonConvert.DeserializeObject<dynamic>(entity.Value.ToString());
            if (o.Schedule[0] != null)
            {
                // Find and return the entity type and score.
                var entType = o.Schedule[0].type;
                var entScore = o.Schedule[0].score;
                result = "Entity: " + entType + ", Score: " + entScore + ".";
                
                return result;
            }
        }
    }

    // No entities were found, empty string returned.
    return result;
}
```

Isso detectou informações da entidade que podem ser exibidas, juntamente com a intenção do usuário identificado. Para exibir essas informações, adicione as seguinte linhas de código à tarefa _OnTurnAsync_ do código de exemplo, logo após a exibição das informações de intenção.

```cs
// See if LUIS found and used an entity to determine user intent.
var entityFound = ParseLuisForEntities(recognizerResult);

// Inform the user if LUIS used an entity.
if (entityFound.ToString() != string.Empty)
{
   await turnContext.SendActivityAsync($"==>LUIS Entity Found: {entityFound}\n");
}
else
{
   await turnContext.SendActivityAsync($"==>No LUIS Entities Found.\n");
}
```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

O código a seguir pode ser adicionado ao seu bot para extrair informações de entidade do resultado `luisRecognizer` retornado pelo LUIS. Dentro do processamento de `onTurn` do arquivo de exemplo de código, bot.js, adicione a seguinte linha logo após a declaração da constante _topIntent_. Essa ação capturará qualquer informação de entidade retornada: 

```javascript
// Since the LuisRecognizer was configured to include the raw results, get returned entity data.
var entityData = results.luisResult.entities;

```

Para mostrar ao usuário quaisquer informações de entidade retornadas, adicione as seguintes linhas de código, logo após a chamada _sendActivity_ usada dentro do código de exemplo para informar ao usuário quando uma topIntent for encontrada.

```javascript
// See if LUIS found and used an entity to determine user intent.
if (entityData.length > 0)
{
   if ((entityData[0].type == "Appointment") || (entityData[0].type == "Meeting") || (entityData[0].type == "Schedule") )
   {
      // inform user if LUIS used an entity.
      await turnContext.sendActivity(`LUIS Entity Found: Entity: ${entityData[0].entity}, Score: ${entityData[0].score}.`);
   }
}
else{
       await turnContext.sendActivity(`No LUIS Entities Found.`);
}
```

Primeiro, esse código verifica se o LUIS retornou quaisquer informações de entidade no resultado retornado e, se fez isso, exibirá as informações relacionadas à primeira entidade detectada.

---

### <a name="test-bot-with-entities"></a>Testar os bots com entidades

1. Para testar seu bot que inclui entidades, execute o exemplo localmente, conforme explicado [acima](#test-the-bot).

1. No emulador, insira a mensagem mostrada abaixo. 

![testar entrada de exemplo de nlp](./media/nlp-luis-sample-message.png)

Agora, o bot responde com a intenção mais bem pontuada "Calendar_Add", além da entidade "Reuniões" que foi usada pelo LUIS para determinar a intenção do usuário.

![testar resposta de exemplo de nlp](./media/nlp-luis-sample-entity-response.png) 

A detecção de entidades pode ajudar a melhorar o desempenho geral do seu bot. Por exemplo, ao detectar a entidade "Reunião" (mostrada acima) seu pode aplicativo chamar uma função especializada projetada para criar uma nova reunião no calendário do usuário.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Use o QnA Maker para responder a perguntas](./bot-builder-howto-qna.md)
