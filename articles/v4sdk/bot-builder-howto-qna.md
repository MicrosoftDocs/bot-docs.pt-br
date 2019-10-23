---
title: Usar o QnA Maker para responder a perguntas | Microsoft Docs
description: Aprenda a usar o QnA Maker no seu bot.
keywords: perguntas e respostas, QnA, perguntas frequentes, QnA Maker
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a0dac2fb38f065c0dab6044b5421918e73a0c563
ms.sourcegitcommit: b8b2776552b15590a453267dd0141a25418fbc0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72556460"
---
# <a name="use-qna-maker-to-answer-questions"></a>Usar o QnA Maker para responder a perguntas

[!INCLUDE[applies-to](../includes/applies-to.md)]

O QnA Maker fornece uma camada de pergunta e resposta de conversa sobre seus dados. Isso permite que o bot envie ao QnA Maker uma pergunta e receba uma resposta sem a necessidade de analisar e interpretar a intenção da pergunta. 

Um dos requisitos básicos para a criação de seu próprio serviço QnA Maker é propagá-lo com perguntas e respostas. Em muitos casos, as perguntas e respostas já existem no conteúdo, como as perguntas frequentes ou outra documentação; outras vezes, talvez seja conveniente personalizar as respostas às perguntas de forma mais natural e coloquial. 

## <a name="prerequisites"></a>Pré-requisitos

