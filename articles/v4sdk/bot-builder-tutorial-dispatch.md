---
title: Usar vários modelos de LUIS e QnA | Microsoft Docs
description: Aprenda a usar o criador LUIS e QnA no seu bot.
keywords: Luis, QnA, ferramenta Dispatch, vários serviços, rotear intenções
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 01/15/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 373470b000b168e6e434ed5ed08b35c18ab09a99
ms.sourcegitcommit: aea57820b8a137047d59491b45320cf268043861
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59904959"
---
# <a name="use-multiple-luis-and-qna-models"></a>Usar vários modelos de LUIS e QnA

[!INCLUDE[applies-to](../includes/applies-to.md)]

Neste tutorial, demonstraremos como usar o serviço Dispatch para rotear declarações quando houver vários modelos de LUIS e serviços do QnA Maker para cenários diferentes compatíveis com um bot. Nesse caso, configuramos o Dispatch com vários modelos de LUIS para conversas sobre automação residencial e informações sobre o clima, além do serviço QnA Maker para responder a perguntas com base em um arquivo de texto de perguntas frequentes como entrada. Este exemplo combina os serviços a seguir.

| NOME | DESCRIÇÃO |
|------|------|
| HomeAutomation | Um aplicativo LUIS que reconhece uma intenção de automação residencial com dados de entidade associados.|
| Clima | Um aplicativo LUIS que reconhece as intenções `Weather.GetForecast` e `Weather.GetCondition` com dados de localização.|
| Perguntas frequentes  | Uma base de dados de conhecimento do QnA Maker que fornece respostas a perguntas simples sobre o bot. |

## <a name="prerequisites"></a>Pré-requisitos

