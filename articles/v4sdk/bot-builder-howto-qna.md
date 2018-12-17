---
title: Usar o QnA Maker para responder a perguntas | Microsoft Docs
description: Aprenda a usar o QnA Maker no seu bot.
keywords: perguntas e respostas, QnA, perguntas frequentes, QnA Maker
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 11/19/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0708244b9f9e4859ba069ed463cef83a0ecdf20d
ms.sourcegitcommit: b9482670285295a2af0dfbb8f4b7e543c1c10542
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53327152"
---
# <a name="use-qna-maker-to-answer-questions"></a>Usar o QnA Maker para responder a perguntas

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Use o serviço QnA Maker para adicionar suporte a perguntas e respostas ao seu bot. Um dos requisitos básicos para a criação de seu próprio serviço QnA Maker é propagá-lo com perguntas e respostas. Em muitos casos, as perguntas e respostas já existirem no conteúdo, como as perguntas frequentes ou outra documentação. Outras vezes você gostaria de personalizar as respostas para perguntas de forma mais natural e conversacional.

Neste tópico, criaremos uma base de conhecimento e usaremos em um bot.

## <a name="prerequisites"></a>Pré-requisitos
- Conta do [QnA Maker](https://www.qnamaker.ai/)
- O código neste artigo se baseia no exemplo de **QnA Maker**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/cs-qna) ou [JS](https://aka.ms/js-qna-sample).
- [Emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- Conhecimento das [Noções básicas do bot](bot-builder-basics.md), do [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/overview/overview) e do arquivo [.bot](bot-file-basics.md).

## <a name="create-a-qna-maker-service-and-publish-a-knowledge-base"></a>Criar um serviço do QnA Maker e publicar uma base de Conhecimento
1. Primeiro, você precisará criar um [serviço do QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure).
1. Em seguida, você criará uma base de conhecimento usando o arquivo `smartLightFAQ.tsv` localizado na pasta CognitiveModels do projeto. As etapas para criar, treinar e publicar sua [base de dados de conhecimento](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base) do QnA Maker são listadas na documentação do QnA Maker. Ao seguir essas etapas, nomeie a KB `qna`e use o arquivo `smartLightFAQ.tsv` para preencher sua KB.

## <a name="obtain-values-to-connect-to-your-connect-your-bot-to-the-knowledge-base"></a>Obtenha os valores para conectar seu bot à base de conhecimento
1. No site do [QnA Maker](https://www.qnamaker.ai/), selecione sua base de conhecimento.
1. Com sua base de conhecimento aberta, selecione **Settings** (Configurações). Registre o valor mostrado para _service name_ (nome do serviço) como <nome_da_sua_bd>
1. Role a tela para baixo até encontrar **Deployment details** (Detalhes da implantação) e registre estes valores:
   - POST /knowledgebases/<id_da_sua_base_de_conhecimento>/generateAnswer
   - Host: https://<nome_de_seu_host>.azurewebsites.net/qnamaker
   - Autorização: EndpointKey <sua_chave_de_ponto_de_extremidade>

## <a name="update-the-bot-file"></a>Atualize o arquivo .bot
Primeiro, adicione ao `qnamaker.bot` as informações necessárias para acessar a sua base de conhecimento, incluindo o nome do host, chave de ponto de extremidade e a base de conhecimento (KbId). Esses são os valores que você salvou nas **Configurações** de sua base de conhecimento do QnA Maker.

```json
{
  "name": "qnamaker",
  "services": [
    {
      "type": "endpoint",
      "name": "development",
      "endpoint": "http://localhost:3978/api/messages",
      "appId": "",
      "appPassword": ""
      "id": "25",
    
    },
    {
      "type": "qna",
      "name": "QnABot",
      "KbId": "<YOUR_KNOWLEDGE_BASE_ID>",
      "subscriptionKey": "<Your_Azure_Subscription_Key>", // Used when creating your QnA service.
      "endpointKey": "<Your_Recorded_Endpoint_Key>",
      "hostname": "<Your_Recorded_Hostname>",
      "id": "117"
    }
  ],
  "padlock": "",
   "version": "2.0"
}
```

# <a name="ctabcs"></a>[C#](#tab/cs)
Em seguida, inicializamos uma nova instância da classe BotService em BotServices.cs, que captura as informações do arquivo .bot. O serviço externo é configurado usando a classe BotConfiguration.

```csharp
private static BotServices InitBotServices(BotConfiguration config)
{
    var qnaServices = new Dictionary<string, QnAMaker>();
    foreach (var service in config.Services)
    {
        switch (service.Type)
        {
            case ServiceTypes.QnA:
            {
                // Create a QnA Maker that is initialized and suitable for passing
                // into the IBot-derived class (QnABot).
                var qna = (QnAMakerService)service;
                if (qna == null)
                {
                    throw new InvalidOperationException("The QnA service is not configured correctly in your '.bot' file.");
                }

                if (string.IsNullOrWhiteSpace(qna.KbId))
                {
                    throw new InvalidOperationException("The QnA KnowledgeBaseId ('kbId') is required to run this sample. Please update your '.bot' file.");
                }

                if (string.IsNullOrWhiteSpace(qna.EndpointKey))
                {
                    throw new InvalidOperationException("The QnA EndpointKey ('endpointKey') is required to run this sample. Please update your '.bot' file.");
                }

                if (string.IsNullOrWhiteSpace(qna.Hostname))
                {
                    throw new InvalidOperationException("The QnA Host ('hostname') is required to run this sample. Please update your '.bot' file.");
                }

                var qnaEndpoint = new QnAMakerEndpoint()
                {
                    KnowledgeBaseId = qna.KbId,
                    EndpointKey = qna.EndpointKey,
                    Host = qna.Hostname,
                };

                var qnaMaker = new QnAMaker(qnaEndpoint);
                qnaServices.Add(qna.Name, qnaMaker);
                break;
            }
        }
    }
    var connectedServices = new BotServices(qnaServices);
    return connectedServices;
}
```

Em seguida, em QnABot.cs, fornecemos ao bot essa instância do QnAMaker. Se você estiver acessando sua própria base de conhecimento, altere a mensagem de _boas-vindas_ abaixo para fornecer instruções inicias úteis aos seus usuários.

```csharp
public class QnABot : IBot
{
    public static readonly string QnAMakerKey = "QnABot";
    private const string WelcomeText = @"This bot will introduce you to QnA Maker.
                                         Ask a question to get started.";
    private readonly BotServices _services;
    public QnABot(BotServices services)
    {
        _services = services ?? throw new System.ArgumentNullException(nameof(services));
        Console.WriteLine($"{_services}");
        if (!_services.QnAServices.ContainsKey(QnAMakerKey))
        {
            throw new System.ArgumentException($"Invalid configuration. Please check your '.bot' file for a QnA service named '{QnAMakerKey}'.");
        }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Em nosso exemplo, o código de inicialização está em um arquivo **index.js**, o código para a lógica do bot está em um arquivo **bot.js** e informações adicionais de configuração estão no arquivo **qnamaker.bot**.

No arquivo **index.js**, lemos as informações de configuração para gerar o serviço QnA Maker e inicializar o bot.

Atualize o valor de `QNA_CONFIGURATION` com o nome da sua base de dados, como ele aparece no arquivo de configuração.

```js
// QnA Maker knowledge base name as specified in .bot file.
const QNA_CONFIGURATION = '<YOUR_KB_NAME>';

// Get endpoint and QnA Maker configurations by service name.
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);
const qnaConfig = botConfig.findServiceByNameOrId(QNA_CONFIGURATION);

// Map the contents to the required format for `QnAMaker`.
const qnaEndpointSettings = {
    knowledgeBaseId: qnaConfig.kbId,
    endpointKey: qnaConfig.endpointKey,
    host: qnaConfig.hostname
};

// Create adapter...

// Create the QnAMakerBot.
let bot;
try {
    bot = new QnAMakerBot(qnaEndpointSettings, {});
} catch (err) {
    console.error(`[botInitializationError]: ${ err }`);
    process.exit();
}
```

Em seguida, criamos o servidor HTTP e escutamos as solicitações de entrada, as quais gerarão chamadas para a lógica do nosso bot.

```js
// Create HTTP server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }.`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator.`);
    console.log(`\nTo talk to your bot, open qnamaker.bot file in the emulator.`);
});

// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (turnContext) => {
        await bot.onTurn(turnContext);
    });
});
```

---

## <a name="calling-qna-maker-from-your-bot"></a>Chamar o QnA Maker de seu bot

# <a name="ctabcs"></a>[C#](#tab/cs)

Quando seu bot precisar de uma resposta do QnAMaker, chame `GetAnswersAsync()` do código do seu bot para obter a resposta apropriada com base no contexto atual. Se você estiver acessando sua própria base de conhecimento, altere a mensagem de _sem respostas_ abaixo para fornecer instruções úteis aos seus usuários.

```csharp
// Check QnA Maker model
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext);
if (response != null && response.Length > 0)
{
    await turnContext.SendActivityAsync(response[0].Answer, cancellationToken: cancellationToken);
}
else
{
    var msg = @"No QnA Maker answers were found. This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs.
                To see QnA Maker in action, ask the bot questions like 'Why won't it turn on?' or 'I need help'.";
    await turnContext.SendActivityAsync(msg, cancellationToken: cancellationToken);
}

    /// ...
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

No arquivo **bot.js**, passamos a entrada do usuário para o método `generateAnswer` do serviço do QnA Maker a fim de obter respostas da base de conhecimento. Se você estiver acessando sua própria base de conhecimento, altere a mensagem de _sem respostas_ e _boas-vindas_ abaixo para fornecer instruções úteis aos seus usuários.

