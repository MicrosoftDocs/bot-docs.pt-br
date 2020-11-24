---
title: Solucionar problemas de erros HTTP 500 do bot – Serviço de Bot
description: Saiba como solucionar problemas de erros HTTP 500. Consulte Como habilitar Application Insights, recuperar informações sobre exceções e verificar logs e arquivos de configuração.
keywords: solucionar problemas, HTTP 500, problemas.
author: jonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/19/2019
ms.openlocfilehash: 92894ca049642c15dfda88d0d4330559d9ceba17
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456110"
---
# <a name="troubleshoot-http-500-errors"></a>Solucionar erros de HTTP 500

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

<!-- Attention writers!!
     1 - When you create a new FAQ, please add the related link to the proper section in bot-service-troubleshoot-index.md.-->

A primeira etapa na solução de erros 500 é habilitar o Application Insights.

Para obter exemplos de AppInsights, consulte o exemplo de [C#](https://aka.ms/cs-luis-with-appinsights-sample) de Luis-com-AppInsights e exemplo de [js](https://aka.ms/js-luis-with-appinsights-sample).

 <!-- qna-with-appinsights ([C# sample](https://aka.ms/qna-with-appinsights) / [JS sample](https://aka.ms/js-qna-with-appinsights-sample)) samples demonstrate bots that support Azure Application Insights.-->

Confira [telemetria de análise de conversa](https://aka.ms/botframeworkanalytics) para obter informações sobre como adicionar o Application Insights a um bot existente.

## <a name="enable-application-insights-on-aspnet"></a>Habilitar o Application Insights no ASP.Net

Para o suporte básico do Application Insights, confira como [configurar o Application Insights para seu site do ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net). O Bot Framework (começando com a v4.2) fornece um nível adicional de telemetria do Application Insights, mas ele não é necessário para diagnosticar erros HTTP 500.

## <a name="enable-application-insights-on-nodejs"></a>Habilitar o Application Insights no Node.js

Para o suporte básico do Application Insights, confira como [monitorar serviços e aplicativos do Node.js com o Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/nodejs-quick-start). O Bot Framework (começando com a v4.2) fornece um nível adicional de telemetria do Application Insights, mas ele não é necessário para diagnosticar erros HTTP 500.

## <a name="query-for-exceptions"></a>Consultar exceções

O método mais fácil de analisar erros 500 do código de status HTTP começar pelas exceções.

As consultas a seguir informarão as exceções mais recentes:

```sql
exceptions
| order by timestamp desc
| project timestamp, operation_Id, appName
```

Na primeira consulta, selecione algumas das IDs de operação e procure por mais informações:

```sql
let my_operation_id = "d298f1385197fd438b520e617d58f4fb";
let union_all = () {
    union
    (traces | where operation_Id == my_operation_id),
    (customEvents | where operation_Id == my_operation_id),
    (requests | where operation_Id == my_operation_id),
    (dependencies | where operation_Id  == my_operation_id),
    (exceptions | where operation_Id == my_operation_id)
};

union_all
    | order by timestamp desc
```

Se você tiver apenas `exceptions`, analise os detalhes e veja se eles correspondem às linhas em seu código. Se você só vir exceções provenientes do Conector de Canal (`Microsoft.Bot.ChannelConnector`), então confira [Sem eventos do Application Insights](#no-application-insights-events) para garantir que o Application Insights está configurado corretamente e seu código está registrando eventos.

## <a name="no-application-insights-events"></a>Sem eventos do Application Insights

Se você está recebendo 500 erros e não há nenhum outro evento no Application Insights do seu bot, verifique o seguinte:

### <a name="ensure-bot-runs-locally"></a>Certificar-se de que o bot é executado localmente

Verifique se o bot é executado localmente primeiro com o emulador.

### <a name="ensure-configuration-files-are-being-copied-net-only"></a>Certificar-se de que os arquivos de configuração estão sendo copiados (.NET)

Certifique-se de que seu arquivo `appsettings.json` e qualquer outro arquivo de configuração sejam empacotados corretamente durante o processo de implantação.

#### <a name="application-assemblies"></a>Assemblies do aplicativo

Certifique-se de que os assemblies do Application Insights estão sendo empacotados corretamente durante o processo de implantação.

- Microsoft.ApplicationInsights
- Microsoft.ApplicationInsights.TraceListener
- Microsoft.AI.Web
- Microsoft.AI.WebServer
- Microsoft.AI.ServeTelemetryChannel
- Microsoft.AI.PerfCounterCollector
- Microsoft.AI.DependencyCollector
- Microsoft.AI.Agent.Intercept

Certifique-se de que seu arquivo `appsettings.json` e qualquer outro arquivo de configuração sejam empacotados corretamente durante o processo de implantação.

#### <a name="appsettingsjson"></a>appsettings.json

Verifique se a chave de instrumentação está definida no seu arquivo `appsettings.json`.

## <a name="aspnet-web-api"></a>[ASP.NET Web API](#tab/dotnetwebapi)

```json
{
    "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
            "Default": "Debug",
            "System": "Information",
            "Microsoft": "Information"
        },
        "Console": {
            "IncludeScopes": "true"
        }
    }
}
```

## <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

```json
{
    "ApplicationInsights": {
        "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
}
```

---

### <a name="verify-config-file"></a>Verifique o arquivo de configuração

Verifique se há uma chave do Application Insights incluída em seu arquivo de configuração.

```json
{
    "ApplicationInsights": {
        "type": "appInsights",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "resourceGroup": "my resource group",
        "name": "my appinsights name",
        "serviceName": "my service name",
        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "applicationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "apiKeys": {},
        "id": ""
    }
},
```

### <a name="check-logs"></a>Verificar os logs

O ASP.Net e o Nó do bot emitirão os logs no nível do servidor que pode ser inspecionado.

#### <a name="set-up-a-browser-to-watch-your-logs"></a>Configurar um navegador para observar os logs

1. Abra o bot no [Portal do Azure](https://portal.azure.com/).
1. Abra a página **Configurações do Serviço de Aplicativo / Todas as configurações do Serviço de Aplicativo** para ver todas as configurações de serviço.
1. Abra a página **Monitoramento / Logs de diagnóstico** para o serviço de aplicativo.
   - Verifique se o **Registro em log de aplicativo (sistema de arquivos)** está habilitado. Certifique-se de clicar em **Salvar** se você alterar essa configuração.
1. Alterne para a página **Monitoramento / Fluxo de log**.
   - Selecione **Logs do servidor Web** e verifique se você vê uma mensagem informando que você está conectado. A saída deve ter uma aparência semelhante à que se segue:

     ```bash
     Connecting...
     2018-11-14T17:24:51  Welcome, you are now connected to log-streaming service.
     ```

     Mantenha essa janela aberta.

#### <a name="set-up-browser-to-restart-your-bot-service"></a>Configurar o navegador para reiniciar o serviço de bot

1. Usando um navegador separado, abra seu bot no Portal do Azure.
1. Abra a página **Configurações do Serviço de Aplicativo / Todas as configurações do Serviço de Aplicativo** para ver todas as configurações de serviço.
1. Alterne para a página **Visão geral** para o serviço de aplicativo e clique em **Reiniciar**.
   - Você será solicitado se tem certeza; selecione **Sim**.
1. Retorne para a primeira janela do navegador e observe os logs.
1. Verifique se você está recebendo novos logs.
   - Se não houver nenhuma atividade, reimplante seu bot.
   - Em seguida, alterne para a página **Logs de aplicativo** e procure erros.
