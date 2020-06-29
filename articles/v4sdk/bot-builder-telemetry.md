---
title: Adicionar telemetria ao seu bot – Serviço de Bot
description: Saiba como integrar seu bot aos novos recursos de telemetria.
keywords: telemetria, appinsights, bot de monitor
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e0064980a7b4cb1f5c37485550aa56a338209676
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77173041"
---
# <a name="add-telemetry-to-your-bot"></a>Adicionar telemetria ao seu bot

[!INCLUDE[applies-to](../includes/applies-to.md)]


O registro em log de telemetria foi adicionado à versão 4.2 do SDK do Bot Framework.  Isso permite que os aplicativos do bot enviem dados de evento para serviços de telemetria, tais como o [Application Insights](https://aka.ms/appinsights-overview). A telemetria oferece informações sobre o bot mostrando quais recursos são mais usados, detecta comportamento indesejado e fornece visibilidade da disponibilidade, do desempenho e do uso.

***Observação: Na versão 4.6, o método padrão para implementar a telemetria em um bot foi atualizado para garantir que a telemetria seja registrada corretamente ao usar um adaptador personalizado. Este artigo foi atualizado para mostrar o novo método. As alterações são compatíveis com versões anteriores e os bots que usam o método anterior continuarão a registrar a telemetria em log corretamente.***


Neste artigo, você aprenderá a implementar a telemetria em seu bot usando o Application Insights:

* O código necessário para ligar a telemetria no bot e conectar-se a Application Insights

* Habilitar a telemetria nas [caixas de diálogo](bot-builder-concept-dialog.md) de bots

* Habilite a telemetria para capturar dados de uso de outros serviços, tais como o [LUIS](bot-builder-howto-v4-luis.md) e o [QnA Maker](bot-builder-howto-qna.md).

* Visualizar os dados telemétricos no Application Insights

## <a name="prerequisites"></a>Pré-requisitos

* O [código de exemplo do CoreBot](https://aka.ms/cs-core-sample)

* O [código de exemplo do Application Insights](https://aka.ms/csharp-corebot-app-insights-sample)

* Uma assinatura do [Microsoft Azure](https://portal.azure.com/)

* Uma [chave do Application Insights](../bot-service-resources-app-insights-keys.md)

* Familiaridade com o [Application Insights](https://aka.ms/appinsights-overview)

> [!NOTE]
> O [código de exemplo do Application Insights](https://aka.ms/csharp-corebot-app-insights-sample) foi criado com base no [código de exemplo do CoreBot](https://aka.ms/cs-core-sample). Este artigo guiará você pela modificação do código de exemplo do CoreBot para incorporar a telemetria. Se acompanhar no Visual Studio, você terá o código de exemplo do Application Insights quando terminarmos.

## <a name="wiring-up-telemetry-in-your-bot"></a>Conectar a telemetria em seu bot

Começaremos com o [aplicativo de exemplo CoreBot](https://aka.ms/cs-core-sample) e adicionaremos o código necessário para integrar a telemetria em qualquer bot. Isso permitirá que o Application Insights comece a acompanhar as solicitações.

1. Abrir o [aplicativo de exemplo do CoreBot](https://aka.ms/cs-core-sample) no Visual Studio

2. Adicione o pacote NuGet `Microsoft.Bot.Builder.Integration.ApplicationInsights.Core `. Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs):


3. Inclua as instruções a seguir em `Startup.cs`:
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.Bot.Builder.ApplicationInsights;
    using Microsoft.Bot.Builder.Integration.ApplicationInsights.Core;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    ```

    Observação: Se estiver acompanhando e atualizando o código de exemplo do CoreBot, você observará que a instrução using para `Microsoft.Bot.Builder.Integration.AspNet.Core` já existe no exemplo do CoreBot.

4. Inclua o código a seguir no método `ConfigureServices()` em `Startup.cs`. Isso tornará os serviços de telemetria disponíveis para o bot por meio da [DI (injeção de dependência)](https://aka.ms/asp.net-core-dependency-interjection):
    ```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        ...
            // Create the Bot Framework Adapter with error handling enabled.
            services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();

            // Add Application Insights services into service collection
            services.AddApplicationInsightsTelemetry();

            // Create the telemetry client.
            services.AddSingleton<IBotTelemetryClient, BotTelemetryClient>();

            // Add telemetry initializer that will set the correlation context for all telemetry items.
            services.AddSingleton<ITelemetryInitializer, OperationCorrelationTelemetryInitializer>();

            // Add telemetry initializer that sets the user ID and session ID (in addition to other bot-specific properties such as activity ID)
            services.AddSingleton<ITelemetryInitializer, TelemetryBotIdInitializer>();

            // Create the telemetry middleware to initialize telemetry gathering
            services.AddSingleton<TelemetryInitializerMiddleware>();

            // Create the telemetry middleware (used by the telemetry initializer) to track conversation events
            services.AddSingleton<TelemetryLoggerMiddleware>();
        ...
    }
    ```

    Observação: Se estiver acompanhando atualizando o código de exemplo do CoreBot, você notará que `services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();` já existe 

5. Instrua o adaptador a usar o código de middleware que foi adicionado ao método `ConfigureServices()`. Você faz isso em `AdapterWithErrorHandler.cs` com o parâmetro TelemetryInitializerMiddleware telemetryInitializerMiddleware na lista de parâmetros construtores e a instrução `Use(telemetryInitializerMiddleware);` no construtor, conforme mostrado aqui:
    ```csharp
        public AdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger, TelemetryInitializerMiddleware telemetryInitializerMiddleware, ConversationState conversationState = null)
            : base(configuration, logger)
    {
        ...
        Use(telemetryInitializerMiddleware);
    }
    ```

6. Você também precisará adicionar `Microsoft.Bot.Builder.Integration.ApplicationInsights.Core` à lista de instruções Using no `AdapterWithErrorHandler.cs`.

7. Adicione a chave de instrumentação do Application Insights ao arquivo `appsettings.json`. O arquivo `appsettings.json` contém metadados sobre serviços externos que o bot usa durante a execução. Por exemplo, as conexões de serviço e os metadados do CosmosDB, Application Insights e LUIS (Reconhecimento Vocal) são armazenados nele. A adição ao arquivo `appsettings.json` deve estar neste formato:

    ```json
    {
        "MicrosoftAppId": "",
        "MicrosoftAppPassword": "",
        "ApplicationInsights": {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        }
    }
    ```
    Observação: Detalhes sobre como obter a _chave de instrumentação do Application Insights_ podem ser encontrados no artigo [Chaves do Application Insights](../bot-service-resources-app-insights-keys.md).

Neste ponto, o trabalho preliminar para habilitar a telemetria usando o Application Insights está concluído.  Você pode executar o bot localmente usando o emulador de bot e, em seguida, ir para o Application Insights para ver o que está sendo registrado em log, assim como o tempo de resposta, a integridade geral do aplicativo e as informações gerais de execução. 

## <a name="enabling--disabling-activity-event-and-personal-information-logging"></a>Habilitar/desabilitar o registro em log de eventos de atividade e de informações pessoais

### <a name="enabling-or-disabling-activity-logging"></a>Habilitar ou desabilitar o log de atividades

Por padrão, o `TelemetryInitializerMiddleware` usará o `TelemetryLoggerMiddleware` para registrar a telemetria em log quando o bot enviar/receber atividades. O log de atividades cria logs de eventos personalizados em seu recurso do Application Insights.  Se desejar, você poderá desabilitar o log de eventos de atividade definindo `logActivityTelemetry` como false no `TelemetryInitializerMiddleware` ao registrá-lo em **Startup.cs**.

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry initializer middleware
    services.AddSingleton<TelemetryInitializerMiddleware>(sp =>
            {
                var httpContextAccessor = sp.GetService<IHttpContextAccessor>();
                var loggerMiddleware = sp.GetService<TelemetryLoggerMiddleware>();
                return new TelemetryInitializerMiddleware(httpContextAccessor, loggerMiddleware, logActivityTelemetry: false);
            });
    ...
}
```

A adição de `IHttpContextAccessor` também exigirá que você referencie a biblioteca HTTP Microsoft ASPNetCore, por meio da adição desta instrução Using:

```cs
using Microsoft.AspNetCore.Http; 
```

### <a name="enable-or-disable-logging-personal-information"></a>Habilitar ou desabilitar o registro em log de informações pessoais

Por padrão, se o log de atividades estiver habilitado, algumas propriedades nas atividades de entrada/saída serão excluídas do registro em log, pois é provável que contenham informações pessoais, tais como o nome de usuário e o texto da atividade. Você pode optar por incluir essas propriedades em seu log fazendo a alteração a seguir a **Startup.cs** ao registrar o `TelemetryLoggerMiddleware`.

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry initializer middleware
    services.AddSingleton<TelemetryLoggerMiddleware>(sp =>
            {
                var telemetryClient = sp.GetService<IBotTelemetryClient>();
                return new TelemetryLoggerMiddleware(telemetryClient, logPersonalInformation: true);
            });
    ...
}
```

Em seguida, veremos o que precisa ser incluído para adicionar a funcionalidade de telemetria às caixas de diálogo. Isso permitirá que você obtenha informações adicionais, tais como quais caixas de diálogo serão executadas, além de estatísticas sobre cada uma delas.

## <a name="enabling-telemetry-in-your-bots-dialogs"></a>Habilitar a telemetria nas caixas de diálogo de bots

Ao adicionar uma nova caixa de diálogo a qualquer ComponentDialog, ela herdará o Microsoft.Bot.Builder.IBotTelemetryClient da caixa de diálogo pai.  Por exemplo, no aplicativo de exemplo CoreBot, todas as caixas de diálogo são adicionadas à MainDialog, que é uma ComponentDialog.  Depois de definir a propriedade TelemetryClient para a MainDialog, todas as caixas de diálogo adicionadas a ela herdarão automaticamente a telemetryClient dela, portanto, não precisará ser definida explicitamente ao adicionar caixas de diálogo.
 
 Siga as etapas abaixo para atualizar o exemplo de CoreBot:

1.  Em `MainDialog.cs`, atualize a lista de parâmetros do construtor para incluir o parâmetro `IBotTelemetryClient` e, em seguida, defina a propriedade TelemetryClient da MainDialog para esse valor, conforme mostrado no seguinte snippet de código:

    ```csharp
    public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger, IBotTelemetryClient telemetryClient)
        : base(nameof(MainDialog))
    {
        // Set the telemetry client for this and all child dialogs.
        this.TelemetryClient = telemetryClient;
        ...
    }
    ```

> [!TIP] 
> Se você estiver acompanhando e atualizando o código de exemplo do CoreBot, poderá consultar o [código de exemplo do Application Insights](https://aka.ms/csharp-corebot-app-insights-sample) se tiver algum problema.

Isso é tudo o que é necessário para adicionar telemetria às caixas de diálogo de bots, neste ponto, se você tiver executado o bot, verá que as coisas estão sendo registradas no Application Insights. No entanto, se você tiver uma tecnologia integrada, tal como o LUIS e o QnA Maker, será necessário adicionar o `TelemetryClient` a esse código também.


## <a name="enabling-telemetry-to-capture-usage-data-from-other-services-like-luis-and-qna-maker"></a>Habilitar a telemetria para capturar dados de uso de outros serviços, tais como o LUIS e o QnA Maker

Em seguida, implementaremos a funcionalidade de telemetria em seu serviço LUIS. O serviço LUIS tem o registro em log de telemetria interno disponível para que o esforço necessário para começar a obter dados de telemetria do LUIS seja mínimo.  Se você estiver interessado em habilitar a telemetria em um bot habilitado para o QnA Maker, confira [Adicionar telemetria ao seu bot do QnAMaker](bot-builder-telemetry-QnAMaker.md)

1. O parâmetro _`IBotTelemetryClient telemetryClient`_ é necessário no construtor `FlightBookingRecognizer` em `FlightBookingRecognizer.cs`:

    ```cs
    public FlightBookingRecognizer(IConfiguration configuration, IBotTelemetryClient telemetryClient)
    ```

2. Em seguida, você precisará habilitar o `telemetryClient` ao criar seu `LuisRecognizer` no construtor `FlightBookingRecognizer`. Para fazer isso, adicione o `telemetryClient` como um novo _LuisPredictionOption_:

    ```cs
    if (luisIsConfigured)
    {
        var luisApplication = new LuisApplication(
            configuration["LuisAppId"],
            configuration["LuisAPIKey"],
            "https://" + configuration["LuisAPIHostName"]);

        // Set the recognizer options depending on which endpoint version you want to use.
        // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
        var recognizerOptions = new LuisRecognizerOptionsV3(luisApplication)
        {
            TelemetryClient = telemetryClient,
        };
        _recognizer = new LuisRecognizer(recognizerOptions);
    }
    ```

3. Você precisará ter o LUIS 

Isso é tudo, agora você deve ter um bot funcional que registra dados de telemetria no Application Insights. Você pode usar o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) para executar o bot localmente. Você não deve experienciar nenhuma alteração no comportamento do bot, mas ele estará registrando informações no Application Insights. Interaja com o bot enviando várias mensagens e, na próxima seção, examinaremos os resultados da telemetria no Application Insights.

Para obter informações sobre como testar e depurar o bot, consulte os seguintes artigos:

 * [Depurar um bot](../bot-service-debug-bot.md)
 * [Diretrizes para testes e depuração](bot-builder-testing-debugging.md)
 * [Depurar com o emulador](../bot-service-debug-emulator.md)


## <a name="visualizing-your-telemetry-data-in-application-insights"></a>Visualizar os dados telemétricos no Application Insights
O Application Insights monitora a disponibilidade, o desempenho e o uso do aplicativo de bot, esteja ele hospedado na nuvem ou localmente. Ele aproveita a plataforma avançada de análise de dados no Azure Monitor para fornecer informações detalhadas sobre as operações do aplicativo e diagnosticar erros sem esperar que um usuário relate-os. Há algumas maneiras de ver os dados de telemetria coletados pelo Application Insights, duas das principais maneiras são por meio de consultas e do painel. 

### <a name="querying-your-telemetry-data-in-application-insights-using-kusto-queries"></a>Consultar os dados telemétricos no Application Insights usando o Kusto Queries
Use esta seção como um ponto de partida para aprender a usar consultas de log no Application Insights. Ela demonstra duas consultas úteis e fornece links para outras documentações com informações adicionais.

Para consultar seus dados

1. Vá para o [Portal do Azure](https://portal.azure.com)
2. Navegue até o Application Insights. A maneira mais fácil de fazer isso é clicar em **Monitorar > Aplicativos** e encontrá-lo lá. 
3. Uma vez no Application Insights, você pode clicar em _Logs (Análise)_ na barra de navegação.

    ![Logs (Análise)](media/AppInsights-LogView.png)

4. Isso abrirá a janela Consulta.  Insira a consulta a seguir e selecione _Executar_:

    ```sql
    customEvents
    | where name=="WaterfallStart"
    | extend DialogId = customDimensions['DialogId']
    | extend InstanceId = tostring(customDimensions['InstanceId'])
    | join kind=leftouter (customEvents | where name=="WaterfallComplete" | extend InstanceId = tostring(customDimensions['InstanceId'])) on InstanceId    
    | summarize starts=countif(name=='WaterfallStart'), completes=countif(name1=='WaterfallComplete') by bin(timestamp, 1d), tostring(DialogId)
    | project Percentage=max_of(0.0, completes * 1.0 / starts), timestamp, tostring(DialogId) 
    | render timechart
    ```
5. Isso retornará o percentual de caixas de diálogo em cascata que são executados até a conclusão.

    ![Logs (Análise)](media/AppInsights-Query-PercentCompleteDialog.png)


> [!TIP]
> Você pode fixar qualquer consulta ao seu painel do Application Insights selecionando o botão no canto superior direito da folha **Logs (Análise)** . Basta selecionar o painel ao qual você deseja que ele seja fixado e ele estará disponível na próxima vez que você visitar esse painel.


## <a name="the-application-insights-dashboard"></a>O painel do Application Insights

Sempre que você criar um recurso do Application Insights no Azure, um painel será automaticamente criado e associado a ele.  Você pode ver esse painel selecionando o botão na parte superior da sua folha do Application Insights, rotulado **Painel de Aplicativo**. 

![Link do Painel do Aplicativo](media/Application-Dashboard-Link.png)


Como alternativa, para exibir os dados, acesse o portal do Azure. À esquerda, clique em **Painel**, depois, na lista suspensa, selecione o painel que você deseja.

Lá, você verá algumas informações padrão sobre o desempenho do bot e quaisquer consultas adicionais que tenha fixado ao seu painel.



## <a name="additional-information"></a>Informações adicionais

* [Adicionar telemetria ao seu bot do QnA Maker](bot-builder-telemetry-qnamaker.md)

* [O que é o Application Insights?](https://aka.ms/appinsights-overview)

* [Usar a Pesquisa no Application Insights](https://aka.ms/search-in-application-insights)

* [Criar painéis de KPI personalizados usando o Azure Application Insights](https://aka.ms/custom-kpi-dashboards-application-insights)


<!--
The easiest way to test is by creating a dashboard using [Azure portal's template deployment page](https://portal.azure.com/#create/Microsoft.Template).
- Click ["Build your own template in the editor"]
- Copy and paste either one of these .json file that is provided to help you create the dashboard:
  - [System Health Dashboard](https://aka.ms/system-health-appinsights)
  - [Conversation Health Dashboard](https://aka.ms/conversation-health-appinsights)
- Click "Save"
- Populate `Basics`: 
   - Subscription: <your test subscription>
   - Resource group: <a test resource group>
   - Location: <such as West US>
- Populate `Settings`:
   - Insights Component Name: <like `core672so2hw`>
   - Insights Component Resource Group: <like `core67`>
   - Dashboard Name:  <like `'ConversationHealth'` or `SystemHealth`>
- Click `I agree to the terms and conditions stated above`
- Click `Purchase`
- Validate
   - Click on [`Resource Groups`](https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fsubscriptions%2FresourceGroups)
   - Select your Resource Group from above (like `core67`).
   - If you don't see a new Resource, then look at "Deployments" and see if any have failed.
   - Here's what you typically see for failures:
     
```json
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug-deployment for usage details.","details":[{"code":"BadRequest","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidTemplate\",\r\n \"message\": \"Unable to process template language expressions for resource '/subscriptions/45d8a30e-3363-4e0e-849a-4bb0bbf71a7b/resourceGroups/core67/providers/Microsoft.Portal/dashboards/Bot Analytics Dashboard' at line '34' and column '9'. 'The template parameter 'virtualMachineName' is not found. Please see https://aka.ms/arm-template-structure for usage details.'\"\r\n }\r\n}"}]}
```
-->

<!--
## Additional information

### Customize your telemetry client

If you want to customize your telemetry to log into a separate service, you have to configure the system differently. If using Application Insights to do so, download the package `Microsoft.Bot.Builder.ApplicationInsights` via NuGet, or use npm to install `botbuilder-applicationinsights`. Details on getting the Application Insights keys can be found [here](../bot-service-resources-app-insights-keys.md). Otherwise, include what is necessary for logging to that service, then follow the section below.

**Use Custom Telemetry**
If you want to log telemetry events generated by the Bot Framework into a completely separate system, create a new class derived from the base interface `IBotTelemetryClient` and configure. Then, when adding your telemetry client as above, just inject your custom client. 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry client.
    services.AddSingleton<IBotTelemetryClient, CustomTelemetryClient>();
    ...
}
```

