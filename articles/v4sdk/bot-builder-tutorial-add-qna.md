---
title: Tutorial do Serviço de Bot do Azure para que o bot responda a perguntas | Microsoft Docs
description: Tutorial para usar o QnA Maker em seu bot para responder a perguntas.
keywords: QnA Maker, pergunta e resposta, base de dados de conhecimento
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: tutorial
ms.service: bot-service
ms.subservice: sdk
ms.date: 01/15/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b1c34531ee60b2ce9037f42e4f5a7093501cf83a
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360937"
---
# <a name="tutorial-use-qna-maker-in-your-bot-to-answer-questions"></a>Tutorial: usar o QnA Maker em seu bot para responder a perguntas

Para adicionar suporte de perguntas e respostas ao seu bot, você pode usar o serviço QnA Maker e a base de dados de conhecimento. Ao criar sua base de dados de conhecimento, você pode propagá-la com perguntas e respostas.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um serviço do QnA Maker e uma base de dados de conhecimento
> * Adicionar informações da base de dados de conhecimento ao seu arquivo .bot
> * Atualizar seu bot para consultar a base de dados de conhecimento
> * Republicar seu bot

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* O bot criado no [tutorial anterior](bot-builder-tutorial-basic-deploy.md). Vamos adicionar um recurso de pergunta e resposta ao bot.
* É útil ter alguma familiaridade com o QnA Maker. Usaremos o portal QnA Maker para criar, treinar e publicar a base de dados de conhecimento que será usada com o bot.

Você já deve ter os pré-requisitos do tutorial anterior:

[!INCLUDE [deployment prerequisites snippet](~/includes/deploy/snippet-prerequisite.md)]

## <a name="sign-in-to-qna-maker-portal"></a>Entre no portal do QnA Maker

<!-- This and the next step are close duplicates of what's in the QnA How-To -->

