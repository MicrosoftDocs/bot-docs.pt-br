---
title: Adicionar telemetria ao seu bot do QnA – Serviço de Bot
description: Saiba como integrar os novos recursos de telemetria em seu bot habilitado para o QnA Maker.
keywords: telemetria, appinsights, Application Insights, bot de monitoramento, QnA Maker
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fc7bf3453ab9f0f83033cad63e8d2578b7bb249a
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95449251"
---
# <a name="add-telemetry-to-your-qnamaker-bot"></a>Adicionar telemetria ao seu bot do QnA Maker

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O registro em log de telemetria foi adicionado à versão 4.2 do SDK do Bot Framework.  Isso permite que os aplicativos do bot enviem dados de evento para serviços de telemetria, tais como o [Application Insights](https://aka.ms/appinsights-overview). A telemetria oferece informações sobre o bot mostrando quais recursos são mais usados, detecta comportamento indesejado e fornece visibilidade da disponibilidade, do desempenho e do uso.

Dois novos componentes foram adicionados ao SDK do Bot Framework que habilita o log de telemetria nos bots habilitados para o QnA Maker: `TelemetryLoggerMiddleware` e a classe `QnAMaker`. `TelemetryLoggerMiddleware` é um componente de middleware que registra cada vez que as mensagens são recebidas, enviadas, atualizadas ou excluídas. A classe 'QnAMaker' fornece o log personalizado que estende as funcionalidades de telemetria.

Neste artigo, você aprenderá sobre:

* O código necessário para ligar a telemetria a seu bot. 

* O código necessário para habilitar o registro em log de QnA pronto para uso e os relatórios que usam as propriedades de evento padrão. 

* Como modificar ou estender as propriedades de evento padrão do SDK para habilitar uma ampla gama de necessidades de relatório.


## <a name="prerequisites"></a>Pré-requisitos

* O [código de exemplo do QnA Maker](https://aka.ms/cs-qna)

* Uma assinatura do [Microsoft Azure](https://portal.azure.com/)

* Uma [chave do Application Insights](../bot-service-resources-app-insights-keys.md)

* É útil ter familiaridade com o [QnA Maker](https://qnamaker.ai/).

* Uma conta do [QnA Maker](https://aka.ms/create-qna-maker).

* Uma base de dados de conhecimento do QnA Maker publicada. Se você não tiver uma, siga as etapas no tutorial [Criar e responder a partir de KB](https://aka.ms/create-publish-query-in-portal) para criar uma base de conhecimento do QnA Maker com perguntas e respostas.

> [!NOTE]
> Este artigo será criado no [código de exemplo do QnA Maker](https://aka.ms/cs-qna), guiando você pelas etapas necessárias para incorporar a telemetria. 

## <a name="wiring-up-telemetry-in-your-qna-maker-bot"></a>Ligar a telemetria a seu bot do QnA Maker

Começaremos com o [aplicativo de exemplo do QnA Maker](https://aka.ms/cs-qna) e adicionaremos o código necessário para integrar a telemetria a um bot usando o serviço de QnA. Isso permitirá que o Application Insights comece a acompanhar as solicitações.

1. Abra o [aplicativo de exemplo do QnA Maker](https://aka.ms/cs-qna) no Visual Studio

2. Adicione o pacote NuGet `Microsoft.Bot.Builder.Integration.ApplicationInsights.Core `. Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs):

3. Inclua as instruções a seguir em `Startup.cs`:
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.Bot.Builder.ApplicationInsights;
    using Microsoft.Bot.Builder.Integration.ApplicationInsights.Core;
    ```

    > [!NOTE] 
    > Se estiver acompanhando e atualizando o código de exemplo do QnA Maker, você observará que a instrução using para `Microsoft.Bot.Builder.Integration.AspNet.Core` já existe no exemplo do QnA Maker.

4. Adicione o código a seguir ao método `ConfigureServices()` em `Startup.cs`. Isso torna os serviços de telemetria disponíveis para o bot por meio da [DI (injeção de dependência)](https://aka.ms/asp.net-core-dependency-interjection):
    ```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        // Create the Bot Framework Adapter with error handling enabled.
        services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();

        // Add Application Insights services into service collection
        services.AddApplicationInsightsTelemetry();

        // Add the standard telemetry client
        services.AddSingleton<IBotTelemetryClient, BotTelemetryClient>();

        // Create the telemetry middleware to track conversation events
        services.AddSingleton<TelemetryLoggerMiddleware>();

        // Add the telemetry initializer middleware
        services.AddSingleton<IMiddleware, TelemetryInitializerMiddleware>();

        // Add telemetry initializer that will set the correlation context for all telemetry items
        services.AddSingleton<ITelemetryInitializer, OperationCorrelationTelemetryInitializer>();

        // Add telemetry initializer that sets the user ID and session ID (in addition to other bot-specific properties, such as activity ID)
        services.AddSingleton<ITelemetryInitializer, TelemetryBotIdInitializer>();
        ...
    }
    ```
    
    > [!NOTE] 
    > Se você estiver acompanhando atualizando o código de exemplo do QnA Maker, você notará que `services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();` já existe.

5. Instrua o adaptador a usar o código de middleware que foi adicionado ao método `ConfigureServices()`. Abra `AdapterWithErrorHandler.cs` e adicione `IMiddleware middleware` à lista de parâmetros de construtores. Adicione a instrução `Use(middleware);` como a última linha no construtor:
    ```csharp
    public AdapterWithErrorHandler(ICredentialProvider credentialProvider, ILogger<BotFrameworkHttpAdapter> logger, IMiddleware middleware, ConversationState conversationState = null)
            : base(credentialProvider)
    {
        ...

        Use(middleware);
    }
    ```

6. Adicione a chave de instrumentação do Application Insights ao seu arquivo `appsettings.json`. O arquivo `appsettings.json` contém metadados sobre serviços externos que o bot usa durante a execução. Por exemplo, as conexões de serviço e os metadados do CosmosDB, Application Insights e do QnA Maker são armazenados nele. A adição ao arquivo `appsettings.json` deve estar neste formato:

    ```json
    {
        "MicrosoftAppId": "",
        "MicrosoftAppPassword": "",
        "QnAKnowledgebaseId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "QnAEndpointKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "QnAEndpointHostName": "https://xxxxxxxx.azurewebsites.net/qnamaker",
        "ApplicationInsights": {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        }
    }
    ```

    > [!Note]
    >
    > * Detalhes sobre como obter a _chave de instrumentação do Application Insights_ podem ser encontrados no artigo [Chaves do Application Insights](../bot-service-resources-app-insights-keys.md).
    > * Para isso, você já deverá ter uma [conta do QnA Maker](https://aka.ms/create-qna-maker). Se necessário, você pode encontrar informações sobre como obter os valores do QnA KnowledgebaseId, EndpointKey e HostName [aqui](https://aka.ms/bot-framework-emulator-qna-keys).

Neste ponto, o trabalho preliminar para habilitar a telemetria usando o Application Insights está concluído.  Você pode executar o bot localmente usando o emulador de bot e, em seguida, entrar em Application Insights para ver o que está sendo registrado em log, como o tempo de resposta, a integridade geral do aplicativo e as informações gerais em execução.

> [!TIP]
> Para obter informações sobre como habilitar/desabilitar o registro em log de eventos de atividade e de informações pessoais, confira [Adicionar telemetria ao bot](bot-builder-telemetry.md#enabling-or-disabling-activity-logging)

Em seguida, veremos o que precisa ser incluído para adicionar a funcionalidade de telemetria ao serviço do QnA Maker.

## <a name="enabling-telemetry-to-capture-usage-data-from-the-qna-maker-service"></a>Habilitar a telemetria para capturar dados de uso do serviço do QnA Maker

O serviço do QnA Maker tem o registro em log de telemetria interno disponível para que o esforço necessário para começar a obter dados de telemetria do QnA Maker seja mínimo.  Primeiro, veremos como incorporar a telemetria ao código do QnA Maker para habilitar o registro em log de telemetria interno. Em seguida, aprenderemos como substituir ou adicionar propriedades adicionais aos dados de evento existentes para atender a uma ampla gama de necessidades de relatório.

### <a name="enabling-default-qna-logging"></a>Como habilitar o registro em log padrão do QnA

1. Crie um campo privado somente leitura do tipo `IBotTelemetryClient` em sua classe `QnABot` em `QnABot.cs`:

    ```cs
    public class QnABot : ActivityHandler
        {
            private readonly IBotTelemetryClient _telemetryClient;
            ...
   }
    ```

2. Adicione um parâmetro `IBotTelemetryClient` ao construtor da classe `QnABot` no `QnABot.cs` e atribua o valor dele ao campo privado criado na etapa anterior:

    ```cs
    public QnABot(IConfiguration configuration, ILogger<QnABot> logger, IHttpClientFactory httpClientFactory, IBotTelemetryClient telemetryClient)
    {
        ...
        _telemetryClient = telemetryClient;
    }
    ```

3. O parâmetro _`telemetryClient`_ é necessário ao instanciar o novo objeto QnAMaker em `QnABot.cs`:

    ```cs
    var qnaMaker = new QnAMaker(new QnAMakerEndpoint
                {
                    KnowledgeBaseId = _configuration["QnAKnowledgebaseId"],
                    EndpointKey = _configuration["QnAEndpointKey"],
                    Host = _configuration["QnAEndpointHostName"]
                },
                null,
                httpClient,
                _telemetryClient);
    ```

    > [!TIP]
    > Verifique se os nomes de propriedade que você usa nas `_configuration` entradas correspondem aos nomes de propriedade usados na AppSettings.jsno arquivo e os valores dessas propriedades são obtidos selecionando o botão _Exibir código_ na página [minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices) no portal de QnA Maker:

    ![AppSettings](media/AppSettings.json-QnAMaker.png)

#### <a name="viewing-telemetry-data-logged-from-the-qna-maker-default-entries"></a>Como exibir dados de telemetria registrados em log das entradas padrão do QnA Maker

Você pode exibir os resultados do uso do QnA Maker bot em Application Insights depois de executar o bot no emulador de bot executando as seguintes etapas:

1. Vá para o [Portal do Azure](https://portal.azure.com/)

2. Navegue até o Application Insights clicando em __Monitorar > Aplicativos__.

3. Uma vez no Application Insights, você pode clicar em _Logs (Análise)_ na barra de navegação, conforme mostrado abaixo:

    ![Log Analytics](media/AppInsights-LogView-QnaBot.png)

4. Insira a consulta em Kusto a seguir e selecione _Executar_

    ```SQL
    customEvents
    | where name == 'QnaMessage'
    | extend answer = tostring(customDimensions.answer)
    | summarize count() by answer
    ```
5. Deixe essa página aberta em seu navegador, voltaremos a ela depois de adicionar uma nova propriedade personalizada.

> [!TIP]
> Se você não estiver familiarizado com a linguagem de consulta Kusto que é usada para gravar consultas de log no Azure Monitor, mas estiver familiarizado com a linguagem de consulta SQL, a [folha de referências para consultas de log do SQL para o Azure Monitor](https://aka.ms/azureMonitor-SQL-cheatsheet) poderá lhe ser útil. 

### <a name="modifying-or-extending-the-default-event-properties"></a>Modificar ou estender as propriedades do evento padrão
Se você precisa de propriedades que não são definidas na classe `QnAMaker`, há duas maneiras de lidar com isso. As duas exigem a criação de sua própria classe, derivada da classe `QnAMaker`. A primeira é explicada na seção abaixo, intitulada [Adicionar propriedades](#adding-properties), em que você adiciona propriedades ao evento `QnAMessage` existente. O segundo método permite que você crie eventos aos quais é possível adicionar propriedades, conforme descrito em [Adicionar novos eventos com propriedades personalizadas](#adding-new-events-with-custom-properties).  

> [!Note]
> O evento `QnAMessage` faz parte do SDK do Bot Framework e fornece todas as propriedades de evento prontas para uso que são registradas em log no Application Insights.



#### <a name="adding-properties"></a>Adicionando propriedades 

A seguir, é demonstrado como é possível derivar da classe `QnAMaker`.  O exemplo mostra a adição da propriedade "MyImportantProperty" ao evento `QnAMessage`.  O evento `QnAMessage` é registrado sempre que uma chamada a [GetAnswers](https://aka.ms/namespace-QnAMaker-GetAnswersAsync) do QnA é realizada.  

Depois de aprender a adicionar propriedades personalizadas, aprenderemos como criar um evento personalizado e associar propriedades a ele. Em seguida, executaremos o bot localmente usando o Bot Framework Emulator e veremos o que está sendo registrado em log no Application Insights usando a linguagem de consulta Kusto.

1. Crie uma classe chamada `MyQnAMaker` no namespace `Microsoft.BotBuilderSamples` que herda da classe `QnAMaker` e salve-a como `MyQnAMaker.cs`. Para herdar da classe `QnAMaker`, você precisará adicionar a instrução using `Microsoft.Bot.Builder.AI.QnA`. O código deve aparecer da seguinte maneira:


    ```cs
    using Microsoft.Bot.Builder.AI.QnA;

    namespace Microsoft.BotBuilderSamples
    {
        public class MyQnAMaker : QnAMaker
        {

        }
    }
    ```
2. Adicione um construtor de classe para `MyQnAMaker`. Observe que você precisará de duas instruções using adicionais para os parâmetros de construtores `System.Net.Http` e `Microsoft.Bot.Builder`:

    ```cs
    ...
    using Microsoft.Bot.Builder.AI.QnA;
    using System.Net.Http;
    using Microsoft.Bot.Builder;

    namespace Microsoft.BotBuilderSamples
    {
        public class MyQnAMaker : QnAMaker
        {
            public MyQnAMaker(
                QnAMakerEndpoint endpoint,
                QnAMakerOptions options = null,
                HttpClient httpClient = null,
                IBotTelemetryClient telemetryClient = null,
                bool logPersonalInformation = false)
                : base(endpoint, options, httpClient, telemetryClient, logPersonalInformation)
            {

            }
        } 
    }  
    ```
3. Adicione a nova propriedade ao evento QnAMessage após o construtor e inclua as instruções `System.Collections.Generic`, `System.Threading` e `System.Threading.Tasks`:

    ```cs
    using Microsoft.Bot.Builder.AI.QnA;
    using System.Net.Http;
    using Microsoft.Bot.Builder;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
 
    namespace Microsoft.BotBuilderSamples
    {
            public class MyQnAMaker : QnAMaker
            {
            ...

            protected override async Task OnQnaResultsAsync(
                                QueryResult[] queryResults,
                                Microsoft.Bot.Builder.ITurnContext turnContext,
                                Dictionary<string, string> telemetryProperties = null,
                                Dictionary<string, double> telemetryMetrics = null,
                                CancellationToken cancellationToken = default(CancellationToken))
            {
                var eventData = await FillQnAEventAsync(
                                        queryResults,
                                        turnContext,
                                        telemetryProperties,
                                        telemetryMetrics,
                                        cancellationToken)
                                    .ConfigureAwait(false);

                // Add new property
                eventData.Properties.Add("MyImportantProperty", "myImportantValue");

                // Log QnAMessage event
                TelemetryClient.TrackEvent(
                                QnATelemetryConstants.QnaMsgEvent,
                                eventData.Properties,
                                eventData.Metrics
                                );
            }

        } 
    }    
    ```

4. Modifique o bot para usar a nova classe. Em vez de criar um objeto `QnAMaker`, você criará um objeto `MyQnAMaker` em `QnABot.cs`:

    ```cs
    var qnaMaker = new MyQnAMaker(new QnAMakerEndpoint
                {
                    KnowledgeBaseId = _configuration["QnAKnowledgebaseId"],
                    EndpointKey = _configuration["QnAEndpointKey"],
                    Host = _configuration["QnAEndpointHostName"]
                },
                null,
                httpClient,
                _telemetryClient);
    ```

##### <a name="viewing-telemetry-data-logged-from-the-new-property-_myimportantproperty_"></a>Exibindo dados de telemetria registrados da nova propriedade _MyImportantProperty_

Depois de executar o bot no emulador, você pode exibir os resultados em Application Insights fazendo o seguinte:

1. Volte para o navegador que tem a exibição _Logs (Análise)_ ativa.

2. Insira a consulta em Kusto a seguir e selecione _Executar_.  Isso fornecerá uma contagem do número de vezes que a nova propriedade foi executada:

    ```SQL
    customEvents
    | where name == 'QnaMessage'
    | extend MyImportantProperty = tostring(customDimensions.MyImportantProperty)
    | summarize count() by MyImportantProperty
    ```

3. Para mostrar detalhes em vez da contagem, remova a última linha e execute novamente a consulta:

    ```SQL
    customEvents
    | where name == 'QnaMessage'
    | extend MyImportantProperty = tostring(customDimensions.MyImportantProperty)
    ```
### <a name="adding-new-events-with-custom-properties"></a>Como adicionar novos eventos com propriedades personalizadas
Se precisar registrar dados em log para um evento diferente de `QnaMessage`, você poderá criar seu próprio evento personalizado com suas próprias propriedades.  Para fazer isso, adicionaremos o código ao final da classe `MyQnAMaker` da seguinte maneira:

```CS
public class MyQnAMaker : QnAMaker
{
    ...

    // Create second event.
    var secondEventProperties = new Dictionary<string, string>();

    // Create new property for the second event.
    secondEventProperties.Add(
                        "MyImportantProperty2",
                        "myImportantValue2");

    // Log secondEventProperties event
    TelemetryClient.TrackEvent(
                    "MySecondEvent",
                    secondEventProperties);

} 
```                            
## <a name="the-application-insights-dashboard"></a>O painel do Application Insights

Sempre que você criar um recurso do Application Insights no Azure, um painel será automaticamente criado e associado a ele.  Você pode ver esse painel selecionando o botão na parte superior da sua folha do Application Insights, rotulado **Painel de Aplicativo**. 

![Link do Painel do Aplicativo](media/Application-Dashboard-Link.png)


Como alternativa, para exibir os dados, acesse o portal do Azure. À esquerda, clique em **Painel**, depois, na lista suspensa, selecione o painel que você deseja.

Lá, você verá algumas informações padrão sobre o desempenho do bot e quaisquer consultas adicionais que tenha fixado ao seu painel.


## <a name="additional-information"></a>Informações adicionais

* [Adicionar telemetria ao seu bot](bot-builder-telemetry.md)

* [O que é o Application Insights?](https://aka.ms/appinsights-overview)

* [Usar a Pesquisa no Application Insights](https://aka.ms/search-in-application-insights)

* [Criar painéis de KPI personalizados usando o Azure Application Insights](https://aka.ms/custom-kpi-dashboards-application-insights)