### Add custom logging to your bot

Once the Bot has the new telemetry logging support configured, you can begin adding telemetry to your bot.  The `BotTelemetryClient`(in C#, `IBotTelemetryClient`) has several methods to log distinct types of events.  Choosing the appropriate type of event enables you to take advantage of Application Insights existing reports (if you are using Application Insights).  For general scenarios `TraceEvent` is typically used.  The data logged using `TraceEvent` lands in the `CustomEvent` table in Kusto.

If using a Dialog within your Bot, every Dialog-based object (including Prompts) will contain a new `TelemetryClient` property.  This is the `BotTelemetryClient` that enables you to perform logging.  This is not just a convenience, we'll see later in this article if this property is set, `WaterfallDialogs` will generate events.

### Details of telemetry options

There are three main components available for your bot to log telemetry, and each component has customization available for logging your own events, which are discussed in this section. 

- A  [Bot Framework Middleware component](#telemetry-middleware) (*TelemetryLoggerMiddleware*) that will log when messages are received, sent, updated or deleted. You can override for custom logging.
- [*LuisRecognizer* class.](#telemetry-support-luis)  You can override for custom logging in two ways - per invocation (add/replace properties) or derived classes.
- [*QnAMaker*  class.](#telemetry-qnamaker)  You can override for custom logging in two ways - per invocation (add/replace properties) or derived classes.

All components log using the `IBotTelemetryClient`  (or `BotTelemetryClient` in node.js) interface which can be overridden with a custom implementation.

#### Telemetry Middleware

|C#  | JavaScript |
|:-----|:------------|
|**Microsoft.Bot.Builder.TelemetryLoggerMiddleware** | **botbuilder-core** |

##### Out of box usage

The TelemetryLoggerMiddleware is a Bot Framework component that can be added without modification, and it will perform logging that enables out of the box reports that ship with the Bot Framework SDK. 

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, TelemetryLoggerMiddleware>();

// Create the Bot Framework Adapter with error handling enabled.
services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();
```

And in our adapter, we would specify the use of middleware:

```csharp
public AdapterWithErrorHandler(ICredentialProvider credentialProvider, ILogger<BotFrameworkHttpAdapter> logger, IMiddleware middleware, ConversationState conversationState = null)
           : base(credentialProvider)
{
    ...
    Use(middleware);
    ...
}
```

##### Adding properties
If you decide to add additional properties, the TelemetryLoggerMiddleware class can be derived.  For example, if you would like to add the property "MyImportantProperty" to the `BotMessageReceived` event.  `BotMessageReceived` is logged when the user sends a message to the bot.  Adding the additional property can be accomplished in the following way:

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    protected override Task OnReceiveActivityAsync(
                  Activity activity,
                  CancellationToken cancellation)
    {
        // Fill in the "standard" properties for BotMessageReceived
        // and add our own property.
        var properties = FillReceiveEventProperties(activity, 
                    new Dictionary<string, string>
                    { {"MyImportantProperty", "myImportantValue" } } );
                    
        // Use TelemetryClient to log event
        TelemetryClient.TrackEvent(
                        TelemetryLoggerConstants.BotMsgReceiveEvent,
                        properties);
    }
    ...
}
```

In Startup, we would add the new class:

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, MyTelemetryMiddleware>();
```

##### Completely replacing properties / Additional event(s)

If you decide to completely replace properties being logged, the `TelemetryLoggerMiddleware` class can be derived (like above when extending properties).   Similarly, logging new events is performed in the same way.

For example, if you would like to completely replace the`BotMessageSend` properties and send multiple events, the following demonstrates how this could be performed:

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    protected override Task OnReceiveActivityAsync(
                  Activity activity,
                  CancellationToken cancellation)
    {
        // Override properties for BotMsgSendEvent
        var botMsgSendProperties = new Dictionary<string, string>();
        properties.Add("MyImportantProperty", "myImportantValue");
        // Log event
        TelemetryClient.TrackEvent(
                        TelemetryLoggerConstants.BotMsgSendEvent,
                        botMsgSendProperties);
                        
        // Create second event.
        var secondEventProperties = new Dictionary<string, string>();
        secondEventProperties.Add("activityId",
                                   activity.Id);
        secondEventProperties.Add("MyImportantProperty",
                                   "myImportantValue");
        TelemetryClient.TrackEvent(
                        "MySecondEvent",
                        secondEventProperties);
    }
    ...
}
```
Note: When the standard properties are not logged, it will cause the out of box reports shipped with the product to stop working.

##### Events Logged from Telemetry Middleware
[BotMessageSend](bot-builder-telemetry-reference.md#customevent-botmessagesend)
[BotMessageReceived](bot-builder-telemetry-reference.md#customevent-botmessagereceived)
[BotMessageUpdate](bot-builder-telemetry-reference.md#customevent-botmessageupdate)
[BotMessageDelete](bot-builder-telemetry-reference.md#customevent-botmessagedelete)

#### Telemetry support LUIS 

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.Luis** | **botbuilder-ai** |

##### Out of box usage
The LuisRecognizer is an existing Bot Framework component, and telemetry can be enabled by passing a IBotTelemetryClient interface through `luisOptions`.  You can override the default properties being logged and log new events as required.

During construction of `luisOptions`, an `IBotTelemetryClient` object must be provided for this to work.

```csharp
var luisPredictionOptions = new LuisPredictionOptions()
{
    TelemetryClient = telemetryClient
};
var recognizer = new LuisRecognizer(luisApplication, luisPredictionOptions);
```

##### Adding properties
If you decide to add additional properties, the `LuisRecognizer` class can be derived.  For example, if you would like to add the property "MyImportantProperty" to the `LuisResult` event.  `LuisResult` is logged when a LUIS prediction call is performed.  Adding the additional property can be accomplished in the following way:

```csharp
class MyLuisRecognizer : LuisRecognizer 
{
   ...
   protected override Task OnRecognizerResultAsync(
           RecognizerResult recognizerResult,
           ITurnContext turnContext,
           Dictionary<string, string> properties = null,
           CancellationToken cancellationToken = default(CancellationToken))
   {
       var luisEventProperties = FillLuisEventProperties(result, 
               new Dictionary<string, string>
               { {"MyImportantProperty", "myImportantValue" } } );
        
        TelemetryClient.TrackEvent(
                        LuisTelemetryConstants.LuisResultEvent,
                        luisEventProperties);
        ..
   }    
   ...
}
```

##### Add properties per invocation
Sometimes it's necessary to add additional properties during the invocation:
```csharp
var additionalProperties = new Dictionary<string, string>
{
   { "dialogId", "myDialogId" },
   { "conversationInfo", "myConversationInfo" },
};

var result = await recognizer.RecognizeAsync(turnContext,
     additionalProperties).ConfigureAwait(false);
```

##### Completely replacing properties / Additional event(s)
If you decide to completely replace properties being logged, the `LuisRecognizer` class can be derived (like above when extending properties).   Similarly, logging new events is performed in the same way.

For example, if you would like to completely replace the`LuisResult` properties and send multiple events, the following demonstrates how this could be performed:

```csharp
class MyLuisRecognizer : LuisRecognizer
{
    ...
    protected override Task OnRecognizerResultAsync(
             RecognizerResult recognizerResult,
             ITurnContext turnContext,
             Dictionary<string, string> properties = null,
             CancellationToken cancellationToken = default(CancellationToken))
    {
        // Override properties for LuisResult event
        var luisProperties = new Dictionary<string, string>();
        properties.Add("MyImportantProperty", "myImportantValue");
        
        // Log event
        TelemetryClient.TrackEvent(
                        LuisTelemetryConstants.LuisResult,
                        luisProperties);
                        
        // Create second event.
        var secondEventProperties = new Dictionary<string, string>();
        secondEventProperties.Add("MyImportantProperty2",
                                   "myImportantValue2");
        TelemetryClient.TrackEvent(
                        "MySecondEvent",
                        secondEventProperties);
        ...
    }
    ...
}
```
Note: When the standard properties are not logged, it will cause the Application Insights out of box reports shipped with the product to stop working.

##### Events Logged from TelemetryLuisRecognizer
[LuisResult](bot-builder-telemetry-reference.md#customevent-luisevent)

### Telemetry QnAMaker

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.QnA** | **botbuilder-ai** |


##### Out of box usage
The QnAMaker class is an existing Bot Framework component that adds two additional constructor parameters which enable logging that enable out of the box reports that ship with the Bot Framework SDK. The new `telemetryClient` references a `IBotTelemetryClient` interface which performs the logging.  

```csharp
var qna = new QnAMaker(endpoint, options, client, 
                       telemetryClient: telemetryClient,
                       logPersonalInformation: true);
```
##### Adding properties 
If you decide to add additional properties, there are two methods of doing this - when properties need to be added during the QnA call to retrieve answers or deriving from the `QnAMaker` class.  

The following demonstrates deriving from the `QnAMaker` class.  The example shows adding the property "MyImportantProperty" to the `QnAMessage` event.  The`QnAMessage` event is logged when a QnA `GetAnswers`call is performed.  In addition, we log a second event "MySecondEvent".

```csharp
class MyQnAMaker : QnAMaker 
{
   ...
   protected override Task OnQnaResultsAsync(
                 QueryResult[] queryResults, 
                 ITurnContext turnContext, 
                 Dictionary<string, string> telemetryProperties = null, 
                 Dictionary<string, double> telemetryMetrics = null, 
                 CancellationToken cancellationToken = default(CancellationToken))
   {
            var eventData = await FillQnAEventAsync(queryResults, turnContext, telemetryProperties, telemetryMetrics, cancellationToken).ConfigureAwait(false);

            // Add my property
            eventData.Properties.Add("MyImportantProperty", "myImportantValue");

            // Log QnaMessage event
            TelemetryClient.TrackEvent(
                            QnATelemetryConstants.QnaMsgEvent,
                            eventData.Properties,
                            eventData.Metrics
                            );

            // Create second event.
            var secondEventProperties = new Dictionary<string, string>();
            secondEventProperties.Add("MyImportantProperty2",
                                       "myImportantValue2");
            TelemetryClient.TrackEvent(
                            "MySecondEvent",
                            secondEventProperties);       }    
    ...
}
```

##### Adding properties during GetAnswersAsync
If you have properties that need to be added during runtime, the `GetAnswersAsync` method can provide properties and/or metrics to add to the event.

For example, if you want to add a `dialogId` to the event, it can be done like the following:
```csharp
var telemetryProperties = new Dictionary<string, string>
{
   { "dialogId", myDialogId },
};

var results = await qna.GetAnswersAsync(context, opts, telemetryProperties);
```
The `QnaMaker` class provides the capability of overriding properties, including PersonalInfomation properties.

##### Completely replacing properties / Additional event(s)
If you decide to completely replace properties being logged, the `TelemetryQnAMaker` class can be derived (like above when extending properties).   Similarly, logging new events is performed in the same way.

For example, if you would like to completely replace the`QnAMessage` properties, the following demonstrates how this could be performed:

```csharp
class MyQnAMaker : QnAMaker
{
    ...
    protected override Task OnQnaResultsAsync(
         QueryResult[] queryResults, 
         ITurnContext turnContext, 
         Dictionary<string, string> telemetryProperties = null, 
         Dictionary<string, double> telemetryMetrics = null, 
         CancellationToken cancellationToken = default(CancellationToken))
    {
        // Add properties from GetAnswersAsync
        var properties = telemetryProperties ?? new Dictionary<string, string>();
        // GetAnswerAsync properties overrides - don't add if already present.
        properties.TryAdd("MyImportantProperty", "myImportantValue");

        // Log event
        TelemetryClient.TrackEvent(
                           QnATelemetryConstants.QnaMsgEvent,
                            properties);
    }
    ...
}
```
Note: When the standard properties are not logged, it will cause the out of box reports shipped with the product to stop working.

##### Events Logged from TelemetryLuisRecognizer
[QnAMessage](bot-builder-telemetry-reference.md#customevent-qnamessage)

### All other events

A full list of events logged for your bot's telemetry can be found on the [telemetry reference page](bot-builder-telemetry-reference.md).

#### Identifiers and Custom Events

When logging events into Application Insights, the events generated contain default properties that you won't have to fill.  For example, `user_id` and `session_id`properties are contained in each Custom Event (generated with the `TraceEvent` API).  In addition, `activitiId`, `activityType` and `channelId` are also added.

> [!NOTE]
> Custom telemetry clients will not be provided these values.


Property |Type | Details
--- | --- | ---
`user_id`| `string` | [ChannelID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id) + [From.Id](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from)
`session_id`| `string`|  [ConversationID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation)
`customDimensions.activityId`| `string` | [The bot activity ID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#id)
`customDimensions.activityType` | `string` | [The bot activity type](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)
`customDimensions.channelId` | `string` |  [The bot activity channel ID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)
-->
