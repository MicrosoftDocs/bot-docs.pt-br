---
title: Usando LUIS para Compreensão de Linguagem | Microsoft Docs
description: Aprenda a usar o LUIS para entender a linguagem natural com o SDK do Bot Builder.
keywords: Entendimento de Linguagem, LUIS, intenção, reconhecedor, entidades, middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/19/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c4a7cfba6f588c95dbebf4886ffd7e432d99c3ae
ms.sourcegitcommit: 3cb288cf2f09eaede317e1bc8d6255becf1aec61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47389795"
---
# <a name="using-luis-for-language-understanding"></a>Usando o LUIS para Compreensão de Linguagem

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A capacidade de entender o que seu usuário significa em conversação e contextualmente pode ser uma tarefa difícil, mas pode dar ao seu bot uma sensação de conversa mais natural. O Language Understanding, chamado LUIS, permite que você faça exatamente isso para que seu bot possa reconhecer a intenção das mensagens do usuário, permitir uma linguagem mais natural do seu usuário e direcionar melhor o fluxo de conversação. Se você precisar de mais informações sobre como o LUIS se integra com um bot, veja [entendimento de linguagem para bots](./bot-builder-concept-LUIS.md). 

Este tópico orienta você ao configurar bots simples que usam o LUIS para reconhecer algumas intenções diferentes.

## <a name="installing-packages"></a>Instalando pacotes

Primeiro, verifique se você possui os pacotes necessários para o LUIS.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Adicione uma referência](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) para a versão v4 dos seguintes pacotes NuGet:


* `Microsoft.Bot.Builder.AI.LUIS`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Instale os pacotes botbuilder e botbuilder-ai em seu projeto via npm:

* `npm install --save botbuilder`
* `npm install --save botbuilder-ai`

---

## <a name="set-up-your-luis-app"></a>Configurar seu aplicativo LUIS

