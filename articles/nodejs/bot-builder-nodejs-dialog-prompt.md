---
title: Solicitar entrada do usuário | Microsoft Docs
description: Saiba como usar prompts para coletar a entrada do usuário com o SDK do Bot Builder para Node.js.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 0926c15d2c62bfa74ddb465d8c816dee7c8fb576
ms.sourcegitcommit: 6c719b51c9e4e84f5642100a33fe346b21360e8a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52451968"
---
# <a name="prompt-for-user-input"></a>Prompt de entrada do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

O SDK do Bot Builder para Node.js fornece um conjunto de instruções internas para simplificar a coleta de entradas de um usuário. 

Um *prompt* é usado sempre que um bot precisa de uma entrada do usuário. Você pode usar os prompts para pedir ao usuário uma série de entradas por meio do encadeamento dos prompts em cascata. Você pode usar prompts em [cascata](bot-builder-nodejs-dialog-waterfall.md) para ajudá-lo a [gerenciar o fluxo da conversa](bot-builder-nodejs-manage-conversation-flow.md) no seu bot. 

Este artigo ajudará você a entender como funcionam os prompts e como usá-los para coletar informações de usuários.

## <a name="prompts-and-responses"></a>Prompts e respostas

Sempre que você precisa de entrada de um usuário, você pode enviar um prompt, aguardar até que o usuário responda com uma entrada e, em seguida, processar a entrada e enviar uma resposta para o usuário.

O exemplo de código a seguir solicita o nome do usuário e responde com uma mensagem de saudação.

```javascript
bot.dialog('greetings', [
    // Step 1
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    // Step 2
    function (session, results) {
        session.endDialog(`Hello ${results.response}!`);
    }
]);
```

Usando essa construção básica, você pode modelar o fluxo da conversa, adicionando quantos prompts e respostas forem necessários para o seu bot.

## <a name="prompt-results"></a>Resultados de prompt 

