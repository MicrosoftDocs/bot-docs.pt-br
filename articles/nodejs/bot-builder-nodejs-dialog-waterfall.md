---
title: Definir etapas de conversa com cascatas | Microsoft Docs
description: Saiba como usar as cascatas para definir as etapas de uma conversa com o SDK do Bot Builder para Node.js.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: fcda5bb35712f3a1ec27fba64a492ced911c41db
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905958"
---
# <a name="define-conversation-steps-with-waterfalls"></a>Definir a conversa em cascata

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Uma conversa é uma série de mensagens trocadas entre o usuário e o bot. Quando o objetivo do bot é conduzir o usuário através de uma série de etapas, você pode usar o recurso de cascata para definir as etapas da conversa.

Uma cascata é uma implementação específica de uma [caixa de diálogo](bot-builder-nodejs-dialog-overview.md) que é mais comumente usada para coletar informações do usuário ou orientá-lo através de uma série de tarefas. As tarefas são implementadas como uma matriz de funções onde os resultados da primeira função são passados como entrada para a função seguinte, e assim por diante. Cada função normalmente representa uma etapa no processo geral. Em cada etapa, um bot solicita ao usuário uma entrada, aguarda uma resposta e, em seguida, passa o resultado para a próxima etapa.

Este artigo o ajudará a entender como o recurso de cascata funciona e como você pode usá-la para [gerenciar o fluxo da conversa](bot-builder-nodejs-dialog-manage-conversation.md).

## <a name="conversation-steps"></a>Etapas de conversa

Uma conversa normalmente envolve várias trocas de prompt/resposta entre o usuário e o bot. Cada troca de prompt/resposta é um passo à frente na conversa. Você pode criar uma conversa usando uma cascata com um mínimo de duas etapas.

Por exemplo, considere a seguinte caixa de diálogo `greetings`. A primeira etapa da cascata solicita o nome do usuário e a segunda etapa usa a resposta para saudar o usuário pelo nome.

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

O que torna isso possível é o uso de prompts. O SDK do Bot Builder para Node.js fornece vários tipos diferentes de [prompts](bot-builder-nodejs-dialog-prompt.md) internos que você pode usar para solicitar ao usuário vários tipos de informações.