Primeiro, configure um _aplicativo LUIS_, que é um serviço que você cria em [luis.ai](https://www.luis.ai). Esse aplicativo LUIS pode ser treinado para certas intenções que ele deve ser capaz de reconhecer. Detalhes sobre como criar seu aplicativo LUIS podem ser encontrados no site LUIS.

Para este exemplo, você apenas usará um aplicativo LUIS demo que pode reconhecer as intenções de Ajuda, Cancelar e Tempo; o ID do aplicativo já está no código de amostra. Você precisará ter uma chave de Serviços Cognitivos que possa obter fazendo login em [luis.ai](https://www.luis.ai) e copiando a chave de **Configurações do usuário** > **Chave de Criação**.

> [!NOTE]
> Para criar sua própria cópia do aplicativo LUIS público usado neste exemplo, copie o arquivo LUIS [JSON](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/simple-bot-example/FirstSimpleBotExample.json). Em seguida, [importe](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app) o aplicativo LUIS, [treine](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) e [publique-o](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp). Substitua a ID do aplicativo público no código de exemplo com a ID do aplicativo do seu novo aplicativo LUIS.


### <a name="configure-your-bot-to-call-your-luis-app"></a>Configure seu bot para chamar seu aplicativo LUIS.

# <a name="ctabcs"></a>[C#](#tab/cs)

Embora seja possível criar e chamar seu aplicativo LUIS em cada turno, a melhor prática é a codificação para registrar seu serviço LUIS como um singleton e, em seguida, passá-lo como um parâmetro para o construtor do bot. Vamos mostrar que o método aqui é um pouco mais complicado.

Comece com o modelo de bot de eco e abra **Startup.cs**. 

Adicionar um `using` instrução para `Microsoft.Bot.Builder.AI.LUIS`

```csharp
// add this
using Microsoft.Bot.Builder.AI.LUIS;
```

Adicione o código a seguir no final de `ConfigureServices`, após a inicialização do estado. Isso capturará as informações do arquivo `appsettings.json`. No entanto, essas cadeia de caracteres podem ser obtidas no arquivo `.bot`, como o exemplo vinculado no final deste artigo, ou codificadas para teste.

O singleton retorna um novo `LuisRecognizer` para o construtor.

```csharp
    // Create and register a LUIS recognizer.
    services.AddSingleton(sp =>
    {
        // Get LUIS information from appsettings.json.
        var section = this.Configuration.GetSection("Luis");
        var luisApp = new LuisApplication(
            applicationId: section["AppId"],
            endpointKey: section["SubscriptionKey"],
            azureRegion: section["Region"]);

        // Specify LUIS options. These may vary for your bot.
        var luisPredictionOptions = new LuisPredictionOptions
        {
            IncludeAllIntents = true,
        };

        return new LuisRecognizer(
            application: luisApp,
            predictionOptions: luisPredictionOptions,
            includeApiResults: true);
    });
```

Cole sua chave de assinatura de [luis.ai](https://www.luis.ai) no lugar de `<subscriptionKey>`. O processo é mais simples se você clicar no nome de sua conta no canto superior direito e acessar **Configurações**, onde ele é chamado de **Chave de Autoria**.

> [!NOTE]
> Se você estiver usando seu próprio aplicativo LUIS em vez do público, obtenha a ID, a chave de assinatura e a URL do aplicativo LUIS em [luis.ai](https://www.luis.ai). Elas podem ser encontradas nas guias **Publicar** e **Configurações** na página do aplicativo.
>
>Para encontrar a URL base para usar no `LuisModel`, faça logon em [luis.ai](https://www.luis.ai), acesse a guia **Publicar** e, em seguida, observe a coluna **Ponto de extremidade** em  **Recursos e as chaves**. O URL base é a parte do **URL do endpoint** antes do ID da assinatura e de outros parâmetros.

Em seguida, precisamos dar ao bot esta instância do LUIS. Abra **EchoBot.cs** e, na parte superior do arquivo, adicione o seguinte código. Para fins de referência, o cabeçalho da classe e os itens de estado também estão incluídos, mas eles não serão explicados aqui.

```csharp
public class EchoBot : IBot
{
    /// <summary>
    /// Gets the Echo Bot state.
    /// </summary>
    private IStatePropertyAccessor<EchoState> EchoStateAccessor { get; }

    /// <summary>
    /// Gets the LUIS recognizer.
    /// </summary>
    private LuisRecognizer Recognizer { get; } = null;

    public EchoBot(ConversationState state, LuisRecognizer luis)
    {
        EchoStateAccessor = state.CreateProperty<EchoState>("EchoBot.EchoState");

        // The incoming luis variable is the LUIS Recognizer we added above.
        this.Recognizer = luis ?? throw new ArgumentNullException(nameof(luis));
    }

    /// ...
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Primeiro, siga as etapas no [início rápido](../javascript/bot-builder-javascript-quickstart.md) do JavaScript para criar um bot. Aqui estamos codificando nossas informações do LUIS no bot, mas elas podem ser extraídas do arquivo `.bot`, como no exemplo vinculado no final deste artigo.

No novo bot, edite **app.js** para exigir a classe `LuisRecognizer` e criar uma instância para seu modelo LUIS:

```javascript
const { ActivityTypes } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');

const luisApplication = {
    // This appID is for a public app that's made available for demo purposes
    // You can use it or use your own LUIS "Application ID" at https://www.luis.ai under "App Settings".
     applicationId: 'eb0bf5e0-b468-421b-9375-fdfb644c512e',
    // Replace endpointKey with your "Subscription Key"
    // your key is at https://www.luis.ai under Publish > Resources and Keys, look in the Endpoint column
    // The "subscription-key" is embeded in the Endpoint link. 
    endpointKey: '<your subscription key>',
    // You can find your app's region info embeded in the Endpoint link as well.
    // Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
    azureRegion: 'westus'
}

// Create configuration for LuisRecognizer's runtime behavior.
const luisPredictionOptions = {
    includeAllIntents: true,
    log: true,
    staging: false
}

// Create the bot that handles incoming Activities.
const luisBot = new LuisBot(luisApplication, luisPredictionOptions);
```

Em seguida, no construtor do bot `LuisBot`, obtenha o aplicativo para criar a instância LuisRecognizer.

```javascript
    /**
     * The LuisBot constructor requires one argument (`application`) which is used to create an instance of `LuisRecognizer`.
     * @param {object} luisApplication The basic configuration needed to call LUIS. In this sample the configuration is retrieved from the .bot file.
     * @param {object} luisPredictionOptions (Optional) Contains additional settings for configuring calls to LUIS.
     */
    constructor(application, luisPredictionOptions) {
        this.luisRecognizer = new LuisRecognizer(application, luisPredictionOptions, true);
    }
```

> [!NOTE] 
> Se você estiver usando seu próprio aplicativo LUIS em vez do público, obtenha a ID, a chave de assinatura e a região do seu aplicativo LUIS em [https://www.luis.ai](https://www.luis.ai). Podem ser encontradas nas guias Publicar e Configurações na página do aplicativo.

---

O reconhecimento vocal do LUIS já está configurado em seu bot. Em seguida, vamos ver como obter a intenção do LUIS.

## <a name="get-the-intent-by-calling-luis"></a>Obter a intenção chamando o LUIS

O bot recebe resultados do LUIS chamando o reconhecedor do LUIS.

# <a name="ctabcs"></a>[C#](#tab/cs)

Para que seu bot apenas envie uma resposta com base na intenção que o aplicativo LUIS detectou, chame o `LuisRecognizer` para receber um `RecognizerResult`. Isso pode ser feito no código sempre que você precisar da intenção do LUIS.

```cs
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;
// add this reference
using Microsoft.Bot.Builder.AI.LUIS;

namespace EchoBot
{
    public class EchoBot : IBot
    {
        /// <summary>
        /// Echo bot turn handler 
        /// </summary>
        /// <param name="context">Turn scoped context containing all the data needed
        /// for processing this conversation turn. </param>        
        public async Task OnTurnAsync(ITurnContext context, System.Threading.CancellationToken token)
        {            
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {
                // Call LUIS recognizer
                var result = this.Recognizer.RecognizeAsync(context, System.Threading.CancellationToken.None);
                
                var topIntent = result?.GetTopScoringIntent();

                switch ((topIntent != null) ? topIntent.Value.intent : null)
                {
                    case null:
                        await context.SendActivity("Failed to get results from LUIS.");
                        break;
                    case "None":
                        await context.SendActivity("Sorry, I don't understand.");
                        break;
                    case "Help":
                        await context.SendActivity("<here's some help>");
                        break;
                    case "Cancel":
                        // Cancel the process.
                        await context.SendActivity("<cancelling the process>");
                        break;
                    case "Weather":
                        // Report the weather.
                        await context.SendActivity("The weather today is sunny.");
                        break;
                    default:
                        // Received an intent we didn't expect, so send its name and score.
                        await context.SendActivity($"Intent: {topIntent.Value.intent} ({topIntent.Value.score}).");
                        break;
                }
            }
        }
    }    
}

```

Qualquer intenções reconhecidas na declaração serão retornadas como um mapa de nomes de intenção de pontuações e podem ser acessadas de `result.Intents`. Um método estático `LuisRecognizer.topIntent()` é fornecido para ajudar a simplificar a localização da intenção de pontuação superior para um conjunto de resultados.

Quaisquer entidades reconhecidas serão retornadas como um mapa de nomes de entidades para valores e acessadas via `results.entities`. Metadados de entidade adicionais podem ser retornados, passando um `verbose=true` definir ao criar o LuisRecognizer. Os metadados adicionados podem ser acessados via `results.entities.$instance`.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Edite o código para escutar a atividade de entrada para que ela chame `LuisRecognizer` para obter um `RecognizerResult`.

```javascript
const { ActivityTypes } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            // Perform a call to LUIS to retrieve results for the user's message.
            const results = await this.luisRecognizer.recognize(turnContext);

            // Since the LuisRecognizer was configured to include the raw results, get the `topScoringIntent` as specified by LUIS.
            const topIntent = results.luisResult.topScoringIntent;
            
            switch (topIntent) {
                case 'None':
                    await context.sendActivity("Sorry, I don't understand.")
                    break;
                case 'Cancel':
                    await context.sendActivity("<cancelling the process>")
                    break;
                case 'Help':
                    await context.sendActivity("<here's some help>");
                    break;
                case 'Weather':
                    await context.sendActivity("The weather today is sunny.");
                    break;                        
                case 'null':
                    await context.sendActivity("Failed to get results from LUIS.")
                    break;
                default:
                    // Received an intent we didn't expect, so send its name and score.
                    await context.sendActivity(`The top intent was ${topIntent}`);
            }
        }
    });
});
```

Qualquer intenções reconhecidas na declaração serão retornadas como um mapa de nomes de intenção de pontuações e podem ser acessadas de `results.intents`. Um método estático `LuisRecognizer.topIntent()` é fornecido para ajudar a simplificar a localização da intenção de pontuação superior para um conjunto de resultados.


---

Tente executar o bot no Emulador do Bot Framework e diga coisas como "weather", "help" e "cancel" para ele.

![executar o bot](./media/how-to-luis/run-luis-bot.png)

## <a name="extract-entities"></a>Extrair entidades

Além de reconhecer a intenção, um aplicativo LUIS também pode extrair entidades, que são palavras importantes para atender a solicitação de um usuário. Por exemplo, no bot de previsão do tempo, o aplicativo LUIS pode conseguir extrair o local do relatório meteorológico da mensagem do usuário.

Uma maneira comum de estruturar sua conversa é identificar qualquer entidade na mensagem do usuário e solicitar qualquer uma das entidades necessárias que não foram encontradas. Em seguida, as etapas subsequentes manipula a resposta para o prompt.

# <a name="ctabcs"></a>[C#](#tab/cs)

Vamos dizer que a mensagem do usuário foi "Qual é o tempo em Seattle"? O `LuisRecognizer` oferece um `RecognizerResult` com uma propriedade `Entities` que tem a seguinte estrutura:

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

A função auxiliar seguinte pode ser adicionada ao bot para obter entidades fora do `RecognizerResult` no LUIS. Ela exigirá o uso da biblioteca `Newtonsoft.Json.Linq` que você terá que adicionar às instruções **using**.

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

Ao coletar informações como entidades de várias etapas em uma conversa, pode ser útil salvar as informações necessárias no estado. Se uma entidade for encontrada, ela poderá ser adicionada ao campo de estado apropriado. Na conversa, se a etapa atual já tiver o campo associado concluído, a etapa para solicitar informações poderá ser ignorada.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Vamos dizer que a mensagem do usuário foi "Qual é o tempo em Seattle"? O `LuisRecognizer` oferece um `RecognizerResult` com uma propriedade `entities` que tem a seguinte estrutura:

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

Essa função `findEntities` procura entidades reconhecidas pelo aplicativo LUIS que correspondam à entrada `entityName`.


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

Ao coletar informações como entidades de várias etapas em uma conversa, pode ser útil salvar as informações necessárias no estado. Se uma entidade for encontrada, ela poderá ser adicionada ao campo de estado apropriado. Na conversa, se a etapa atual já tiver o campo associado concluído, a etapa para solicitar informações poderá ser ignorada.

## <a name="additional-resources"></a>Recursos adicionais

Para obter um exemplo usando o LUIS, confira os projetos para [[C#](https://aka.ms/cs-luis-sample)] ou [[JavaScript](https://aka.ms/js-luis-sample)].

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Combinar o LUIS e os serviços QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)
