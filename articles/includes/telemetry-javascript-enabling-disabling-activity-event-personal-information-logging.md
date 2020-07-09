---
ms.openlocfilehash: 10539ba926ff84976e88458aeb85e06ae50dbb01
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124126"
---
### <a name="enable-or-disable-activity-logging"></a>Habilitar ou desabilitar o log de atividades

Por padrão, o `TelemetryInitializerMiddleware` usará o `TelemetryLoggerMiddleware` para registrar a telemetria quando o bot enviar ou receber atividades. O log de atividades cria logs de eventos personalizados em seu recurso do Application Insights.  Se desejar, você pode desabilitar o log de eventos de atividade definindo `logActivityTelemetry` como false no `TelemetryInitializerMiddleware` ao registrá-lo no **index.js**.

O trecho de código a seguir vem do exemplo `21.corebot-app-insights` e mostra a chamada para `TelemetryInitializerMiddleware` :

[!code-javascript[dialog.telemetryClient](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=64-68)]

O trecho de código abaixo mostra a alteração necessária no exemplo `21.corebot-app-insights` , na chamada para `TelemetryInitializerMiddleware` para desabilitar o log de atividades:

```javascript
// Add telemetry middleware to the adapter middleware pipeline
var telemetryClient = getTelemetryClient(process.env.InstrumentationKey);
var telemetryLoggerMiddleware = new TelemetryLoggerMiddleware(telemetryClient);
var initializerMiddleware = new TelemetryInitializerMiddleware(telemetryLoggerMiddleware, false);
adapter.use(initializerMiddleware);
```

### <a name="enable-or-disable-logging-personal-information"></a>Habilitar ou desabilitar o registro em log de informações pessoais

Quando o log de atividades está habilitado, algumas propriedades nas atividades de entrada/saída são excluídas do log por padrão, pois é provável que contenham informações pessoais, como o nome de usuário e o texto da atividade. Você pode optar por incluir essas propriedades em seu registro em log alterando o `logPersonalInformation` parâmetro de `false` para `true` ao registrar o `TelemetryLoggerMiddleware` no **index.js**.

[!code-javascript[dialog.telemetryClient](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=64-68&highlight=3)]

<!-- This is the code block that the code snippet link should point to:
```javascript
// Add telemetry middleware to the adapter middleware pipeline
var telemetryClient = getTelemetryClient(process.env.InstrumentationKey);
var telemetryLoggerMiddleware = new TelemetryLoggerMiddleware(telemetryClient, true);  //This line should be highlighted
var initializerMiddleware = new TelemetryInitializerMiddleware(telemetryLoggerMiddleware);
adapter.use(initializerMiddleware);
```
-->

Em seguida, veremos o que precisa ser incluído para adicionar a funcionalidade de telemetria às caixas de diálogo. Isso permitirá que você obtenha informações adicionais, tais como quais caixas de diálogo serão executadas, além de estatísticas sobre cada uma delas.
