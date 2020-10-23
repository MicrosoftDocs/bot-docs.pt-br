---
title: Usar o QnA Maker para responder a perguntas – Serviço de Bot
description: Saiba como os bots podem responder a perguntas de usuários sem analisar ou interpretar as perguntas. Consulte como usar QnA Maker para esta tarefa.
keywords: perguntas e respostas, QnA, perguntas frequentes, QnA Maker
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/02/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0960f5965628fd661cbe561b750a4cf7094c3c4c
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92414167"
---
# <a name="use-qna-maker-to-answer-questions"></a>Usar o QnA Maker para responder a perguntas

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O QnA Maker fornece uma camada de pergunta e resposta de conversa sobre seus dados. Isso permite que o bot envie uma pergunta ao QnA Maker e receba uma resposta sem a necessidade de analisar e interpretar a intenção de pergunta.

Um dos requisitos básicos para a criação de seu próprio serviço QnA Maker é preenchê-lo com perguntas e respostas. Em muitos casos, as perguntas e respostas já existem no conteúdo, como as perguntas frequentes ou outra documentação; outras vezes, talvez seja conveniente personalizar as respostas às perguntas de forma mais natural e coloquial.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do [QnA Maker](https://www.qnamaker.ai/)
- Conhecimento das [Noções básicas do bot](bot-builder-basics.md), do [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/overview) e do [gerenciamento de recursos do bot](bot-file-basics.md).
- Uma cópia da amostra do **QnA Maker**. Você precisará de uma cópia dela em [**C#** ](https://aka.ms/cs-qna), [**JavaScript**](https://aka.ms/js-qna-sample) ou [**Python**](https://aka.ms/bot-qna-python-sample-code).

## <a name="about-this-sample"></a>Sobre este exemplo

Para usar o QnA Maker em seu bot, você precisa criar uma base de dados de conhecimento no portal do [QnA Maker](https://www.qnamaker.ai/), conforme mostrado na próxima seção. Em seguida, o bot pode usar a base de dados de conhecimento para responder às perguntas do usuário.

## <a name="c"></a>[C#](#tab/cs)

![Fluxo lógico do QnABot](./media/qnabot-logic-flow.png)

`OnMessageActivityAsync` é chamado em cada entrada do usuário recebida. Quando chamado, ele acessa informações de `_configuration`, armazenadas no arquivo de `appsetting.json` do código de exemplo para localizar o valor a ser conectado à sua base de dados de conhecimento pré-configurada do QnA Maker.

## <a name="javascript"></a>[JavaScript](#tab/js)

![Fluxo lógico do QnABot JS](./media/qnabot-js-logic-flow.png)

`OnMessage` é chamado em cada entrada do usuário recebida. Quando chamado, ele acessa seu conector `qnamaker`, que foi pré-configurado usando os valores fornecidos no arquivo `.env` do seu código de exemplo.  O método `getAnswers` do qnamaker conecta o bot à sua base de dados de conhecimento externa do QnA Maker.

## <a name="python"></a>[Python](#tab/python)

![Fluxo lógico do QnABot JS](./media/qnabot-python-logic-flow.png)

`on_message_activity` é chamado em cada entrada do usuário recebida. Quando chamado, ele acessa seu conector `qna_maker`, que foi pré-configurado usando os valores fornecidos no arquivo `config.py` do seu código de exemplo.  O método `qna_maker.getAnswers` conecta o bot à sua base de dados de conhecimento externa do QnA Maker.

---

A entrada do usuário é enviada à sua base de dados de conhecimento e a melhor resposta retornada é exibida para o usuário.

## <a name="create-a-qna-maker-service-and-publish-a-knowledge-base"></a>Criar um serviço do QnA Maker e publicar uma base de Conhecimento

1. Crie um serviço do QnA Maker.
1. Crie uma base de dados de conhecimento usando o arquivo **smartLightFAQ.tsv** localizado na pasta CognitiveModels do projeto de amostra. Nomeie a sua base de dados de conhecimento `qna` e use o arquivo **smartLightFAQ.tsv** para preenchê-la.

Você também pode usar essas etapas para acessar as suas próprias bases de dados de conhecimento do QnA Maker.

> [!NOTE]
> A documentação do QnA Maker tem instruções sobre como [criar um serviço](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) no Azure e como [criar, treinar e publicar a sua base de dados de conhecimento](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base).

## <a name="obtain-values-to-connect-your-bot-to-the-knowledge-base"></a>Obtenha os valores para conectar seu bot à base de conhecimento

1. No site do [QnA Maker](https://www.qnamaker.ai/), selecione sua base de conhecimento.
1. Com sua base de conhecimento aberta, selecione **Settings** (Configurações). Registre o valor mostrado para o _nome do serviço_. Esse valor é útil para localizar sua base de dados de conhecimento de interesse ao usar a interface do portal do QnA Maker. Ele não é usado para conectar seu aplicativo bot a esta base de dados de conhecimento.
1. Role a tela para baixo até encontrar **Detalhes da implantação** e registre estes valores na solicitação HTTP do exemplo Postman:
   - POST /knowledgebases/\<knowledge-base-id>/generateAnswer
   - Host: \<your-host-url>
   - Autorização: EndpointKey \<your-endpoint-key>

A URL do host será iniciada com `https://` e terminará com `/qnamaker`, como `https://<hostname>.azure.net/qnamaker`. O bot precisará da ID da base de dados de conhecimento, da URL do host e da chave do ponto de extremidade para se conectar à sua base de dados de conhecimento do QnA Maker.

## <a name="update-the-settings-file"></a>Atualizar o arquivo de configurações

Primeiro, adicione ao arquivo de configurações as informações necessárias para acessar a sua base de dados de conhecimento, incluindo nome do host, a chave de ponto de extremidade e a kbId (ID da base de dados de conhecimento). Esses são os valores que você salvou na guia **Configurações** de sua base de conhecimento do QnA Maker.

Se essa implantação não for para produção, os campos de senha e a ID do aplicativo do seu bot poderão ser deixados em branco.

> [!NOTE]
> Para adicionar uma base de dados de conhecimento do QnA Maker em um aplicativo bot existente, certifique-se de adicionar títulos informativos para as suas entradas do QnA. O valor "name" dessa seção fornece a chave necessária para acessar essas informações de dentro de seu aplicativo.

## <a name="c"></a>[C#](#tab/cs)

**appsettings.json**

[!code-csharp[appsettings](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/appsettings.json)]

## <a name="javascript"></a>[JavaScript](#tab/js)

**.env**

[!code-javascript[.env file](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/.env)]

## <a name="python"></a>[Python](#tab/python)

**config.py**

[!code-python[config.py](~/../botbuilder-samples/samples/python/11.qnamaker/config.py?range=10-18)]

---

## <a name="set-up-the-qna-maker-instance"></a>Configurar a instância do QnA Maker

Primeiro, criamos um objeto para acessar nossa base de dados de conhecimento do QnA Maker.

## <a name="c"></a>[C#](#tab/cs)

Verifique se o pacote do NuGet **Microsoft.Bot.Builder.AI.QnA** está instalado para o seu projeto.

No **QnABot.cs**, no método `OnMessageActivityAsync`, criamos uma instância do QnAMaker. A classe `QnABot` também é de onde os nomes das informações de conexão, salvos em `appsettings.json` acima, são extraídos. Se você tiver escolhido nomes diferentes para as informações de conexão da base de conhecimento no seu arquivo de configurações, procure atualizar os nomes aqui para refletir o nome escolhido.

**Bots/QnABot.cs**

[!code-csharp[qna connection](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=32-39)]

## <a name="javascript"></a>[JavaScript](#tab/js)

Confira se o pacote npm **botbuilder ai** está instalado no seu projeto.

Em nosso exemplo, o código para a lógica do bot está no arquivo **QnABot.js**.

No arquivo **QnABot.js**, usamos as informações de conexão fornecidas pelo arquivo. env para estabelecer uma conexão para o serviço do QnA Maker: _this.qnaMaker_.

**bots/QnABot.js**

[!code-javascript[QnAMaker](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=12-16)]

## <a name="python"></a>[Python](#tab/python)

No arquivo **qna_bot.py**, usamos as informações de conexão fornecidas pelo arquivo `config.py` para estabelecer uma conexão com o serviço do QnA Maker: `self.qna_maker`.

**bots/qna_bot.py** [!code-python[QnAMaker](~/../botbuilder-samples/samples/python/11.qnamaker/bots/qna_bot.py?range=13-19)]

---

## <a name="calling-qna-maker-from-your-bot"></a>Chamar o QnA Maker de seu bot

## <a name="c"></a>[C#](#tab/cs)

Quando seu bot precisar de uma resposta do QnAMaker, chame `GetAnswersAsync()` do código do seu bot para obter a resposta apropriada com base no contexto atual. Se você estiver acessando sua própria base de conhecimento, altere a mensagem de _nenhuma resposta localizada_ abaixo para fornecer instruções úteis aos seus usuários.

**Bots/QnABot.cs**

[!code-csharp[qna get answers](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=43-54)]

## <a name="javascript"></a>[JavaScript](#tab/js)

No arquivo **QnABot.js**, passamos a entrada do usuário para o método `getAnswers` do serviço do QnA Maker a fim de obter respostas da base de conhecimento. Se o QnA Maker retornar uma resposta, ela será mostrada ao usuário. Caso contrário, o usuário receberá a mensagem “Nenhuma resposta do QnA Maker foi localizada”.

**bots/QnABot.js**

[!code-javascript[OnMessage](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=45-54)]

## <a name="python"></a>[Python](#tab/python)

No arquivo **qna_bot.py**, passamos a entrada do usuário para o método `get_answers` do serviço do QnA Maker a fim de obter respostas da base de conhecimento. Se o QnA Maker retornar uma resposta, ela será mostrada ao usuário. Caso contrário, o usuário receberá a mensagem *Nenhuma resposta do QnA Maker foi localizada.*

**bots/qna_bot.py** [!code-python[get_answers](~/../botbuilder-samples/samples/python/11.qnamaker/bots/qna_bot.py?range=32-37)]

---

## <a name="test-the-bot"></a>Testar o bot

Execute o exemplo localmente em seu computador. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download). Para obter mais instruções, consulte o arquivo Leiame do [C#](https://aka.ms/cs-qna) ou do [Javascript](https://aka.ms/js-qna-sample).
ou da amostra de [Python](https://aka.ms/bot-qna-python-sample-code).

Inicie o emulador, conecte-se ao seu bot e envie uma mensagem, conforme mostrado a seguir.

![exemplo de qna de teste](../media/emulator-v4/qna-test-bot.png)

## <a name="additional-information"></a>Informações adicionais

A amostra de **vários turnos do QnA Maker** ([**C#** ](https://aka.ms/cs-qna-multiturn), [**JavaScript**](https://aka.ms/js-qna-multiturn), [**Python**](https://aka.ms/py-qna-multiturn)) mostra como usar um diálogo do QnA Maker para dar suporte a prompts de acompanhamento e recursos de aprendizado ativo do QnA Maker.

- O QnA Maker dá suporte a avisos de acompanhamento, também conhecidos como prompts de vários turnos.
Se a base de dados de conhecimento do QnA Maker exigir uma resposta adicional do usuário, o QnA Maker enviará informações de contexto que você poderá usar para enviar avisos ao usuário. Essas informações também são usadas para fazer qualquer chamada de acompanhamento para o serviço QnA Maker.
Na versão 4.6, o SDK do Bot Framework adicionou suporte para esse recurso.

  Para construir essa base de dados de conhecimento, consulte a documentação QnA Maker sobre como [Usar avisos de acompanhamento para criar várias rodadas de uma conversa](https://aka.ms/qnamaker-multiturn-conversation).

- O QnA Maker também dá suporte a sugestões de aprendizado ativo, permitindo que a base de dados de conhecimento seja aprimorada ao longo do tempo.
O diálogo do QnA Maker dá suporte a comentários explícitos para o recurso de aprendizado ativo.

  Para habilitar esse recurso em uma base de dados de conhecimento, consulte a documentação do QnA Maker sobre [Sugestões de aprendizado ativo](https://aka.ms/qnamaker-active-learning).

<!--To learn how to incorporate multi-turn support in your bot, take a look at the QnA Maker Multi-turn [[**C#**](https://aka.ms/cs-qna-multiturn) | [**JS**](https://aka.ms/js-qna-multiturn)] sample.-->

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