- O código neste artigo se baseia no exemplo de **NLP com Dispatch**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/dispatch-sample-cs) ou [JS](https://aka.ms/dispatch-sample-js).
- É necessário ter conhecimento dos [conceitos básicos do bot](bot-builder-basics.md), [processamento de linguagem natural](bot-builder-howto-v4-luis.md), [QnA Maker](bot-builder-howto-qna.md) e do arquivo [.bot](bot-file-basics.md).
- O [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download) para teste.

## <a name="create-the-services-and-test-the-bot"></a>Criar os serviços e testar o bot

Você pode seguir as instruções em **LEIAME** para [C#](https://aka.ms/dispatch-sample-readme-cs) ou [JS](https://aka.ms/dispatch-sample-readme-js) para criar este bot usando chamadas de Interface de linha de comando, ou seguir as etapas abaixo para criar manualmente seu bot usando as Interfaces do usuário do Azure, LUIS e QnAMaker.

 ### <a name="create-your-bot-using-service-ui"></a>Criar seu bot usando a interface do usuário de serviço
 
Para começar a criação de um bot manualmente, baixe os 4 arquivos a seguir, localizados no repositório [BotFramework-Samples](https://aka.ms/botdispatchgitsamples) do GitHub em uma pasta local: [home-automation.json](https://aka.ms/dispatch-home-automation-json), [weather.json ](https://aka.ms/dispatch-weather-json), [nlp-with-dispatchDispatch.json](https://aka.ms/dispatch-dispatch-json), [QnAMaker.tsv](https://aka.ms/dispatch-qnamaker-tsv) Para fazer isso, você pode usar o método que consiste em abrir o link do repositório do GitHub acima, clicar em **BotFramework-Samples** e, em seguida, "clonar ou fazer o download" do repositório para seu computador local. Observe que esses arquivos estão em um repositório diferente do exemplo mencionado nos pré-requisitos.

### <a name="manually-create-luis-apps"></a>Criar manualmente aplicativos LUIS

Faça logon no [portal da Web do LUIS](https://www.luis.ai/). Na seção _Meus aplicativos_, selecione a guia _Importar novo aplicativo_. A caixa de diálogo a seguir será exibida:

![Importar arquivo json do LUIS](./media/tutorial-dispatch/import-new-luis-app.png)

Selecione o botão _Escolher arquivo de aplicativo_ e selecione o arquivo “home-automation.json” baixado. Deixe o campo de nome opcional em branco. Selecione _Concluído_.

Depois que o LUIS abrir seu aplicativo de automação residencial, selecione o botão _Treinar_. Isso treinará seu aplicativo usando o conjunto de declarações que você acabou de importar usando o arquivo “home-automation.json”.

Quando o treinamento estiver concluído, selecione o botão _Publicar_. A caixa de diálogo a seguir será exibida:

![Publicar aplicativo LUIS](./media/tutorial-dispatch/publish-luis-app.png)

Escolha o ambiente de “produção” e, em seguida, selecione o botão _Publicar_.

Depois que seu novo aplicativo LUIS tiver sido publicado, selecione a guia _GERENCIAR_. Na página de “Informações do aplicativo”, registre os valores `Application ID` e `Display name`. Na página de “Chave e pontos de extremidade”, registre os valores `Authoring Key` e `Region`. Esses valores serão usados posteriormente por seu arquivo “nlp-with-dispatch.bot”.

Uma vez concluído, você deve _Treinar_ e _Publicar_ seu aplicativo de informações sobre o clima e seu aplicativo de expedição do LUIS, repetindo essas mesmas etapas para os arquivos “weather.json” e “nlp-with-dispatchDispatch.json” baixados localmente.

### <a name="manually-create-qna-maker-app"></a>Criar manualmente o aplicativo do QnA Maker

O primeiro passo para definir uma base de dados de conhecimento do QnA Maker é configurar um serviço do QnA Maker no Azure. Para fazer isso, siga as instruções passo a passo encontradas [aqui](https://aka.ms/create-qna-maker). Agora faça logon no [portal da Web do QnA Maker](https://qnamaker.ai). Vá para a Etapa 2

![Criar QnA Etapa 2](./media/tutorial-dispatch/create-qna-step-2.png)

e selecione
1. Sua conta do Azure AD.
1. Seu nome da assinatura do Azure.
1. O nome que você criou para seu serviço QnA Maker. (Se o seu serviço QnA do Azure não for exibido inicialmente nessa lista suspensa, tente atualizar a página). 

Vá para a Etapa 3

![Crie QnA Etapa 3](./media/tutorial-dispatch/create-qna-step-3.png)

Forneça um nome para a sua base de dados de conhecimento do QnA Maker. Para este exemplo, usaremos o nome “exemplo-qna”.

Vá para a Etapa 4

![Criar QnA Etapa 4](./media/tutorial-dispatch/create-qna-step-4.png)

selecione a opção _+ Adicionar arquivo_ e selecione o arquivo “QnAMaker.tsv” baixado

Há uma seleção adicional para adicionar uma personalidade de _Bate-papo_ à sua base de dados de conhecimento, mas nosso exemplo não inclui essa opção.

Selecione _Salvar e treinar_ e quando terminar, selecione a guia _PUBLICAR_ e publique seu aplicativo.

Depois que seu aplicativo do QnA Maker for publicado, selecione a guia _CONFIGURAÇÕES_ e role para baixo até os “Detalhes da implantação”. Registre os seguintes valores da solicitação HTTP de exemplo, _Carteiro_.

```
POST /knowledgebases/<Your_Knowledgebase_Id>/generateAnswer
Host: <Your_Hostname>
Authorization: EndpointKey <Your_Endpoint_Key>
```
Esses valores serão usados posteriormente por seu arquivo “nlp-with-dispatch.bot”.

### <a name="manually-update-your-bot-file"></a>Atualizar manualmente o arquivo .bot

Depois que todos os seus aplicativos de serviço forem criados, as informações para cada um precisam ser adicionadas ao seu arquivo “nlp-with-dispatch.bot”. Abra esse arquivo dentro do arquivo de exemplo C# ou JS que você baixou anteriormente. Adicione os seguintes valores para cada seção de “type”: “luis” ou “type”: “dispatch”

```
"appId": "<Your_Recorded_App_Id>",
"authoringKey": "<Your_Recorded_Authoring_Key>",
"subscriptionKey": "<Your_Recorded_Authoring_Key>",
"version": "0.1",
"region": "<Your_Recorded_Region>",
```

Para a seção de “type”: “qna”, adicione os seguintes valores:

```
"type": "qna",
"name": "sample-qna",
"id": "201",
"kbId": "<Your_Recorded_Knowledgebase_Id>",
"subscriptionKey": "<Your_Azure_Subscription_Key>", // Used when creating your QnA service.
"endpointKey": "<Your_Recorded_Endpoint_Key>",
"hostname": "<Your_Recorded_Hostname>"
```

Quando todas as alterações tiverem sido realizadas, salve esse arquivo.

### <a name="test-your-bot"></a>Testar seu bot

Agora execute o exemplo usando o emulador. Depois que o emulador for aberto, selecione o arquivo “nlp-with-dispatch.bot”.

Para sua referência, estas são algumas das perguntas e comandos cobertos pelos serviços incluídos:

* QnA Maker
  * `hi`, `good morning`
  * `what are you`, `what do you do`
* LUIS (automação doméstica)
  * `turn on bedroom light`
  * `turn off bedroom light`
  * `make some coffee`
* LUIS (clima)
  * `whats the weather in redmond washington`
  * `what's the forecast for london`
  * `show me the forecast for nebraska`

### <a name="connecting-to-the-services-from-your-bot"></a>Conectar-se aos serviços de seu bot

Para se conectar aos serviços Dispatch, LUIS e QnA Maker, seu bot obtém informações do arquivo **.bot**.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em **Startup.cs**, `ConfigureServices` lê o arquivo de configuração, e `InitBotServices` usa essas informações para inicializar os serviços. Cada vez que é criado, o bot é inicializado com o objeto `BotServices` registrado. Estas são as partes relevantes desses dois métodos.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //...
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\nlp-with-dispatch.bot", secretKey);
    services.AddSingleton(sp => botConfig
        ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

    // ...
    
    var connectedServices = InitBotServices(botConfig);
    services.AddSingleton(sp => connectedServices);
    
    services.AddBot<NlpDispatchBot>(options =>
    {
          
          // The Memory Storage used here is for local bot debugging only. 
          // When the bot is restarted, everything stored in memory will be gone.

          Storage dataStore = new MemoryStorage();

          // ...

          // Create Conversation State object.
          // The Conversation State object is where we persist anything at the conversation-scope.

          var conversationState = new ConversationState(dataStore);
          options.State.Add(conversationState);
     });
}

```
O código a seguir inicializa as referências do bot para serviços externos. Por exemplo, os serviços LUIS e QnaMaker são criados aqui. Esses serviços externos são configurados usando a classe `BotConfiguration` (com base no conteúdo do arquivo `.bot`).

```csharp
private static BotServices InitBotServices(BotConfiguration config)
{
    var qnaServices = new Dictionary<string, QnAMaker>();
    var luisServices = new Dictionary<string, LuisRecognizer>();

    foreach (var service in config.Services)
    {
        switch (service.Type)
        {
            case ServiceTypes.Luis:
                {
                    // ...
                    var app = new LuisApplication(luis.AppId, luis.AuthoringKey, luis.GetEndpoint());
                    var recognizer = new LuisRecognizer(app);
                    luisServices.Add(luis.Name, recognizer);
                    break;
                }

            case ServiceTypes.Dispatch:
                // ...
                var dispatchApp = new LuisApplication(dispatch.AppId, dispatch.AuthoringKey, dispatch.GetEndpoint());

                // Since the Dispatch tool generates a LUIS model, we use the LuisRecognizer to resolve the
                // dispatching of the incoming utterance.
                var dispatchARecognizer = new LuisRecognizer(dispatchApp);
                luisServices.Add(dispatch.Name, dispatchARecognizer);
                break;

            case ServiceTypes.QnA:
                {
                    // ...
                    var qnaEndpoint = new QnAMakerEndpoint()
                    {
                        KnowledgeBaseId = qna.KbId,
                        EndpointKey = qna.EndpointKey,
                        Host = qna.Hostname,
                    };

                    var qnaMaker = new QnAMaker(qnaEndpoint);
                    qnaServices.Add(qna.Name, qnaMaker);
                    break;
                }
        }
    }

    return new BotServices(qnaServices, luisServices);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código de exemplo usa constantes de nomenclatura predefinidas para identificar as várias seções do seu arquivo `.bot`. Se você modificou qualquer nome de seção das nomenclaturas de exemplo originais no seu arquivo _nlp-with-dispatch.bot_, certifique-se de localizar a declaração de constantes associada no arquivo **bot.js**,  **homeAutomation.js**, **qna.js** ou **weather.js** e altere essa entrada para o nome modificado.  

```javascript
// In file bot.js
// this is the LUIS service type entry in the .bot file.
const DISPATCH_CONFIG = 'nlp-with-dispatchDispatch';

// In file homeAutomation.js
// this is the LUIS service type entry in the .bot file.
const LUIS_CONFIGURATION = 'Home Automation';

// In file qna.js
// Name of the QnA Maker service in the .bot file.
const QNA_CONFIGURATION = 'sample-qna';

// In file weather.js
// this is the LUIS service type entry in the .bot file.
const WEATHER_LUIS_CONFIGURATION = 'Weather';
```

No **bot.js**, as informações contidas no arquivo de configuração _nlp-with-dispatch.bot_ são usadas para conectar seu bot de expedição a vários serviços. Cada construtor procura e usa as seções apropriadas do arquivo de configuração com base nos nomes de seção detalhados acima.

```javascript
class DispatchBot {
    constructor(conversationState, userState, botConfig) {
        //...
        this.homeAutomationDialog = new HomeAutomation(conversationState, userState, botConfig);
        this.weatherDialog = new Weather(botConfig);
        this.qnaDialog = new QnA(botConfig);

        this.conversationState = conversationState;
        this.userState = userState;

        // dispatch recognizer
        const dispatchConfig = botConfig.findServiceByNameOrId(DISPATCH_CONFIG);
        //...
```
---

### <a name="calling-the-services-from-your-bot"></a>Chamar os serviços de seu bot

A lógica do bot verifica a entrada do usuário com base no modelo de Dispatch combinado.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No arquivo **NlpDispatchBot.cs**, o construtor do bot obtém o objeto `BotServices` que registramos na inicialização.

```csharp
private readonly BotServices _services;

public NlpDispatchBot(BotServices services)
{
    _services = services ?? throw new System.ArgumentNullException(nameof(services));

    //...
}
```

No método `OnTurnAsync` do bot, verificamos as mensagens de entrada do usuário com base no modelo do Dispatch.

```csharp
// Get the intent recognition result
var recognizerResult = await _services.LuisServices[DispatchKey].RecognizeAsync(context, cancellationToken);
var topIntent = recognizerResult?.GetTopScoringIntent();

if (topIntent == null)
{
    await context.SendActivityAsync("Unable to get the top intent.");
}
else
{
    await DispatchToTopIntentAsync(context, topIntent, cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No método `onTurn` do **bot.js**, podemos verificar mensagens de entrada do usuário. Se o tipo _ActivityType.Message_ for recebido, essa mensagem será enviada por meio do _dispatchRecognizer_ do bot.

```javascript
if (turnContext.activity.type === ActivityTypes.Message) {
    // determine which dialog should fulfill this request
    // call the dispatch LUIS model to get results.
    const dispatchResults = await this.dispatchRecognizer.recognize(turnContext);
    const dispatchTopIntent = LuisRecognizer.topIntent(dispatchResults);
    //...
 }
```
---

### <a name="working-with-the-recognition-results"></a>Trabalhar com os resultados de reconhecimento

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Quando o modelo produz um resultado, ele indica qual serviço pode processar mais adequadamente a declaração. O código neste bot encaminha a solicitação para o serviço correspondente e, então, resume a resposta do serviço chamado.

```csharp
// Depending on the intent from Dispatch, routes to the right LUIS model or QnA service.
private async Task DispatchToTopIntentAsync(
    ITurnContext context,
    (string intent, double score)? topIntent,
    CancellationToken cancellationToken = default(CancellationToken))
{
    const string homeAutomationDispatchKey = "l_Home_Automation";
    const string weatherDispatchKey = "l_Weather";
    const string noneDispatchKey = "None";
    const string qnaDispatchKey = "q_sample-qna";

    switch (topIntent.Value.intent)
    {
        case homeAutomationDispatchKey:
            await DispatchToLuisModelAsync(context, HomeAutomationLuisKey);

            // Here, you can add code for calling the hypothetical home automation service, passing in any entity
            // information that you need.
            break;
        case weatherDispatchKey:
            await DispatchToLuisModelAsync(context, WeatherLuisKey);

            // Here, you can add code for calling the hypothetical weather service,
            // passing in any entity information that you need
            break;
        case noneDispatchKey:
            // You can provide logic here to handle the known None intent (none of the above).
            // In this example we fall through to the QnA intent.
        case qnaDispatchKey:
            await DispatchToQnAMakerAsync(context, QnAMakerKey);
            break;

        default:
            // The intent didn't match any case, so just display the recognition results.
            await context.SendActivityAsync($"Dispatch intent: {topIntent.Value.intent} ({topIntent.Value.score}).");
            break;
    }
}

// Dispatches the turn to the request QnAMaker app.
private async Task DispatchToQnAMakerAsync(
    ITurnContext context,
    string appName,
    CancellationToken cancellationToken = default(CancellationToken))
{
    if (!string.IsNullOrEmpty(context.Activity.Text))
    {
        var results = await _services.QnAServices[appName].GetAnswersAsync(context).ConfigureAwait(false);
        if (results.Any())
        {
            await context.SendActivityAsync(results.First().Answer, cancellationToken: cancellationToken);
        }
        else
        {
            await context.SendActivityAsync($"Couldn't find an answer in the {appName}.");
        }
    }
}


// Dispatches the turn to the requested LUIS model.
private async Task DispatchToLuisModelAsync(
    ITurnContext context,
    string appName,
    CancellationToken cancellationToken = default(CancellationToken))
{
    await context.SendActivityAsync($"Sending your request to the {appName} system ...");
    var result = await _services.LuisServices[appName].RecognizeAsync(context, cancellationToken);

    await context.SendActivityAsync($"Intents detected by the {appName} app:\n\n{string.Join("\n\n", result.Intents)}");

    if (result.Entities.Count > 0)
    {
        await context.SendActivityAsync($"The following entities were found in the message:\n\n{string.Join("\n\n", result.Entities)}");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Quando o modelo produz um resultado, ele indica qual serviço pode processar mais adequadamente a declaração. O código neste bot encaminha a solicitação para o serviço correspondente.

```javascript
switch (dispatchTopIntent) {
   case HOME_AUTOMATION_INTENT:
      await this.homeAutomationDialog.onTurn(turnContext);
      break;
   case WEATHER_INTENT:
      await this.weatherDialog.onTurn(turnContext);
      break;
   case QNA_INTENT:
      await this.qnaDialog.onTurn(turnContext);
      break;
   case NONE_INTENT:
      default:
      // Unknown request
       await turnContext.sendActivity(`I do not understand that.`);
       await turnContext.sendActivity(`I can help with weather forecast, turning devices on and off and answer general questions like 'hi', 'who are you' etc.`);
 }
 ```

 Em `homeAutomation.js`
 
 ```javascript
 async onTurn(turnContext) {
    // make call to LUIS recognizer to get home automation intent + entities
    const homeAutoResults = await this.luisRecognizer.recognize(turnContext);
    const topHomeAutoIntent = LuisRecognizer.topIntent(homeAutoResults);
    // depending on intent, call turn on or turn off or return unknown
    switch (topHomeAutoIntent) {
       case HOME_AUTOMATION_INTENT:
          await this.handleDeviceUpdate(homeAutoResults, turnContext);
          break;
       case NONE_INTENT:
       default:
         await turnContext.sendActivity(`HomeAutomation dialog cannot fulfill this request.`);
    }
}
```

Em `weather.js`

```javascript
async onTurn(turnContext) {
   // Call weather LUIS model.
   const weatherResults = await this.luisRecognizer.recognize(turnContext);
   const topWeatherIntent = LuisRecognizer.topIntent(weatherResults);
   // Get location entity if available.
   const locationEntity = (LOCATION_ENTITY in weatherResults.entities) ? weatherResults.entities[LOCATION_ENTITY][0] : undefined;
   const locationPatternAnyEntity = (LOCATION_PATTERNANY_ENTITY in weatherResults.entities) ? weatherResults.entities[LOCATION_PATTERNANY_ENTITY][0] : undefined;
   // Depending on intent, call "Turn On" or "Turn Off" or return unknown.
   switch (topWeatherIntent) {
      case GET_CONDITION_INTENT:
         await turnContext.sendActivity(`You asked for current weather condition in Location = ` + (locationEntity || locationPatternAnyEntity));
         break;
      case GET_FORECAST_INTENT:
         await turnContext.sendActivity(`You asked for weather forecast in Location = ` + (locationEntity || locationPatternAnyEntity));
         break;
      case NONE_INTENT:
      default:
         wait turnContext.sendActivity(`Weather dialog cannot fulfill this request.`);
   }
}
```

Em `qna.js`

```javascript
async onTurn(turnContext) {
   // Call QnA Maker and get results.
   const qnaResult = await this.qnaRecognizer.generateAnswer(turnContext.activity.text, QNA_TOP_N, QNA_CONFIDENCE_THRESHOLD);
   if (!qnaResult || qnaResult.length === 0 || !qnaResult[0].answer) {
       await turnContext.sendActivity(`No answer found in QnA Maker KB.`);
       return;
    }
    // respond with qna result
    await turnContext.sendActivity(qnaResult[0].answer);
}
```
---

## <a name="edit-intents-to-improve-performance"></a>Editar intenções para melhorar o desempenho

Depois que seu bot estiver em execução, é possível melhorar o desempenho do bot removendo declarações semelhantes ou sobrepostas. Por exemplo, vamos supor que no aplicativo LUIS `Home Automation`, solicitações como "acender minhas luzes" são mapeadas para uma intenção "TurnOnLights", mas solicitações como "Por que minhas luzes não acendem?" são mapeadas para uma intenção "None", para que possam ser passadas para o QnA Maker. Quando você combina o aplicativo LUIS e o serviço do QnA Maker usando dispatch, é necessário executar um dos seguintes procedimentos:

* Remova a intenção “None” do aplicativo LUIS `Home Automation` original e, em vez disso, adicione as declarações dessa intenção à intenção “None” no aplicativo do dispatcher.
* Se você não remover a intenção “None” do aplicativo LUIS original, será necessário adicionar uma lógica em seu bot para passar as mensagens que correspondem à intenção “None” para o serviço do QnA Maker.

Qualquer uma dessas duas ações acima reduzirá o número de vezes que o seu bot responde aos usuários com a mensagem “Não foi possível encontrar uma resposta”. 

## <a name="additional-resources"></a>Recursos adicionais

**Atualizar ou criar um novo modelo do LUIS:** Este exemplo é baseado em um modelo predefinido do LUIS. Confira [aqui](https://aka.ms/create-luis-model#updating-your-cognitive-models
) mais informações para ajudá-lo a atualizar esse modelo ou criar um novo modelo do LUIS.

**Excluir recursos:** Este exemplo cria vários aplicativos e recursos que você pode excluir usando as etapas listadas abaixo, mas você não deve excluir os recursos dos quais *outros aplicativos ou serviços* dependam. 

_Recursos do LUIS_
1. Entre no portal [luis.ai](https://www.luis.ai).
1. Acesse a página _Meus Aplicativos_.
1. Selecione os aplicativos criados por este exemplo.
   * `Home Automation`
   * `Weather`
   * `NLP-With-Dispatch-BotDispatch`
1. Clique em _Excluir_ e clique em _OK_ para confirmar.

_Recursos do QnA Maker_
1. Entre no portal [qnamaker.ai](https://www.qnamaker.ai/).
1. Acesse a página _My knowledge bases_ (Minhas bases de conhecimento).
1. Clique no botão de exclusão da base de conhecimento `Sample QnA` e clique em _Delete_ (Excluir) para confirmar.

**Prática recomendada:** Para melhorar os serviços usados neste exemplo, confira a prática recomendada para [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-concept-best-practices) e [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/concepts/best-practices).