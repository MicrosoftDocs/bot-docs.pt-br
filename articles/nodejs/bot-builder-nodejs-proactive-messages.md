---
title: Enviar mensagens proativas | Microsoft Docs
description: Saiba como interromper o fluxo da conversa atual com uma mensagem proativa usando o SDK do Bot Framework para Node.js
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 58a6678561d048d0257dc81d37d4db4cbca9b382
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299796"
---
# <a name="send-proactive-messages"></a>Enviar mensagens proativas
[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-proactive-messages.md)
> - [Node.js](../nodejs/bot-builder-nodejs-proactive-messages.md)

[!INCLUDE [Introduction to proactive messages - part 1](../includes/snippet-proactive-messages-intro-1.md)]

## <a name="types-of-proactive-messages"></a>Tipos de mensagens proativas

[!INCLUDE [Introduction to proactive messages - part 2](../includes/snippet-proactive-messages-intro-2.md)]

## <a name="send-an-ad-hoc-proactive-message"></a>Enviar uma mensagem proativa ad hoc

Os exemplos de código a seguir mostram como enviar uma mensagem proativa ad hoc usando o SDK do Bot Framework para Node.js.

Para poder enviar uma mensagem ad hoc para um usuário, primeiro o bot deve coletar e salvar as informações sobre o usuário da conversa atual. A propriedade **address** da mensagem inclui todas as informações que o bot precisará para enviar posteriormente uma mensagem ad hoc ao usuário. 

```javascript
bot.dialog('adhocDialog', function(session, args) {
    var savedAddress = session.message.address;

    // (Save this information somewhere that it can be accessed later, such as in a database, or session.userData)
    session.userData.savedAddress = savedAddress;

    var message = 'Hello user, good to meet you! I now know your address and can send you notifications in the future.';
    session.send(message);
})
```

> [!NOTE]
> O bot pode armazenar os dados de usuário de qualquer maneira, desde que o bot possa acessá-los mais tarde.

Após o bot coletar informações sobre o usuário, ele poderá enviar uma mensagem proativa ad hoc ao usuário a qualquer momento. Para fazer isso, ele simplesmente recupera os dados de usuário que armazenou anteriormente, constrói a mensagem e a envia.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

var bot = new builder.UniversalBot(connector)
                .set('storage', inMemoryStorage); // Register in-memory storage 

function sendProactiveMessage(address) {
    var msg = new builder.Message().address(address);
    msg.text('Hello, this is a notification');
    msg.textLocale('en-US');
    bot.send(msg);
}
```

> [!TIP]
> Uma mensagem proativa ad hoc pode ser iniciada em disparadores assíncronos, como solicitações http, temporizadores, filas ou de outro lugar que o desenvolvedor escolher.

## <a name="send-a-dialog-based-proactive-message"></a>Enviar uma mensagem proativa baseada em diálogo

Os exemplos de código a seguir mostram como enviar uma mensagem proativa baseada em diálogo usando o SDK do Bot Framework para Node.js. Você pode encontrar o exemplo de trabalho completo na pasta [startNewDialog](https://aka.ms/js-startnewdialog-sample-v3).

Para poder enviar uma mensagem baseada em diálogo para um usuário, primeiro o bot deve coletar (e salvar) as informações da conversa atual. O objeto `session.message.address` inclui todas as informações que o bot precisará para enviar uma mensagem proativa baseada em diálogo para o usuário. 

```javascript
// proactiveDialog dialog
bot.dialog('proactiveDialog', function (session, args) {

    savedAddress = session.message.address;

    var message = 'Hey there, I\'m going to interrupt our conversation and start a survey in five seconds...';
    session.send(message);

    message = `You can also make me send a message by accessing: http://localhost:${server.address().port}/api/CustomWebApi`;
    session.send(message);

    setTimeout(() => {
        startProactiveDialog(savedAddress);
    }, 5000);
});
```

Quando for a hora de enviar a mensagem, o bot criará um novo diálogo e o adicionará ao início da pilha de diálogos. O novo diálogo assume o controle da conversa, entrega a mensagem proativa, fecha e, em seguida, retorna o controle ao diálogo anterior na pilha. 

```javascript
// initiate a dialog proactively 
function startProactiveDialog(address) {
    bot.beginDialog(address, "*:survey");
}
```

> [!NOTE]
> O exemplo de código acima exige um arquivo personalizado, **botadapter.js**, que você pode [baixar do GitHub](https://aka.ms/js-botadaptor-file-v3).

O diálogo de pesquisa controla a conversa até que ela termine. Em seguida, ele fecha (chamando `session.endDialog()`) e devolve o controle para o diálogo anterior. 


```javascript
// handle the proactive initiated dialog
bot.dialog('survey', function (session, args, next) {
  if (session.message.text === "done") {
    session.send("Great, back to the original conversation");
    session.endDialog();
  } else {
    session.send('Hello, I\'m the survey dialog. I\'m interrupting your conversation to ask you a question. Type "done" to resume');
  }
});
```

## <a name="sample-code"></a>Exemplo de código

Veja um exemplo completo mostrando como enviar mensagens proativas usando o SDK do Bot Framework para Node.js em <a href="https://aka.ms/js-proactivemessages-sample-v3" target="_blank">Exemplo de mensagens proativas</a> no GitHub. No exemplo de Mensagens Proativas, <a href="https://aka.ms/js-simplesendmessage-sample-v3" target="_blank">simpleSendMessage</a> mostra como enviar uma mensagem proativa ad-hoc e <a href="https://aka.ms/js-startnewdialog-sample-v3" target="_blank">startNewDialog</a> mostra como enviar uma mensagem proativa baseada em diálogo.

## <a name="additional-resources"></a>Recursos adicionais

- [Projetar o fluxo da conversa](../bot-service-design-conversation-flow.md)
