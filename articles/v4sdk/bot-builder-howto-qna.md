---
title: Usar o QnA Maker para responder a perguntas | Microsoft Docs
description: Aprenda a usar o QnA Maker no seu bot.
keywords: perguntas e respostas, QnA, perguntas frequentes, QnA Maker
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/01/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9ac71659e6420f5181aa7e332d8e5806f1edc348
ms.sourcegitcommit: 4751c7b8ff1d3603d4596e4fa99e0071036c207c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2019
ms.locfileid: "73441550"
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

## <a name="additional-information"></a>Informações adicionais

### <a name="multi-turn-prompts"></a>Prompts de vários turnos

O QnA Maker dá suporte a avisos de acompanhamento, também conhecidos como prompts de vários turnos.
Se a base de dados de conhecimento do QnA Maker exigir uma resposta adicional do usuário, o QnA Maker enviará informações de contexto que você poderá usar para enviar avisos ao usuário. Essas informações também são usadas para fazer qualquer chamada de acompanhamento para o serviço QnA Maker.
Na versão 4.6, o SDK do Bot Framework adicionou suporte para esse recurso.

Para construir essa base de dados de conhecimento, consulte a documentação QnA Maker sobre como [Usar avisos de acompanhamento para criar várias rodadas de uma conversa](https://aka.ms/qnamaker-multiturn-conversation). Para saber como incorporar o suporte a várias transformações em seu bot, confira a amostra de vários turnos do QnA Maker [[**C#** ](https://aka.ms/cs-qna-multiturn) | [**JS**](https://aka.ms/js-qna-multiturn)].

<!--TODO: Update code based on final sample 
The following code snippets come from the proof-of-concept **multi-turn QnA Maker prompts** sample for
[**C#**](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting/csharp_dotnetcore) and
[**JavaScript**](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting/javascript_nodejs).
-->
<!--
#### Sample code

This sample uses a custom QnA dialog to track state for QnA Maker and handle the user's input and QnA Maker's response. When the user sends a message to the bot, the bot treats the input as either an initial query or a response to a follow-up question. The bot starts or continues its QnA dialog, which tracks the QnA Maker context information.

1. When the dialog **starts**, it makes an initial call to the QnA Maker service. QnA Maker context information **is not** included in the call.
1. When the dialog **continues**, it makes a follow-up call to the QnA Maker service. QnA Maker context information included **is** included in the call.
1. In either case, the dialog evaluates the query results.
   - Each QnA Maker result includes either an answer or a follow-up question to the user's initial query. (This sample uses only the first result.)
   - If the result includes follow-up prompts, the dialog prompts the user, saves the context information, and stays on the dialog stack, waiting for additional information from the user.
   - Otherwise, the result represents an answer, the dialog sends the answer and ends.

This sample implements this across two dialog classes:

- The base _functional dialog_ defines the begin, continue, and state logic for the dialog, notably, the _run state machine_ method.
- The derived _QnA dialog_ defines the logic to call QnA Maker, evaluate its response, and send an answer or follow-up question to the user.

#### QnA Maker context

You need to track context information for the QnA Maker service.
The dialog funnels incoming activities through its _run state machine_ method, which:

1. Gets previous QnA Maker context, if any, from state.
1. Calls its _process_ method to call QnA Maker and generates a response for the user.
1. If the result was a follow-up question, sends the question, saves new QnA Maker context to state, and waits for more input on the next turn.
1. If the result was an answer, sends the answer and ends the dialog.

##### [C#](#tab/csharp)

**Dialogs\FunctionDialogBase.cs** defines the **RunStateMachineAsync** method.

```csharp
private async Task<DialogTurnResult> RunStateMachineAsync(DialogContext dialogContext, CancellationToken cancellationToken)
{
     // Get the Process function's current state from the dialog state
     var oldState = GetPersistedState(dialogContext.ActiveDialog);

     // Run the Process function.
     var (newState, output, result) = await ProcessAsync(oldState, dialogContext.Context.Activity).ConfigureAwait(false);

     // If we have output to send then send it.
     foreach (var activity in output)
     {
          await dialogContext.Context.SendActivityAsync(activity).ConfigureAwait(false);
     }

     // If we have new state then we must still be running.
     if (newState != null)
     {
          // Add the state returned from the Process function to the dialog state.
          dialogContext.ActiveDialog.State[FunctionStateName] = newState;

          // Return Waiting indicating this dialog is still in progress.
          return new DialogTurnResult(DialogTurnStatus.Waiting);
     }
     else
     {
          // The Process function indicates it's completed by returning null for the state.
          return await dialogContext.EndDialogAsync(result).ConfigureAwait(false);
     }
}
```

##### [JavaScript](#tab/javascript)

**dialogs/functionDialogBase.js** defines the **runStateMachine** method.

```javascript
async runStateMachine(dc) {

     var oldState = this.getPersistedState(dc.activeDialog);

     var processResult = await this.processAsync(oldState, dc.context.activity);

     var newState = processResult[0];
     var output = processResult[1];
     var result = processResult[2];

     await dc.context.sendActivity(output);

     if(newState != null){
          dc.activeDialog.state[functionStateName] = newState;
          return { status: DialogTurnStatus.waiting };
     }
     else{
          return await dc.endDialog();
     }
}
```

---

#### QnA Maker input and response

You need to provide any previous context information when you call the QnA Maker service.
The dialog handles the call to QnA Maker in its _process_ method, which:

1. Makes the call to QnA Maker, passing in the previous context, if any.
   - This sample uses a _query QnA service_ helper method to format the parameters and make the call.
   - Importantly, if this is a follow-up call to QnA Maker, the _QnA Maker options_ should include values for the _QnA request context_ and the _QnA question ID_.
1. Gets QnA Maker's response and any follow-up prompts from the top result.
1. If the result was a follow-up question:
   - Generates a hero card that contains the question and options for the user's response.
   - Includes the hero card in a message activity.
   - Returns the message activity and the new QnA Maker context.
1. If the result was an answer, returns a message activity that contains the answer.

You can format a follow-up activity in many ways. This sample uses a hero card. However, using suggested actions would be another option.

##### [C#](#tab/csharp)

**Dialogs\QnADialog.cs** defines the **RunStateMachineAsync** method.

```csharp
protected override async Task<(object newState, IEnumerable<Activity> output, object result)> ProcessAsync(object oldState, Activity inputActivity)
{
     Activity outputActivity = null;
     QnABotState newState = null;

     var query = inputActivity.Text;
     var qnaResult = await _qnaService.QueryQnAServiceAsync(query, (QnABotState)oldState);
     var qnaAnswer = qnaResult[0].Answer;
     var prompts = qnaResult[0].Context?.Prompts;

     if (prompts == null || prompts.Length < 1)
     {
          outputActivity = MessageFactory.Text(qnaAnswer);
     }
     else
     {
          // Set bot state only if prompts are found in QnA result
          newState = new QnABotState
          {
          PreviousQnaId = qnaResult[0].Id,
          PreviousUserQuery = query
          };

          outputActivity = CardHelper.GetHeroCard(qnaAnswer, prompts);
     }

     return (newState, new Activity[] { outputActivity }, null);
}
```

##### [JavaScript](#tab/javascript)

**dialogs/qnaDialog.js** defines the **runStateMachine** method.

```javascript
async processAsync(oldState, activity){

     var newState = null;
     var query = activity.text;
     var qnaResult = await QnAServiceHelper.queryQnAService(query, oldState);
     var qnaAnswer = qnaResult[0].answer;

     var prompts = null;
     if(qnaResult[0].context != null){
          prompts = qnaResult[0].context.prompts;
     }

     var outputActivity = null;
     if(prompts == null || prompts.length < 1){
          outputActivity = MessageFactory.text(qnaAnswer);
     }
     else{
          var newState = {
               PreviousQnaId: qnaResult[0].id,
               PreviousUserQuery: query
          }

          outputActivity = CardHelper.GetHeroCard(qnaAnswer, prompts);
     }

     return [newState, outputActivity , null];
}  
```

---
-->
## <a name="next-steps"></a>Próximas etapas

O QnA Maker pode ser combinado com outros Serviços Cognitivos, para tornar seu bot ainda mais poderoso. A ferramenta de expedição fornece uma forma de combinar QnA com Reconhecimento Vocal (LUIS) no seu bot.

> [!div class="nextstepaction"]
> [Combinar o LUIS e os serviços QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)