```javascript
const { ActivityTypes, TurnContext } = require('botbuilder');
const { QnAMaker, QnAMakerEndpoint, QnAMakerOptions } = require('botbuilder-ai');

/**
 * A simple bot that responds to utterances with answers from QnA Maker.
 * If an answer is not found for an utterance, the bot responds with help.
 */
class QnAMakerBot {
    /**
     * The QnAMakerBot constructor requires one argument (`endpoint`) which is used to create an instance of `QnAMaker`.
     * @param {QnAMakerEndpoint} endpoint The basic configuration needed to call QnA Maker. In this sample the configuration is retrieved from the .bot file.
     * @param {QnAMakerOptions} config An optional parameter that contains additional settings for configuring a `QnAMaker` when calling the service.
     */
    constructor(endpoint, qnaOptions) {
        this.qnaMaker = new QnAMaker(endpoint, qnaOptions);
    }

    /**
     * Every conversation turn for our QnAMakerBot will call this method.
     * @param {TurnContext} turnContext Contains all the data needed for processing the conversation turn.
     */
    async onTurn(turnContext) {
        // By checking the incoming Activity type, the bot only calls QnA Maker in appropriate cases.
        if (turnContext.activity.type === ActivityTypes.Message) {
            // Perform a call to the QnA Maker service to retrieve matching Question and Answer pairs.
            const qnaResults = await this.qnaMaker.generateAnswer(turnContext.activity.text);

            // If an answer was received from QnA Maker, send the answer back to the user.
            if (qnaResults[0]) {
                await turnContext.sendActivity(qnaResults[0].answer);

            // If no answers were returned from QnA Maker, reply with help.
            } else {
                await turnContext.sendActivity('No QnA Maker answers were found. This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs. To see QnA Maker in action, ask the bot questions like "Why won\'t it turn on?" or "I need help."');
            }

        // If the Activity is a ConversationUpdate, send a greeting message to the user.
        } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate &&
                   turnContext.activity.recipient.id !== turnContext.activity.membersAdded[0].id) {
            await turnContext.sendActivity('Welcome to the QnA Maker sample! Ask me a question and I will try to answer it.');

        // Respond to all other Activity types.
        } else if (turnContext.activity.type !== ActivityTypes.ConversationUpdate) {
            await turnContext.sendActivity(`[${ turnContext.activity.type }]-type activity detected.`);
        }
    }
}

module.exports.QnAMakerBot = QnAMakerBot;
```

---

## <a name="test-the-bot"></a>Testar o bot

Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo Leiame para o exemplo em [C# ](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/11.qnamaker) ou [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/11.qnamaker/README.md).

No emulador, envie mensagem ao bot, conforme mostrado abaixo.

![exemplo de qna de teste](~/media/emulator-v4/qna-test-bot.png)


## <a name="next-steps"></a>Próximas etapas

O QnA Maker pode ser combinado com outros Serviços Cognitivos, para tornar seu bot ainda mais poderoso. A ferramenta de expedição fornece uma forma de combinar QnA com Reconhecimento Vocal (LUIS) no seu bot.

> [!div class="nextstepaction"]
> [Combinar o LUIS e os serviços QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)