- O código neste artigo baseia-se no exemplo do QnA Maker. Você precisará de uma cópia dele contida em **[C#](https://aka.ms/cs-qna) ou [JavaScript](https://aka.ms/js-qna-sample)** .
- Conta do [QnA Maker](https://www.qnamaker.ai/)
- Conhecimento das [Noções básicas do bot](bot-builder-basics.md), do [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/overview) e do [gerenciamento de recursos do bot](bot-file-basics.md).

## <a name="about-this-sample"></a>Sobre este exemplo

Para o bot utilizar o QnA Maker, você precisará primeiro criar uma base de conhecimento no [QnA Maker](https://www.qnamaker.ai/), o que descreveremos na próxima seção. Assim, seu bot poderá enviar a essa base a consulta do usuário e ela fornecerá a melhor resposta para a pergunta feita.

## <a name="ctabcs"></a>[C#](#tab/cs)
![Fluxo lógico do QnABot](./media/qnabot-logic-flow.png)

`OnMessageActivityAsync` é chamado em cada entrada do usuário recebida. Quando chamado, ele acessa informações de `_configuration` armazenadas no arquivo `appsetting.json` do código de exemplo para localizar o valor para se conectar à sua base de conhecimento pré-configurada no QnA Maker. 

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)
![Fluxo lógico do QnABot JS](./media/qnabot-js-logic-flow.png)

`OnMessage` é chamado em cada entrada do usuário recebida. Quando chamado, ele acessa seu conector `qnamaker`, que foi pré-configurado usando os valores fornecidos no arquivo `.env` do seu código de exemplo.  O método `getAnswers` do qnamaker conecta o bot à sua base de conhecimento externa do QnA Maker.

---
A entrada do usuário é enviada à sua base de conhecimento e a melhor resposta retornada é exibida para o usuário.

## <a name="create-a-qna-maker-service-and-publish-a-knowledge-base"></a>Criar um serviço do QnA Maker e publicar uma base de Conhecimento
A primeira etapa é criar um serviço do QnA Maker. Siga as etapas listadas na [documentação](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) do QnA Maker para criar o serviço no Azure.

Em seguida, você criará uma base de conhecimento usando o arquivo `smartLightFAQ.tsv` localizado na pasta CognitiveModels do projeto de exemplo. As etapas para criar, treinar e publicar sua [base de dados de conhecimento](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base) do QnA Maker são listadas na documentação do QnA Maker. Ao seguir essas etapas, nomeie a KB `qna`e use o arquivo `smartLightFAQ.tsv` para preencher sua KB.

> Observação. Este artigo também pode ser usado para acessar sua própria base de conhecimento do QnA Maker desenvolvido pelo usuário.

## <a name="obtain-values-to-connect-your-bot-to-the-knowledge-base"></a>Obtenha os valores para conectar seu bot à base de conhecimento
1. No site do [QnA Maker](https://www.qnamaker.ai/), selecione sua base de conhecimento.
1. Com sua base de conhecimento aberta, selecione **Settings** (Configurações). Registre o valor mostrado para o _nome do serviço_. Esse valor é útil para localizar sua base de conhecimento de interesse ao usar a interface do portal do QnA Maker. Ele não é usado para conectar seu aplicativo do bot a essa base de conhecimento. 
1. Role a tela para baixo até encontrar **Detalhes da implantação** e registre estes valores na solicitação HTTP do exemplo Postman:
   - POST /knowledgebases/\<knowledge-base-id>/generateAnswer
   - Host: \<your-hostname> // URL completo terminando com /qnamaker
   - Autorização: EndpointKey \<your-endpoint-key>
   
A cadeia de caracteres da URL completa do seu nome do host será semelhante a "https://< >.azure.net/qnamaker". Esses três valores fornecerão as informações necessárias para o aplicativo se conectar à base de conhecimento do QnA Maker por meio do seu serviço do QnA do Azure.  

## <a name="update-the-settings-file"></a>Atualizar o arquivo de configurações

Primeiro, adicione ao arquivo de configurações as informações necessárias para acessar sua base de conhecimento, incluindo nome do host, chave de ponto de extremidade e a ID da base de conhecimento (kbId). Esses são os valores que você salvou na guia **Configurações** de sua base de conhecimento do QnA Maker. 

Se essa implantação não for para produção, os campos ID do aplicativo e senha poderão ser deixados em branco.

> [!NOTE]
> Se você estiver adicionando o acesso a uma base de conhecimento do QnA Maker em um aplicativo de bot existente, adicione títulos informativos para suas entradas do QnA. O valor "name" dessa seção fornece a chave necessária para acessar essas informações de dentro de seu aplicativo.

## <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="update-your-appsettingsjson-file"></a>Atualizar o arquivo appsettings.json

```json
{
  "MicrosoftAppId": "",
  "MicrosoftAppPassword": "",
  
  "QnAKnowledgebaseId": "<knowledge-base-id>",
  "QnAAuthKey": "<your-endpoint-key>",
  "QnAEndpointHostName": "<your-hostname>"
}
```

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="update-your-env-file"></a>Atualizar o arquivo .env

```file
MicrosoftAppId=""
MicrosoftAppPassword=""

QnAKnowledgebaseId="<knowledge-base-id>"
QnAAuthKey="<your-endpoint-key>"
QnAEndpointHostName="<your-hostname>"
```

---

## <a name="set-up-the-qna-maker-instance"></a>Configurar a instância do QnA Maker

Primeiro, criamos um objeto para acessar nossa base de dados de conhecimento do QnA Maker.

## <a name="ctabcs"></a>[C#](#tab/cs)

Verifique se o pacote do NuGet **Microsoft.Bot.Builder.AI.QnA** está instalado para o seu projeto.

No **QnABot.cs**, no método `OnMessageActivityAsync`, criamos uma instância do QnAMaker. A classe `QnABot` também é de onde os nomes das informações de conexão, salvos em `appsettings.json` acima, são extraídos. Se você tiver escolhido nomes diferentes para as informações de conexão da base de conhecimento no seu arquivo de configurações, procure atualizar os nomes aqui para refletir o nome escolhido.

**Bots/QnABot.cs**  
[!code-csharp[qna connection](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=32-37)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Confira se o pacote npm **botbuilder ai** está instalado no seu projeto.

Em nosso exemplo, o código para a lógica do bot está em um arquivo **QnABot.js**.

No arquivo **QnABot.js**, usamos as informações de conexão fornecidas pelo arquivo. env para estabelecer uma conexão para o serviço do QnA Maker: _this.qnaMaker_.

**QnAMaker.js** [!code-javascript[QnAMaker](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=12-16)]
---

## <a name="calling-qna-maker-from-your-bot"></a>Chamar o QnA Maker de seu bot

## <a name="ctabcs"></a>[C#](#tab/cs)

Quando seu bot precisar de uma resposta do QnAMaker, chame `GetAnswersAsync()` do código do seu bot para obter a resposta apropriada com base no contexto atual. Se você estiver acessando sua própria base de conhecimento, altere a mensagem de _nenhuma resposta localizada_ abaixo para fornecer instruções úteis aos seus usuários.

**QnABot.cs**  
[!code-csharp[qna connection](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=43-52)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

No arquivo **QnABot.js**, passamos a entrada do usuário para o método `getAnswers` do serviço do QnA Maker a fim de obter respostas da base de conhecimento. Se o QnA Maker retornar uma resposta, ela será mostrada ao usuário. Caso contrário, o usuário receberá a mensagem “Nenhuma resposta do QnA Maker foi localizada”. 

**QnABot.js**

[!code-javascript[OnMessage](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=43-59)]

---

## <a name="test-the-bot"></a>Testar o bot

Execute o exemplo localmente em seu computador. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download). Para obter mais instruções, veja [Exemplo de C#](https://aka.ms/cs-qna) ou [Exemplo de JS](https://aka.ms/js-qna-sample) no arquivo LEIAME.

Inicie o emulador, conecte-se ao seu bot e envie uma mensagem, conforme mostrado a seguir.

![exemplo de qna de teste](../media/emulator-v4/qna-test-bot.png)

## <a name="next-steps"></a>Próximas etapas

O QnA Maker pode ser combinado com outros Serviços Cognitivos, para tornar seu bot ainda mais poderoso. A ferramenta de expedição fornece uma forma de combinar QnA com Reconhecimento Vocal (LUIS) no seu bot.

> [!div class="nextstepaction"]
> [Combinar o LUIS e os serviços QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)
