---
title: Use a ferramenta de distribuição para o LUIS e o QnA Maker | Microsoft Docs
description: Aprenda a usar o criador LUIS e QnA no seu bot.
keywords: Ferramenta do Luis, QnA, expedição, vários serviços
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d3c9355a0e87d31029b92614dc182f3d7010c736
ms.sourcegitcommit: 9a38d76afb0e82fdccc1f36f9b1a65042671e538
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2018
ms.locfileid: "39514936"
---
## <a name="integrate-multiple-luis-apps-and-qna-services-with-the-dispatch-tool"></a>Integrar vários aplicativos LUIS e serviços QnA com a ferramenta de distribuição

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Este tutorial demonstra como usar um modelo LUIS gerado pela ferramenta Despachar, para integrar seu bot com vários aplicativos LUIS (Compreensão de Linguagem) e serviços QnAMaker. 

Imagine que você desenvolveu os seguintes serviços e deseja criar um bot que se integre a todos eles.

| Tipo de serviço | NOME | DESCRIÇÃO |
|------|------|------|
| Aplicativo LUIS | HomeAutomation | Reconhece as tentativas de HomeAutomation.TurnOn, HomeAutomation.TurnOff e HomeAutomation.None.|
| Aplicativo LUIS | Clima | Reconhece as intenções Weather.GetForecast e Weather.GetCondition.|
| Serviço QnAMaker | Perguntas frequentes  | Fornece respostas a perguntas sobre um sistema de iluminação domótica |

Vamos primeiro criar aplicativos e serviços e integrá-los juntos.

> [!NOTE]
> Todos os três aplicativos precisam ser criados dentro do mesmo local do Azure para que o Dispatch os acesse com êxito. Nosso exemplo de código de Despacho abaixo usa o local de exemplo West US.

## <a name="create-the-luis-apps"></a>Crie o LUIS aplicativos

