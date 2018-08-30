---
title: Usando LUIS para Compreensão de Linguagem | Microsoft Docs
description: Aprenda a usar o LUIS para entender a linguagem natural com o SDK do Bot Builder.
keywords: Entendimento de Linguagem, LUIS, intenção, reconhecedor, entidades, middleware
author: ivorb
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/30/17
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 88651a2c86698d55e3a429d7ea62662976d2115f
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42906160"
---
# <a name="using-luis-for-language-understanding"></a>Usando o LUIS para Compreensão de Linguagem

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A capacidade de entender o que seu usuário significa em conversação e contextualmente pode ser uma tarefa difícil, mas pode dar ao seu bot uma sensação de conversa mais natural. O Language Understanding, chamado LUIS, permite que você faça exatamente isso para que seu bot possa reconhecer a intenção das mensagens do usuário, permitir uma linguagem mais natural do seu usuário e direcionar melhor o fluxo de conversação. Se você precisar de mais informações sobre como o LUIS se integra com um bot, veja [entendimento de linguagem para bots](./bot-builder-concept-LUIS.md). 

Este tópico orienta você ao configurar bots simples que usam o LUIS para reconhecer algumas intenções diferentes.

## <a name="installing-packages"></a>Instalando pacotes

Primeiro, verifique se você possui os pacotes necessários para o LUIS.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Adicione uma referência](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) para a versão de pré-lançamento v4 dos seguintes pacotes NuGet:


* `Microsoft.Bot.Builder.Ai.LUIS`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Você pode adicionar referência ao pacote botbuilder e botbuilder-ai em seu projeto via npm:

* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---


## <a name="set-up-middleware-to-use-your-luis-app"></a>Configurar o middleware para usar seu aplicativo LUIS

