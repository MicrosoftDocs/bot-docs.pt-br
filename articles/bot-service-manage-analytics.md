---
title: Análise de bot | Microsoft Docs
description: Saiba como usar a análise e a coleta de dados para melhorar seu bot com a análise no Bot Framework.
keywords: análise de bot, application insights, tráfego, latência, integrações, AppInsights
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 503e9b2231b198346f5a7cd767a1e6a866e9e5b3
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296821"
---
# <a name="bot-analytics"></a>Análise de bot
O Analytics é uma extensão do [Application Insights](/azure/application-insights/app-insights-analytics). O Application Insights fornece dados de **nível de serviço** e de instrumentação, como tráfego, latência e integrações. O Google Analytics fornece relatórios no **nível da conversa** sobre dados de usuários, mensagens e canais.

## <a name="view-analytics-for-a-bot"></a>Exibir a análise para um bot
Para acessar o Analytics, abra o bot no portal do desenvolvedor e clique em **Analytics**.

Dados demais? [Habilitar e configurar a amostragem](/azure/application-insights/app-insights-sampling) para reduzir o tráfego de telemetria e armazenamento, mantendo estatisticamente a análise correta. 

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
O número total de usuários ativos e as mensagens enviadas e recebidas durante o período de tempo especificado.
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

### <a name="messages"></a>Mensagens
O gráfico de mensagens controla quantas mensagens foram enviadas e recebidas por qual canal durante o período de tempo especificado.

![Gráfico de mensagens](~/media/analytics-messages.png)

* O gráfico de percentual mostra qual a porcentagem de mensagens que foi comunicada em cada canal.
* O gráfico de linha indica quantas mensagens foram enviadas e recebidas pelo período de tempo especificado.
* A legenda do gráfico de linha indica qual cor de linha representa qual canal e o número total de mensagens enviadas e recebidas nesse canal durante o período de tempo especificado. 

## <a name="enable-analytics"></a>Habilitar análise
A análise não está disponível até que o Application Insights tenha sido habilitado e configurado. O Application Insights começará a coletar dados assim que estiver habilitado. Por exemplo, se o Application Insights tiver sido habilitado uma semana atrás para um bot de seis meses, ele terá coletado uma semana de dados.
> [!NOTE]
> A análise requer uma assinatura do Azure e o [recurso](/azure/application-insights/app-insights-create-new-resource) do Application Insights.
Para acessar o Application Insights, abra o bot no [Portal do Bot Framework](https://dev.botframework.com/) e clique em **Configurações**.

1. Crie um [recurso](/azure/application-insights/app-insights-create-new-resource) Application Insights.
2. Abra o bot no painel. Clique em **Configurações** e role até a seção **Analytics**.
3. Insira as informações para conectar o bot ao Application Insights. Todos os campos são obrigatórios.

![Conectar o Insights](~/media/analytics-enable.png)

### <a name="appinsights-instrumentation-key"></a>Chave de Instrumentação AppInsights
Para localizar esse valor, abra o Application Insights e navegue até **Configurar** > **Propriedades**.

### <a name="appinsights-api-key"></a>Chave de API do AppInsights
Forneça uma chave de API do Azure App Insights. Saiba como [gerar uma nova chave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID). Apenas a permissão **Leitura** é necessária.

### <a name="appinsights-application-id"></a>ID do Aplicativo do AppInsights
Para localizar esse valor, abra o Application Insights e navegue até **Configurar** > **Acesso de API**.

Para saber mais sobre como localizar esses valores, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md).

## <a name="additional-resources"></a>Recursos adicionais
* [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md)