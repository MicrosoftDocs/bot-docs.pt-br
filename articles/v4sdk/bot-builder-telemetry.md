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
ms.date: 02/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 75e12ab44915783c33c3b2ee10775cc6f00487bb
ms.sourcegitcommit: aea57820b8a137047d59491b45320cf268043861
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59905029"
---
# <a name="add-telemetry-to-your-bot"></a>Adicionar telemetria ao seu bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

Na versão 4.2 do SDK do Bot Framework, o registro em log de telemetria foi adicionado ao produto.  Isso permite que os aplicativos do bot enviem dados de evento para os serviços, como o Application Insights.

Este documento aborda como integrar seu bot aos novos recursos de telemetria.  

## <a name="using-bot-configuration-option-1-of-2"></a>Como usar a configuração de bot (opção 1 de 2)
Há dois métodos para configurar seu bot.  O primeiro assume que você está se integrando ao Application Insights.

O arquivo de configuração do bot contém metadados sobre serviços externos que o bot usa durante a execução.  Por exemplo, as conexões de serviço e os metadados do CosmosDB, Application Insights e LUIS (Serviço Inteligente de Reconhecimento Vocal) são armazenados aqui.   

Se você quiser “armazenar” o Application Insights sem nenhuma configuração adicional específica do Application Insights necessária (por exemplo, Inicializadores de telemetria), passe o objeto de configuração de bot durante a inicialização.   Esse é o método mais fácil de inicialização e configurará o Application Insights para começar a acompanhar solicitações, chamadas externas a outros serviços e eventos correlacionados entre serviços.

```csharp
// ASP.Net Core - Startup.cs

public void ConfigureServices(IServiceCollection services)
{
     ...
     // Add Application Insights - pass in the bot configuration
     services.AddBotApplicationInsights(botConfig);
     ...
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
     app.UseBotApplicationInsights()
                 ...
                .UseDefaultFiles()
                .UseStaticFiles()
                .UseBotFramework();
                ...
}
```

```JavaScript
const appInsightsClient = new ApplicationInsightsTelemetryClient(botConfig);
```

**Nenhum Application Insights na configuração do bot** E se a configuração do seu bot não contiver o Application Insights?  Sem problemas, ela padroniza a um cliente nulo que deixa as chamadas de método inoperantes.

**Vários Application Insights** Há várias seções do Application Insight na sua configuração de bot?  Você pode designar qual instância do serviço do Application Insights deseja usar na configuração do bot.

```csharp
// ASP.Net Core - Startup.cs

public void ConfigureServices(IServiceCollection services)
{
     // Add Application Insights
     services.AddBotApplicationInsights(botConfig, "myAppInsights");
     ...
}
```

```JavaScript
const appInsightsClient = new ApplicationInsightsTelemetryClient(botConfig);
```

## <a name="overriding-the-telemetry-client-option-2-of-2"></a>Como substituir o cliente de telemetria (opção 2 de 2)

Caso deseje personalizar o cliente do Application Insights ou fazer logon em um serviço completamente separado, você precisa configurar o sistema de modo diferente.

**Modificar a configuração do Application Insights**

```csharp

public void ConfigureServices(IServiceCollection services)
{
     ...
     // Create Application Insight Telemetry Client
     // with custom configuration.
     var telemetryClient = TelemetryClient(myCustomConfiguration)
     
     // Add Application Insights
     services.AddBotApplicationInsights(new BotTelemetryClient(telemetryClient), "InstrumentationKey");
```

```JavaScript
const appInsightsClient = new ApplicationInsightsTelemetryClient(botConfig);
```

**Usar a telemetria personalizada** Se você quiser registrar eventos de telemetria gerados pelo Bot Framework em um sistema completamente separado, crie uma nova classe derivada da interface base e a configure.  

```csharp
public void ConfigureServices(IServiceCollection services)
{
     ...
     // Create my IBotTelemetryClient-based logger
     var myTelemetryClient = MyTelemetryLogger();
     
     // Add Application Insights
     services.AddBotApplicationInsights(myTelemetryClient);
     ...
}
```

```JavaScript
const appInsightsClient = new ApplicationInsightsTelemetryClient(botConfig);
```

## <a name="add-custom-logging-to-your-bot"></a>Adicionar registro em log personalizado ao seu bot