Primeiro, configure um _aplicativo LUIS_, que é um serviço que você cria em [www.luis.ai](https://www.luis.ai). Esse aplicativo LUIS pode ser treinado para certas intenções que ele deve ser capaz de reconhecer. Detalhes sobre como criar seu aplicativo LUIS podem ser encontrados no [site LUIS](https://www.luis.ai).

Para este exemplo, você apenas usará um aplicativo LUIS demo que pode reconhecer as intenções de Ajuda, Cancelar e Tempo; o ID do aplicativo já está no código de amostra. Você precisará ter uma chave de Serviços Cognitivos que possa obter fazendo login em [www.luis.ai](https://www.luis.ai) e copiando a chave de **Configurações do usuário** > **Chave de Criação**.

> [!NOTE] 
> Para criar sua própria cópia do aplicativo LUIS público usado neste exemplo, copie o arquivo LUIS [FirstSimpleBotExample.json](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/simple-bot-example/FirstSimpleBotExample.json). Em seguida, [importe o aplicativo LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app), [treine](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) e [publique](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp). Substitua a ID do aplicativo público no código de exemplo com a ID do aplicativo do seu novo aplicativo LUIS.

Configure seu bot para chamar seu aplicativo LUIS para cada mensagem recebida de um usuário simplesmente adicionando-o à pilha de middlewares do seu bot. O middleware armazena os resultados de reconhecimento no objeto de contexto e pode ser acessado pela sua lógica de bot.

# <a name="ctabcs"></a>[C#](#tab/cs)
Comece com o modelo de bot de eco e abra **Startup.cs**. 

Adicionar um `using` instrução para `Microsoft.Bot.Builder.Ai.LUIS`

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
// add this
using Microsoft.Bot.Builder.Ai.LUIS;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System;
```

Atualização do `ConfigureServices` método no seu `Startup.cs` arquivo para adicionar um `LuisRecognizerMiddleware` objeto que se conecta ao seu aplicativo LUIS. 

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
. 
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity("EchoBot Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        options.Middleware.Add(new ConversationState<EchoState>(dataStore));

        // Add LUIS recognizer as middleware
        options.Middleware.Add(
            new LuisRecognizerMiddleware(
                new LuisModel(
                    // This appID is for a public app that's made available for demo purposes
                    "eb0bf5e0-b468-421b-9375-fdfb644c512e",
                    // You can use it by replacing <subscriptionKey> with your Authoring Key
                    // which you can find at https://www.luis.ai under User settings > Authoring Key
                    "<subscriptionKey>",
                    // The location-based URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
                    new Uri("https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/"))));
    });
}
```

<!--TODO: DOES PUBLIC APP WORK WITH KEYS IN DIFFERENT REGIIONS? --> Cole sua chave de assinatura de [https://www.luis.ai](https://www.luis.ai) no lugar de `<subscriptionKey>`.

> [!NOTE] 
> Se você estiver usando seu próprio aplicativo LUIS em vez do público, poderá obter o ID, a chave de assinatura e o URL do aplicativo LUIS de [https://www.luis.ai](https://www.luis.ai). 
>
>Você pode encontrar a URL base para usar em seu `LuisModel` fazendo logon na [site LUIS](https://www.luis.ai), indo para o **publicar** guia e, em seguida, observando o **ponto de extremidade** coluna sob  **Recursos e as chaves**. O URL base é a parte do **URL do endpoint** antes do ID da assinatura e de outros parâmetros.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Primeiro exigem/importação na [LuisRecognizer](https://github.com/Microsoft/botbuilder-js/tree/master/doc/botbuilder-ai/classes/botbuilder_ai.luisrecognizer.md) de classe e criar uma instância para o seu modelo LUIS:

```javascript
const { LuisRecognizer } = require('botbuilder-ai');

const model = new LuisRecognizer({
    // This appID is for a public app that's made available for demo purposes
    // You can use it by providing your LUIS subscription key
     appId: 'eb0bf5e0-b468-421b-9375-fdfb644c512e',
    // replace subscriptionKey with your Authoring Key
    // your key is at https://www.luis.ai under User settings > Authoring Key 
    subscriptionKey: '<your subscription key>',
    // The serviceEndpoint URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com'
});
```

Adicione o modelo para a pilha de middleware:

```javascript
adapter.use(model);
```


> [!NOTE] 
> Se você estiver usando seu próprio aplicativo LUIS em vez do público, poderá obter o ID, o ID de assinatura e o URL do aplicativo LUIS de [https://www.luis.ai](https://www.luis.ai). 

---



Compreensão de idioma do LUIS agora está conectado ao seu bot. Em seguida, vamos ver como obter a intenção do modelo LUIS armazenado no objeto de contexto.


## <a name="get-the-intent-from-the-turn-context"></a>Obter a intenção do contexto de turno

Os resultados do LUIS são acessíveis a partir do seu bot usando o contexto em cada turno de conversação.

# <a name="ctabcs"></a>[C#](#tab/cs)

Para que seu bot simplesmente envie uma resposta com base na intenção que o aplicativo LUIS detectou, substitua o código em `OnTurn` pelo seguinte:

```cs
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Ai.LUIS;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

namespace Bot_Builder_Echo_Bot1
{
    public class EchoBot : IBot
    {
        /// <summary>
        /// Every Conversation turn for our EchoBot will call this method. In here
        /// the bot checks the Activty type to verify it's a message, checks the /// intent from the LUIS recognizer, and sends a reply based on the recognized intent
        /// </summary>
        /// <param name="context">Turn-scoped context containing all the data needed
        /// for processing this conversation turn. </param>        
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {

                var result = context.Services.Get<RecognizerResult>
                    (LuisRecognizerMiddleware.LuisRecognizerResultKey);
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
                        // Cancel the process.
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

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const results = model.get(context);
            const topIntent = LuisRecognizer.topIntent(results);
            switch (topIntent) {

                case 'Cancel':
                    await context.sendActivity("<cancelling the process>")
                    break;
                case 'Help':
                    await context.sendActivity("<here's some help>");
                    break;
                case 'Weather':
                    await context.sendActivity("The weather today is sunny.");
                    break;
                case 'None':                    
                    await context.sendActivity("Sorry, I don't understand.")
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
Quaisquer entidades reconhecidas serão retornadas como um mapa de nomes de entidades para valores e acessadas via `results.entities`. Metadados de entidade adicionais podem ser retornados, passando um `verbose=true` definir ao criar o LuisRecognizer. Os metadados adicionados, em seguida, podem ser acessados via `results.entities.$instance`.

---

<!-- TODO: SHOW RUNNING THE FIRST BOT --> Tente executar o bot no Emulador do Bot Framework e diga coisas como "weather", "help" e "cancel" para ele.

![Execute o bot](./media/how-to-luis/run-luis-bot.png)


## <a name="using-a-luis-recognizer-with-conversation-state"></a>Usando um reconhecedor LUIS com o estado da conversa
<!-- TBD, complete example --> Se a sua resposta ao usuário tiver mais de um único turno, você poderá decidir acompanhar onde você está na conversa, salvando o estado da conversa. Você pode usar a intenção de LUIS para ajudá-lo a definir dados de estado de conversação, como se um tópico de conversa foi iniciado ou concluído.

O middleware do reconhecedor LUIS é executado para cada turno de seu bot, para que você possa obter uma intenção para cada mensagem recebida pelo usuário. Se você quiser iniciar um fluxo de conversas com várias voltas com base em uma intenção, uma maneira de fazer isso é ignorar a lógica de alteração de tópicos até terminar o tópico atual.

# <a name="ctabcs"></a>[C#](#tab/cs)

No EchoState.cs, altere EchoState para o seguinte:

```csharp
public class ConversationStateInfo 
{
    public bool WeatherTopicStarted  { get; set; }
    public bool HelpTopicStarted  { get; set; }
    public bool CancelTopicStarted  { get; set; }
}
```

No Startup.cs, altere a inicialização do ConversationState usar `ConversationStateInfo`.
```cs
    options.Middleware.Add(new ConversationState<ConversationStateInfo>(dataStore));
```

Editar EchoBot.cs, `OnTurn`:
```cs
public async Task OnTurn(ITurnContext context)
{
    // This bot is only handling Messages
    if (context.Activity.Type == ActivityTypes.Message)
    {
        var text = context.Activity.Text;
        var conversationState = context.GetConversationState<ConversationStateInfo>() ?? new ConversationStateInfo();

        // Here, you can add some other logic based on the topic flags in conversation state
        // For example, if you know that a particular topic was started in a previous turn,
        // you can send the reply for that topic and bypass getting the intent from LUIS 
        if (conversationState.WeatherTopicStarted)
        {
            // Set this flag to false since this reply concludes the topic.
            conversationState.WeatherTopicStarted = false;
            // Assume that they responded to the prompt with a location.
            await context.SendActivity($"The weather in {text} is sunny.");
        }
        else
        {
            var result = context.Services.Get<RecognizerResult>
            (LuisRecognizerMiddleware.LuisRecognizerResultKey);
            var topIntent = result?.GetTopScoringIntent();
            switch ((topIntent != null) ? topIntent.Value.intent : null)
            {
                case null:
                    await context.SendActivity("Failed to get results from LUIS.");
                    break;
                case "None":
                    await context.SendActivity("Sorry, I don't understand.");
                    break;
                case "Weather":
                    conversationState.WeatherTopicStarted = true;
                    await context.SendActivity($"Looks like you want a weather forecast. What city do you want the forecast for?");

                    break;
                case "Help":
                    conversationState.HelpTopicStarted = true;
                    await context.SendActivity("<here's some help>");
                    break;
                case "Cancel":
                    // Cancel the process.
                    conversationState.CancelTopicStarted = true;
                    await context.SendActivity("<cancelling the process>");
                    break;
                default:
                    // Received an intent we didn't expect, so send its name and score.
                    await context.SendActivity($"Intent: {topIntent.Value.intent} ({topIntent.Value.score}).");
                    break;
            }
        }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Adicione o middleware de estado da conversa depois de adicionar o `LuisRecognizer`.

```javascript
const model = new LuisRecognizer({
    // This appID is for a public app that's made available for demo purposes
    // You can use it by providing your LUIS subscription key
     appId: 'eb0bf5e0-b468-421b-9375-fdfb644c512e',
    // replace subscriptionKey with your Authoring Key
    // your key is at https://www.luis.ai under User settings > Authoring Key 
    subscriptionKey: '<your subscription>',
    // The serviceEndpoint URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com'
});
adapter.use(model)

// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);
```

Então você pode salvar o estado indicando qual tópico foi iniciado.

```javascript
// Listen for incoming activities 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async(context) => {
        if (context.activity.type === 'message') {
            var utterance = context.activity.text;
            
            // Check topic flags in conversation state 
            if (conversationState.weatherTopicStarted) 
            {
                // Assume the user's message is a reply to the bot's prompt for a location
                await context.sendActivity(`The weather in ${utterance} is sunny.`);
                // This conversation flow is now finished. Set flag to false,
                // so that on the next turn the user can ask for another weather forecast.
                conversationState.WeatherTopicStarted = false;
            }
            // To add more steps to the other topics
            // you could check the topic flags here
            else 
            {
                const results = model.get(context);
                const topIntent = LuisRecognizer.topIntent(results);
                switch (topIntent) {
                    case 'None':
                        //Add app logic when there is no result
                        await context.sendActivity("<null case>")
                        break;
                    case 'Cancel':
                        conversationState.cancelTopicStarted = true;
                        await context.sendActivity("<cancelling the process>")
                        break;
                    case 'Help':
                        conversationState.helpTopicStarted = true;
                        await context.sendActivity("<here's some help>");
                        break;
                    case 'Weather':
                        conversationState.weatherTopicStarted = true;
                        await context.sendActivity("Looks like you want a weather forecast. What city do you want the forecast for?");
                        break;
                    default:
                        // Add app logic for the recognition results.
                        await context.sendActivity(`Received this intent: ${topIntent}`);
                }
            }
        }
    });
});
```

---

Tente executar o bot no Emulador do Bot Framework e observe que "get weather" é agora um fluxo de conversação de duas voltas.

![Execute o bot](./media/how-to-luis/run-luis-bot-2step-weather.png)


## <a name="using-luis-with-dialogs"></a>Usando o LUIS com caixas de diálogo

Se você estiver usando a intenção do LUIS para acionar um fluxo de conversa com várias voltas, pode ser útil usar diálogos para encapsular esse fluxo. Este exemplo de bot funciona com um aplicativo LUIS que detecta as intenções usadas para acionar um diálogo de automação residencial ou um diálogo de previsão do tempo.

> [!NOTE] 
> Para criar sua própria cópia do aplicativo LUIS público usado neste exemplo, copie o arquivo LUIS [WeatherOrHomeAutomation.json](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/simple-bot-example/WeatherOrHomeAutomation.json). Em seguida, [importe o aplicativo LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app), [treine](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) e [publique](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp). Substitua a ID do aplicativo público no código de exemplo com a ID do aplicativo do seu novo aplicativo LUIS.

# <a name="ctabcs"></a>[C#](#tab/cs)

Em primeiro lugar, modificar `ConfigureServices` em Startup.cs adicionar middleware para seu aplicativo LUIS. Renomeie `EchoBot` para `LuisDialogBot`.
Neste exemplo, o aplicativo LUIS adicionado como `LuisRecognizerMiddleware` detecta as intenções `homeautomation` ou `weather`, para acionar diálogos com esses nomes. 

```csharp
public void ConfigureServices(IServiceCollection services)
{  
    // Rename EchoBot to LuisDialogBot
    services.AddBot<LuisDialogBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration); 
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity("EchoBot Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        // Use Dictionary<string, object> for the conversation state type
        options.Middleware.Add(new ConversationState<Dictionary<string, object>>(dataStore));

        // Add LUIS recognizer as middleware
        options.Middleware.Add(
            new LuisRecognizerMiddleware(
                new LuisModel(
                    // This appID is for a public app that's made available for demo purposes
                    "428affb6-7650-46ac-9184-68c00a4f1729",
                    // You can use it by replacing <subscriptionKey> with your Authoring Key
                    // which you can find at https://www.luis.ai under User settings > Authoring Key
                    "<subscriptionKey>",
                    // The location-based URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
                    new Uri("https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/"))));
    });
}
```

Renomeie **EchoBot.cs** para **LuisDialogBot.cs**e renomeie o `EchoBot` classe `LuisDialogBot`. Em seguida, na **LuisDialogBot.cs** adicione o seguinte `using` instruções.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Ai.LUIS;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts;
using Microsoft.Bot.Schema;
```

Em **LuisDialogBot.cs**, adicione o seguinte código à classe `LuisDialogBot`.

```csharp
    public class LuisDialogBot : IBot
    {
        private DialogSet _dialogs;

        public LuisDialogBot()
        {
            _dialogs = new DialogSet();

            _dialogs.Add("homeautomation", CreateHomeAutomationWaterfall());
            _dialogs.Add("weather", CreateWeatherWaterfall());
            _dialogs.Add("weather_city", new Builder.Dialogs.TextPrompt());
        }

        // App ID for a separate LUIS app used to tell if the user wants to turn the lights on or off
        // The `homeautomation` dialogs uses results from this app to determine the "on/off" argument. 
        private static LuisModel luisHomeAutomation =
            new LuisModel("76feb726-515b-44c4-acc9-adb216965a58", "SUBSCRIPTION-KEY", new System.Uri("https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/"));

        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {

                // Create a dialog context
                var state = ConversationState<Dictionary<string, object>>.Get(context);
                var dc = _dialogs.CreateContext(context, state);

                // Run the next dialog step
                await dc.Continue();
                
                // Check if any dialog has responded on this turn
                if (!context.Responded)
                {

                    var luisResult = context.Services.Get<RecognizerResult>(LuisRecognizerMiddleware.LuisRecognizerResultKey);
                    var topIntent = luisResult?.GetTopScoringIntent();
                    var utterance = context.Activity.Text;
                    var dialogArgs = new Dictionary<string, object>();

                    switch ((topIntent != null) ? topIntent.Value.intent.ToLowerInvariant() : null)
                    {
                        case "homeautomation":
                            // The Homeautomation_TurnOn and Homeautomation_TurnOff dialogs 
                            // use results from a separate LUIS app.
                            // The results determine the "on/off" argument to pass to the dialog. 
                            var recognizerHomeAutomation = new LuisRecognizer(luisHomeAutomation);
                            RecognizerResult recognizerResult = await recognizerHomeAutomation.Recognize(utterance, System.Threading.CancellationToken.None);
                            var topHomeAutoIntent = recognizerResult.GetTopScoringIntent().intent;


                            dialogArgs.Add("IntentFromHomeAuto", "");
                            switch ((topHomeAutoIntent != null) ? topHomeAutoIntent.ToLowerInvariant() : null)
                            {
                                case "homeautomation_turnon":
                                    dialogArgs["Intent_HomeAutomation"] = "on";
                                    await dc.Begin("homeautomation", dialogArgs);
                                    break;
                                case "homeautomation_turnoff":
                                    dialogArgs["Intent_HomeAutomation"] = "off";
                                    await dc.Begin("homeautomation", dialogArgs);
                                    break;
                                case null:
                                    await dc.Begin("homeautomation", null);
                                    break;
                                default:
                                    dialogArgs["Intent_HomeAutomation"] = topHomeAutoIntent;
                                    await dc.Begin("homeautomation", dialogArgs);
                                    break;
                            }
                            break;
                        case "weather":
                            dialogArgs.Add("LuisResult", luisResult);
                            await dc.Begin("weather", dialogArgs);
                            break;
                        case null:
                            await context.SendActivity($"Couldn't get a result from LUIS. You said: {utterance}");
                            break;
                        default:
                            // The intent didn't match any case, so just display the recognition results.
                            await context.SendActivity($"you said: {utterance}");
                            await context.SendActivity($"Recognized intent: {topIntent.Value.intent}.");

                            break;

                    }

                }
            }
        }

    }


```

Após `OnTurn`, adicione o código dentro de `LuisDialogBot` classe para implementar as caixas de diálogo.

```csharp
        // The home automation waterfall has one step
        private WaterfallStep[] CreateHomeAutomationWaterfall()
        {
            return new WaterfallStep[] {
                TurnLightsOnOrOff
            };
        }

        // The weather waterfall has two steps
        private WaterfallStep[] CreateWeatherWaterfall()
        {
            return new WaterfallStep[] {
                AskWeatherLocation,
                SendWeatherReport
            };
        }

        /// <summary>
        /// This is the first step and only of the home automation dialog.
        /// </summary>
        /// <param name="dc"></param>
        /// <param name="args">Can be "on", "off", another string for an intent name, or null.
        /// null indicates that no result was received from which to get an intent name.</param>
        /// <param name="next"></param>
        /// <returns></returns>
        private async Task TurnLightsOnOrOff(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var intentFromHomeAutomation = args["Intent_HomeAutomation"];
            if (args != null)
            {
                switch (intentFromHomeAutomation)
                {
                    case "on":
                    case "off":
                        await dc.Context.SendActivity($"Turning {intentFromHomeAutomation} your lights!");
                        break;
                    default:
                        await dc.Context.SendActivity($"Intent detected by homeautomation was: {intentFromHomeAutomation}, but the home automation system doesn't support that yet.");
                        break;
                }

            }
            else
            {
                await dc.Context.SendActivity($"You said {dc.Context.Activity.AsMessageActivity().Text}. Unable to get a result from which to determine on/off/other operation");
            }

            await dc.End();

        }

        // This is the first step of the weather dialog
        private async Task AskWeatherLocation(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
            
            if (args["LuisResult"] is RecognizerResult luisResult)
            {
                var location = GetEntity<string>(luisResult, "Weather_Location");
                if (!string.IsNullOrEmpty(location))
                {
                    dialogState.Add("Location", location);
                }
            }

            // Save info back to the dialog instance
            dc.ActiveDialog.State = dialogState;

            if (!dialogState.ContainsKey("Location"))
            {
                await dc.Prompt("weather_city", "What city do you want the weather for?");
            }
            else
            {
                // We've set the location parameter for the weather report,
                // so go to the next step in the waterfall
                await dc.Continue();
            }
        }

        // This the second step of the weather dialog
        private async Task SendWeatherReport(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
            if (args != null)
            {
                if (!dialogState.ContainsKey("Location"))
                {   // Interpret args as a reply to prompt only if they didn't give location
                    TextResult locationResult = (TextResult)args;
                    dialogState.Add("Location", locationResult.Text);
                }

            }

            // You can add some logic that uses the location 
            // to get the weather from a weather service instead of hard-coding it
            await dc.Context.SendActivity($"The weather forecast for '{dialogState["Location"]}' is sunny and 70 degrees F.");

            dc.ActiveDialog.State = new Dictionary<string, object>(); // clear the dialog state 
            await dc.End();
        }
```

Adicione essa função auxiliar.

```cs
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

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Você pode precisar instalar a biblioteca de diálogos se ainda não o fez. 

```cmd
npm install --save botbuilder-dialogs
```

Primeiro, crie um aplicativo LUIS e adicione-o ao seu bot usando `adapter.use`:

```javascript
const { BotFrameworkAdapter, ConversationState, MemoryStorage, TurnContext } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');
const { DialogSet } = require('botbuilder-dialogs');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

// Create LuisRecognizer 
// The LUIS application is public, meaning you can use your own subscription key to test the applications.
const luisRecognizer = new LuisRecognizer({
    appId: '1fefd4a7-ae5b-4e63-99f7-0cf499a1423b',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});

// Add the recognizer to your bot
adapter.use(luisRecognizer);

// create conversation state
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// register some dialogs for usage with the intents detected by the LUIS app
const dialogs = new DialogSet();
```

Em seguida, adicione as caixas de diálogo:

```javascript

dialogs.add('HomeAutomation_TurnOn', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        // state.homeAutomationTurnOn counts how many times this dialog was called 
        state.homeAutomationTurnOn = state.homeAutomationTurnOn ? state.homeAutomationTurnOn + 1 : 1;
        await dialogContext.context.sendActivity(`${state.homeAutomationTurnOn}: You reached the "HomeAutomation_TurnOn" dialog.`);

        await dialogContext.end();
    }
]);

dialogs.add('Weather_GetForecast', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        // state.weatherGetForecast counts how many times this dialog was called  
        state.weatherGetForecast = state.weatherGetForecast ? state.weatherGetForecast + 1 : 1;
        await dialogContext.context.sendActivity(`${state.weatherGetForecast}: You reached the "Weather_GetForecast" dialog.`);

        await dialogContext.end();
    }
]);

dialogs.add('None', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        // state.None counts how many times this dialog was called        
        state.None = state.None ? state.None + 1 : 1;
        await dialogContext.context.sendActivity(`${state.None}: You reached the "None" dialog.`);

        await dialogContext.end();
    }
]);
```

Em seu bot, invoque cada caixa de diálogo com base na intenção reconhecida:
```javascript
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const dc = dialogs.createContext(context, state);

            // Retrieve the LUIS results from our LUIS application
            const luisResults = luisRecognizer.get(context);

            // Extract the top intent from LUIS and use it to select which dialog to start
            // "NotFound" is the intent name for when no top intent can be found.
            const topIntent = LuisRecognizer.topIntent(luisResults, "NotFound");

            const isMessage = context.activity.type === 'message';
            if (isMessage) {
                switch (topIntent) {
                    case 'homeautomation':                    
                        await dc.begin("HomeAutomation_TurnOn", luisResults);
                        break;
                    case 'weather':                    
                        await dc.begin("Weather_GetForecast", luisResults);
                        break;
                    case 'None':
                        await dc.begin("None");
                        break;
                    case 'NotFound':
                        await context.sendActivity(`Sorry, I didn't get any results from LUIS.`);
                        break;
                    default:
                        // handle intents for which we have no dialog
                        await context.sendActivity(`You reached the ${topIntent} intent.`);
                        break;
                }
            }
            
            if (!context.responded) {
                await dc.continue();
                if (!context.responded && isMessage) {
                    await dc.context.sendActivity(`Hi! I'm the LUIS dialog bot. Say something and LUIS will decide how the message should be routed.`);
                }
            }
        }
    });
});
```

Tente executar o bot no Bot Framework Emulator e diga coisas como "acenda as luzes" e "pegue o tempo em Seattle".

![Execute o bot](./media/how-to-luis/run-luis-bot-js-single-step-dialog.png)

---

## <a name="extract-entities"></a>Extrair entidades

Além de reconhecer a intenção, um aplicativo LUIS também pode extrair entidades, que são palavras importantes para atender a solicitação de um usuário. Por exemplo, no exemplo do diálogo de previsão do tempo, o aplicativo LUIS pode conseguir extrair o local do relatório meteorológico da mensagem do usuário. 

Uma maneira comum de usar caixas de diálogo é identificar qualquer entidade na mensagem do usuário e solicitar qualquer uma das entidades necessárias que não foram encontradas. Em seguida, a etapa de diálogo subsequente manipula a resposta para o prompt.

# <a name="ctabcs"></a>[C#](#tab/cs)

Vamos dizer que a mensagem do usuário foi "Qual é o tempo em Seattle"? Para o bot neste exemplo, o [LuisRecognizer](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.luisrecognizer) fornece um [RecognizerResult](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult) com uma propriedade [`Entities`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult#properties-) com essa estrutura:

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

A seguinte função auxiliar que você adicionou ao seu `LuisDialogBot` classe obtém entidades fora do `RecognizerResult` LUIS.

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

Ao coletar informações como entidades de várias etapas em um diálogo, pode ser útil salvar as informações necessárias no estado específico do diálogo. Por exemplo, no diálogo `AskWeatherLocation`, as entidades são extraídas dos resultados LUIS passados para o diálogo. Se um `Weather_Location` entidade for encontrada, ele é adicionado para o estado da caixa de diálogo.

```cs

        // This is the first step of the weather dialog
        private async Task AskWeatherLocation(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
            
            if (args["LuisResult"] is RecognizerResult luisResult)
            {
                var location = GetEntity<string>(luisResult, "Weather_Location");
                if (!string.IsNullOrEmpty(location))
                {
                    dialogState.Add("Location", location);
                }
            }

            // Save info back to the dialog instance
            dc.ActiveDialog.State = dialogState;

            if (!dialogState.ContainsKey("Location"))
            {
                await dc.Prompt("weather_city", "What city do you want the weather for?");
            }
            else
            {
                // We've set the location parameter for the weather report,
                // so go to the next step in the waterfall
                await dc.Continue();
            }
        }
