---
title: Adicionar telemetria ao seu bot – Serviço de Bot
description: Saiba como exibir informações sobre a disponibilidade, o desempenho, o uso e o comportamento do bot. Consulte Como ativar o acompanhamento de telemetria para Application Insights.
keywords: telemetria, appinsights, bot de monitor
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c9f14cdf01cabf5178bb8b1779cb53b63bb8cb42
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360072"
---
# <a name="add-telemetry-to-your-bot"></a>Adicionar telemetria ao seu bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

O registro em log de telemetria foi adicionado à versão 4.2 do SDK do Bot Framework.  Isso permite que os aplicativos do bot enviem dados de evento para serviços de telemetria, tais como o [Application Insights](https://aka.ms/appinsights-overview). A telemetria oferece informações sobre o bot mostrando quais recursos são mais usados, detecta comportamento indesejado e fornece visibilidade da disponibilidade, do desempenho e do uso.

***Observação: Na versão 4.6, o método padrão para implementar a telemetria em um bot foi atualizado para garantir que a telemetria seja registrada corretamente ao usar um adaptador personalizado. Este artigo foi atualizado para mostrar o novo método. As alterações são compatíveis com versões anteriores e os bots que usam o método anterior continuarão a registrar a telemetria em log corretamente.***

Neste artigo, você aprenderá a implementar a telemetria em seu bot usando Application Insights. Ele abrangerá:

* O código necessário para ligar a telemetria no bot e conectar-se a Application Insights.

* Habilitando a telemetria nas [caixas de diálogo](bot-builder-concept-dialog.md)do bot.

* Habilite a telemetria para capturar dados de uso de outros serviços, tais como o [LUIS](bot-builder-howto-v4-luis.md) e o [QnA Maker](bot-builder-howto-qna.md).

* Visualizando seus dados de telemetria em Application Insights.

<!-- Prerequisites-->
## <a name="prerequisites"></a>Pré-requisitos

# <a name="c"></a>[C#](#tab/csharp)

* O [código de exemplo do CoreBot](https://aka.ms/cs-core-sample)
* O [código de exemplo do Application Insights](https://aka.ms/csharp-corebot-app-insights-sample)
* Uma assinatura do [Microsoft Azure](https://portal.azure.com/)
* Uma [chave do Application Insights](../bot-service-resources-app-insights-keys.md)
* Familiaridade com o [Application Insights](https://aka.ms/appinsights-overview)
* [git](https://git-scm.com/)

> [!NOTE]
> O [código de exemplo do Application Insights](https://aka.ms/csharp-corebot-app-insights-sample) foi criado com base no [código de exemplo do CoreBot](https://aka.ms/cs-core-sample). Este artigo guiará você pela modificação do código de exemplo do CoreBot para incorporar a telemetria. Se estiver acompanhando o Visual Studio, você terá o Application Insights código de exemplo quando terminar.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* O [código de exemplo do CoreBot](https://aka.ms/js-core-sample)
* O [código de exemplo do Application Insights](https://aka.ms/js-corebot-app-insights-sample)
* Uma assinatura do [Microsoft Azure](https://portal.azure.com/)
* Uma [chave do Application Insights](../bot-service-resources-app-insights-keys.md)
* Familiaridade com o [Application Insights](https://aka.ms/appinsights-overview)
* [Visual Studio Code](https://www.visualstudio.com/downloads)
* [Node.js](https://nodejs.org/) versão 10,14 ou superior. Use `node --version` o comando para determinar a versão do nó que você instalou.
* [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)

> [!NOTE]
> O [código de exemplo do Application Insights](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/21.corebot-app-insights) foi criado com base no [código de exemplo do CoreBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/13.core-bot). Este artigo guiará você pela modificação do código de exemplo do CoreBot para incorporar a telemetria. Se estiver acompanhando Visual Studio Code você terá o Application Insights código de exemplo quando terminar.

<!--

    # [Python](#tab/python)

    * The [CoreBot sample code](https://aka.ms/py-core-sample)
    * The [Application Insights sample code](https://aka.ms/py-corebot-app-insights-sample)
    * A subscription to [Microsoft Azure](https://portal.azure.com/)
    * An [Application Insights key](../bot-service-resources-app-insights-keys.md)
    * Familiarity with [Application Insights](https://aka.ms/appinsights-overview)

    > [!NOTE]
    > The [Application Insights sample code](https://aka.ms/py-corebot-app-insights-sample) was built on top of the [CoreBot sample code](https://aka.ms/py-core-sample). This article will step you through modifying the CoreBot sample code to incorporate telemetry. If you are following along in Visual Studio you will have the Application Insights sample code by the time you are finished.
-->

---

## <a name="wiring-up-telemetry-in-your-bot"></a>Conectar a telemetria em seu bot
<!-- Main article on implementing telemetry--->

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet telemetry section](../includes/telemetry-dotnet-main.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript telemetry section](../includes/telemetry-javascript-main.md)]

<!--
    # [Python](#tab/python)
    [!INCLUDE [python telemetry section](../includes/telemetry-python.md)]
-->

---

## <a name="enabling--disabling-activity-event-and-personal-information-logging"></a>Habilitar/desabilitar o registro em log de eventos de atividade e de informações pessoais

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet telemetry-Enabling disabling activity section](../includes/telemetry-dotnet-enabling-disabling-activity-event-personal-information-logging.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript telemetry section](../includes/telemetry-javascript-enabling-disabling-activity-event-personal-information-logging.md)]

<!--
    # [Python](#tab/python)

    [!INCLUDE [python telemetry section](../includes/telemetry-python-enabling-disabling-activity-event-personal-information-logging.md)]
-->

---

## <a name="enabling-telemetry-to-capture-usage-data-from-other-services-like-luis-and-qna-maker"></a>Habilitar a telemetria para capturar dados de uso de outros serviços, tais como o LUIS e o QnA Maker

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet telemetry-luis section](../includes/telemetry-dotnet-luis.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript telemetry-luis section](../includes/telemetry-javascript-luis.md)]

<!--
    # [Python](#tab/python)

    [!INCLUDE [python telemetry-luis section](../includes/telemetry-python-luis.md)]
-->

---

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
