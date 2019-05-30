---
title: Adicionar telemetria ao seu bot | Microsoft Docs
description: Saiba como integrar seu bot aos novos recursos de telemetria.
keywords: telemetria, appinsights, bot de monitor
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b6b4d30aea493180fddaee4a7f74bef72c1992ae
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215271"
---
# <a name="add-telemetry-to-your-bot"></a>Adicionar telemetria ao seu bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

Na versão 4.2 do SDK do Bot Framework, o registro em log de telemetria foi adicionado ao produto.  Isso permite que os aplicativos do bot enviem dados de evento para os serviços, como o Application Insights. A primeira seção aborda esses métodos, com recursos de telemetria mais amplos depois deles.

Este documento aborda como integrar seu bot aos novos recursos de telemetria. 

## <a name="basic-telemetry-options"></a>Opções básicas de telemetria

### <a name="basic-application-insights"></a>Application Insights básico

Primeiro vamos adicionar telemetria básica ao bot usando o Application Insights. Para obter outras informações sobre configuração, confira as primeiras seções de [Introdução ao Application Insights](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started-with-Application-Insights-for-ASP.NET-Core).   

Se você quiser “armazenar” o Application Insights sem nenhuma outra configuração necessária e específica do Application Insights (por exemplo, Inicializadores de telemetria), adicione o seguinte ao método `ConfigureServices()`.   Esse é o método mais fácil de inicialização e configurará o Application Insights para começar a acompanhar solicitações, chamadas externas a outros serviços e eventos correlacionados entre serviços.

Você precisará adicionar os pacotes NuGet incluídos no snippet a seguir.

**Startup.cs**
```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Bot.Builder.ApplicationInsights;
using Microsoft.Bot.Builder.Integration.ApplicationInsights.Core;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
 
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add Application Insights services into service collection
    services.AddApplicationInsightsTelemetry();

    // Add the standard telemetry client
    services.AddSingleton<IBotTelemetryClient, BotTelemetryClient>();

    // Add ASP middleware to store the HTTP body, mapped with bot activity key, in the httpcontext.items
    // This will be picked by the TelemetryBotIdInitializer
    services.AddTransient<TelemetrySaveBodyASPMiddleware>();

    // Add telemetry initializer that will set the correlation context for all telemetry items
    services.AddSingleton<ITelemetryInitializer, OperationCorrelationTelemetryInitializer>();

    // Add telemetry initializer that sets the user ID and session ID (in addition to other 
    // bot-specific properties, such as activity ID)
    services.AddSingleton<ITelemetryInitializer, TelemetryBotIdInitializer>();
    ...
}

// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    ...
    app.UseBotApplicationInsights();
}
```

Em seguida, precisará armazenar sua chave de instrumentação do Application Insights no arquivo `appsettings.json` ou como uma variável de ambiente. O arquivo `appsettings.json` contém metadados sobre serviços externos que o bot usa durante a execução.  Por exemplo, as conexões de serviço e os metadados do CosmosDB, Application Insights e LUIS (Serviço Inteligente de Reconhecimento Vocal) são armazenados aqui. A chave de instrumentação pode ser encontrada no portal do Azure, na seção **Visão geral** (no menu suspenso `Essentials` do seu serviço nessa página, se ele estiver recolhido). Detalhes sobre como obter as chaves podem ser encontrados [aqui](~/bot-service-resources-app-insights-keys.md).

A estrutura encontrará a chave para você, se estiver formatada corretamente. A formatação de suas entradas de `appsettings.json` deve ser assim:

```json
    "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
    },
    "Logging": {
        "LogLevel": {
            "Default": "Warning"
        }
    }
```