```

Na segunda etapa da caixa de diálogo, você pode obter as informações salvas na etapa anterior do estado da instância de diálogo, bem como a resposta do usuário ao prompt de localização e usá-lo para enviar um relatório meteorológico ao usuário.

```cs

// This the second step of the weather dialog
private async Task SendWeatherReport(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
{
    var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
    if (args != null)
    {
        if (!dialogState.ContainsKey("Location"))
        {   // Interpret args as a reply to prompt only if they didn't give location
            TextResult locationResult = (TextResult)args;
            dialogState.Add("Location", locationResult.Text);
        }

    }

    // You can add some logic that uses the location and date
    // to get the weather from a weather service instead of hard-coding it
    await dc.Context.SendActivity($"The weather forecast for '{dialogState["Location"]}' is sunny and 70 degrees F.");
    
    // clear the dialog state 
    dc.ActiveDialog.State = new Dictionary<string, object>(); 
    await dc.End();
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Por exemplo, digamos que a mensagem do usuário foi "Qual é o clima em Seattle"? Para o bot neste exemplo, o [LuisRecognizer](https://docs.microsoft.com/en-us/javascript/api/botbuilder-ai/luisrecognizer) fornece um [RecognizerResult](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core-extensions/recognizerresult) com uma propriedade `entities` que possui essa estrutura:

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

Isso `findEntities` função procura qualquer `Weather_Location` entidades reconhecidas pelo aplicativo LUIS.

<!-- TODO: Turn into a waterfall -->

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

Chame `findEntities` do `Weather_GetForecast` caixa de diálogo.

```javascript
// Pass the LUIS recognizer result to the args parameter
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
```

As entidades são extraídas dos resultados do LUIS transmitidos para `dc.begin`.

```javascript
await dc.begin("Weather_GetForecast", luisResults);
```
---

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações sobre o LUIS, veja [Language Understanding](./bot-builder-concept-luis.md).

Para obter informações sobre como criar os aplicativos LUIS usados nesses exemplos, consulte [aplicativos LUIS para o Bot Builder](https://aka.ms/luis-bot-examples).

O LUIS pode ser combinado com outros Serviços Cognitivos, para tornar seu bot ainda mais poderoso. Veja [Combine os aplicativos LUIS e os serviços QnA usando a ferramenta Expedição](./bot-builder-tutorial-dispatch.md) para aprender como combinar o QnA com o Language Understanding (LUIS) no seu bot.

## <a name="next-steps"></a>Próximas etapas


> [!div class="nextstepaction"]
> [Gerar resultados de LUIS fortemente tipados](./bot-builder-howto-v4-luisgen.md)