Assim que o suporte do novo registro em log do bot for configurado, você poderá começar a adicionar telemetria ao seu bot.  O `BotTelemetryClient`(em C#, `IBotTelemetryClient`) tem vários métodos para registrar diferentes tipos de log de eventos.  Escolher o tipo apropriado de eventos permite que você aproveite os relatórios existentes do Application Insights (se você estiver usando-o).  Para cenários gerais, normalmente é usado o `TraceEvent`.  Os dados registrados usando o `TraceEvent` ficam na tabela `CustomEvent` no Kusto.

Caso você use um diálogo dentro do Bot, cada objeto baseado em diálogo (incluindo Prompts) conterá uma nova propriedade `TelemetryClient`.  Esse é o `BotTelemetryClient` que permite que você execute o registro em log.  Não se trata apenas de uma conveniência; posteriormente, será visto nesse artigo que se essa propriedade for definida, `WaterfallDialogs` gerará eventos.

### <a name="identifiers-and-custom-events"></a>Eventos personalizados e identificadores

Ao registrar logs de eventos no Application Insights, os eventos gerados contêm propriedades padrão que você não precisará preencher.  Por exemplo, as propriedades `user_id` e `session_id` ficam contidas em cada Evento personalizado (gerado com a API `TraceEvent`).  Além disso, também são adicionados `activitiId`, `activityType` e `channelId`.

>Observação: Esses valores não serão fornecidos aos clientes de telemetria personalizada.

Propriedade |Type | Detalhes
--- | --- | ---
`user_id`| `string` | [ChannelID](https://github.com/Microsoft/botframework-obi/blob/master/botframework-activity/botframework-activity.md#channel-id) + [From.Id](https://github.com/Microsoft/botframework-obi/blob/master/botframework-activity/botframework-activity.md#from)
`session_id`| `string`|  [ConversationID](https://github.com/Microsoft/botframework-obi/blob/master/botframework-activity/botframework-activity.md#conversation)
`customDimensions.activityId`| `string` | [A ID de atividade do bot](https://github.com/Microsoft/botframework-obi/blob/master/botframework-activity/botframework-activity.md#id)
`customDimensions.activityType` | `string` | [O tipo de atividade do bot](https://github.com/Microsoft/botframework-obi/blob/master/botframework-activity/botframework-activity.md#channel-id)
`customDimensions.channelId` | `string` |  [A ID do canal de atividade do bot ](https://github.com/Microsoft/botframework-obi/blob/master/botframework-activity/botframework-activity.md#channel-id)

## <a name="waterfalldialog-events"></a>Eventos WaterfallDialog

Além disso, o objeto `WaterfallDialog` dentro do SDK agora pode gerar eventos. A seção a seguir descreve os eventos gerados no Bot Framework. Ao definir a propriedade `TelemetryClient` no `WaterfallDialog`, esses eventos serão armazenados.

Veja um exemplo de como modificar uma amostra (BasicBot) que usa o `WaterfallDialog` para registrar eventos de telemetria.  O BasicBot utiliza um padrão comum usado onde um `WaterfallDialog` é colocado dentro de um `ComponentDialog` (`GreetingDialog`).

```csharp
// IBotTelemetryClient is direct injected into our Bot
public BasicBot(BotServices services, UserState userState, ConversationState conversationState, IBotTelemetryClient telemetryClient)
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


## <a name="events-generated-by-the-bot-framework-service"></a>Eventos gerados pelo serviço de Bot Framework

Além do `WaterfallDialog`, que gera eventos a partir de seu código de bot, o serviço do canal do Bot Framework também registra os eventos.  Isso ajuda você a diagnosticar problemas com os Canais ou falhas gerais do bot.

### <a name="customevent-activity"></a>CustomEvent: "Activity"
**Registrado em log em:** Serviço de canal registrado pelo serviço de canal quando uma mensagem é recebida.

### <a name="exception-bot-errors"></a>Exceção: "Bot Errors"
**Registrado em log em:** Serviço de Canal Registrado pelo canal quando uma chamada ao bot retorna uma resposta não 2XX do Http.

## <a name="additional-events"></a>Eventos adicionais

O [modelo do Enterprise](https://github.com/Microsoft/AI/tree/master/templates/Enterprise-Template) é um código de software livre para cópia.  Ele contém vários componentes que podem ser reutilizados e modificados para atender às suas necessidades de relatório.

### <a name="customevent-botmessagereceived"></a>CustomEvent: BotMessageReceived 
**Registrado em log em:** TelemetryLoggerMiddleware (**amostra do Enterprise**)

Registrado em log quando o bot recebe uma nova mensagem.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ConversationID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Texto (opcional para PII)
- FromId
- FromName
- RecipientId
- RecipientName
- ConversationId
- ConversationName
- Local

### <a name="customevent-botmessagesend"></a>CustomEvent: BotMessageSend 
**Registrado em log em:** TelemetryLoggerMiddleware (**amostra do Enterprise**)

Registrado quando o bot envia uma mensagem.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ConversationID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ReplyToID
- Channel (canal de origem; por exemplo, as equipes do Skype, Cortana e Teams)
- RecipientId
- ConversationName
- Local
- Texto (opcional para PII)
- RecipientName (opcional para PII)

### <a name="customevent-botmessageupdate"></a>CustomEvent: BotMessageUpdate
**Registrado em log em:** TelemetryLoggerMiddleware Registrado quando uma mensagem é atualizada pelo bot (caso raro)

### <a name="customevent-botmessagedelete"></a>CustomEvent: BotMessageDelete
**Registrado em log em:** TelemetryLoggerMiddleware Registrado quando uma mensagem é excluída pelo bot (caso raro)

### <a name="customevent-luisintentinentname"></a>CustomEvent: LuisIntent.INENTName 
**Registrado em log em:** TelemetryLuisRecognizer (**amostra do Enterprise**)

Registra resultados do serviço LUIS.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ConversationID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Intenção
- IntentScore
- Pergunta
- ConversationId
- SentimentLabel
- SentimentScore
- *Entidades do LUIS*
- **NOVO** DialogId

### <a name="customevent-qnamessage"></a>CustomEvent: QnAMessage
**Registrado em log em:** TelemetryQnaMaker (**amostra do Enterprise**)

Registra os resultados do serviço QnA Maker.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ConversationID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Nome de Usuário
- ConversationId
- OriginalQuestion
- Pergunta
- Resposta
- Pontuação (*opcional*: se conhecimento for encontrado)

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

