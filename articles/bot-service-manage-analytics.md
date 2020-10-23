---
title: Análise de bot – Serviço de Bot
description: Saiba como usar a análise e a coleta de dados para melhorar seu bot com a análise no Bot Framework.
keywords: análise de bot, application insights, tráfego, latência, integrações, AppInsights
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/04/2018
ms.openlocfilehash: c2ec8f1831215512685c4397a82292a563704788
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92414932"
---
# <a name="bot-analytics"></a>Análise de bot

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

O Analytics é uma extensão do [Application Insights](/azure/application-insights/app-insights-analytics). O Application Insights fornece dados de **nível de serviço** e de instrumentação, como tráfego, latência e integrações. O Google Analytics fornece relatórios no **nível da conversa** sobre dados de usuários, mensagens e canais.

## <a name="view-analytics-for-a-bot"></a>Exibir a análise para um bot

Para acessar o Analytics, abra o bot no portal do Azure e clique em **Analytics**.

Dados demais? Você pode [habilitar e configurar a amostragem](/azure/application-insights/app-insights-sampling) para o Application Insights vinculado ao seu bot. Isso reduz o tráfego de telemetria e armazenamento, mantendo estatisticamente a análise correta.

### <a name="specify-channel"></a>Especificar canal

Escolha quais canais são exibidos nos gráficos abaixo. Se um bot não estiver habilitado em um canal, não haverá dados nesse canal.

![Selecionar canal](~/media/analytics-channels.png)

* Marque a caixa de seleção para incluir um canal no gráfico.
* Desmarque a caixa de seleção para remover um canal do gráfico.

### <a name="specify-time-period"></a>Especificar o período de tempo

A análise está disponível apenas nos últimos 90 dias. A coleta de dados começou quando o Application Insights foi habilitado.

![Selecionar período de tempo](~/media/analytics-timepick.png)

Clique no menu suspenso e, em seguida, clique em quanto tempo os gráficos devem ser exibidos.
Alterar o quadro geral do tempo aumentará proporcionalmente o incremento de tempo (eixo x) nos gráficos.

### <a name="grand-totals"></a>Totais gerais

O número total de usuários ativos e as atividades enviadas e recebidas durante o período de tempo especificado.
Os traços `--` indicam que não há atividade.

### <a name="retention"></a>Retenção

A retenção controla quantos usuários que enviaram uma mensagem voltaram posteriormente e enviaram outra.
O gráfico é uma janela de 10 dias sem interrupção; os resultados não são afetados alterando o período de tempo.

![Gráfico de retenção](~/media/analytics-retention.png)

A data mais recente possível é de dois dias atrás; um usuário enviou mensagens anteontem e então *retornou* ontem.

### <a name="user"></a>Usuário

O gráfico de usuários controla quantos usuários acessavam o bot usando cada canal durante o período de tempo especificado.

![Gráfico de usuários](~/media/analytics-users.png)

* O gráfico de percentual mostra qual é o percentual de usuários usado em cada canal.
* O gráfico de linha indica quantos usuários acessaram o bot em um determinado momento.
* A legenda do gráfico de linha indica qual cor representa qual canal e inclui o número total de usuários durante o período de tempo especificado.

### <a name="activities"></a>Atividades

O gráfico Atividades controla quantas atividades foram enviadas e recebidas por qual canal durante o período de tempo especificado.

![gráfico de atividades](~/media/analytics-activities.png)

* O gráfico de percentual mostra qual a porcentagem de atividades que foi comunicada em cada canal.
* O gráfico de linhas indica quantas atividades foram enviadas e recebidas pelo período de tempo especificado.
* A legenda do gráfico de linha indica qual cor de linha representa qual canal e o número total de atividades enviadas e recebidas nesse canal durante o período de tempo especificado.

## <a name="enable-analytics"></a>Habilitar análise

A análise não está disponível até que o Application Insights tenha sido habilitado e configurado. O Application Insights começará a coletar dados assim que estiver habilitado. Por exemplo, se o Application Insights tiver sido habilitado uma semana atrás para um bot de seis meses, ele terá coletado uma semana de dados.

> [!NOTE]
> A análise requer uma assinatura do Azure e o [recurso](/azure/application-insights/app-insights-create-new-resource) do Application Insights.
Para acessar o Application Insights, abra o bot no [Portal do Azure](https://portal.azure.com/) e clique em **Configurações**.

Você pode adicionar o Application Insights quando você cria seu recurso de bot.

Você também pode criar um recurso do Application Insights mais tarde e conectá-lo ao seu bot.

1. Crie um [recurso](/azure/application-insights/app-insights-create-new-resource) Application Insights.
2. Abra o bot no painel. Clique em **Configurações** e role até a seção **Analytics**.
3. Insira as informações para conectar o bot ao Application Insights. Todos os campos são obrigatórios.

![Conectar o Insights](~/media/analytics-enable.png)

<!--Snip: As of 12/04/2018, parts of this appear to be out of date. However, ~/bot-service-resources-app-insights-keys.md appears to be up to date.

### AppInsights Instrumentation Key

To find this value, open the Application Insights resource for your bot and navigate to **Configure** > **Properties**.

### AppInsights API key

Provide an Azure App Insights API key. Learn how to [generate a new API key](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID). Only **Read** permission is required.

### AppInsights Application ID

To find this value, open Application Insights and navigate to **Configure** > **API Access**.

/Snip-->

Para saber mais sobre como localizar esses valores, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md).

## <a name="additional-resources"></a>Recursos adicionais
* [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md)