Para obter mais informações sobre como adicionar o Application Insights a um aplicativo do ASP.NET Core, confira [este artigo](https://docs.microsoft.com/en-us/azure/azure-monitor/app/asp-net-core-no-visualstudio). 

### <a name="customize-your-telemetry-client"></a>Personalizar o cliente de telemetria

Caso deseje personalizar o cliente do Application Insights ou fazer logon em um serviço completamente separado, você precisa configurar o sistema de modo diferente. Baixe o pacote `Microsoft.Bot.Builder.ApplicationInsights` via nuget ou use o npm para instalar o `botbuilder-applicationinsights`. Detalhes sobre como obter as chaves do Application Insights podem ser encontrados [aqui](~/bot-service-resources-app-insights-keys.md).

**Modificar a configuração do Application Insights**

Para modificar sua configuração, inclua `options` ao adicionar o Application Insights. Caso contrário, tudo ficará igual ao descrito acima.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add Application Insights services into service collection
    services.AddApplicationInsightsTelemetry(options);
    ...
}
```

O objeto `options` é do tipo `ApplicationInsightsServiceOptions`. Detalhes sobre quais são as opções [podem ser encontrados aqui]().

**Usar a telemetria personalizada** Se você quiser registrar eventos de telemetria gerados pelo Bot Framework em um sistema completamente separado, crie uma nova classe derivada da interface base `IBotTelemetryClient` e configure. Em seguida, ao adicionar o cliente de telemetria conforme acima, simplesmente injete o cliente personalizado. 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry client.
    services.AddSingleton<IBotTelemetryClient, CustomTelemetryClient>();
    ...
}
```

### <a name="add-custom-logging-to-your-bot"></a>Adicionar registro em log personalizado ao seu bot

