---
title: Integrar vários LUIS e serviços do QnA com a ferramenta Expedição | Microsoft Docs
description: Aprenda a usar o criador LUIS e QnA no seu bot.
keywords: Ferramenta do Luis, QnA, expedição, vários serviços
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/09/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e59a7fd86e3802ea23d59a439165a194314639f8
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997393"
---
# <a name="integrate-multiple-luis-and-qna-services-with-the-dispatch-tool"></a>Integrar vários serviços LUIS e QnA com a ferramenta de Expedição

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

<!--TODO: Add the JS sections back in and update them once the JS sample is working.-->

Este tutorial demonstra como usar um modelo LUIS gerado pela ferramenta Despachar, para integrar seu bot com vários aplicativos LUIS (Compreensão de Linguagem) e serviços QnAMaker. Este exemplo combina os serviços a seguir.

| Tipo de serviço | NOME | DESCRIÇÃO |
|------|------|------|
| Aplicativo LUIS | HomeAutomation | Reconhece uma intenção de automação residencial com dados de entidade associados.|
| Aplicativo LUIS | Clima | Reconhece as intenções Weather.GetForecast e Weather.GetCondition com dados de localização.|
| Serviço QnAMaker | Perguntas frequentes  | Fornece respostas a algumas perguntas simples sobre o bot. |

