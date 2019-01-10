---
title: Análise de conversa usando o PowerBI | Microsoft Docs
description: Saiba mais sobre como o modelo de Bot do Enterprise faz uso do Application Insights para habilitar insights por meio do Power BI
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 88208a2f5b0eb88d3b2964e63a21585484166d73
ms.sourcegitcommit: 2d84d5d290359ac3cfb8c8f977164f799666f1ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54152169"
---
# <a name="enterprise-bot-template---conversational-analytics-using-powerbi-dashboard-and-application-insights"></a>Modelo de Bot do Enterprise — análise de conversa usando o painel do PowerBI e o Application Insights

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

Depois que seu Bot for implantado e começar a processar mensagens, você verá dados telemétricos fluindo para a instância do Application Insights em seu grupo de recursos de dados telemétricos. 

Esses dados telemétricos podem ser exibidos dentro do Application Insights Blade, no portal do Microsoft Azure e usando o Log Analytics. Além disso, os mesmos dados telemétricos podem ser usados pelo Power BI para fornecer insights de negócios mais gerais sobre o uso do seu Bot.

Um painel do Power BI de exemplo é fornecido em [Telemetria de inteligência artificial de conversação](https://aka.ms/botPowerBiTemplate). 

Ele é fornecido para fins de exemplo e demonstra como você pode começar a criar seus próprios insights. Ao longo do tempo, aprimoraremos essas visualizações. 


## <a name="middleware-processing"></a>Processamento de middleware

Os wrappers de dados telemétricos das classes QnAMaker e LuisRecognizer são fornecidos para garantir consistência na saída dos dados telemétricos independentemente do cenário e habilitar um dashboard padrão para funcionar em cada projeto.

```TelemetryLuisRecognizer``` e ```TelemetryQnAMaker``` fornecem propriedades no construtor, permitindo que um desenvolvedor desabilite o registro de log de mensagens originais e nomes de usuário. No entanto, isso reduzirá a quantidade de informações disponíveis.

## <a name="telemetry-captured"></a>Dados telemétricos capturados

Quatro eventos distintos de dados telemétricos são capturados por meio do uso de ```TelemetryLuisRecognizer``` e ```TelemetryQnAMaker```, que são habilitados por padrão no modelo Enterprise. 

Cada Intenção do LUIS usada pelo seu projeto terá LuisIntent como prefixo. para habilitar a fácil identificação de intenções pelo painel.

```
-BotMessageReceived
    - ActivityId
    - Channel
    - FromId
    - FromName
    - ConversationId
    - ConversationName
    - Locale
    - Text
    - RecipientId
    - RecipientName
```
  
```
-BotMessageSent
    - ActivityId,
    - Channel
    - RecipientId
    - ConversationId
    - ConversationName
    - Locale
    - RecipientId
    - RecipientName
    - ReplyToId
    - Text
```

```
- LuisIntent.*
    - ActivityId
    - Intent
    - IntentScore
    - SentimentLabel
    - SentimentScore
    - ConversationId
    - Question
    - DialogId
```

```
- QnAMaker
    - ActivityId
    - ConversationId
    - OriginalQuestion
    - FromName
    - ArticleFound
    - Question
    - Answer
    - Score
```