Entre no [portal do QnA Maker](https://qnamaker.ai/) com suas credenciais do Azure.

## <a name="create-a-qna-maker-service-and-knowledge-base"></a>Criar um serviço do QnA Maker e uma base de dados de conhecimento

Vamos importar uma definição da base de dados de conhecimento existente do exemplo do QnA Maker no repositório [BotBuilder/Microsoft-Samples](https://github.com/Microsoft/BotBuilder-Samples).

1. Clone ou copie o repositório de exemplos para seu computador.
1. No portal do QnA Maker, **Crie uma base de dados de conhecimento**.
   1. Se necessário, crie um serviço QnA. (Você pode usar um serviço QnA Maker existente ou criar um novo para este tutorial.) Para obter mais instruções sobre o QnA Maker, consulte [Criar um serviço QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) e [Criar, treinar e publicar sua base de dados de conhecimento do QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base).
   1. Conecte seu serviço QnA à base de dados de conhecimento.
   1. Nomeie sua base de dados de conhecimento.
   1. Para preencher sua base de dados de conhecimento, use o arquivo **BotBuilder-Samples\samples\csharp_dotnetcore\11.qnamaker\CognitiveModels\smartLightFAQ.tsv** a partir do repositório de exemplos.
   1. Clique em **Criar sua Base de Dados de Conhecimento** para criar a base de dados de conhecimento.
1. **Salve e treine** sua base de dados de conhecimento.
1. **Publique** sua base de dados de conhecimento.

   A base de dados de conhecimento já está pronta para usar seu bot. Registre a ID da base de dados de conhecimento, a chave de ponto de extremidade e o nome do host. Você precisará desses dados para a próxima etapa.

## <a name="add-knowledge-base-information-to-your-bot-file"></a>Adicionar informações da base de dados de conhecimento ao seu arquivo .bot

Adicione as informações necessárias ao seu arquivo .bot para acessar sua base de dados de conhecimento.

1. Abra o arquivo .bot em um editor.
1. Adicione um elemento `qna` à matriz `services`.

    ```json
    {
        "type": "qna",
        "name": "<your-knowledge-base-name>",
        "kbId": "<your-knowledge-base-id>",
        "hostname": "<your-qna-service-hostname>",
        "endpointKey": "<your-knowledge-base-endpoint-key>",
        "subscriptionKey": "<your-azure-subscription-key>",
        "id": "<a-unique-id>"
    }
    ```

    | Campo | Valor |
    |:----|:----|
    | Tipo | Deve ser `qna`. Isso indica que essa entrada de serviço descreve uma base de dados de conhecimento do QnA. |
    | Nome | O nome atribuído à sua base de dados de conhecimento. |
    | kbId | A ID da base de dados de conhecimento que o portal QnA Maker gerou para você. |
    | hostname | A URL do host que o portal QnA Maker gerou. Use a URL completa, começando com `https://` e terminando com `/qnamaker`. |
    | endpointKey | A chave do ponto de extremidade que o portal QnA Maker gerou para você. |
    | subscriptionKey | A ID da assinatura que você usou ao criar o serviço QnA Maker no Azure. |
    | ID | Uma ID exclusiva que ainda não utilizada para um dos serviços listados no arquivo .bot, como "201". |

1. Salve suas edições.

## <a name="update-your-bot-to-query-the-knowledge-base"></a>Atualizar seu bot para consultar a base de dados de conhecimento

Atualize seu código de inicialização para carregar as informações de serviço para sua base de dados de conhecimento.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. Adicione o pacote NuGet **Microsoft.Bot.Builder.AI.QnA** ao projeto.
1. Renomeie a classe que implementa **IBot** para `QnaBot`.
1. Renomeie a classe que contém os acessadores do bot para `QnaBotAccessors`.
1. No arquivo **Startup.cs**, adicione essas referências de namespace.
    ```csharp
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Bot.Builder.AI.QnA;
    using Microsoft.Bot.Builder.Integration;
    ```
1. Além disso, modifique o método **ConfigureServices** para inicializar e registrar as bases de dados de conhecimento definidas no arquivo **.bot**. Observe que essas primeiras linhas foram movidas do corpo da chamada `services.AddBot<QnaBot>(options =>` para vir antes dela.
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        var secretKey = Configuration.GetSection("botFileSecret")?.Value;
        var botFilePath = Configuration.GetSection("botFilePath")?.Value;

        // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
        var botConfig = BotConfiguration.Load(botFilePath ?? @".\jfEchoBot.bot", secretKey);
        services.AddSingleton(sp => botConfig ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

        // Initialize the QnA knowledge bases for the bot.
        services.AddSingleton(sp => {
            var qnaServices = new List<QnAMaker>();
            foreach (var qnaService in botConfig.Services.OfType<QnAMakerService>())
            {
                qnaServices.Add(new QnAMaker(qnaService));
            }
            return qnaServices;
        });

        services.AddBot<QnaBot>(options =>
        {
            // Retrieve current endpoint.
            // ...
        });

        // Create and register state accessors.
        // ...
    }
    ```
1. No arquivo **QnaBot.cs**, adicione essas referências de namespace.
    ```csharp
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.AI.QnA;
    ```
1. Adicione uma propriedade `_qnaServices` e inicialize-a no construtor do bot.
    ```csharp
    private readonly List<QnAMaker> _qnaServices;

    /// ...
    public QnaBot(QnaBotAccessors accessors, List<QnAMaker> qnaServices, ILoggerFactory loggerFactory)
    {
        // ...
        _qnaServices = qnaServices;
    }
    ```
1. Modifique o manipulador de turnos para consultar as bases de dados de conhecimento registradas em relação à entrada do usuário. Quando seu bot precisar de uma resposta do QnAMaker, chame `GetAnswersAsync` do código do seu bot para obter a resposta apropriada com base no contexto atual. Se você estiver acessando sua própria base de conhecimento, altere a mensagem de _sem respostas_ abaixo para fornecer instruções úteis aos seus usuários.
    ```csharp
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            foreach(var qnaService in _qnaServices)
            {
                var response = await qnaService.GetAnswersAsync(turnContext);
                if (response != null && response.Length > 0)
                {
                    await turnContext.SendActivityAsync(
                        response[0].Answer,
                        cancellationToken: cancellationToken);
                    return;
                }
            }

            var msg = "No QnA Maker answers were found. This example uses a QnA Maker knowledge base that " +
                "focuses on smart light bulbs. Ask the bot questions like 'Why won't it turn on?' or 'I need help'.";

            await turnContext.SendActivityAsync(msg, cancellationToken: cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
        }
    }
    ```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. Abra um terminal ou um prompt de comando e navegue até o diretório raiz de seu projeto.
1. Adicione o pacote npm **botbuilder-ai** ao seu projeto.
    ```shell
    npm i botbuilder-ai
    ```
1. No arquivo **index.js**, adicione essa instrução require.
    ```javascript
    const { QnAMaker } = require('botbuilder-ai');
    ```
1. Leia as informações de configuração para gerar os serviços QnA Maker.
    ```javascript
    // Read bot configuration from .bot file.
    // ...

    // Initialize the QnA knowledge bases for the bot.
    // Assume each QnA entry in the .bot file is well defined.
    const qnaServices = [];
    botConfig.services.forEach(s => {
        if (s.type == 'qna') {
            const endpoint = {
                knowledgeBaseId: s.kbId,
                endpointKey: s.endpointKey,
                host: s.hostname
            };
            const options = {};
            qnaServices.push(new QnAMaker(endpoint, options));
        }
    });

    // Get bot endpoint configuration by service name
    // ...
    ```
1. Atualize a construção de bot para passar nos serviços QnA.
    ```javascript
    // Create the bot.
    const myBot = new MyBot(qnaServices);
    ```
1. No arquivo **bot.js**, adicione um construtor.
    ```javascript
    constructor(qnaServices) {
        this.qnaServices = qnaServices;
    }
    ```
1. Além disso, atualize seu manipulador de turnos para consultar suas bases de dados de conhecimento para obter uma resposta.
    ```javascript
    async onTurn(turnContext) {
        if (turnContext.activity.type === ActivityTypes.Message) {
            for (let i = 0; i < this.qnaServices.length; i++) {
                // Perform a call to the QnA Maker service to retrieve matching Question and Answer pairs.
                const qnaResults = await this.qnaServices[i].getAnswers(turnContext);

                // If an answer was received from QnA Maker, send the answer back to the user and exit.
                if (qnaResults[0]) {
                    await turnContext.sendActivity(qnaResults[0].answer);
                    return;
                }
            }
            // If no answers were returned from QnA Maker, reply with help.
            await turnContext.sendActivity('No QnA Maker answers were found. '
                + 'This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs. '
                + `Ask the bot questions like "Why won't it turn on?" or "I need help."`);
        } else {
            await turnContext.sendActivity(`[${ turnContext.activity.type } event detected]`);
        }
    }
    ```

---

### <a name="test-the-bot-locally"></a>Testar o bot localmente

Neste ponto, seu bot deve ser capaz de responder a algumas perguntas. Execute o bot localmente e abra-o no Emulador.

![exemplo de qna de teste](~/media/emulator-v4/qna-test-bot.png)

## <a name="re-publish-your-bot"></a>Republicar seu bot

Agora podemos republicar seu bot.

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish.md)]

### <a name="test-the-published-bot"></a>Testar o bot publicado

Depois de publicar o bot, dê ao Azure, um ou dois minutos para atualizar e iniciar o bot.

1. Usar o Emulador para testar o ponto de extremidade de produção do bot ou usar o portal do Azure para testar o bot no WebChat.

   Em ambos os casos, você deve ver o mesmo comportamento de quando o ponto de extremidade foi testado localmente.

## <a name="clean-up-resources"></a>Limpar recursos

<!-- In the first tutorial, we should tell them to use a new resource group, so that it is easy to clean up resources. We should also mention in this step in the first tutorial not to clean up resources if they are continuing with the sequence. -->

Se você não quiser continuar usando este aplicativo, exclua os recursos associados seguindo estas etapas:

1. No portal do Azure, abra o grupo de recursos para seu bot.
1. Clique em **Excluir grupo de recursos** para excluir o grupo de recursos e todos os recursos que ele contém.
1. No painel de confirmação, insira o nome do grupo de recursos e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como adicionar recursos ao seu bot, consulte os artigos na seção instruções de desenvolvimento.
> [!div class="nextstepaction"]
> [Botão Próximas etapas](bot-builder-howto-send-messages.md)