O exemplo de código a seguir mostra uma caixa de diálogo que usa prompts para coletar várias partes das informações do usuário das 4 etapas da cascata.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This is a dinner reservation bot that uses a waterfall technique to prompt users for input.
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.send("Welcome to the dinner reservation.");
        builder.Prompts.time(session, "Please provide a reservation date and time (e.g.: June 6th at 5pm)");
    },
    function (session, results) {
        session.dialogData.reservationDate = builder.EntityRecognizer.resolveTime([results.response]);
        builder.Prompts.text(session, "How many people are in your party?");
    },
    function (session, results) {
        session.dialogData.partySize = results.response;
        builder.Prompts.text(session, "Whose name will this reservation be under?");
    },
    function (session, results) {
        session.dialogData.reservationName = results.response;

        // Process request and display reservation details
        session.send(`Reservation confirmed. Reservation details: <br/>Date/Time: ${session.dialogData.reservationDate} <br/>Party size: ${session.dialogData.partySize} <br/>Reservation name: ${session.dialogData.reservationName}`);
        session.endDialog();
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 
```

Neste exemplo, a caixa de diálogo padrão tem quatro funções, cada uma deles representando uma etapa na cascata. Cada etapa solicita a entrada do usuário e envia os resultados para a próxima etapa a ser processada. Esse processo continua até que a última etapa seja executada, confirmando assim a reserva e terminando a caixa de diálogo.

A captura de tela a seguir mostra os resultados deste bot em execução no [Emulador do Bot Framework](~/bot-service-debug-emulator.md).

![Gerenciar o fluxo da conversa com o recurso de cascata](~/media/bot-builder-nodejs-dialog-manage-conversation/waterfall-results.png)

## <a name="create-a-conversation-with-multiple-waterfalls"></a>Criar uma conversa com vários cascatas

Você pode usar vários cascatas dentro de uma conversa para definir qualquer estrutura de conversa exigida pelo seu bot. Por exemplo, você pode usar uma cascata para gerenciar o fluxo da conversa e usar as cascatas adicionais para coletar informações do usuário. Cada cascata é encapsulada em uma caixa de diálogo e pode ser invocada ao chamar a função `session.beginDialog`.

O exemplo de código a seguir mostra como usar as várias cascatas em uma conversa. A cascata dentro da caixa de diálogo `greetings` gerencia o fluxo da conversa, enquanto a cascata dentro da caixa de diálogo `askName` coleta informações do usuário.

```javascript
bot.dialog('greetings', [
    function (session) {
        session.beginDialog('askName');
    },
    function (session, results) {
        session.endDialog('Hello %s!', results.response);
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

## <a name="advance-the-waterfall"></a>Avançar na cascata

Uma cascata progride passo a passo na sequência que as funções são definidas na matriz. A primeira função dentro de uma cascata pode receber os argumentos que são passados para a caixa de diálogo. Qualquer função dentro de uma cascata pode usar a função `next` para continuar para a próxima etapa sem solicitar entrada do usuário.

O exemplo de código a seguir mostra como usar a função `next` dentro de uma caixa de diálogo que orienta o usuário durante o processo de fornecer informações para seu perfil do usuário. Em cada etapa de cascata de, o bot solicita ao usuário uma parte das informações (se necessário) e a resposta do usuário (se houver) é processada pela etapa subsequente da cascata. A etapa final de cascata na caixa de diálogo `ensureProfile` termina essa caixa de diálogo e retorna as informações de perfil completas para a caixa de diálogo de chamada que, em seguida, envia uma saudação personalizada para o usuário.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This bot ensures user's profile is up to date.
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.beginDialog('ensureProfile', session.userData.profile);
    },
    function (session, results) {
        session.userData.profile = results.response; // Save user profile.
        session.send(`Hello ${session.userData.profile.name}! I love ${session.userData.profile.company}!`);
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 

bot.dialog('ensureProfile', [
    function (session, args, next) {
        session.dialogData.profile = args || {}; // Set the profile or create the object.
        if (!session.dialogData.profile.name) {
            builder.Prompts.text(session, "What's your name?");
        } else {
            next(); // Skip if we already have this info.
        }
    },
    function (session, results, next) {
        if (results.response) {
            // Save user's name if we asked for it.
            session.dialogData.profile.name = results.response;
        }
        if (!session.dialogData.profile.company) {
            builder.Prompts.text(session, "What company do you work for?");
        } else {
            next(); // Skip if we already have this info.
        }
    },
    function (session, results) {
        if (results.response) {
            // Save company name if we asked for it.
            session.dialogData.profile.company = results.response;
        }
        session.endDialogWithResult({ response: session.dialogData.profile });
    }
]);
```

> [!NOTE]
> Este exemplo usa dois pacotes de dados diferentes para armazenar dados: `dialogData` e `userData`. Se o bot é distribuído entre vários nós de computação, cada etapa da cascata pode ser processada por um nó diferente. Confira mais informações sobre o armazenamento de dados do estado em [Gerenciar dados de estado](bot-builder-nodejs-state.md).

## <a name="end-a-waterfall"></a>Terminar uma cascata

Uma caixa de diálogo que é criada usando uma cascata deverá ser encerrada explicitamente, caso contrário, o bot repetirá a cascata indefinidamente. Você pode encerrar uma cascata usando um dos seguintes métodos:

* `session.endDialog`: Use esse método para encerrar a cascata se não houver nenhum dado para retornar à caixa de diálogo de chamada.

* `session.endDialogWithResult`: Use esse método para encerrar a cascata se houver dados para retornar à caixa de diálogo de chamada. O argumento `response` que é retornado pode ser um objeto JSON ou qualquer tipo de dado primitivo JavaScript. Por exemplo: 
  ```javascript
  session.endDialogWithResult({
    response: { name: session.dialogData.name, company: session.dialogData.company }
  });
  ```

* `session.endConversation`: Use esse método para encerrar a cascata se o final da cascata representa o fim da conversa.

Como alternativa ao uso de um desses três métodos para encerrar uma cascata, você pode anexar o gatilho `endConversationAction` à caixa de diálogo. Por exemplo: 

```javascript
bot.dialog('dinnerOrder', [
    //...waterfall steps...,
    // Last step
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${session.dialogData.room}`;
            session.endConversation(msg);
        }
    }
])
.endConversationAction(
    "endOrderDinner", "Ok. Goodbye.",
    {
        matches: /^cancel$|^goodbye$/i,
        confirmPrompt: "This will cancel your order. Are you sure?"
    }
);
```

## <a name="next-steps"></a>Próximas etapas

Usando o recurso de cascata, você pode coletar informações do usuário com *prompts*. Vamos nos aprofundar em como você pode solicitar entrada do usuário.

> [!div class="nextstepaction"]
> [Solicitar entrada ao usuário](bot-builder-nodejs-dialog-prompt.md)
