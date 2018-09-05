---
title: Usar o QnA Maker para responder a perguntas | Microsoft Docs
description: Aprenda a usar o QnA Maker no seu bot.
keywords: perguntas e respostas, QnA, perguntas frequentes, middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7dd973e2b5a151e754925e6f19c6e4f82507f745
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42906170"
---
# <a name="use-qna-maker-to-answer-questions"></a>Usar o QnA Maker para responder a perguntas


Para adicionar suporte de perguntas e respostas simples ao seu bot, você pode usar o serviço [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/home).


Um dos requisitos básicos em escrever seu próprio serviço QnA Maker é propagá-lo com perguntas e respostas. Em muitos casos, as perguntas e respostas já existirem no conteúdo, como as perguntas frequentes ou outra documentação. Outras vezes você gostaria de personalizar as respostas para perguntas de forma mais natural e conversacional. 

## <a name="create-a-qna-maker-service"></a>Criar um serviço do QnA Maker
Primeiro crie uma conta e entre no [QnA Maker](https://qnamaker.ai/). Em seguida, navegue até **Criar uma base de dados de conhecimento**. Clique em **Criar um serviço QnA** e siga as instruções para criar um serviço QnA do Azure.

![Imagem 1 para qna](media/QnA_1.png)

Você será redirecionado para [Criar QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker). Preencha o formulário e clique em **Criar**.

![Imagem 2 para qna](media/QnA_2.png)
 
Depois de criar seu serviço QnA no Portal do Azure você receberá chaves com o título do Gerenciamento de Recursos que você pode ignorar. Vá para a etapa 2 para se conectar a seu serviço QnA do Azure. Atualize a página para selecionar o serviço do Azure que você acabou de criar e digite o nome de sua base de dados de conhecimento.

![Imagem 3 para qna](media/QnA_3.png)

Clique em **Criar sua KB**. Insira suas próprias perguntas e respostas ou copie os exemplos a seguir. 

![Imagem 4 para qna](media/QnA_4.png)

Como alternativa, você pode escolher **Popular sua KB** e fornecer um arquivo ou URL. Um arquivo de origem de exemplo para gerar um serviço simples do QnA Maker está disponível [aqui](https://aka.ms/qna-tsv).

Depois de adicionar novos pares de QnA ou popular sua KB, clique em **Salvar e treinar**. Depois que você concluir, na guia **PUBLICAR**, clique em **Publicar**.

Para conectar seu serviço QnA ao seu bot, você precisará da cadeia de cadeia de caracteres de solicitação HTTP que contém a ID da base de dados conhecimento e a chave de assinatura do QnA Maker. Copie o exemplo de solicitação HTTP do resultado da publicação. 

![Imagem 5 para qna](media/QnA_5.png)

## <a name="installing-packages"></a>Instalando pacotes

Antes que comecemos a codificar, verifique se você tem os pacotes necessários para o QnA Maker.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Adicione uma referência](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) para a versão de pré-lançamento v4 dos seguintes pacotes NuGet:

* `Microsoft.Bot.Builder.Ai.QnA`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Qualquer um desses serviços pode ser adicionado ao bot usando o pacote botbuilder-ai. Adicione este pacote ao projeto por meio do npm:

* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---


## <a name="using-qna-maker"></a>Usando o QnA Maker

O QnA Maker é adicionado primeiro como o middleware. Em seguida, podemos usar os resultados na nossa lógica do bot.

# <a name="ctabcs"></a>[C#](#tab/cs)

Atualizar o método `ConfigureServices` no seu arquivo `Startup.cs` para adicionar um objeto `QnAMakerMiddleware`. Você pode configurar o bot para verificar sua base de dados de conhecimento para cada mensagem recebida de um usuário, simplesmente adicionando-o à pilha de middleware do seu bot.


```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.Ai.Qna;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System;

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(_ => Configuration);
    services.AddBot<AiBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        var endpoint = new QnAMakerEndpoint
        {
           KnowledgeBaseId = "YOUR-KB-ID",
           // Get the Host from the HTTP request example at https://www.qnamaker.ai
           // For GA services: https://<Service-Name>.azurewebsites.net/qnamaker
           // For Preview services: https://westus.api.cognitive.microsoft.com/qnamaker/v2.0           
           Host = "YOUR-HTTP-REQUEST-HOST",
           EndpointKey = "YOUR-QNA-MAKER-SUBSCRIPTION-KEY"
        };
        options.Middleware.Add(new QnAMakerMiddleware(endpoint));
    });
}
```



Edite o código no arquivo EchoBot.cs, para que `OnTurn` envie uma mensagem de fallback no caso de que o middleware do QnA Maker não envie uma resposta para a pergunta do usuário:

```csharp
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;

namespace Bot_Builder_Echo_Bot_QnA
{
    public class EchoBot : IBot
    {    
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {             
                if (!context.Responded)
                {
                    // QnA didn't send the user an answer
                    await context.SendActivity("Sorry, I couldn't find a good match in the KB.");

                }
            }
        }
    }
}
```


Consulte o [exemplo do QnA Maker](https://aka.ms/qna-cs-bot-sample) para um bot de exemplo.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Primeiro solicite/importe na classe [QnAMaker](https://github.com/Microsoft/botbuilder-js/tree/master/doc/botbuilder-ai/classes/botbuilder_ai.qnamaker.md):

```js
const { QnAMaker } = require('botbuilder-ai');
```

Crie um `QnAMaker` inicializando-o com uma cadeia de caracteres com base na solicitação HTTP para o serviço QnA. Você pode copiar um exemplo de solicitação para o serviço no [portal do QnA Maker](https://qnamaker.ai) em Configurações > Detalhes da implantação.


O formato da cadeia de caracteres varia, dependendo se o serviço QnA Maker está usando o GA ou a versão prévia do QnA Maker. Copie a solicitação HTTP de exemplo e obtenha a ID da base de dados de conhecimento, a chave de assinatura e o host para usar ao inicializar o `QnAMaker`.

<!--
**Preview**
```js
const qnaEndpointString = 
    // Replace xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx with your knowledge base ID
    "POST /knowledgebases/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/generateAnswer\r\n" + 
    "Host: https://westus.api.cognitive.microsoft.com/qnamaker/v2.0\r\n" +
    // Replace xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx with your QnAMaker subscription key
    "Ocp-Apim-Subscription-Key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\r\n"
const qna = new QnAMaker(qnaEndpointString);
```

**GA**
```js
const qnaEndpointString = 
    // Replace xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx with your knowledge base ID
    "POST /knowledgebases/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/generateAnswer\r\n" + 
    // Replace <Service-Name> to match the Azure URL where your service is hosted
    "Host: https://<Service-Name>.azurewebsites.net/qnamaker\r\n" +
    // Replace xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx with your QnAMaker subscription key
    "Authorization: EndpointKey xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\r\n"
const qna = new QnAMaker(qnaEndpointString);
```
-->
**Visualização**
```js
const qna = new QnAMaker(
    {
        knowledgeBaseId: '<KNOWLEDGE-BASE-ID>',
        endpointKey: '<QNA-SUBSCRIPTION-KEY>',
        host: 'https://westus.api.cognitive.microsoft.com/qnamaker/v2.0'
    },
    {
        // set this to true to send answers from QnA Maker
        answerBeforeNext: true
    }
);
```
**GA**
```js
const qna = new QnAMaker(
    {
        knowledgeBaseId: '<KNOWLEDGE-BASE-ID>',
        endpointKey: '<QNA-SUBSCRIPTION-KEY>',
        host: 'https://<Service-Name>.azurewebsites.net/qnamaker'
    },
    {
        answerBeforeNext: true
    }
);
```
Você pode configurar seu bot para chamar automaticamente o QnA Maker, simplesmente adicionando-o à pilha de middleware do seu bot:

```js
// Add QnA Maker as middleware
adapter.use(qna);

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        // If `!context.responded`, that means an answer wasn't found for the user's utterance.
        // In this case, we send the user a fallback message.
        if (context.activity.type === 'message' && !context.responded) {
            await context.sendActivity('No QnA Maker answers were found.');
        } else if (context.activity.type !== 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

Inicializando `answerBeforeNext` para `true` ao inicializar `QnAMaker` significa que o middleware do QnA Maker responde automaticamente se ele encontrar uma resposta, antes de acessar a lógica principal do seu bot em `processActivity`. Se em vez disso, você definir `answerBeforeNext` para `false`, o bot somente chamará o QnA Maker após toda a lógica principal do seu bot em `processActivity` ser executada, e somente se seu bot respondeu ao usuário. 

## <a name="calling-qna-maker-without-using-middleware"></a>Chamar o QnA Maker sem usar o middleware

No exemplo anterior, a instrução `adapter.use(qna);` significa que o QnA está em execução como middleware, e, portanto, responde a todas as mensagens que o bot recebe. Para obter mais controle sobre como e quando o QnA Maker é chamado, você pode chamar `qna.answer()` diretamente de dentro de sua lógica do bot, em vez de instalá-lo como uma peça de middleware.

Remova a instrução `adapter.use(qna);` e use o seguinte código ao chamar diretamente o QnA Maker.

```js
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            var handled = await qna.answer(context)
                if (!handled) {
                    await context.sendActivity(`I'm sorry. I didn't understand.`);
                }
        }
    });
});
```

Outra maneira de personalizar o QnA Maker é com `qna.generateAnswer()`. Esse método permite que você obtenha mais detalhes sobre as respostas do QnA Maker.


```js
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            // Get all the answers QnA Maker finds
            var results = await qna.generateAnswer(context.activity.text);
                if (results && results.length > 0) {
                    await context.sendActivity(results[0].answer);
                } else {
                    await context.sendActivity(`I don't know.`);
                }
    
        }
    });
});
```
---

Faça as perguntas ao seu bot para ver as respostas de seu serviço QnA Maker.

![Imagem 6 para qna](media/QnA_6.png)



## <a name="next-steps"></a>Próximas etapas

O QnA Maker pode ser combinado com outros Serviços Cognitivos, para tornar seu bot ainda mais poderoso. A ferramenta de expedição fornece uma forma de combinar QnA com Reconhecimento Vocal (LUIS) no seu bot.

> [!div class="nextstepaction"]
> [Combinar os aplicativos LUIS e os serviços QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)
