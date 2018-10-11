---
title: Análise de conversa usando o PowerBI | Microsoft Docs
description: Saiba mais sobre como o modelo de Bot do Enterprise faz uso do Application Insights para habilitar insights por meio do Power BI
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 18dcaeaf2967a90ca3ecb8ff32c1ef6399d5f498
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2018
ms.locfileid: "46708464"
---
# <a name="enterprise-bot-template---conversational-analytics-using-powerbi-dashboard-and-application-insights"></a>Modelo de Bot do Enterprise — análise de conversa usando o painel do PowerBI e o Application Insights

> [!NOTE]
> Este tópico aplica-se à versão v4 do SDK. 

Depois que seu Bot for implantado e começar a processar mensagens, você verá dados telemétricos fluindo para a instância do Application Insights em seu grupo de recursos de dados telemétricos. 

Esses dados telemétricos podem ser exibidos dentro do Application Insights Blade, no portal do Microsoft Azure e usando o Log Analytics. Além disso, os mesmos dados telemétricos podem ser usados pelo Power BI para fornecer insights de negócios mais gerais sobre o uso do seu Bot.

Um painel de exemplo do Power BI é fornecido dentro da pasta do Power BI de seu projeto criado. Ele é fornecido para fins de exemplo e demonstra como você pode começar a criar seus próprios insights. Ao longo do tempo, aprimoraremos essas visualizações. 

## <a name="getting-started"></a>Introdução

- Faça o download do PowerBI Desktop [aqui](https://powerbi.microsoft.com/en-us/desktop/)
 
- Recuperar um ```Application Id``` para o recurso do Application Insights usado pelo seu bot. Você pode obtê-lo navegando até a página de acesso à API na seção Configurar do Application Insights Azure Blade.

Clique duas vezes no arquivo de modelo do Power BI fornecido, localizado dentro da pasta do Power BI de sua solução. Você será solicitado a fornecer o ```Application Id``` recuperado na etapa anterior. Se solicitado, conclua a autenticação usando as credenciais de assinatura do Azure. Talvez seja necessário clicar na configuração Conta organizacional para entrar.

O painel resultante agora está vinculado à sua instância do Application Insights, e você deverá ver insights inicias dentro do painel se as mensagens tiverem sido enviadas e recebidas.

>Observe que a visualização de Sentimento não mostrará dados, uma vez que o script de implantação atual não habilita Sentimento ao publicar o modelo LUIS. Se você [republicar](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-publish-app) o modelo LUIS e habilitar o Sentimento, isso funcionará.

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
    - Conversationid
    - ConversationName
    - Locale
    - UserName
    - Text
```
  
```
-BotMessageSent
    - ActivityId,
    - Channel
    - RecipientId
    - Conversationid
    - ConversationName
    - Locale
    - ReceipientName
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
```

```
- QnAMaker
    - ActivityId
    - ConversationId
    - OriginalQuestion
    - UserName
    - QnAItemFound
    - Question
    - Answer
    - Score
```