Assim que o suporte do novo registro em log do bot for configurado, você poderá começar a adicionar telemetria ao seu bot.  O `BotTelemetryClient`(em C#, `IBotTelemetryClient`) tem vários métodos para registrar diferentes tipos de log de eventos.  Escolher o tipo apropriado de eventos permite que você aproveite os relatórios existentes do Application Insights (se você estiver usando-o).  Para cenários gerais, normalmente é usado o `TraceEvent`.  Os dados registrados usando o `TraceEvent` ficam na tabela `CustomEvent` no Kusto.

Caso você use um diálogo dentro do Bot, cada objeto baseado em diálogo (incluindo Prompts) conterá uma nova propriedade `TelemetryClient`.  Esse é o `BotTelemetryClient` que permite que você execute o registro em log.  Não se trata apenas de uma conveniência; posteriormente, será visto nesse artigo que se essa propriedade for definida, `WaterfallDialogs` gerará eventos.

#### <a name="identifiers-and-custom-events"></a>Eventos personalizados e identificadores

Ao registrar logs de eventos no Application Insights, os eventos gerados contêm propriedades padrão que você não precisará preencher.  Por exemplo, as propriedades `user_id` e `session_id` ficam contidas em cada Evento personalizado (gerado com a API `TraceEvent`).  Além disso, também são adicionados `activitiId`, `activityType` e `channelId`.

>Observação: Esses valores não serão fornecidos aos clientes de telemetria personalizada.

Propriedade |Type | Detalhes
--- | --- | ---
`user_id`| `string` | [ChannelID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id) + [From.Id](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from)
`session_id`| `string`|  [ConversationID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation)
`customDimensions.activityId`| `string` | [A ID de atividade do bot](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#id)
`customDimensions.activityType` | `string` | [O tipo de atividade do bot](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)
`customDimensions.channelId` | `string` |  [A ID do canal de atividade do bot ](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)

## <a name="in-depth-telemetry"></a>Telemetria detalhada

Foram adicionados três novos componentes ao SDK na versão 4.4.  Todos os componentes registram em log usando a interface `IBotTelemetryClient` (ou `BotTelemetryClient` no Node.js), que pode ser substituída por uma implementação personalizada.

- Um componente middleware do Bot Framework (*TelemetryLoggerMiddleware*) que registrará em log quando as mensagens são recebidas, enviadas, atualizadas ou excluídas. Você pode substituir por registro em log personalizado.
- Classe *LuisRecognizer*.  Você pode substituir pelo registro em log personalizado de duas maneiras: por invocação (adicionar/substituir propriedades) ou por classes derivadas.
- Classe *QnA Maker*.  Você pode substituir pelo registro em log personalizado de duas maneiras: por invocação (adicionar/substituir propriedades) ou por classes derivadas.

### <a name="telemetry-middleware"></a>Middleware de telemetria

|C#  | JavaScript |
|:-----|:------------|
|**Microsoft.Bot.Builder.TelemetryLoggerMiddleware** | **botbuilder-core** |

#### <a name="out-of-box-usage"></a>Uso imediato

O TelemetryLoggerMiddleware é um componente do Bot Framework que pode ser adicionado sem modificação, e que fará o registro em log que permite relatórios imediatos fornecidos com o SDK do Bot Framework. 

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, TelemetryLoggerMiddleware>();

// Create the Bot Framework Adapter with error handling enabled.
services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();
```

#### <a name="adding-properties"></a>Adicionando propriedades
Caso opte por adicionar propriedades, a classe TelemetryLoggerMiddleware pode ser derivada.  Por exemplo, caso deseje adicionar a propriedade “MyImportantProperty” ao evento `BotMessageReceived`.  `BotMessageReceived` é registrada em log quando o usuário envia uma mensagem ao bot.  A adição de outra propriedade pode ser feita da seguinte maneira:

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    public Task OnReceiveActivityAsync(
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

E, na Inicialização, adicionaríamos a nova classe:

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, MyTelemetryMiddleware>();
```

#### <a name="completely-replacing-properties--additional-events"></a>Substituição completa de propriedades/eventos adicionais

Caso opte por substituir completamente as propriedades sendo registradas em log, a classe `TelemetryLoggerMiddleware` pode ser derivada (como acima, ao estender propriedades).   O registro de log de novos eventos é realizado da mesma maneira.

Por exemplo, caso você deseje substituir completamente as propriedades da `BotMessageSend` e enviar vários eventos, a seguir é demonstrado como isso poderia ser feito:

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    public Task<RecognizerResult> OnLuisRecognizeAsync(
                  Activity activity,
                  string dialogId = null,
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
Observação: quando as propriedades padrão não estiverem registradas em log, isso fará com que os relatórios imediatos fornecidos com o produto parem de funcionar.

#### <a name="events-logged-from-telemetry-middleware"></a>Eventos registrados em log do Middleware de telemetria
[BotMessageSend](#customevent-botmessagesend)
[BotMessageReceived](#customevent-botmessagereceived)
[BotMessageUpdate](#customevent-botmessageupdate)
[BotMessageDelete](#customevent-botmessagedelete)

### <a name="telemetry-support-luis"></a>Suporte à telemetria LUIS 

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.Luis** | **botbuilder-ai** |

#### <a name="out-of-box-usage"></a>Uso imediato
O LuisRecognizer é um componente existente do Bot Framework, e a telemetria pode ser habilitada, passando uma interface de IBotTelemetryClient por `luisOptions`.  Você pode substituir as propriedades padrão que estão sendo registradas e registrar novos eventos de log conforme necessário.

Durante a construção de `luisOptions`, um objeto `IBotTelemetryClient` deve ser fornecido para isso funcionar.

```csharp
var luisOptions = new LuisPredictionOptions(
      ...
      telemetryClient,
      false); // Log personal information flag. Defaults to false.

var client = new LuisRecognizer(luisApp, luisOptions);
```

#### <a name="adding-properties"></a>Adicionando propriedades
Caso opte por adicionar propriedades, a classe `LuisRecognizer` pode ser derivada.  Por exemplo, caso deseje adicionar a propriedade “MyImportantProperty” ao evento `LuisResult`.  `LuisResult` é registrada quando uma chamada prevista do LUIS é executada.  A adição de outra propriedade pode ser feita da seguinte maneira:

```csharp
class MyLuisRecognizer : LuisRecognizer 
{
   ...
   override protected Task OnRecognizerResultAsync(
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

#### <a name="add-properties-per-invocation"></a>Adicionar propriedades por invocação
Às vezes, é necessário adicionar propriedades durante a invocação:
```csharp
var additionalProperties = new Dictionary<string, string>
{
   { "dialogId", "myDialogId" },
   { "conversationInfo", "myConversationInfo" },
};

var result = await recognizer.RecognizeAsync(turnContext,
     additionalProperties,
     CancellationToken.None).ConfigureAwait(false);
```

#### <a name="completely-replacing-properties--additional-events"></a>Substituição completa de propriedades/eventos adicionais
Caso opte por substituir completamente as propriedades sendo registradas em log, a classe `LuisRecognizer` pode ser derivada (como acima, ao estender propriedades).   O registro de log de novos eventos é realizado da mesma maneira.

Por exemplo, caso você deseje substituir completamente as propriedades da `LuisResult` e enviar vários eventos, a seguir é demonstrado como isso poderia ser feito:

```csharp
class MyLuisRecognizer : LuisRecognizer
{
    ...
    override protected Task OnRecognizerResultAsync(
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
Observação: quando as propriedades padrão não estiverem registradas em log, isso fará com os relatórios imediatos do Application Insights fornecidos com o produto parem de funcionar.

#### <a name="events-logged-from-telemetryluisrecognizer"></a>Eventos registrados em log do TelemetryLuisRecognizer
[LuisResult](#customevent-luisevent)

### <a name="telemetry-qna-recognizer"></a>Reconhecedor de telemetria QnA

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.QnA** | **botbuilder-ai** |


#### <a name="out-of-box-usage"></a>Uso imediato
A classe QnA Maker é um componente existente do Bot Framework que adiciona dois parâmetros de construtor, os quais geram o registro em log que habilitam os relatórios imediatos fornecidos com o SDK do Bot Framework. A nova `telemetryClient` faz referência a uma interface `IBotTelemetryClient` que executa o registro em log.  

```csharp
var qna = new QnAMaker(endpoint, options, client, 
                       telemetryClient: telemetryClient,
                       logPersonalInformation: true);
```
#### <a name="adding-properties"></a>Adicionando propriedades 
Caso você decida adicionar propriedades, há duas maneiras de fazer isso: quando as propriedades precisam ser adicionadas durante a chamada do QnA para recuperar respostas ou derivando da classe `QnAMaker`.  

A seguir, é demonstrada a derivação da classe `QnAMaker`.  O exemplo mostra a adição da propriedade "MyImportantProperty" ao evento `QnAMessage`.  O evento `QnAMessage` é registrado em log quando uma chamada `GetAnswers` do QnA é realizada.  Além disso, registramos em log um segundo evento "MySecondEvent".

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

#### <a name="adding-properties-during-getanswersasync"></a>Adição de propriedades durante o GetAnswersAsync
Caso você tenha propriedades que precisem ser adicionadas durante o tempo de execução, o método `GetAnswersAsync` pode fornecer propriedades e/ou métricas para adicionar ao evento.

Por exemplo, caso deseje adicionar uma `dialogId` ao evento, isso pode ser feito da seguinte forma:
```csharp
var telemetryProperties = new Dictionary<string, string>
{
   { "dialogId", myDialogId },
};

var results = await qna.GetAnswersAsync(context, opts, telemetryProperties);
```
A classe `QnaMaker` fornece a capacidade de substituir propriedades, incluindo propriedades PersonalInfomation.

#### <a name="completely-replacing-properties--additional-events"></a>Substituição completa de propriedades/eventos adicionais
Caso opte por substituir completamente as propriedades sendo registradas em log, a classe `TelemetryQnAMaker` pode ser derivada (como acima, ao estender propriedades).   O registro de log de novos eventos é realizado da mesma maneira.

Por exemplo, caso você deseje substituir completamente as propriedades da `QnAMessage`, a seguir é demonstrado como isso poderia ser feito:

```csharp
class MyLuisRecognizer : TelemetryQnAMaker
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
Observação: quando as propriedades padrão não estiverem registradas em log, isso fará com que os relatórios imediatos fornecidos com o produto parem de funcionar.

#### <a name="events-logged-from-telemetryluisrecognizer"></a>Eventos registrados em log do TelemetryLuisRecognizer
[QnAMessage](#customevent-qnamessage)


## <a name="waterfalldialog-events"></a>Eventos WaterfallDialog

Além disso, para gerar seus próprios eventos, o objeto `WaterfallDialog` dentro do SDK agora pode fazer isso. A seção a seguir descreve os eventos gerados no Bot Framework. Ao definir a propriedade `TelemetryClient` no `WaterfallDialog`, esses eventos serão armazenados.

Veja um exemplo de como modificar uma amostra (CoreBot) que usa o `WaterfallDialog` para registrar eventos de telemetria.  O CoreBot utiliza um padrão comum usado onde um `WaterfallDialog` é colocado dentro de um `ComponentDialog` (`GreetingDialog`).

```csharp
// IBotTelemetryClient is direct injected into our Bot
public CoreBot(BotServices services, UserState userState, ConversationState conversationState, IBotTelemetryClient telemetryClient)
...

// The IBotTelemetryClient passed to the GreetingDialog
...
Dialogs = new DialogSet(_dialogStateAccessor);
Dialogs.Add(new GreetingDialog(_greetingStateAccessor, telemetryClient));
...

// The IBotTelemetryClient to the WaterfallDialog
...
AddDialog(new WaterfallDialog(ProfileDialog, waterfallSteps) { TelemetryClient = telemetryClient });
...

```

`WaterfallDialog` começará a registrar eventos assim que tiver um `IBotTelemetryClient` configurado.

## <a name="events-generated-by-the-bot-framework-service"></a>Eventos gerados pelo serviço de Bot Framework

Além do `WaterfallDialog`, que gera eventos a partir de seu código de bot, o serviço do canal do Bot Framework também registra os eventos.  Isso ajuda você a diagnosticar problemas com os Canais ou falhas gerais do bot.

### <a name="customevent-activity"></a>CustomEvent: "Activity"
**Registrado em log em:** Serviço de canal registrado pelo serviço de canal quando uma mensagem é recebida.

### <a name="exception-bot-errors"></a>Exceção: "Bot Errors"
**Registrado em log em:** Serviço de Canal Registrado pelo canal quando uma chamada ao bot retorna uma resposta não 2XX do Http.

## <a name="all-events-generated"></a>Todos os eventos gerados

### <a name="customevent-waterfallstart"></a>CustomEvent: "WaterfallStart" 

Quando um WaterfallDialog é iniciado, um evento `WaterfallStart` é registrado.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

### <a name="customevent-waterfallstep"></a>CustomEvent: "WaterfallStep" 

Registra etapas individuais de um diálogo em cascata.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.StepName` (o nome do método ou `StepXofY`, se lambda)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

### <a name="customevent-waterfalldialogcomplete"></a>CustomEvent: "WaterfallDialogComplete"

Registra quando um diálogo em cascata é concluído.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

### <a name="customevent-waterfalldialogcancel"></a>CustomEvent: "WaterfallDialogCancel" 

Registra quando um diálogo em cascata é cancelado.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.StepName` (o nome do método ou `StepXofY`, se lambda)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

### <a name="customevent-botmessagereceived"></a>CustomEvent: BotMessageReceived 
Registrado em log quando o bot recebe uma nova mensagem de um usuário.

Quando não for substituído, esse evento é registrado em log na `Microsoft.Bot.Builder.TelemetryLoggerMiddleware` usando o método `Microsoft.Bot.Builder.IBotTelemetry.TrackEvent()`.

- Identificador de sessão  
  - Ao usar o Application Insights, isso é registrado em log da `TelemetryBotIdInitializer` como o identificador da **sessão**  (*Temeletry.Context.Session.Id*) usado no Application Insights.  
  - Corresponde à [ID de Conversa](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation), conforme definido pelo protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `session_id`.

- Identificador de usuário
  - Ao usar o Application Insights, isso é registrado em log da `TelemetryBotIdInitializer` como o identificador de **usuário**  (*Telemetry.Context.User.Id*) usado no Application Insights.  
  - O valor dessa propriedade é uma combinação das propriedades [Identificador de canal](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id) e [ID de usuário](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (concatenadas), conforme definido pelo protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `user_id`.

- ActivityID 
  - Ao usar o Application Insights, isso é registrado da `TelemetryBotIdInitializer` como uma propriedade para o evento.
  - Corresponde à [ID da Atividade](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#Id), conforme definido pelo protocolo do Bot Framework.
  - O nome da propriedade é `activityId`.

- Identificador de canal
  - Ao usar o Application Insights, isso é registrado da `TelemetryBotIdInitializer` como uma propriedade para o evento.  
  - Corresponde ao [Identificador de Canal](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#id) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `channelId`.

- activityType 
  - Ao usar o Application Insights, isso é registrado da `TelemetryBotIdInitializer` como uma propriedade para o evento.  
  - Corresponde ao [Tipo de Atividade](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#type) do protocolo do Bot Framework...
  - O nome da propriedade registrado em log é `activityType`.

- Texto
  - **Opcionalmente** registrado em log quando a `logPersonalInformation` estiver definida como `true`.
  - Corresponde ao campo [Texto de Atividade](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#text) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `text`.

- Speak

  - **Opcionalmente** registrado em log quando a `logPersonalInformation` estiver definida como `true`.
  - Corresponde ao campo [Fala de Atividade](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#speak) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `speak`.

  - 

- FromId
  - Corresponde ao campo [Do Identificador](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `fromId`.

- FromName
  - **Opcionalmente** registrado em log quando a `logPersonalInformation` estiver definida como `true`.
  - Corresponde ao campo [Do Nome](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `fromName`.

- RecipientId
  - Corresponde ao campo [Do Nome](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `fromName`.

- RecipientName
  - Corresponde ao campo [Do Nome](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `fromName`.

- ConversationId
  - Corresponde ao campo [Do Nome](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `fromName`.

- ConversationName
  - Corresponde ao campo [Do Nome](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `fromName`.

- Local
  - Corresponde ao campo [Do Nome](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) do protocolo do Bot Framework.
  - O nome da propriedade registrado em log é `fromName`.

### <a name="customevent-botmessagesend"></a>CustomEvent: BotMessageSend 
**Registrado em log em:** TelemetryLoggerMiddleware 

Registrado quando o bot envia uma mensagem.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- SessionID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ReplyToID
- RecipientId
- ConversationName
- Local
- RecipientName (opcional para PII)
- Texto (opcional para PII)
- Fala (opcional para PII)


### <a name="customevent-botmessageupdate"></a>CustomEvent: BotMessageUpdate
**Registrado em log em:** TelemetryLoggerMiddleware Registrado quando uma mensagem é atualizada pelo bot (caso raro)
- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- SessionID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- RecipientId
- ConversationId
- ConversationName
- Local
- Texto (opcional para PII)


### <a name="customevent-botmessagedelete"></a>CustomEvent: BotMessageDelete
**Registrado em log em:** TelemetryLoggerMiddleware Registrado quando uma mensagem é excluída pelo bot (caso raro)
- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- SessionID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- RecipientId
- ConversationId
- ConversationName

### <a name="customevent-luisevent"></a>CustomEvent: LuisEvent
**Registrado em log em:** LuisRecognizer

Registra resultados do serviço LUIS.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- SessionID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ApplicationId
- Intenção
- IntentScore
- Intent2 
- IntentScore2 
- FromId
- SentimentLabel
- SentimentScore
- Entidades (como json)
- Pergunta (opcional para PII)

## <a name="customevent-qnamessage"></a>CustomEvent: QnAMessage
**Registrado em log em:** QnAMaker

Registra os resultados do serviço QnA Maker.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- SessionID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Nome de usuário (opcional para PII)
- Pergunta (opcional para PII)
- MatchedQuestion
- QuestionId
- Resposta
- Pontuação
- ArticleFound

## <a name="querying-the-data"></a>Consulta dos dados
Ao usar o Application Insights, todos os dados estão correlacionados (mesmo entre serviços).  É possível ver isso ao consultar uma solicitação bem-sucedida e ver todos os eventos associados a essa solicitação.  
As consultas a seguir apresentarão as solicitações mais recentes:
```sql
requests 
| where timestamp > ago(3d) 
| where resultCode == 200
| order by timestamp desc
| project timestamp, operation_Id, appName
| limit 10
```

Na primeira consulta, selecione algumas `operation_Id` e procure mais informações:

```sql
let my_operation_id = "<OPERATION_ID>";
let union_all = () {
    union
    (traces | where operation_Id == my_operation_id),
    (customEvents | where operation_Id == my_operation_id),
    (requests | where operation_Id == my_operation_id),
    (dependencies | where operation_Id  == my_operation_id),
    (exceptions | where operation_Id == my_operation_id)
};
union_all
    | order by timestamp asc
    | project itemType, name, performanceBucket
```

Isso apresentará o detalhamento cronológico de uma única solicitação, com o bucket de duração de cada chamada.
![Chamada de exemplo](media/performance_query.png)

> Observação: O carimbo de data/hora do evento “Activity” `customEvent` está fora de ordem porque esses eventos são registrados de forma assíncrona.

## <a name="create-a-dashboard"></a>Criar um painel

A maneira mais fácil de testar é criando um painel usando a [página de implantação de modelo do portal do Azure](https://portal.azure.com/#create/Microsoft.Template).  
- Clique em [“Criar seu próprio modelo no editor”]
- Copie e cole um desses arquivos .json que são fornecidos para ajudar você a criar o painel:
  - [Painel de integridade do sistema](https://aka.ms/system-health-appinsights)
  - [Painel de integridade da conversa](https://aka.ms/conversation-health-appinsights)
- Clique em “Salvar”
- Preencha `Basics`: 
   - Assinatura: <your test subscription>
   - Grupo de recursos: <a test resource group>
   - Local: <such as West US>
- Preencha `Settings`:
   - Nome do componente do Insights: <como `core672so2hw`>
   - Grupo de recursos do componente do Insights: <como `core67`>
   - Nome do painel: <como `'ConversationHealth'` ou `SystemHealth`>
- Clique em `I agree to the terms and conditions stated above`
- Clique em `Purchase`
- Validar
   - Clique em [`Resource Groups`](https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fsubscriptions%2FresourceGroups)
   - Selecione seu grupo de recursos entre as opções acima (como `core67`).
   - Caso não veja um novo recurso, examine "Implantações" e veja se algum apresentou falhas.
   - Eis as falhas que você normalmente vê:
     
```json
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"BadRequest","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidTemplate\",\r\n \"message\": \"Unable to process template language expressions for resource '/subscriptions/45d8a30e-3363-4e0e-849a-4bb0bbf71a7b/resourceGroups/core67/providers/Microsoft.Portal/dashboards/Bot Analytics Dashboard' at line '34' and column '9'. 'The template parameter 'virtualMachineName' is not found. Please see https://aka.ms/arm-template/#parameters for usage details.'\"\r\n }\r\n}"}]}
```

Para exibir os dados, acesse o portal do Azure. À esquerda, clique em **Painel**, depois, na lista suspensa, selecione o painel que você criou.

## <a name="additional-resources"></a>Recursos adicionais
Você pode consultar esses exemplos que implementam a telemetria:
- C#
  - [LUIS com AppInsights](https://aka.ms/luis-with-appinsights-cs)
  - [QnA com AppInsights](https://aka.ms/qna-with-appinsights-cs)
- JS
  - [LUIS com AppInsights](https://aka.ms/luis-with-appinsights-js)
  - [QnA com AppInsights](https://aka.ms/qna-with-appinsights-js)