O código deste artigo é obtido no exemplo **NLP com Dispatch** [[C#](https://aka.ms/dispatch-sample-cs)<!-- | [JS](https://aka.ms/dispatch-sample-js)-->].

Confira [reconhecimento de linguagem](bot-builder-concept-luis.md), para ter uma visão geral dos serviços de linguagem. Confira os artigos de instrução para [LUIS](bot-builder-howto-v4-luis.md) e [QnA Maker](bot-builder-howto-qna.md) e obtenha instruções sobre a implementação deles em um bot.

Siga as instruções no LEIAME do exemplo para configurar e testar o bot, ou você pode prosseguir para [Observações sobre o código](#notes-about-the-code).

## <a name="create-the-services-and-test-the-bot"></a>Criar os serviços e testar o bot

Siga as instruções do LEIAME para o exemplo. Você usará as ferramentas da CLI para criar e publicar esses serviços e atualizar informações sobre eles em seu arquivo de configuração (**.bot**).

1. Clonar ou efetuar pull do repositório de exemplos.
1. Instale as ferramentas de CLI do BotBuilder.
1. Configure manualmente os serviços necessários.

### <a name="test-your-bot"></a>Testar seu bot

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Inicie seu bot usando o Visual Studio ou o Visual Studio Code.

<!--
# [JavaScript](#tab/javascript)
-->

---

Conecte-se ao seu bot com o Bot Framework Emulator.

Estas são algumas entradas cobertas pelos serviços que incluímos:

* QnA Maker
  * `hi`, `good morning`
  * `what are you`, `what do you do`
* LUIS (automação doméstica)
  * `turn on bedroom light`
  * `turn off bedroom light`
  * `make some coffee`
* LUIS (clima)
  * `whats the weather in chennai india`
  * `what's the forecast for bangalore`
  * `show me the forecast for nebraska`

## <a name="notes-about-the-code"></a>Observações sobre o código

### <a name="packages"></a>Pacotes

Este exemplo usa os seguintes pacotes.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

As versões v4 mais recentes desses [pacotes do NuGet](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui#updating-a-package).

* `Microsoft.Bot.Builder`
* `Microsoft.Bot.Builder.AI.Luis`
* `Microsoft.Bot.Builder.AI.QnA`
* `Microsoft.Bot.Builder.Integration.AspNet.Core`
* `Microsoft.Bot.Configuration`

<!--
# [JavaScript](#tab/javascript)

Download the [LUIS Dispatch sample][DispatchBotJs].  Install the required packages, including the `botbuilder-ai` package for LUIS and QnA Maker, using npm:

* `npm install --save botbuilder`
* `npm install --save botbuilder-ai`
-->

---

### <a name="botbuilder-cli-tools"></a>Ferramentas de CLI do BotBuilder

O exemplo usa estas [ferramentas de CLI do BotBuilder](https://aka.ms/botbuilder-tools-readme) (disponíveis via npm) para criar, treinar e publicar os serviços LUIS, QnA Maker e Dispatch; e para registrar informações sobre esses serviços no arquivo de configuração do seu bot (**. bot**).

* [Dispatch](https://aka.ms/botbuilder-tools-dispatch)
* [LUDown](https://aka.ms/botbuilder-tools-ludown-readme)
* [LUIS](https://aka.ms/botbuilder-tools-luis)
* [MSBot](https://aka.ms/botbuilder-tools-msbot-readme)
* [QnAMaker](https://aka.ms/botbuilder-tools-qnaMaker)

> [!TIP]
> Para verificar se você tem a versão mais recente do npm e dessas ferramentas de CLI, execute o comando a seguir.
>
> ```shell
> npm i -g npm dispatch ludown luis-apis msbot qnamaker
> ```

Após usar as ferramentas para configurar os serviços, seu arquivo **.bot** para este exemplo deverá se parecer com o seguinte. (Executar `msbot secret -n` para criptografar os valores confidenciais nesse arquivo.)

```json
{
    "name": "NLP-With-Dispatch-Bot",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "id": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages"
        },
        {
            "type": "luis",
            "name": "Home Automation",
            "appId": "<your-home-automation-luis-app-id>",
            "version": "0.1",
            "authoringKey": "<your-luis-authoring-key>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "region": "westus",
            "id": "110"
        },
        {
            "type": "luis",
            "name": "Weather",
            "appId": "<your-weather-luis-app-id>",
            "version": "0.1",
            "authoringKey": "<your-luis-authoring-key>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "region": "westus",
            "id": "92"
        },
        {
            "type": "qna",
            "name": "Sample QnA",
            "kbId": "<your-qna-knowledge-base-id>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "endpointKey": "<your-qna-endpoint-key>",
            "hostname": "<your-qna-host-name>",
            "id": "184"
        },
        {
            "type": "dispatch",
            "name": "NLP-With-Dispatch-BotDispatch",
            "appId": "<your-dispatch-app-id>",
            "authoringKey": "<your-luis-authoring-key>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "version": "Dispatch",
            "region": "westus",
            "serviceIds": [
                "110",
                "92",
                "184"
            ],
            "id": "27"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

### <a name="connecting-to-the-services-from-your-bot"></a>Conectar-se aos serviços de seu bot

Para se conectar aos serviços Dispatch, LUIS e QnA Maker, seu bot obtém informações do arquivo **.bot**.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em **Startup.cs**, `ConfigureServices` lê o arquivo de configuração, e `InitBotServices` usa essas informações para inicializar os serviços. Cada vez que é criado, o bot é inicializado com o objeto `BotServices` registrado. Estas são as partes relevantes desses dois métodos.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //...
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
    services.AddSingleton(sp => botConfig
        ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

    // Retrieve current endpoint.
    var environment = _isProduction ? "production" : "development";
    var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
    if (!(service is EndpointService endpointService))
    {
        throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
    }

    var connectedServices = InitBotServices(botConfig);

    services.AddSingleton(sp => connectedServices);
    //...
}
```

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
/// <summary>
/// Depending on the intent from Dispatch, routes to the right LUIS model or QnA service.
/// </summary>
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

/// <summary>
/// Dispatches the turn to the request QnAMaker app.
/// </summary>
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

/// <summary>
/// Dispatches the turn to the requested LUIS model.
/// </summary>
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

A execução de `dispatch eval` gera um arquivo **Summary.html** que fornece estatísticas sobre o desempenho previsto do modelo de linguagem.

> [!TIP]
> Você pode executar o `dispatch eval` em qualquer aplicativo LUIS, não apenas em aplicativos LUIS criados pela ferramenta de expedição.

### <a name="edit-intents-for-duplicates-and-overlaps"></a>Editar as intenções de duplicatas e sobreposições

Examine as declarações de exemplo são sinalizadas como duplicatas na **Summary.html**e remover exemplos semelhantes ou sobrepostos. Por exemplo, vamos supor que no aplicativo LUIS `Home Automation`, solicitações como "acender minhas luzes" são mapeadas para uma intenção "TurnOnLights", mas solicitações como "Por que minhas luzes não acendem?" são mapeadas para uma intenção "None", para que possam ser passadas para o QnA Maker. Quando você combina o aplicativo LUIS e o serviço do QnA Maker usando dispatch, é necessário executar um dos seguintes procedimentos:

* Remova o "None" intenção original `Home Automation` aplicativo LUIS e adicione as declarações nessa intenção de "None" intencional no aplicativo do dispatcher.
* Se você não remover a intenção "Nenhum" do aplicativo LUIS original, será necessário adicionar lógica em seu bot para passar as mensagens que correspondem a essa intenção para o serviço do criador QnA.

> [!TIP]
> Analise [Práticas recomendadas para o entendimento de idiomas](./bot-builder-concept-luis.md#best-practices-for-language-understanding) para obter dicas sobre como melhorar o desempenho do seu modelo de idioma.

## <a name="to-clean-up-resources-from-this-sample"></a>Para limpar os recursos deste exemplo

Este exemplo cria vários aplicativos e recursos. Siga estas instruções para exclui-los.

### <a name="luis-resources"></a>Recursos do LUIS

1. Entre no portal [luis.ai](https://www.luis.ai).
1. Acesse a página **Meus Aplicativos**.
1. Selecione os aplicativos criados por este exemplo.
   * `Home Automation`
   * `Weather`
   * `NLP-With-Dispatch-BotDispatch`
1. Clique em **Excluir** e clique em **OK** para confirmar.

### <a name="qna-maker-resources"></a>Recursos do QnA Maker

1. Entre no portal [qnamaker.ai](https://www.qnamaker.ai/).
1. Acesse a página **My knowledge bases** (Minhas bases de conhecimento).
1. Clique no botão de exclusão da base de conhecimento `Sample QnA` e clique em **Delete** (Excluir) para confirmar.

### <a name="azure-resources"></a>Recursos do Azure

> [!WARNING]
> Não exclua qualquer recurso do qual qualquer outro aplicativo ou serviço dependa.

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Acesse a página **Visão geral** do recurso dos Serviços Cognitivos que você criou para o exemplo.
1. Clique em **Excluir** e clique em **Sim** para confirmar.

## <a name="additional-resources"></a>Recursos adicionais

* [Reconhecimento vocal](bot-builder-concept-luis.md)
* [Projetar bots de conhecimento](../bot-service-design-pattern-knowledge-base.md)
* [Configurar a desobstrução de fala](../bot-service-manage-speech-priming.md)
* [Usar o LUIS para Reconhecimento vocal](bot-builder-howto-v4-luis.md)
* [Use o QnA Maker para responder a perguntas](bot-builder-howto-qna.md)
