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
ms.date: 11/26/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9b0ddf5cf8af61048ba78f10824c9573da82fc08
ms.sourcegitcommit: a722f960cd0a8513d46062439eb04de3a0275346
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336266"
---
# <a name="use-multiple-luis-and-qna-models"></a>Usar vários modelos de LUIS e QnA

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Neste tutorial, demonstraremos como usar o serviço Dispatch para rotear declarações quando houver vários modelos de LUIS e serviços do QnA Maker para cenários diferentes compatíveis com um bot. Nesse caso, configuramos a expedição com vários modelos de LUIS para conversas sobre automação residencial e informações sobre o clima, além do serviço QnA Maker para responder a perguntas com base em um arquivo de texto de perguntas frequentes como entrada. Este exemplo combina os serviços a seguir.

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

Siga as instruções em **LEIAME** para [C#](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/14.nlp-with-dispatch/README.md) ou [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/14.nlp-with-dispatch/README.md) para compilar e executar o exemplo usando o emulador. 

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
O código a seguir inicializa as referências do bot para serviços externos. Por exemplo, os serviços LUIS e QnaMaker são criados aqui. Esses serviços externos são configurados usando a classe `BotConfiguration` (com base no conteúdo do arquivo ".bot".

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

<!--
# [JavaScript](#tab/javascript)

```javascript
```
-->

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

<!--
# [JavaScript](#tab/javascript)

```javascript
```
-->

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

<!--
# [JavaScript](#tab/javascript)

```javascript
```
-->

---

## <a name="evaluate-the-dispatchers-performance"></a>Avaliar o desempenho do despachante

Às vezes, há mensagens do usuário fornecidas como exemplos nos aplicativos LUIS e nos serviços do fabricante do QnA, e o aplicativo LUIS combinado que o Dispatch gera não terá um bom desempenho para essas entradas. Verifique o desempenho do seu aplicativo usando a opção `eval`.

```shell
dispatch eval
```

A execução de `dispatch eval` gera um arquivo **Summary.html** que fornece estatísticas sobre o desempenho previsto do modelo de linguagem. Você pode executar o `dispatch eval` em qualquer aplicativo LUIS, não apenas em aplicativos LUIS criados pela ferramenta de expedição.

### <a name="edit-intents-for-duplicates-and-overlaps"></a>Editar as intenções de duplicatas e sobreposições

Examine as declarações de exemplo são sinalizadas como duplicatas na **Summary.html**e remover exemplos semelhantes ou sobrepostos. Por exemplo, vamos supor que no aplicativo LUIS `Home Automation`, solicitações como "acender minhas luzes" são mapeadas para uma intenção "TurnOnLights", mas solicitações como "Por que minhas luzes não acendem?" são mapeadas para uma intenção "None", para que possam ser passadas para o QnA Maker. Quando você combina o aplicativo LUIS e o serviço do QnA Maker usando dispatch, é necessário executar um dos seguintes procedimentos:

* Remova o "None" intenção original `Home Automation` aplicativo LUIS e adicione as declarações nessa intenção de "None" intencional no aplicativo do dispatcher.
* Se você não remover a intenção "Nenhum" do aplicativo LUIS original, será necessário adicionar lógica em seu bot para passar as mensagens que correspondem a essa intenção para o serviço do criador QnA.


## <a name="additional-resources"></a>Recursos adicionais 

**Excluir recursos:** este exemplo cria vários aplicativos e recursos que você pode excluir usando as etapas listadas abaixo, mas você não deve excluir os recursos dos quais *outros aplicativos ou serviços* dependam. 

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

**Melhor prática:** para melhorar os serviços usados neste exemplo, confira a melhor prática para [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-concept-best-practices) e [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/concepts/best-practices).
