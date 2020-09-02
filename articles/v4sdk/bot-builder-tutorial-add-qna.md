---
title: Tutorial do Serviço de Bot do Azure para que o bot responda a perguntas – Serviço de Bot
description: Saiba como adicionar suporte a perguntas e respostas a bots. Consulte como usar QnA Maker e uma base de dados de conhecimento com um bot para que o bot possa responder a perguntas.
keywords: QnA Maker, pergunta e resposta, base de dados de conhecimento
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: tutorial
ms.service: bot-service
ms.date: 03/23/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4780fb3ad893518c90fbd2d3de3e9c32eacb38a2
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89359969"
---
# <a name="tutorial-use-qna-maker-in-your-bot-to-answer-questions"></a>Tutorial: usar o QnA Maker em seu bot para responder a perguntas

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Para adicionar suporte de perguntas e respostas ao seu bot, você pode usar o serviço QnA Maker e a base de dados de conhecimento. Ao criar sua base de dados de conhecimento, você pode propagá-la com perguntas e respostas.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um serviço do QnA Maker e uma base de dados de conhecimento
> * Adicionar informações da base de conhecimento ao seu arquivo de configuração
> * Atualizar seu bot para consultar a base de dados de conhecimento
> * Republique seu bot

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* O bot criado no [tutorial anterior](bot-builder-tutorial-basic-deploy.md). Vamos adicionar um recurso de pergunta e resposta ao bot.
* É útil ter alguma familiaridade com o [QnA Maker](https://qnamaker.ai/). Usaremos o portal QnA Maker para criar, treinar e publicar a base de dados de conhecimento que será usada com o bot.
* Familiaridade com a [criação de bot de QnA](https://aka.ms/azure-create-qna) usando o Serviço de Bot do Azure.

Você também já deve ter os pré-requisitos do tutorial anterior.

## <a name="sign-in-to-qna-maker-portal"></a>Entre no portal do QnA Maker

<!-- This and the next step are close duplicates of what's in the QnA How-To -->

Entre no [portal do QnA Maker](https://qnamaker.ai/) com suas credenciais do Azure.

## <a name="create-a-qna-maker-service-and-knowledge-base"></a>Criar um serviço do QnA Maker e uma base de dados de conhecimento

Vamos importar uma definição da base de dados de conhecimento existente do exemplo do QnA Maker no repositório [BotBuilder/Microsoft-Samples](https://github.com/Microsoft/BotBuilder-Samples).

1. Clone ou copie o repositório de exemplos para seu computador.
1. No portal do QnA Maker, **crie uma base de conhecimento**.
   1. Se necessário, crie um serviço QnA. (Você pode usar um serviço QnA Maker existente ou criar um novo para este tutorial.) Para obter mais instruções sobre o QnA Maker, consulte [Criar um serviço QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) e [Criar, treinar e publicar sua base de dados de conhecimento do QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base).
   1. Conecte seu serviço QnA à base de dados de conhecimento.
   1. Nomeie sua base de dados de conhecimento.
   1. Para preencher sua base de dados de conhecimento, use o arquivo `BotBuilder-Samples\samples\csharp_dotnetcore\11.qnamaker\CognitiveModels\smartLightFAQ.tsv` do repositório de exemplos. Se você tiver baixado os exemplos, atualize o arquivo *smartLightFAQ.tsv* do seu computador.
   1. Clique em **Criar sua Base de Dados de Conhecimento** para criar a base de dados de conhecimento.
1. **Salve e treine** sua base de dados de conhecimento.
1. **Publique** sua base de dados de conhecimento.

Depois que seu aplicativo do QnA Maker for publicado, selecione a guia **CONFIGURAÇÕES** e role para baixo até os *Detalhes da implantação*. Copie os valores a seguir da solicitação HTTP de exemplo, *Postman*.

```text
POST /knowledgebases/<knowledge-base-id>/generateAnswer
Host: <your-hostname>  // NOTE - this is a URL ending in /qnamaker.
Authorization: EndpointKey <qna-maker-resource-key>
```

A cadeia de caracteres da URL completa do seu nome do host será semelhante a "https://< >.azure.net/qnamaker".

Esses valores serão usados no seu arquivo `appsettings.json` ou `.env`, na próxima etapa.

A base de dados de conhecimento já está pronta para usar seu bot.

## <a name="add-knowledge-base-information-to-your-bot"></a>Adicionar informações da base de dados de conhecimento ao seu bot

A partir do bot framework v4.3, o Azure não fornece mais um arquivo .bot como parte do código-fonte de bot baixado. Use as seguintes instruções para conectar seu bot CSharp, JavaScript ou Python à base de dados de conhecimento.

# <a name="c"></a>[C#](#tab/csharp)

Adicione os seguintes valores ao arquivo appsetting.json:

```json
{
  "MicrosoftAppId": "",
  "MicrosoftAppPassword": "",
  "ScmType": "None",

  "QnAKnowledgebaseId": "knowledge-base-id",
  "QnAAuthKey": "qna-maker-resource-key",
  "QnAEndpointHostName": "your-hostname" // This is a URL ending in /qnamaker
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Adicione os seguintes valores ao arquivo .env:

```text
MicrosoftAppId=""
MicrosoftAppPassword=""
ScmType=None

QnAKnowledgebaseId="knowledge-base-id"
QnAAuthKey="qna-maker-resource-key"
QnAEndpointHostName="your-hostname" // This is a URL ending in /qnamaker
```

# <a name="python"></a>[Python](#tab/python)

Adicione os seguintes valores ao arquivo `config.py`:

```python
class DefaultConfig:
    """ Bot Configuration """
    PORT = 3978
    APP_ID = os.environ.get("MicrosoftAppId", "")
    APP_PASSWORD = os.environ.get("MicrosoftAppPassword", "")

    QNA_KNOWLEDGEBASE_ID = os.environ.get("QnAKnowledgebaseId", "")
    QNA_ENDPOINT_KEY = os.environ.get("QnAEndpointKey", "")
    QNA_ENDPOINT_HOST = os.environ.get("QnAEndpointHostName", "")

```

---

| Campo | Valor |
|:----|:----|
| QnAKnowledgebaseId | A ID da base de dados de conhecimento que o portal QnA Maker gerou para você. |
| QnAAuthKey (QnAEndpointKey em Python)  | A chave do ponto de extremidade que o portal QnA Maker gerou para você. |
| QnAEndpointHostName | A URL do host que o portal QnA Maker gerou. Use a URL completa, começando com `https://` e terminando com `/qnamaker`. A cadeia de caracteres de URL completa será semelhante a "https://< >.azure.net/qnamaker". |

Agora salve suas edições.

## <a name="update-your-bot-to-query-the-knowledge-base"></a>Atualizar seu bot para consultar a base de dados de conhecimento

Atualize seu código de inicialização para carregar as informações de serviço para sua base de dados de conhecimento.

# <a name="c"></a>[C#](#tab/csharp)

1. Adicione o pacote NuGet **Microsoft.Bot.Builder.AI.QnA** ao projeto.

   Você pode fazer isso por meio do Gerenciador de Pacotes NuGet ou da linha de comando:

   ```cmd
   dotnet add package Microsoft.Bot.Builder.AI.QnA
   ```

   Para obter mais informações sobre o NuGet, consulte a [Documentação do NuGet](https://docs.microsoft.com/nuget/#pivot=start&panel=start-all).

1. No arquivo **Startup.cs**, adicione essa referência de namespace.

   **Startup.cs**

   ```csharp
   using Microsoft.Bot.Builder.AI.QnA;
   ```

1. E modifique o método _ConfigureServices_ para criar um QnAMakerEndpoint que se conecte à base de conhecimento definida no arquivo **appsettings.json**.

   **Startup.cs**

   ```csharp
   // Create QnAMaker endpoint as a singleton
   services.AddSingleton(new QnAMakerEndpoint
   {
      KnowledgeBaseId = Configuration.GetValue<string>($"QnAKnowledgebaseId"),
      EndpointKey = Configuration.GetValue<string>($"QnAAuthKey"),
      Host = Configuration.GetValue<string>($"QnAEndpointHostName")
    });

   ```

1. No arquivo **EchoBot.cs**, adicione essas referências de namespace.

   **Bots\\EchoBot.cs**

   ```csharp
   using System.Linq;
   using Microsoft.Bot.Builder.AI.QnA;
   ```

1. Adicione um conector `EchoBotQnA` e inicialize-o no construtor do bot.

   **EchoBot.cs**

   ```csharp
   public QnAMaker EchoBotQnA { get; private set; }
   public EchoBot(QnAMakerEndpoint endpoint)
   {
      // connects to QnA Maker endpoint for each turn
      EchoBotQnA = new QnAMaker(endpoint);
   }
   ```

1. Abaixo do método _OnMembersAddedAsync( )_ , crie o método _AccessQnAMaker( )_ adicionando o seguinte código:

   **EchoBot.cs**

   ```csharp
   private async Task AccessQnAMaker(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
   {
      var results = await EchoBotQnA.GetAnswersAsync(turnContext);
      if (results.Any())
      {
         await turnContext.SendActivityAsync(MessageFactory.Text("QnA Maker Returned: " + results.First().Answer), cancellationToken);
      }
      else
      {
         await turnContext.SendActivityAsync(MessageFactory.Text("Sorry, could not find an answer in the Q and A system."), cancellationToken);
      }
   }
   ```

1. Agora dentro de _OnMessageActivityAsync( )_ , chame o novo método _AccessQnAMaker( )_ da seguinte maneira:

   **EchoBot.cs**

   ```csharp
   protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
   {
      await turnContext.SendActivityAsync(MessageFactory.Text($"Echo: {turnContext.Activity.Text}"), cancellationToken);

      await AccessQnAMaker(turnContext, cancellationToken);
   }
   ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Abra um terminal ou um prompt de comando e navegue até o diretório raiz de seu projeto.
1. Adicione o pacote npm **botbuilder-ai** ao seu projeto.

   ```shell
   npm i botbuilder-ai
   ```

1. Em **index.js**, após a seção // Criar Adaptador, adicione o seguinte código para ler as informações de configuração do arquivo .env necessárias para gerar os serviços do QnA Maker.

   **index.js**

   ```javascript
   // Map knowledge base endpoint values from .env file into the required format for `QnAMaker`.
   const configuration = {
      knowledgeBaseId: process.env.QnAKnowledgebaseId,
      endpointKey: process.env.QnAAuthKey,
      host: process.env.QnAEndpointHostName
   };

   ```

1. Atualize a construção do bot para passar as informações de configuração de serviços de QnA.

   **index.js**

   ```javascript
   // Create the main dialog.
   const myBot = new MyBot(configuration, {});
   ```

1. No arquivo **bot.js**, adicione esse requisito para o QnA Maker

   **bot.js**

   ```javascript
   const { QnAMaker } = require('botbuilder-ai');
   ```

1. Modifique o construtor para agora receber os parâmetros de configuração necessários para criar um conector QnA Maker e gerar um erro, caso esses parâmetros não sejam fornecidos.

   **bot.js**

   ```javascript
      class MyBot extends ActivityHandler {
         constructor(configuration, qnaOptions) {
            super();
            if (!configuration) throw new Error('[QnaMakerBot]: Missing parameter. configuration is required');
            // now create a qnaMaker connector.
            this.qnaMaker = new QnAMaker(configuration, qnaOptions);
   ```

1. Por fim, atualize sua função `onMessage` para consultar suas bases de dados de conhecimento para obter uma resposta. Passe cada entrada do usuário à sua base de dados de conhecimento do QnA Maker e retorne a primeira resposta do QnA Maker para o usuário.

    **bot.js**

    ```javascript
    this.onMessage(async (context, next) => {
        // send user input to QnA Maker.
        const qnaResults = await this.qnaMaker.getAnswers(context);

        // If an answer was received from QnA Maker, send the answer back to the user.
        if (qnaResults[0]) {
            await context.sendActivity(`QnAMaker returned response: ' ${ qnaResults[0].answer}`);
        }
        else {
            // If no answers were returned from QnA Maker, reply with help.
            await context.sendActivity('No QnA Maker response was returned.'
                + 'This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs. '
                + `Ask the bot questions like "Why won't it turn on?" or "I need help."`);
        }
        await next();
    });
    ```

# <a name="python"></a>[Python](#tab/python)

1. Certifique-se de que você instalou os pacotes conforme descrito no arquivo LEIAME do repositório de exemplos.
1. Adicione a referência `botbuilder-ai` ao arquivo `requirements.txt`, conforme mostrado abaixo.

   **requirements.txt**
   <!-- Removed version numbers -->
   ```text
      botbuilder-core
      botbuilder-ai
      flask
   ```

   Observe que as versões podem variar.

1. No arquivo `app.py`, modifique a criação da instância de bot, conforme mostrado abaixo.

   **app.py**

   ```python
   # Create the bot
   BOT = EchoBot(CONFIG)
   ```

1. No arquivo `bot.py`, importe `QnAMaker` e `QnAMakerEndpoint`; importe também `Config`, conforme mostrado abaixo.

   **bot.py**

   ```python
   from flask import Config

   from botbuilder.ai.qna import QnAMaker, QnAMakerEndpoint
   from botbuilder.core import ActivityHandler, MessageFactory, TurnContext
   from botbuilder.schema import ChannelAccount
   ```

1. Adicione uma função __init__ para instanciar um objeto `qna-maker`. usando os parâmetros de configuração fornecidos no arquivo `config.py`.

   **bot.py**

   ```python
   def __init__(self, config: Config):
      self.qna_maker = QnAMaker(
         QnAMakerEndpoint(
            knowledge_base_id=config["QNA_KNOWLEDGEBASE_ID"],
            endpoint_key=config["QNA_ENDPOINT_KEY"],
            host=config["QNA_ENDPOINT_HOST"],
      )
   )

   ```

1. Atualize `on_message_activity` para consultar sua base de dados de conhecimento e obter uma resposta. Passe cada entrada do usuário para sua base de dados de conhecimento do QnA Maker e retorne a primeira resposta do QnA Maker ao usuário.

   **bot.py**

   ```python
   async def on_message_activity(self, turn_context: TurnContext):
      # The actual call to the QnA Maker service.
      response = await self.qna_maker.get_answers(turn_context)
      if response and len(response) > 0:
         await turn_context.send_activity(MessageFactory.text(response[0].answer))
      else:
         await turn_context.send_activity("No QnA Maker answers were found.")

   ```

1. Opcionalmente, atualize a mensagem de boas-vindas em `on_members_added_activity`, por exemplo:

   **bot.py**

   ```python
   await turn_context.send_activity("Hello and welcome to QnA!")
   ```

---

### <a name="test-the-bot-locally"></a>Testar o bot localmente

Neste ponto, seu bot deve ser capaz de responder a algumas perguntas. Execute o bot localmente e abra-o no Emulador.

![exemplo de qna de teste](./media/qna-test-bot.png)

## <a name="republish-your-bot"></a>Republique seu bot

Agora você pode republicar seu bot de volta no Azure. Você precisa compactar a pasta do projeto e, em seguida, executar o comando para implantar o bot no Azure. Para obter detalhes, veja o artigo [Implantar um bot](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp).

### <a name="zip-your-project-folder"></a>Compactar sua pasta de projeto

[!INCLUDE [zip up code](~/includes/deploy/snippet-zip-code.md)]

<!-- > [!IMPORTANT]
> Before creating a zip of your project files, make sure that you are _in_ the correct folder.
> - For C# bots, it is the folder that has the .csproj file.
> - For JS bots, it is the folder that has the app.js or index.js file.
> - For Python bots, it is the folder that has the app.py file.
>
> Select all the files and zip them up while in that folder, then run the command while still in that folder.
>
> If your root folder location is incorrect, the **bot will fail to run in the Azure portal**. -->

### <a name="deploy-your-code-to-azure"></a>Implantar seu código no Azure

[!INCLUDE [deploy code to Azure](~/includes/deploy/snippet-deploy-code-to-az.md)]

<!-- # [C#](#tab/csharp)
```cmd
az webapp deployment source config-zip --resource-group "resource-group-name" --name "bot-name-in-azure" --src "c:\bot\mybot.zip"
```

# [JavaScript](#tab/javascript)

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish-js.md)]

# [Python](#tab/python)

az webapp deployment source config-zip --resource-group "resource_group_name" --name "unique_bot_name" --src "zi

### Test the published bot

After you publish the bot, give Azure a minute or two to update and start the bot.

Use the Emulator to test the production endpoint for your bot, or use the Azure portal to test the bot in Web Chat.
In either case, you should see the same behavior as you did when you tested it locally.

## Clean up resources

<!-- In the first tutorial, we should tell them to use a new resource group, so that it is easy to clean up resources. We should also mention in this step in the first tutorial not to clean up resources if they are continuing with the sequence. -->

Se você não quiser continuar usando este aplicativo, exclua os recursos associados seguindo estas etapas:

1. No portal do Azure, abra o grupo de recursos para seu bot.
2. Clique em **Excluir grupo de recursos** para excluir o grupo de recursos e todos os recursos que ele contém.
3. No painel de confirmação, insira o nome do grupo de recursos e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como adicionar recursos ao seu bot, confira o artigo **Enviar e receber mensagem de texto** e os outros artigos da seção de instruções de desenvolvimento.
> [!div class="nextstepaction"]
> [Enviar e receber mensagens de texto](bot-builder-howto-send-messages.md)
