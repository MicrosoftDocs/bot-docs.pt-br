---
title: Eventos gerados pela telemetria do Bot Framework Service – Serviço de Bot
description: Saiba mais sobre os eventos que o serviço de canal do bot Framework registra quando os bots recebem mensagens, geram erros ou experimentam outras atividades.
keywords: telemetria, appinsights, bot de monitor
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d472d2b127f795ac3eae2db4fbaf110f0aede050
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92413785"
---
# <a name="events-generated-by-the-bot-framework-service-telemetry"></a>Eventos gerados pela telemetria do Bot Framework Service

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

## <a name="channel-service-events"></a>Eventos do serviço de canal

Além do `WaterfallDialog`, que discutimos no [tópico sobre telemetria](bot-builder-telemetry.md) e que gera eventos com base em seu código de bot, o serviço de Canal do Bot Framework também registra os eventos.  Isso ajuda você a diagnosticar problemas com os Canais ou falhas gerais do bot.

### <a name="customevent-activity"></a>CustomEvent: "Activity"
**Registrado em log em:** Serviço de canal registrado pelo serviço de canal quando uma mensagem é recebida.

### <a name="exception-bot-errors"></a>Exceção: "Bot Errors"
**Registrado em log em:** Serviço de Canal Registrado pelo canal quando uma chamada ao bot retorna uma resposta não 2XX do Http.

## <a name="customevent-waterfallstart"></a>CustomEvent: "WaterfallStart" 

Quando um WaterfallDialog é iniciado, um evento `WaterfallStart` é registrado.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

## <a name="customevent-waterfallstep"></a>CustomEvent: "WaterfallStep" 

Registra etapas individuais de um diálogo em cascata.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.StepName` (o nome do método ou `StepXofY`, se lambda)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

## <a name="customevent-waterfalldialogcomplete"></a>CustomEvent: "WaterfallDialogComplete"

Registra quando um diálogo em cascata é concluído.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

## <a name="customevent-waterfalldialogcancel"></a>CustomEvent: "WaterfallDialogCancel" 

Registra quando um diálogo em cascata é cancelado.

- `user_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `session_id` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Esse é o dialogId (cadeia de caracteres) passado para a sua cascata.  Você pode considerá-lo como o “tipo cascata”)
- `customDimensions.StepName` (o nome do método ou `StepXofY`, se lambda)
- `customDimensions.InstanceID` (exclusivo por instância do diálogo)

## <a name="customevent-botmessagereceived"></a>CustomEvent: BotMessageReceived 
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

## <a name="customevent-botmessagesend"></a>CustomEvent: BotMessageSend 
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


## <a name="customevent-botmessageupdate"></a>CustomEvent: BotMessageUpdate
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


## <a name="customevent-botmessagedelete"></a>CustomEvent: BotMessageDelete
**Registrado em log em:** TelemetryLoggerMiddleware Registrado quando uma mensagem é excluída pelo bot (caso raro)
- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- SessionID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- RecipientId
- ConversationId
- ConversationName

## <a name="customevent-luisevent"></a>CustomEvent: LuisEvent
**Registrado em log em:** LuisRecognizer

Registra resultados do serviço LUIS.

- UserID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- SessionID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityID ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- Channel ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ActivityType ([Do Inicializador de telemetria](https://aka.ms/telemetry-initializer))
- ApplicationId
- Intencional
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

