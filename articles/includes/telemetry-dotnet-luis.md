---
ms.openlocfilehash: 6e97fdf4007265715e6928bae7cbc7739ba0a131
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95564207"
---

Em seguida, implementaremos a funcionalidade de telemetria em seu serviço LUIS. O serviço LUIS tem o registro em log de telemetria interno disponível para que o esforço necessário para começar a obter dados de telemetria do LUIS seja mínimo.  Se você estiver interessado em habilitar a telemetria em um bot habilitado para o QnA Maker, confira [Adicionar telemetria ao seu bot do QnAMaker](../v4sdk/bot-builder-telemetry-QnAMaker.md)

1. O parâmetro _`IBotTelemetryClient telemetryClient`_ é necessário no construtor `FlightBookingRecognizer` em `FlightBookingRecognizer.cs`:

    ```cs
    public FlightBookingRecognizer(IConfiguration configuration, IBotTelemetryClient telemetryClient)
    ```

2. Em seguida, você precisará habilitar o `telemetryClient` ao criar seu `LuisRecognizer` no construtor `FlightBookingRecognizer`. Você faz isso adicionando o `telemetryClient` como um novo _LuisRecognizerOption_:

    ```cs
    if (luisIsConfigured)
    {
        var luisApplication = new LuisApplication(
            configuration["LuisAppId"],
            configuration["LuisAPIKey"],
            "https://" + configuration["LuisAPIHostName"]);

        // Set the recognizer options depending on which endpoint version you want to use.
        // More details can be found in https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-api-v3
        var recognizerOptions = new LuisRecognizerOptionsV3(luisApplication)
        {
            TelemetryClient = telemetryClient,
        };
        _recognizer = new LuisRecognizer(recognizerOptions);
    }
    ```

Isso é tudo, agora você deve ter um bot funcional que registra dados de telemetria no Application Insights. Você pode usar o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) para executar o bot localmente. Você não deve experienciar nenhuma alteração no comportamento do bot, mas ele estará registrando informações no Application Insights. Interaja com o bot enviando várias mensagens e, na próxima seção, examinaremos os resultados da telemetria no Application Insights.

Para obter informações sobre como testar e depurar o bot, consulte os seguintes artigos:

- [Depurar um bot](../bot-service-debug-bot.md)
- [Diretrizes para testes e depuração](../v4sdk/bot-builder-testing-debugging.md)
- [Depurar com o emulador](../bot-service-debug-emulator.md)
