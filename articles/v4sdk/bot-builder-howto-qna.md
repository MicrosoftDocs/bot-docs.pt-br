---
title: Usar o QnA Maker para responder a perguntas | Microsoft Docs
description: Aprenda a usar o QnA Maker no seu bot.
keywords: perguntas e respostas, QnA, perguntas frequentes, middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 10/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3d488cc2bb61ef460ed45707596cb7db9e6c23e8
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999073"
---
# <a name="use-qna-maker-to-answer-questions"></a>Usar o QnA Maker para responder a perguntas

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Use o serviço QnA Maker para adicionar suporte a perguntas e respostas ao seu bot. Um dos requisitos básicos para a criação de seu próprio serviço QnA Maker é propagá-lo com perguntas e respostas. Em muitos casos, as perguntas e respostas já existirem no conteúdo, como as perguntas frequentes ou outra documentação. Outras vezes você gostaria de personalizar as respostas para perguntas de forma mais natural e conversacional.

## <a name="prerequisites"></a>Pré-requisitos
- Criar um serviço do [QnA Maker](https://www.qnamaker.ai/)
- Baixe o exemplo de QnA Maker [[C#](https://aka.ms/cs-qna) | [JavaScript](https://aka.ms/js-qna-sample)]

## <a name="create-a-qna-maker-service-and-publish-a-knowledge-base"></a>Criar um serviço do QnA Maker e publicar uma base de Conhecimento

Após a criação da conta do QnA Maker, siga as instruções para criação do [serviço do QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) e da [base de conhecimento](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base). 

Após a publicação de sua base de conhecimento, será necessário registrar os seguintes valores para conectar programaticamente seu bot à base de conhecimento.
- No site do [QnA Maker](https://www.qnamaker.ai/), selecione sua base de conhecimento.
- Com sua base de conhecimento aberta, selecione **Settings** (Configurações). Registre o valor mostrado para _service name_ (nome do serviço) como <nome_da_sua_bd>
- Role a tela para baixo até encontrar **Deployment details** (Detalhes da implantação) e registre estes valores:
   - POST /knowledgebases/<id_da_sua_base_de_conhecimento>/generateAnswer
   - Host: https://<nome_de_seu_host>.azurewebsites.net/qnamaker
   - Authorization: EndpointKey <sua_chave_de_ponto_de_extremidade>

## <a name="installing-packages"></a>Instalando pacotes

Antes que comecemos a codificar, verifique se você tem os pacotes necessários para o QnA Maker.

# <a name="ctabcs"></a>[C#](#tab/cs)

Adicione o seguinte [pacote NuGet](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) ao bot.

* `Microsoft.Bot.Builder.AI.QnA`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Os recursos do QnA Maker estão no pacote `botbuilder-ai`. Adicione este pacote ao projeto por meio do npm:

```shell
npm install --save botbuilder-ai
```

---

## <a name="using-cli-tools-to-update-your-bot-configuration"></a>Usar ferramentas de CLI para atualizar a configuração do seu .bot

Um método alternativo para obter os valores de acesso de sua base de conhecimento é usar as ferramentas de CLI do BotBuilder [qnamaker](https://aka.ms/botbuilder-tools-qnaMaker) e [msbot](https://aka.ms/botbuilder-tools-msbot-readme) para obter metadados sobre sua base de conhecimento e adicioná-los ao arquivo do seu .bot.

1. Abra um terminal ou prompt de comando e navegue até o diretório raiz do seu projeto de bot.
2. Execute `qnamaker init` para criar um arquivo de recursos do QnA Maker (**.qnamakerrc**). Você receberá uma solicitação por sua chave de assinatura do QnA Maker.
3. Execute o comando a seguir para baixar seus metadados e adicioná-los ao arquivo de configuração do seu bot.

    ```shell
    qnamaker get kb --kbId <your-kb-id> --msbot | msbot connect qna --stdin [--secret <your-secret>]
    ```
Se você tiver criptografado o arquivo de configuração, precisará fornecer sua chave secreta para atualizar o arquivo.

## <a name="using-qna-maker"></a>Usando o QnA Maker
Uma referência para o QnA Maker é adicionada ao inicializar o bot. Depois, podemos chamá-la dentro de nossa lógica do bot.

# <a name="ctabcs"></a>[C#](#tab/cs)
Abra o exemplo do QnA Maker baixado anteriormente. Modificaremos esse código conforme o necessário.
Primeiro, adicione ao `BotConfiguration.bot` as informações necessárias para acessar a sua base de conhecimento, incluindo o nome do host, chave de ponto de extremidade e a base de conhecimento (KbId). Esses são os valores que você salvou nas **Configurações** de sua base de conhecimento do QnA Maker.

```json
{
  "name": "QnABotSample",
  "services": [
    {
      "type": "endpoint",
      "name": "development",
      "endpoint": "http://localhost:3978/api/messages",
      "appId": "",
      "id": "1",
      "appPassword": ""
    },
    {
      "type": "qna",
      "name": "QnABot",
      "KbId": "<YOUR_KNOWLEDGE_BASE_ID>",
      "Hostname": "https://<YOUR_HOSTNAME>.azurewebsites.net/qnamaker",
      "EndpointKey": "<YOUR_ENDPOINT_KEY>"
    }
  ],
  "version": "2.0",
  "padlock": ""
}
```

Em seguida, criamos uma instância do QnA Maker no `Startup.cs`. Isso obtém as informações mencionadas acima do arquivo `BotConfiguration.bot`. Essas cadeias de caracteres também podem ser codificadas para testes.

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

Depois, precisamos dar ao bot esta instância do QnAMaker. Abra `QnABot.cs` e, na parte superior do arquivo, adicione o seguinte código. Se você estiver acessando sua própria base de conhecimento, altere a mensagem de _boas-vindas_ abaixo para fornecer instruções inicias úteis aos seus usuários.

```csharp
public class QnABot : IBot
{
    public static readonly string QnAMakerKey = "QnABot";
    private const string WelcomeText = @"This bot will introduce you to QnA Maker.
                                         Ask a quesiton to get started.";
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
Abra o exemplo do QnA Maker baixado anteriormente. Modificaremos esse código conforme o necessário.
Em nosso exemplo, o código de inicialização está em um arquivo **index.js**, o código para a lógica do bot está em um arquivo **bot.js** e informações adicionais de configuração estão no arquivo **qnamaker.bot**.

Depois de seguir as instruções para a criação de sua base de conhecimento e para atualizar seu arquivo **.bot**, seu arquivo **qnamaker.bot** deve incluir uma entrada de serviço para a sua base de conhecimento do QnA Maker.

```json
{
    "name": "qnamaker",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "id": "1",
            "appId": "",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages"
        },
        {
            "type": "qna",
            "name": "<YOUR_KB_NAME>",
            "kbId": "<YOUR_KNOWLEDGE_BASE_ID>",
            "endpointKey": "<YOUR_ENDPOINT_KEY>",
            "hostname": "https://<YOUR_HOSTNAME>.azurewebsites.net/qnamaker",
            "id": "221"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

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

Faça as perguntas ao seu bot para ver as respostas de seu serviço QnA Maker. Para saber mais sobre testes e publicação de seu serviço do QnA, confira o artigo do QnA Maker em [testando uma base de conhecimento](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/test-knowledge-base).

## <a name="next-steps"></a>Próximas etapas

O QnA Maker pode ser combinado com outros Serviços Cognitivos, para tornar seu bot ainda mais poderoso. A ferramenta de expedição fornece uma forma de combinar QnA com Reconhecimento Vocal (LUIS) no seu bot.

> [!div class="nextstepaction"]
> [Combinar o LUIS e os serviços QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)