Prompts internos são implementados como [caixas de diálogo](bot-builder-nodejs-dialog-overview.md) que retornam a resposta do usuário no campo `results.response`. Para objetos JSON, as respostas são retornadas no campo `results.response.entity`. Qualquer tipo de [manipulador da caixa de diálogo](bot-builder-nodejs-dialog-overview.md#dialog-handlers) pode receber o resultado de um prompt. Depois que o bot recebe uma resposta, ele pode consumi-la ou passá-la de volta para a caixa de diálogo de chamada chamando o método [`session.endDialogWithResult`][EndDialogWithResult].

O exemplo de código a seguir mostra como retornar um resultado de prompt na caixa de diálogo de chamada usando o método `session.endDialogWithResult`. Neste exemplo, a caixa de diálogo `greetings` usa o resultado do prompt que a caixa de diálogo `askName` retorna para saudar o usuário pelo nome.

```javascript
// Ask the user for their name and greet them by name.
bot.dialog('greetings', [
    function (session) {
        session.beginDialog('askName');
    },
    function (session, results) {
        session.endDialog(`Hello ${results.response}!`);
    }
]);
bot.dialog('askName', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
]);
```

## <a name="prompt-types"></a>Tipos de prompt
O SDK do Bot Builder para Node.js inclui vários tipos diferentes de avisos internos. 

|**Tipo de prompt**     | **Descrição** |     
| ------------------ | --------------- |
|[Prompts.Text](#promptstext) | Solicita que o usuário insira uma cadeia de caracteres de texto. |     
|[Prompts.confirm](#promptsconfirm) | Pede ao usuário para confirmar uma ação.| 
|[Prompts.Number](#promptsnumber) | Solicita que o usuário insira um número.     |
|[Prompts.time](#promptstime) | Pede ao usuário uma hora ou data/hora.      |
|[Prompts.choice](#promptschoice) | Pede ao usuário para escolher a partir de uma lista de opções.    |
|[Prompts.attachment](#promptsattachment) | Pede ao usuário para carregar uma imagem ou vídeo.|       

As seções a seguir fornecem detalhes adicionais sobre cada tipo de prompt.

### <a name="promptstext"></a>Prompts.Text

Use o método [Prompts.text()][PromptsText] para pedir ao usuário uma **cadeia de caracteres de texto**. O prompt retorna a resposta do usuário como um [IPromptTextResult][IPromptTextResult].

```javascript
builder.Prompts.text(session, "What is your name?");
```

### <a name="promptsconfirm"></a>Prompts.confirm

Use o método [Prompts.confirm()][PromptsConfirm] para perguntar ao usuário para confirmar uma ação com uma resposta **sim/não**. O prompt retorna a resposta do usuário como um [IPromptConfirmResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptconfirmresult.html).

```javascript
builder.Prompts.confirm(session, "Are you sure you wish to cancel your order?");
```

### <a name="promptsnumber"></a>Prompts.number

Use o método [Prompts.number()][PromptsNumber] para pedir ao usuário um **número**. O prompt retorna a resposta do usuário como um [IPromptNumberResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptnumberresult.html).

```javascript
builder.Prompts.number(session, "How many would you like to order?");
```

### <a name="promptstime"></a>Prompts.time

Use o método [Prompts.time()][PromptsTime] para pedir ao usuário uma **hora** ou **data/hora**. O prompt retorna a resposta do usuário como um [IPromptTimeResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttimeresult.html). A estrutura usa a biblioteca [Chrono](https://github.com/wanasit/chrono) para analisar a resposta do usuário e suporta respostas relativas (por exemplo, “em 5 minutos”) e não relativas (por exemplo, “6 de junho às 14h”).

O campo [results.response](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttimeresult.html#response), que representa a resposta do usuário, contém um objeto [entity](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ientity.html) que especifica a data e a hora. Para resolver a data e hora em um objeto `Date` do JavaScript, use o método [EntityRecognizer.resolveTime()](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#resolvetime).

> [!TIP] 
> A hora que o usuário insere é convertida em hora UTC com base no fuso horário do servidor que hospeda o bot. Como o servidor pode estar localizado em um fuso horário diferente do usuário, lembre-se de considerar os fusos horários. Para converter a data e a hora para a hora local do usuário, considere solicitar ao usuário o seu respectivo fuso horário.

```javascript
bot.dialog('createAlarm', [
    function (session) {
        session.dialogData.alarm = {};
        builder.Prompts.text(session, "What would you like to name this alarm?");
    },
    function (session, results, next) {
        if (results.response) {
            session.dialogData.name = results.response;
            builder.Prompts.time(session, "What time would you like to set an alarm for?");
        } else {
            next();
        }
    },
    function (session, results) {
        if (results.response) {
            session.dialogData.time = builder.EntityRecognizer.resolveTime([results.response]);
        }

        // Return alarm to caller  
        if (session.dialogData.name && session.dialogData.time) {
            session.endDialogWithResult({ 
                response: { name: session.dialogData.name, time: session.dialogData.time } 
            }); 
        } else {
            session.endDialogWithResult({
                resumed: builder.ResumeReason.notCompleted
            });
        }
    }
]);
```

### <a name="promptschoice"></a>Prompts.choice

Use o método [Prompts.choice()][PromptsChoice] para solicitar que o usuário **escolha a partir de uma lista de opções**. O usuário pode transmitir sua seleção, inserindo o número associado com a opção escolhida ou digitando o nome da opção escolhida. Há suporte para correspondências completos e parciais do nome da opção. O prompt retorna a resposta do usuário como um [IPromptChoiceResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptchoiceresult.html). 

Para especificar o estilo da lista que é apresentada ao usuário, defina a propriedade [IPromptOptions.listStyle](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptoptions.html#liststyle). A tabela a seguir mostra os valores de enumeração de `ListStyle` para essa propriedade.


Normalmente, os valores de enumeração de `ListStyle` são os seguintes:

| Índice | NOME | DESCRIÇÃO |
| ---- | ---- | ---- |
| 0 | Nenhum | Nenhuma lista é renderizada. Isso é usado quando a lista está incluída como parte do prompt. |
| 1 | embutido | As opções são renderizadas como uma lista embutida do formulário “1. vermelho, 2. verde ou 3. azul”. |
| 2 | list | As opções são renderizadas como uma lista numerada. |
| 3 | botão | As opções são renderizadas como botões para os canais que dão suporte a botões. Para outros canais, eles serão renderizados como texto. |
| 4 | auto | O estilo é selecionado automaticamente com base no canal e no número de opções. | 

Você pode acessar essa enumeração a partir do objeto `builder` ou você pode fornecer um índice para escolher um `ListStyle`. Por exemplo, ambas as instruções no snippet de código a seguir realizam a mesma coisa.

```javascript
// ListStyle passed in as Enum
builder.Prompts.choice(session, "Which color?", "red|green|blue", { listStyle: builder.ListStyle.button });

// ListStyle passed in as index
builder.Prompts.choice(session, "Which color?", "red|green|blue", { listStyle: 3 });
```

Para especificar a lista de opções, você pode usar uma cadeia de caracteres delimitada por barra vertical (`|`), uma matriz de cadeias de caracteres ou um mapa de objeto.

Uma cadeia de caracteres delimitados por barra vertical: 

```javascript
builder.Prompts.choice(session, "Which color?", "red|green|blue");
```

Uma matriz de cadeias de caracteres:

```javascript
builder.Prompts.choice(session, "Which color?", ["red","green","blue"]);
```

Um mapa de objeto: 

```javascript
var salesData = {
    "west": {
        units: 200,
        total: "$6,000"
    },
    "central": {
        units: 100,
        total: "$3,000"
    },
    "east": {
        units: 300,
        total: "$9,000"
    }
};

bot.dialog('getSalesData', [
    function (session) {
        builder.Prompts.choice(session, "Which region would you like sales for?", salesData); 
    },
    function (session, results) {
        if (results.response) {
            var region = salesData[results.response.entity];
            session.send(`We sold ${region.units} units for a total of ${region.total}.`); 
        } else {
            session.send("OK");
        }
    }
]);
```

### <a name="promptsattachment"></a>Prompts.attachment

Use o método [Prompts.attachment()][PromptsAttachment] para pedir ao usuário para carregar um arquivo de uma imagem ou vídeo. O prompt retorna a resposta do usuário como um [IPromptAttachmentResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptattachmentresult.html).

```javascript
builder.Prompts.attachment(session, "Upload a picture for me to transform.");
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como conduzir os usuários por meio de uma cascata e pedir para obter informações, vamos dar uma olhada em maneiras de ajudá-lo a gerenciar melhor o fluxo da conversa.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-nodejs-dialog-manage-conversation-flow.md)


[SendAttachments]: bot-builder-nodejs-send-receive-attachments.md
[SendCardWithButtons]: bot-builder-nodejs-send-rich-cards.md
[RecognizeUserIntent]: bot-builder-nodejs-recognize-intent-messages.md
[SaveUserData]: bot-builder-nodejs-save-user-data.md

[UniversalBot]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html
[ChatConnector]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
[Session]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session


[SendTyping]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping

[EndDialogWithResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#enddialogwithresult

[IPromptResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html

[Result_Response]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html#response

[ResumeReason]: https://docs.botframework.com/en-us/node/builder/chat-reference/enums/_botbuilder_d_.resumereason.html

[Result_Resumed]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html#resumed

[entity]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ientity.html

[ResolveTime]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#resolvetime

[PromptsRef]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html

[PromptsText]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#text

[IPromptTextResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttextresult.html

[PromptsConfirm]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#confirm

[IPromptConfirmResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptconfirmresult.html

[PromptsNumber]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#number

[IPromptNumberResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptnumberresult.html

[PromptsTime]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#time

[IPromptTimeResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttimeresult.html

[PromptsChoice]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#choice

[IPromptChoiceResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptchoiceresult.html

[PromptsAttachment]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#attachment

[IPromptAttachmentResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptattachmentresult.html
