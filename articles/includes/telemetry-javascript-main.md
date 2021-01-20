---
ms.openlocfilehash: 331a0545971eb897655a5c25c6ae4f2c3fe25476
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577158"
---
Este artigo começa com o [aplicativo de exemplo CoreBot](https://aka.ms/js-core-sample) e adiciona o código necessário para integrar a telemetria em qualquer bot. Isso permitirá que o Application Insights comece a acompanhar as solicitações.

> [!IMPORTANT]
> Se você não configurou sua conta de [Application insights](/azure/azure-monitor/app/app-insights-overview) e criou sua [chave de Application insights](../bot-service-resources-app-insights-keys.md), faça isso antes de continuar.

1. Abra o [aplicativo de exemplo CoreBot](https://aka.ms/js-core-sample) em Visual Studio Code.

2. Adicione a [chave de Application insights](../bot-service-resources-app-insights-keys.md) ao `.env` arquivo: `InstrumentationKey=<EnterInstrumentationKeyHere>` . O `.env` arquivo contém metadados sobre os serviços externos que o bot usa durante a execução. Por exemplo, Application Insights e a conexão de serviço do Reconhecimento vocal (LUIS) e os metadados são armazenados lá. A adição ao arquivo `.env` deve estar neste formato:

    [!code-json[env](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/.env?range=1-6&highlight=6)]

    <!-- This is the code block that the code snippet link should point to:
    ```json
    MicrosoftAppId=
    MicrosoftAppPassword=
    LuisAppId=
    LuisAPIKey=
    LuisAPIHostName=
    InstrumentationKey=
    ```
    -->

    Observação: Detalhes sobre como obter a _chave de instrumentação do Application Insights_ podem ser encontrados no artigo [Chaves do Application Insights](../bot-service-resources-app-insights-keys.md).

3. Adicione uma referência aos módulos `ApplicationInsightsTelemetryClient` e `TelemetryInitializerMiddleware`  que estão localizados em `botbuilder-applicationinsights` no SDK do bot Framework. Para fazer isso, adicione o seguinte código, começando próximo à parte superior de `index.js` , logo após o código para importar os pacotes necessários:

    [!code-javascript[Import](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=16-17)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
    // Import required services for bot telemetry
    const { ApplicationInsightsTelemetryClient, TelemetryInitializerMiddleware } = require('botbuilder-applicationinsights');
    const { TelemetryLoggerMiddleware } = require('botbuilder-core');
    ```
    -->

    > [!TIP]
    > Os [exemplos de bot de JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs) usam Node.js que segue o sistema de módulo CommonJS e a `require` função interna para incluir módulos que existem em arquivos separados.

4. Crie uma nova função no final do `index.js` nome `getTelemetryClient` que usa sua chave de instrumentação como um parâmetro e retorna um _cliente de telemetria_ usando o `ApplicationInsightsTelemetryClient` módulo referenciado anteriormente. Esse  _cliente de telemetria_ é onde os dados de telemetria serão enviados, neste caso Application insights.

    [!code-javascript[getTelemetryClient](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=116-122)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
    // Creates a new TelemetryClient based on a instrumentation key
    function getTelemetryClient(instrumentationKey) {
        if (instrumentationKey) {
            return new ApplicationInsightsTelemetryClient(instrumentationKey);
        }
        return new NullTelemetryClient();
    }
    ```
    -->

5. Em seguida, você precisa adicionar o _middleware de telemetria_ ao [pipeline de middleware do adaptador](../v4sdk/bot-builder-concept-middleware.md#the-bot-middleware-pipeline). Para fazer isso, adicione o seguinte código, começando logo após o código de tratamento de erro:  

    <!-- This level of detail may be too much:
        - The first step is to create a new telemetry client, in this case you are using Application Insights as the telemetry client using the module `ApplicationInsightsTelemetryClient` referenced in the previous step. This line of code will call the function `getTelemetryClient` that you will soon create, passing in the Application Insights key and that function will return a new telemetry client: `var telemetryClient = getTelemetryClient(process.env.InstrumentationKey);`. 
        - You will pass the telemetry client you just created to the `TelemetryLoggerMiddleware` function: `var telemetryLoggerMiddleware = new TelemetryLoggerMiddleware(telemetryClient, true);` which creates a TelemetryLoggerMiddleware object that you will use to create
        - 
    -->

    [!code-javascript[telemetryClient](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=66-70)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
    // Add telemetry middleware to the adapter middleware pipeline
    var telemetryClient = getTelemetryClient(process.env.InstrumentationKey);
    var telemetryLoggerMiddleware = new TelemetryLoggerMiddleware(telemetryClient, true);
    var initializerMiddleware = new TelemetryInitializerMiddleware(telemetryLoggerMiddleware, true);
    adapter.use(initializerMiddleware);
    ```
    -->

6. Para que sua caixa de diálogo relate dados de telemetria, seu `telemetryClient` deve corresponder ao que foi usado para o middleware de telemetria, ou seja, `dialog.telemetryClient = telemetryClient;`

    [!code-javascript[dialog.telemetryClient](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=88-93&highlight=6)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
    // Create the main dialog.
    const bookingDialog = new BookingDialog(BOOKING_DIALOG);
    const dialog = new MainDialog(luisRecognizer, bookingDialog);
    const bot = new DialogAndWelcomeBot(conversationState, userState, dialog);

    dialog.telemetryClient = telemetryClient; //This should be highlighted
    ```
    -->

7. Depois de criar o objeto de servidor Web HTTP restify, instrua-o a usar o `bodyParser` manipulador. <!--Need better/more detail-->

    [!code-javascript[dialog.telemetryClient](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=112-114)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
    // Enable the Application Insights middleware, which helps correlate all activity
    // based on the incoming request.
    server.use(restify.plugins.bodyParser());
    ```
    -->

    > [!TIP]
    > Isso usa a função _restify_ `bodyParser` . _restify_ é uma estrutura de serviço da Web "a Node.js otimizada para a criação de serviços Web RESTful semanticamente corretos para uso em produção em escala. o restify otimiza a introspecção e o desempenho e é usado em algumas das maiores implantações de Node.js na terra. " Consulte o site do [restify](http://restify.com) para obter mais informações.

    Node.js que segue o sistema de módulo CommonJS e a função interna `require` para incluir módulos que existem em arquivos separados.

Neste ponto, o trabalho preliminar para habilitar a telemetria usando o Application Insights está concluído.  Você pode executar o bot localmente usando o emulador de bot e, em seguida, entrar em Application Insights para ver o que está sendo registrado, como o tempo de resposta, a integridade geral do aplicativo e as informações gerais em execução.