A maneira mais rápida de criar os aplicativos Home Automation e Weather LUIS é fazer o download dos arquivos [home automation.json][HomeAutomationJSON] e arquivos [ weather.json][WeatherJSON]. Em seguida, vá para o [site LUIS](https://www.luis.ai/home) e entre. Clique em **meus aplicativos** > **importação novo aplicativo** e escolha o arquivo homeautomation.json. Nomeie o novo aplicativo `homeautomation`. Clique em **Meus aplicativos** >  **Importe o novo aplicativo** e escolha o arquivo weather.json. Nomeie esse outro aplicativo novo `weather`.

## <a name="create-the-qna-cognitive-service-in-azure"></a>Criar o serviço cognitivo QnA no Azure

Serviço QnA Maker envolve duas partes, que são o serviço cognitivo no Azure e a base de Conhecimento de perguntas e respostas de pares que você publicar usando o serviço cognitivo.

Para criar o serviço cognitivo no Azure, faça logon no portal do Azure em https://portal.azure.com, e faça o seguinte:

1. Clique em **Todos os serviços**.
1. Pesquise `Cognitive` e selecione **os serviços Cognitivos**.
1. Clique em **Adicionar**.
1. Pesquise `QnA` e selecione **QnA Maker**.
1. Na folha do QnA Maker, clique em **criar**.
1. Preencha as informações e crie o serviço QnA Maker.

    <!-- TODO: Add screenshot.-->

    * Digite um nome para o serviço. Para este tutorial, usaremos `SmartLightQnA`.
    * Selecione a assinatura a ser usada.
    * Selecione a camada de preço de gerenciamento a ser usada; usaremos o nível F0 (gratuito).
    * Crie um ou selecione um novo grupo de recursos para usar. Para este tutorial, criaremos um novo `SmartLightQnA` grupo de recursos.
    * Selecione uma camada de preços de pesquisa; vamos usar o nível B (básico).
    * Selecione um local de pesquisa; Vamos usar `West US`.
    * Digite um nome de aplicativo para usar; nós vamos manter o padrão `SmartLightQnA`.
    * Selecione o local do site; Vamos usar `West US`.
    * Mantenha o padrão de habilitar o Application insights.
    * Selecione um local de insights do aplicativo. nós usaremos `West US 2`.
    * Clique em **Criar** para criar seu serviço do Criador de QnA.
    * O Azure cria e começa a implantar seu serviço.

1. Depois que o serviço for implementado, visualize a notificação e clique em **Ir para o recurso** para navegar até o blade do serviço.
1. Clique em **chaves** capturar suas chaves.

    * Copie o nome do serviço e sua primeira chave. Você precisará nas próximas etapas.
    * Você obtém duas chaves para poder regenerar uma delas de cada vez sem ter que interromper seu serviço.

## <a name="create-and-publish-the-qna-maker-knowledge-base"></a>Crie e publique a base de conhecimento do QnA Maker

Vá para o [site do QnA Maker](https://qnamaker.ai) e entre. Selecione **Criar uma base de conhecimento** e crie uma nova base de conhecimento denominada "FAQ". Clique o **Selecionar arquivo** botão e carregar o [exemplo de arquivo TSV][FAQ_TSV]. Clique em **Criar** e, assim que o serviço for criado, clique em **Publicar**.

## <a name="use-the-dispatch-tool-to-create-the-dispatcher-luis-app"></a>Use a ferramenta de expedição para criar o dispatcher do aplicativo LUIS

Em seguida, vamos criar um aplicativo LUIS para combinar cada um dos serviços que criamos.

Instale a [ferramenta de distribuição ][DispatchTool] executando este comando em um prompt de comandos Node.js.

```
npm install -g botdispatch
```

Execute o seguinte comando para inicializar a ferramenta de expedição do nome `CombineWeatherAndLights`. Substitua sua [LUIS a criação de chave](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-concept-keys) para `"YOUR-LUIS-AUTHORING-KEY"`.

```
dispatch init -name CombineWeatherAndLights -luisAuthoringKey "YOUR-LUIS-AUTHORING-KEY" -luisAuthoringRegion westus
```

Para cada um dos aplicativos LUIS que você criou, obtenha o ID do aplicativo LUIS. Aqueles podem ser encontrados para cada aplicativo em **meus aplicativos** sobre o [site LUIS](https://www.luis.ai/home); clique no nome do aplicativo e, em seguida, clique em **configurações** para ver a ID do aplicativo. 

Em seguida, execute o comando `dispatch add` para cada um dos aplicativos LUIS que você criou.

```
dispatch add -type luis -id "HOMEAUTOMATION-APP-ID" -name homeautomation -version 0.1 -key "YOUR-LUIS-AUTHORING-KEY"
dispatch add -type luis -id "WEATHER-APP-ID" -name weather -version 0.1 -key "YOUR-LUIS-AUTHORING-KEY"

```

Execute o `dispatch add` comando para o serviço QnA Maker que você criou. O parâmetro `-key` deve ser a chave do Portal do Azure, que você salvou quando concluiu as etapas para [Criar o Serviço Cognitivo QnA no Azure](./bot-builder-tutorial-dispatch.md#create-the-qna-cognitive-service-in-azure).

```
dispatch add -type qna -id "QNA-KB-ID" -name faq -key "YOUR-QNA-SUBSCRIPTION-KEY"
```

Execute `dispatch create`:

```
dispatch create
```

Isso cria o aplicativo LUIS do dispatcher chamado **CombineWeatherAndLights** . Você pode ver o novo aplicativo no [https://www.luis.ai/home](https://www.luis.ai/home). 

![O aplicativo de dispatcher em LUIS.ai](media/tutorial-dispatch/dispatch-app-in-luis.png)

Clique no novo aplicativo. Em **Intents**, você pode ver que tem as intenções de `l_homeautomation`, `l_weather`, e `q_faq`.

![As tentativas de dispatcher em LUIS.ai](media/tutorial-dispatch/dispatch-intents-in-luis.png)

Clique no botão **Treinar** para treinar o aplicativo LUIS e use a guia **PUBLISH** para [publicá-lo](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp). Clique em **configurações** para copiar a ID do novo aplicativo para usar em seu bot.

## <a name="create-the-bot"></a>Criar o bot

Agora você pode ligar as intenções do aplicativo dispatcher à lógica em seu bot, que roteia as mensagens para os aplicativos LUIS originais e o serviço QnAMaker.

Você pode usar o exemplo incluído com o SDK do Bot Builder como ponto de partida.

# <a name="ctabcsaddref"></a>[C#](#tab/csaddref)

Iniciar com o código na [exemplo de expedição de LUIS][DispatchBotCS]. No Visual Studio, [ atualize os pacotes Nuget ](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui#updating-a-package) para as versões de pré-lançamento mais recentes dos seguintes:

* `Microsoft.Bot.Builder.Integration.AspNet.Core`
* `Microsoft.Bot.Builder.Ai.QnA` (necessário para o QnA Maker)
* `Microsoft.Bot.Builder.Ai.Luis` (necessário para LUIS)

# <a name="javascripttabjsaddref"></a>[JavaScript](#tab/jsaddref)

Baixe o [exemplo de expedição de LUIS][DispatchBotJs].  Instale os pacotes necessários, incluindo o pacote `botbuilder-ai` para o LUIS e o Criador QnA, usando o npm:

* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---


Configure o exemplo para usar o aplicativo de dispatcher.

# <a name="ctabcsbotconfig"></a>[C#](#tab/csbotconfig)

Na **appSettings. JSON** na [amostra de expedição de LUIS][DispatchBotCS], edite os campos a seguir.

| NOME | DESCRIÇÃO |
|------|------|
| `Luis-SubscriptionKey` |  Sua chave de assinatura do LUIS. Isso pode ser uma chave de ponto de extremidade ou uma chave de autoria, descrita [aqui](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-concept-keys). | 
| `Luis-ModelId-Dispatcher` | ID do aplicativo para o aplicativo LUIS que a ferramenta de distribuição gera. | 
| `Luis-ModelId-HomeAutomation` | ID do aplicativo que você criou a partir de homeautomation.json  | 
| `Luis-ModelId-Weather` | ID do aplicativo que você criou a partir do weather.json | 
| `QnAMaker-Endpoint-Url` | Isso deve ser definido como https://westus.api.cognitive.microsoft.com/qnamaker/v2.0 para os serviços do Preview QnA Maker. <br/>Defina isso como https://YOUR-QNA-SERVICE-NAME.azurewebsites.net/qnamaker para novos serviços (GA) QnA Maker.|
| `QnAMaker-SubscriptionKey` | Sua chave de assinatura do QnA Maker. | 
| `QnAMaker-KnowledgeBaseId` | A ID da base de conhecimento que você criar à [QnAMaker portal](https://qnamaker.ai).| 



Em **Startup.cs**, dê uma olhada no método `ConfigureServices`. Ele contém código para inicializar `LuisRecognizerMiddleware` usando o ID do aplicativo LUIS que você acabou de gerar.

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(this.Configuration);
    services.AddBot<LuisDispatchBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        var (luisModelId, luisSubscriptionKey, luisUri) = GetLuisConfiguration(this.Configuration, "Dispatcher");

        var luisModel = new LuisModel(luisModelId, luisSubscriptionKey, luisUri);

        // If you want to get all the intents and scores that LUIS recognized, set Verbose = true in luisOptions
        var luisOptions = new LuisRequest { Verbose = true };

        var middleware = options.Middleware;
        middleware.Add(new LuisRecognizerMiddleware(luisModel, luisOptions: luisOptions));
    });
}
```
Os métodos `GetLuisConfiguration` e `GetQnAMakerConfiguration` obtêm sua configuração LUIS e QnA do appsettings.json.
```csharp
public static (string modelId, string subscriptionId, Uri uri) GetLuisConfiguration(IConfiguration configuration, string serviceName)
{
    var modelId = configuration.GetSection($"Luis-ModelId-{serviceName}")?.Value;
    var subscriptionId = configuration.GetSection("Luis-SubscriptionKey")?.Value;
    var uri = new Uri(configuration.GetSection("Luis-Url")?.Value);
    return (modelId, subscriptionId, uri);
}

public static (string knowledgeBaseId, string subscriptionKey, string uri) GetQnAMakerConfiguration(IConfiguration configuration)
{
    var knowledgeBaseId = configuration.GetSection("QnAMaker-KnowledgeBaseId")?.Value;
    var subscriptionKey = configuration.GetSection("QnAMaker-SubscriptionKey")?.Value;
    var uri = configuration.GetSection("QnAMaker-Endpoint-Url")?.Value;
    return (knowledgeBaseId, subscriptionKey, uri);
}
```

### <a name="dispatch-the-message"></a>A mensagem de expedição

Dê uma olhada no **LuisDispatchBot.cs**, onde o bot despacha a mensagem para o aplicativo LUIS ou o Criador QnA para um subcomponente. 

Em `DispatchToTopIntent`, se seu aplicativo de despachante detectar a intenção `l_homeautomation` ou `l_weather`, ele chama um método `DispatchToTopIntent` que cria um `LuisRecognizer` para chamar os aplicativos `homeautomation` e `weather` originais. Se o bot detecta os `q_faq` intenção, ou o `none` intenção que é usada como um caso de fallback, ele chama um método que consulta QnAMaker.

> [!NOTE] 
> Se os nomes de intenção `l_homeautomation`, `l_weather` ou `q_faq` não coincidir com o aplicativo LUIS que você criou usando a expedição, editá-los de acordo com a versão de letras minúsculas dos nomes de intenção, você vê no [portal LUIS](https://www.luis.ai).

```csharp
private async Task DispatchToTopIntent(ITurnContext context, (string intent, double score)? topIntent)
{
    switch (topIntent.Value.intent.ToLowerInvariant())
    {
        case "l_homeautomation":
            await DispatchToLuisModel(context, this.luisModelHomeAutomation, "home automation");
            break;
        case "l_weather":
            await DispatchToLuisModel(context, this.luisModelWeather, "weather");
            break;
        case "none":
        // You can provide logic here to handle the known None intent (none of the above).
        // In this example we fall through to the QnA intent.
        case "q_faq":
            await DispatchToQnAMaker(context, this.qnaEndpoint, "FAQ");
            break;
        default:
            // The intent didn't match any case, so just display the recognition results.
            await context.SendActivity($"Dispatch intent: {topIntent.Value.intent} ({topIntent.Value.score}).");

            break;
    }
}
```

O método `DispatchToQnAMaker` envia a mensagem do usuário para o serviço do Criador QnA. Certifique-se de ter publicado esse serviço no [portal QnA Maker](https://qnamaker.ai) antes de executar o bot.

```csharp
private static async Task DispatchToQnAMaker(ITurnContext context, QnAMakerEndpoint qnaOptions, string appName)
{
    QnAMaker qnaMaker = new QnAMaker(qnaOptions);
    if (!string.IsNullOrEmpty(context.Activity.Text))
    {
        var results = await qnaMaker.GetAnswers(context.Activity.Text.Trim()).ConfigureAwait(false);
        if (results.Any())
        {
            await context.SendActivity(results.First().Answer);
        }
        else
        {
            await context.SendActivity($"Couldn't find an answer in the {appName}.");
        }
    }
}
```

O `DispatchToLuisModel` método envia a mensagem do usuário para o original `homeautomation` e `weather` aplicativos LUIS. Certifique-se de ter publicado esses aplicativos LUIS no [portal LUIS](https://www.luis.ai) antes de executar o bot.

```csharp
private static async Task DispatchToLuisModel(ITurnContext context, LuisModel luisModel, string appName)
{
    await context.SendActivity($"Sending your request to the {appName} system ...");
    var (intents, entities) = await RecognizeAsync(luisModel, context.Activity.Text);

    await context.SendActivity($"Intents detected by the {appName} app:\n\n{string.Join("\n\n", intents)}");

    if (entities.Count() > 0)
    {
        await context.SendActivity($"The following entities were found in the message:\n\n{string.Join("\n\n", entities)}");
    }
    
    // Here, you can add code for calling the hypothetical home automation or weather service, 
    // passing in the appName and any intent or entity information that you need 
}
```

O `RecognizeAsync` chamadas de método um `LuisRecognizer` obter resultados de um aplicativo LUIS.

```cs
private static async Task<(IEnumerable<string> intents, IEnumerable<string> entities)> RecognizeAsync(LuisModel luisModel, string text)
{
    var luisRecognizer = new LuisRecognizer(luisModel);
    var recognizerResult = await luisRecognizer.Recognize(text, System.Threading.CancellationToken.None);

    // list the intents
    var intents = new List<string>();
    foreach (var intent in recognizerResult.Intents)
    {
        intents.Add($"'{intent.Key}', score {intent.Value}");
    }

    // list the entities
    var entities = new List<string>();
    foreach (var entity in recognizerResult.Entities)
    {
        if (!entity.Key.ToString().Equals("$instance"))
        {
            entities.Add($"{entity.Key}: {entity.Value.First}");
        }
    }

    return (intents, entities);
}
```

# <a name="javascripttabjsbotconfig"></a>[JavaScript](#tab/jsbotconfig)

Comece com o código no exemplo de [Dispatch bot][DispatchBotJs]. Abra **App. js** e, opcionalmente, substituir o `appId` campos com as IDs dos aplicativos LUIS criados por você. Se você deixar o `appId` campos conforme elas forem originalmente estiverem, você estará usando aplicativos de LUIS públicos criados para fins de demonstração.

```javascript
// Create LuisRecognizers and QnAMaker
// The LUIS applications are public, meaning you can use your own subscription key to test the applications.
// For QnAMaker, users are required to create their own knowledge base.
// The exported LUIS applications and QnAMaker knowledge base can be found with the sample bot.

// The corresponding LUIS application JSON is `dispatchSample.json`
const dispatcher = new LuisRecognizer({
    appId: '0b18ab4f-5c3d-4724-8b0b-191015b48ea9',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});

// The corresponding LUIS application JSON is `homeautomation.json`
const homeAutomation = new LuisRecognizer({
    appId: 'c6d161a5-e3e5-4982-8726-3ecec9b4ed8d',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});

// The corresponding LUIS application JSON is `weather.json`
const weather = new LuisRecognizer({
    appId: '9d0c9e9d-ce04-4257-a08a-a612955f2fb5',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});
```

No código a seguir, substitua os `endpointKey` e `knowledgeBaseID` pela sua ID de chave e da Base de dados de conhecimento do QnAMaker. O `host` deve ser definido como `https://westus.api.cognitive.microsoft.com/qnamaker/v2.0` para os serviços do QnA Maker de pré-visualização e `https://YOUR-QNA-SERVICE-NAME.azurewebsites.net/qnamaker` para os novos serviços do QnA Maker (GA).

```javascript
const faq = new QnAMaker(
    {
        knowledgeBaseId: '',
        endpointKey: '',
        host: ''
    },
    {
        answerBeforeNext: true
    }
);

```

O restante do código na **App. js** identificadores de `l_homeautomation`, `l_weather`, e `None` intenções abrindo caixas de diálogo apropriadas. No caso da intenção `q_faq`, ela responde com a resposta do serviço QnAMaker.

> [!NOTE] 
> Se os nomes de intenção `l_homeautomation`, `l_weather` ou `q_faq` não coincidir com o aplicativo LUIS que você criou usando a expedição, editá-los para corresponder aos nomes de intenção, você vê no [portal LUIS](https://www.luis.ai).

```javascript
// create conversation state
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// register some dialogs for usage with the LUIS apps that are being dispatched to
const dialogs = new DialogSet();

function findEntities(entityName, entityResults) {
    let entities = []
    if (entityName in entityResults) {
        entityResults[entityName].forEach((entity, idx) => {
            entities.push(entity);
        });
    }
    return entities.length > 0 ? entities : undefined;
}

dialogs.add('HomeAutomation_TurnOff', [
    async (dialogContext, args) => {
        const devices = findEntities('HomeAutomation_Device', args.entities);
        const operations = findEntities('HomeAutomation_Operation', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.homeAutomationTurnOff = state.homeAutomationTurnOff ? state.homeAutomationTurnOff + 1 : 1;
        await dialogContext.context.sendActivity(`${state.homeAutomationTurnOff}: You reached the "HomeAutomation.TurnOff" dialog.`);
        if (devices) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Device" entities:\n${devices.join(', ')}`);
        }
        if (operations) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Operation" entities:\n${operations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);

dialogs.add('HomeAutomation_TurnOn', [
    async (dialogContext, args) => {
        const devices = findEntities('HomeAutomation_Device', args.entities);
        const operations = findEntities('HomeAutomation_Operation', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.homeAutomationTurnOn = state.homeAutomationTurnOn ? state.homeAutomationTurnOn + 1 : 1;
        await dialogContext.context.sendActivity(`${state.homeAutomationTurnOn}: You reached the "HomeAutomation.TurnOn" dialog.`);
        if (devices) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Device" entities:\n${devices.join(', ')}`);
        }
        if (operations) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Operation" entities:\n${operations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);

dialogs.add('Weather_GetForecast', [
    async (dialogContext, args) => {
        const locations = findEntities('Weather_Location', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.weatherGetForecast = state.weatherGetForecast ? state.weatherGetForecast + 1 : 1;
        await dialogContext.context.sendActivity(`${state.weatherGetForecast}: You reached the "Weather.GetForecast" dialog.`);
        if (locations) {
            await dialogContext.context.sendActivity(`Found these "Weather_Location" entities:\n${locations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);

dialogs.add('Weather_GetCondition', [
    async (dialogContext, args) => {
        const locations = findEntities('Weather_Location', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.weatherGetCondition = state.weatherGetCondition ? state.weatherGetCondition + 1 : 1;
        await dialogContext.context.sendActivity(`${state.weatherGetCondition}: You reached the "Weather.GetCondition" dialog.`);
        if (locations) {
            await dialogContext.context.sendActivity(`Found these "Weather_Location" entities:\n${locations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);

dialogs.add('None', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        state.noneIntent = state.noneIntent ? state.noneIntent + 1 : 1;
        await dialogContext.context.sendActivity(`${state.noneIntent}: You reached the "None" dialog.`);
        await dialogContext.end();
    }
]);

adapter.use(dispatcher);

// Listen for incoming Activities
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const dc = dialogs.createContext(context, state);

            // Retrieve the LUIS results from our dispatcher LUIS application
            const luisResults = dispatcher.get(context);

            // Extract the top intent from LUIS and use it to select which LUIS application to dispatch to
            const topIntent = LuisRecognizer.topIntent(luisResults);

            const isMessage = context.activity.type === 'message';
            if (isMessage) {
                switch (topIntent) {
                    case 'l_homeautomation':
                        const homeAutoResults = await homeAutomation.recognize(context);
                        const topHomeAutoIntent = LuisRecognizer.topIntent(homeAutoResults);
                        await dc.begin(topHomeAutoIntent, homeAutoResults);
                        break;
                    case 'l_weather':
                        const weatherResults = await weather.recognize(context);
                        const topWeatherIntent = LuisRecognizer.topIntent(weatherResults);
                        await dc.begin(topWeatherIntent, weatherResults);
                        break;
                    case 'q_faq':
                        await faq.answer(context);
                        break;
                    default:
                        await dc.begin('None');
                }
            }

            if (!context.responded) {
                await dc.continue();
                if (!context.responded && isMessage) {
                    await dc.context.sendActivity(`Hi! I'm the LUIS dispatch bot. Say something and LUIS will decide how the message should be routed.`);
                }
            }
        }
    });
});

```

---
## <a name="run-the-bot"></a>Execute o bot

Teste o bot usando o [Bot Framework Emulator](../bot-service-debug-emulator.md). Enviar mensagens, como "ativar as luzes" para expedir a mensagem para a automação residencial aplicativo LUIS e lhe enviar mensagens, como "obter informações sobre o clima em Seattle" para o qual expedir o clima aplicativo LUIS.

> [!NOTE] 
> Antes de executar o bot, certifique-se de ter publicado todos os aplicativos LUIS que você criou no [portal LUIS](https://www.luis.ai), e verifique se você publicou o serviço QnA Maker no [portal QnA Maker](https://qnamaker.ai).

![Enviar mensagens para o bot de expedição](media/tutorial-dispatch/run-dispatch-bot.png)

## <a name="evaluate-the-dispatchers-performance"></a>Avaliar o desempenho do despachante

Às vezes, há mensagens do usuário fornecidas como exemplos nos aplicativos LUIS e nos serviços do fabricante do QnA, e o aplicativo LUIS combinado que o Dispatch gera não terá um bom desempenho para essas entradas. Verificar o desempenho do seu aplicativo usando o `eval` opção. 

```
dispatch eval
```

Executando `dispatch eval` gera uma **Summary.html** arquivo que fornece estatísticas sobre o desempenho do novo modelo de idioma.

> [!TIP] 
> Você pode realmente executar `dispatch eval` em qualquer aplicativo LUIS, não apenas em aplicativos LUIS criados pela ferramenta de distribuição.

### <a name="edit-intents-for-duplicates-and-overlaps"></a>Editar as intenções de duplicatas e sobreposições

Examine as declarações de exemplo são sinalizadas como duplicatas na **Summary.html**e remover exemplos semelhantes ou sobrepostos. Por exemplo, digamos que no `homeautomation` aplicativo LUIS para homeautomation, solicitações, como "ativar minha luzes" são mapeados para uma intenção "TurnOnLights", mas as solicitações, como "Por que não meu luzes ativar?" mapear para uma intenção "Nenhum" para que eles possam ser passados para o criador do QnA. Quando você combina o aplicativo LUIS e o serviço do criador QnA usando o despacho, é necessário executar um dos seguintes procedimentos: 

* Remova o "None" intenção original `homeautomation` aplicativo LUIS e adicione as declarações nessa intenção de "None" intencional no aplicativo do dispatcher.
* Se você não remover a intenção "Nenhum" do aplicativo LUIS original, será necessário adicionar lógica em seu bot para passar as mensagens que correspondem a essa intenção para o serviço do criador QnA.

> [!TIP] 
> Analise [Práticas recomendadas para o entendimento de idiomas](./bot-builder-concept-luis.md#best-practices-for-language-understanding) para obter dicas sobre como melhorar o desempenho do seu modelo de idioma.

## <a name="additional-resources"></a>Recursos adicionais

* [Usando o LUIS para o fluxo da conversa][luis-v4-how-to]

[luis-v4-how-to]: bot-builder-howto-v4-luis.md
<!-- links -->

[HomeAutomationJSON]: https://aka.ms/dispatch-luis1
[WeatherJSON]: https://aka.ms/dispatch-luis2
[DispatchJSON]: https://aka.ms/dispatch-luis
[FAQ_TSV]: https://aka.ms/dispatch-qna-tsv

[DispatchTool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch
[DispatchBotCS]: https://aka.ms/dispatch-sample-cs
[DispatchBotJs]: https://aka.ms/dispatch-sample-js


