---
ms.openlocfilehash: 802d7cf1ddba69474438935f283ec91205a91862
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124094"
---

### <a name="enabling-or-disabling-activity-logging"></a>Habilitar ou desabilitar o log de atividades

Por padrão, o `TelemetryInitializerMiddleware` usará o `TelemetryLoggerMiddleware` para registrar a telemetria em log quando o bot enviar/receber atividades. O log de atividades cria logs de eventos personalizados em seu recurso do Application Insights.  Se desejar, você poderá desabilitar o log de eventos de atividade definindo `logActivityTelemetry` como false no `TelemetryInitializerMiddleware` ao registrá-lo em **Startup.cs**.

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry initializer middleware
    services.AddSingleton<TelemetryInitializerMiddleware>(sp =>
            {
                var loggerMiddleware = sp.GetService<TelemetryLoggerMiddleware>();
                return new TelemetryInitializerMiddleware(loggerMiddleware, logActivityTelemetry: false);
            });
    ...
}
```

### <a name="enable-or-disable-logging-personal-information"></a>Habilitar ou desabilitar o registro em log de informações pessoais

Por padrão, se o log de atividades estiver habilitado, algumas propriedades nas atividades de entrada/saída serão excluídas do registro em log, pois é provável que contenham informações pessoais, tais como o nome de usuário e o texto da atividade. Você pode optar por incluir essas propriedades em seu log fazendo a alteração a seguir a **Startup.cs** ao registrar o `TelemetryLoggerMiddleware`.

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry initializer middleware
    services.AddSingleton<TelemetryLoggerMiddleware>(sp =>
            {
                var telemetryClient = sp.GetService<IBotTelemetryClient>();
                return new TelemetryLoggerMiddleware(telemetryClient, logPersonalInformation: true);
            });
    ...
}
```

Em seguida, veremos o que precisa ser incluído para adicionar a funcionalidade de telemetria às caixas de diálogo. Isso permitirá que você obtenha informações adicionais, tais como quais caixas de diálogo serão executadas, além de estatísticas sobre cada uma delas.