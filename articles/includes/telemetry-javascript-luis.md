---
ms.openlocfilehash: 23ca923209b173910b5b909b1ff74eff0a49874e
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360070"
---
Em seguida, implementaremos a funcionalidade de telemetria em seu serviço LUIS. O serviço LUIS tem o registro em log de telemetria interno disponível para que o esforço necessário para começar a obter dados de telemetria do LUIS seja mínimo.  <!---If you are interested in enabling telemetry in a QnA Maker enabled bot, see [Add telemetry to your QnAMaker bot](../v4sdk/bot-builder-telemetry-QnAMaker.md).-->

Para habilitar o cliente de telemetria em seu reconhecedor do LUIS:

1. Abra `FlightBookingRecognizer.js`

2. Passe o `telemetryClient` parâmetro para o `FlightBookingRecognizer` Construtor:

    [!code-javascript[FlightBookingRecognizer](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/dialogs/flightBookingRecognizer.js?range=7)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
        constructor(config, telemetryClient) {
    ```
    -->

3. Defina o `telemetryClient` campo do `recognizerOptions` objeto para a `telemetryClient` propriedade que é passada para o `FlightBookingRecognizer` Construtor, uma vez feito, o Construtor será exibido da seguinte maneira:

    [!code-javascript[FlightBookingRecognizer](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/dialogs/flightBookingRecognizer.js?range=9-18&highlight=6)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
     if (luisIsConfigured) {
            // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
            // More details can be found in https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-api-v3
            const recognizerOptions = {
                apiVersion: 'v3',
                telemetryClient: telemetryClient
            };
    ```
    -->

4. E, finalmente, você precisa incluir o `telemetryClient` ao criar uma instância do `FlightBookingRecognizer` no `index.js` :

    [!code-javascript[FlightBookingRecognizer](~/../botbuilder-samples/samples/javascript_nodejs/21.corebot-app-insights/index.js?range=86)]

    <!-- This is the code block that the code snippet link should point to:
    ```javascript
    const luisRecognizer = new FlightBookingRecognizer(luisConfig, telemetryClient);
    ```
    -->

É isso; Você deve ter um bot funcional que registra dados de telemetria no Application insights. Você pode usar o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) para executar o bot localmente. Você não deve experienciar nenhuma alteração no comportamento do bot, mas ele estará registrando informações no Application Insights. Interaja com o bot enviando várias mensagens e a próxima seção descreve como examinar os resultados da telemetria em Application Insights.

Para obter informações sobre como testar e depurar o bot, consulte os seguintes artigos:

* [Depurar um bot](../bot-service-debug-bot.md)
* [Diretrizes para testes e depuração](../v4sdk/bot-builder-testing-debugging.md)
* [Depurar com o emulador](../bot-service-debug-emulator.md)
