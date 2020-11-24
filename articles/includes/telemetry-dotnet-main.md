---
ms.openlocfilehash: 6149dd01f6bba37281a58b38f8b3c28908392d26
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95561036"
---

Este artigo é iniciado no [aplicativo de exemplo CoreBot](https://aka.ms/cs-core-sample) e adiciona o código necessário para integrar a telemetria em qualquer bot. Isso permitirá que o Application Insights comece a acompanhar as solicitações.

> [!IMPORTANT]
> Se você não configurou sua conta de [Application insights](https://aka.ms/appinsights-overview) e criou sua [chave de Application insights](../bot-service-resources-app-insights-keys.md), faça isso antes de continuar.

1. Abra o [aplicativo de exemplo CoreBot](https://aka.ms/cs-core-sample) no Visual Studio.

2. Adicione o pacote NuGet `Microsoft.Bot.Builder.Integration.ApplicationInsights.Core `. Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs):


3. Inclua as instruções a seguir em `Startup.cs`:
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.Bot.Builder.ApplicationInsights;
    using Microsoft.Bot.Builder.Integration.ApplicationInsights.Core;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    ```

    Observação: Se estiver acompanhando e atualizando o código de exemplo do CoreBot, você observará que a instrução using para `Microsoft.Bot.Builder.Integration.AspNet.Core` já existe no exemplo do CoreBot.

4. Inclua o código a seguir no método `ConfigureServices()` em `Startup.cs`. Isso tornará os serviços de telemetria disponíveis para o bot por meio da [DI (injeção de dependência)](https://aka.ms/asp.net-core-dependency-interjection):
    ```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        ...
            // Create the Bot Framework Adapter with error handling enabled.
            services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();

            // Add Application Insights services into service collection
            services.AddApplicationInsightsTelemetry();

            // Create the telemetry client.
            services.AddSingleton<IBotTelemetryClient, BotTelemetryClient>();

            // Add telemetry initializer that will set the correlation context for all telemetry items.
            services.AddSingleton<ITelemetryInitializer, OperationCorrelationTelemetryInitializer>();

            // Add telemetry initializer that sets the user ID and session ID (in addition to other bot-specific properties such as activity ID)
            services.AddSingleton<ITelemetryInitializer, TelemetryBotIdInitializer>();

            // Create the telemetry middleware to initialize telemetry gathering
            services.AddSingleton<TelemetryInitializerMiddleware>();

            // Create the telemetry middleware (used by the telemetry initializer) to track conversation events
            services.AddSingleton<TelemetryLoggerMiddleware>();
        ...
    }
    ```

    Observação: se estiver acompanhando a atualização do código de exemplo CoreBot, você notará que `services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();` já existe 

5. Instrua o adaptador a usar o código de middleware que foi adicionado ao método `ConfigureServices()`. Você faz isso em `AdapterWithErrorHandler.cs` com o parâmetro telemetryInitializerMiddleware TelemetryInitializerMiddleware na lista de parâmetros do construtor e a `Use(telemetryInitializerMiddleware);` instrução no construtor, conforme mostrado aqui:
    ```csharp
        public AdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger, TelemetryInitializerMiddleware telemetryInitializerMiddleware, ConversationState conversationState = null)
            : base(configuration, logger)
    {
        ...
        Use(telemetryInitializerMiddleware);
    }
    ```

6. Você também precisará adicionar `Microsoft.Bot.Builder.Integration.ApplicationInsights.Core` à lista de instruções Using no `AdapterWithErrorHandler.cs`.

7. Adicione a chave de instrumentação do Application Insights ao seu arquivo `appsettings.json`. O `appsettings.json` arquivo contém metadados sobre os serviços externos que o bot usa durante a execução. Por exemplo, as conexões de serviço e os metadados do CosmosDB, Application Insights e LUIS (Reconhecimento Vocal) são armazenados nele. A adição ao arquivo `appsettings.json` deve estar neste formato:

    ```json
    {
        "MicrosoftAppId": "",
        "MicrosoftAppPassword": "",
        "ApplicationInsights": {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        }
    }
    ```

    Observação: Detalhes sobre como obter a _chave de instrumentação do Application Insights_ podem ser encontrados no artigo [Chaves do Application Insights](../bot-service-resources-app-insights-keys.md).

Neste ponto, o trabalho preliminar para habilitar a telemetria usando o Application Insights está concluído.  Você pode executar o bot localmente usando o emulador de bot e, em seguida, entrar em Application Insights para ver o que está sendo registrado, como o tempo de resposta, a integridade geral do aplicativo e as informações gerais em execução.

### <a name="enabling-telemetry-in-your-bots-dialogs"></a>Habilitar a telemetria nas caixas de diálogo de bots

Ao adicionar uma nova caixa de diálogo a qualquer ComponentDialog, ela herdará o Microsoft.Bot.Builder.IBotTelemetryClient da caixa de diálogo pai.  Por exemplo, no aplicativo de exemplo CoreBot, todas as caixas de diálogo são adicionadas à MainDialog, que é uma ComponentDialog.  Depois de definir a propriedade TelemetryClient para a MainDialog, todas as caixas de diálogo adicionadas a ela herdarão automaticamente a telemetryClient dela, portanto, não precisará ser definida explicitamente ao adicionar caixas de diálogo.

Siga as etapas abaixo para atualizar o exemplo de CoreBot:

1.  Em `MainDialog.cs`, atualize a lista de parâmetros do construtor para incluir o parâmetro `IBotTelemetryClient` e, em seguida, defina a propriedade TelemetryClient da MainDialog para esse valor, conforme mostrado no seguinte snippet de código:

    ```csharp
    public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger, IBotTelemetryClient telemetryClient)
        : base(nameof(MainDialog))
    {
        // Set the telemetry client for this and all child dialogs.
        this.TelemetryClient = telemetryClient;
        ...
    }
    ```

> [!TIP]
> Se você estiver acompanhando e atualizando o código de exemplo do CoreBot, poderá consultar o [código de exemplo do Application Insights](https://aka.ms/csharp-corebot-app-insights-sample) se tiver algum problema.

Isso é tudo o que é necessário para adicionar telemetria às caixas de diálogo de bots, neste ponto, se você tiver executado o bot, verá que as coisas estão sendo registradas no Application Insights. No entanto, se você tiver uma tecnologia integrada, tal como o LUIS e o QnA Maker, será necessário adicionar o `TelemetryClient` a esse código também.
