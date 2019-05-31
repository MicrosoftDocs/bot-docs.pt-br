---
title: Referência rápida da migração do JavaScript v3 para v4 | Microsoft Docs
description: Uma descrição das principais diferenças no SDK v3 e v4 do Bot Framework para JavaScript.
keywords: JavaScript, migração de bot, diálogos, bot v3
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a98110f15277ba9e2a4658d307dc7ec09b711d1a
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215592"
---
# <a name="javascript-migration-quick-reference"></a>Referência rápida de migração do JavaScript

O SDK v4 do BotBuilder para JavaScript introduz várias mudanças fundamentais que afetam como os bots são criados. O objetivo deste guia é fornecer uma referência rápida sobre as diferenças comuns na realização de uma tarefa nos SDKs v3 e v4.

- A forma como as informações passam entre canais e um bot foi alterada.
    No v3, você usava os objetos _connector_ e _session_.
    No v4, eles são substituídos pelos objetos _adapter_ e _turn context_.

- Além disso, as instâncias de caixa de diálogo e bot foram ainda mais separadas.
    No v3, as caixas de diálogo eram registradas diretamente no construtor do bot.
    Agora, no v4, você passa as caixas de diálogo para as instâncias do bot como argumentos, fornecendo melhor flexibilidade na composição.

- Além disso, o v4 fornece uma classe `ActivityHandler`, que ajuda a automatizar o manuseio de tipos diferentes de atividades, como _message_, _conversation update_ e _event_.

Essas melhorias resultam em mudanças na sintaxe para desenvolvimento de bots no JavaScript, especialmente com relação à criação de objetos de bot, definição de caixas de diálogo e lógica de manuseio de evento de codificação.

O restante deste tópico compara as construções do SDK v3 do Bot Framework para JavaScript com seu equivalente no v4.

## <a name="to-listen-for-incoming-requests"></a>Para escutar solicitações de entrada

### <a name="v3"></a>v3

```javascript
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

server.post('/api/messages', connector.listen());
```

### <a name="v4"></a>v4

```javascript
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        await bot.run(context);
    });
});
```

## <a name="to-create-a-bot-instance"></a>Para criar uma instância de bot

### <a name="v3"></a>v3

```javascript
var bot = new builder.UniversalBot(connector, [ ...DIALOG_STEPS ]);
```

### <a name="v4"></a>v4

```javascript
// Define the bot class as extending ActivityHandler.
const { ActivityHandler } = require('botbuilder');

class MyBot extends ActivityHandler {
    // ...
}

// Instantiate a new bot instance.
const bot = new MyBot(conversationState, dialog);
```

## <a name="to-send-a-message-to-a-user"></a>Para enviar uma mensagem para um usuário

### <a name="v3"></a>v3

```javascript
session.send('Hello and welcome to the help desk bot.');
```

### <a name="v4"></a>v4

```javascript
await context.sendActivity('Hello and welcome to the help desk bot.');
```

## <a name="to-define-a-default-dialog"></a>Para definir uma caixa de diálogo padrão

### <a name="v3"></a>v3

```javascript
var bot = new builder.UniversalBot(connector, [
    // Add default dialog waterfall steps...
]);
```

### <a name="v4"></a>v4

```javascript
// In the main dialog class, define a run method.
async run(turnContext, accessor) {
    const dialogSet = new DialogSet(accessor);
    dialogSet.add(this);

    const dialogContext = await dialogSet.createContext(turnContext);
    const results = await dialogContext.continueDialog();
    if (results.status === DialogTurnStatus.empty) {
        await dialogContext.beginDialog(this.id);
    }
}

// Pass conversation state management and a main dialog objects to the bot (inindex.js).
const bot = new MyBot(conversationState, dialog);

// Inside the bot's constructor, add the dialog as a member property and define a DialogState property accessor.
this.dialog = dialog;
this.dialogState = this.conversationState.createProperty('DialogState');

// Inside the bot's message handler, invoke the dialog's run method, passing in the turn context and DialogState accessor.
this.onMessage(async (context, next) => {
    // Run the Dialog with the new message Activity.
    await this.dialog.run(context, this.dialogState);
    await next();
});
```

## <a name="to-start-a-child-dialog"></a>Para iniciar uma caixa de diálogo filho

A caixa de diálogo pai é retomada após o término da caixa de diálogo filho.

### <a name="v3"></a>v3

```javascript
session.beginDialog(DIALOG_ID);
```

### <a name="v4"></a>v4

```javascript
await context.beginDialog(DIALOG_ID);
```

## <a name="to-replace-a-dialog"></a>Para substituir uma caixa de diálogo

A caixa de diálogo atual é substituída na pilha pela nova caixa de diálogo.

### <a name="v3"></a>v3

```javascript
session.replaceDialog(DIALOG_ID);
```

### <a name="v4"></a>v4

```javascript
await context.replaceDialog(DIALOG_ID);
```

## <a name="to-end-a-dialog"></a>Para encerrar uma caixa de diálogo

### <a name="v3"></a>v3

```javascript
session.endDialog();
```

### <a name="v4"></a>v4

Você pode incluir um valor retornado opcional.

```javascript
await context.endDialog(returnValue);
```

## <a name="to-register-a-dialog-with-a-bot-instance"></a>Para registrar uma caixa de diálogo com uma instância de bot

### <a name="v3"></a>v3

```javascript
// Create the bot.
var bot = new builder.UniversalBot(connector, [
    // ...
]};

// Add the dialog to the bot.
bot.dialog('myDialog', require('./mydialog'));
```

### <a name="v4"></a>v4

```javascript
// In the main dialog class constructor.
this.addDialog(new MyDialog(DIALOG_ID));

// In the app entry point file (index.js)
const { MainDialog } = require('./dialogs/main');

// Create the base dialog and bot, passing the main dialog as an argument.
const dialog = new MainDialog();
const reservationBot = new ReservationBot(conversationState, dialog);
```

## <a name="to-prompt-a-user-for-input"></a>Para solicitar a entrada a um usuário

### <a name="v3"></a>v3

```javascript
var builder = require('botbuilder');
builder.Prompts.text(session, 'Please enter your destination');
```

### <a name="v4"></a>v4

```javascript
const { TextPrompt } = require('botbuilder-dialogs');

// In the dialog's constructor, register the prompt.
this.addDialog(new TextPrompt('initialPrompt'));

// In the dialog step, invoke the prompt.
return await stepContext.prompt(
    'initialPrompt', {
        prompt: 'Please enter your destination'
    }
);
```

## <a name="to-retrieve-the-users-response-to-a-prompt"></a>Para recuperar a resposta do usuário a uma solicitação

### <a name="v3"></a>v3

```javascript
function (session, result) {
    var destination = result.response;
    // ...
}
```

### <a name="v4"></a>v4

```javascript
// In the dialog step after the prompt step, retrieve the result from the waterfall step context.
async nextStep(stepContext) {
    const destination = stepContext.result;
    // ...
}
```

## <a name="to-save-information-to-dialog-state"></a>Para salvar as informações para o estado da caixa de diálogo

### <a name="v3"></a>v3

```javascript
session.dialogData.destination = results.response;
```

### <a name="v4"></a>v4

```javascript
// In a dialog, set the value in the waterfall step context.
stepContext.values.destination = destination;
```

## <a name="to-write-changes-in-state-to-the-persistance-layer"></a>Para gravar as alterações no estado para a camada de persistência

### <a name="v3"></a>v3

```javascript
// State data is saved by default at the end of the turn.
// Do this to save it manually.
session.save();
```

### <a name="v4"></a>v4

```javascript
// State changes are not saved automatically at the end of the turn.
await this.conversationState.saveChanges(context, false);
await this.userState.saveChanges(context, false);
```

## <a name="to-create-and-register-state-storage"></a>Para criar e registrar o armazenamento do estado

### <a name="v3"></a>v3

```javascript
var builder = require('botbuilder');

// Create conversation state with in-memory storage provider.
var inMemoryStorage = new builder.MemoryBotStorage();

// Create the base dialog and bot
var bot = new builder.UniversalBot(connector, [ /*...*/ ]).set('storage', inMemoryStorage);
```

### <a name="v4"></a>v4

```javascript
const { MemoryStorage } = require('botbuilder');

// Create the memory layer object.
const memoryStorage = new MemoryStorage();

// Create the conversation state management object, using the storage provider.
const conversationState = new ConversationState(memoryStorage);

// Create the base dialog and bot.
const dialog = new MainDialog();
const reservationBot = new ReservationBot(conversationState, dialog);
```

## <a name="to-catch-an-error-thrown-from-a-dialog"></a>Para capturar um erro lançado de uma caixa de diálogo

### <a name="v3"></a>v3

```javascript
// Set up the error handler.
session.on('error', function (err) {
    session.send('Failed with message:', err.message);
    session.endDialog();
});

// Throw an error.
session.error('An error has occurred.');
```

### <a name="v4"></a>v4

```javascript
// Set up the error handler, using the adapter.
adapter.onTurnError = async (context, error) => {
    const errorMsg = error.message

    // Clear out conversation state to avoid keeping the conversation in a corrupted bot state.
    await conversationState.delete(context);

    // Send a message to the user.
    await context.sendActivity(errorMsg);
};

// Throw an error.
async () => {
    throw new Error('An error has occurred.');
}
```

## <a name="to-register-activity-handlers"></a>Para registrar os manipuladores de atividades

### <a name="v3"></a>v3

```javascript
bot.on('conversationUpdate', function (message) {
    // ...
}

bot.on('contactRelationUpdate', function (message) {
    // ...
}
```

### <a name="v4"></a>v4

```javascript
// In the bot's constructor, add the handlers.
this.onMessage(async (context, next) => {
    // ...
}
  
this.onDialog(async (context, next) => {
    // ...
}

this.onMembersAdded(async (context, next) => {
    // ...
}
```

## <a name="to-send-attachments"></a>Para enviar anexos

### <a name="v3"></a>v3

```javascript
var message = new builder.Message()
    .attachments(hotelHeroCards
    .attachmentLayout(builder.AttachmentLayout.carousel));
```

### <a name="v4"></a>v4

```javascript
await context.sendActivity({
    attachments: hotelHeroCards,
    attachmentLayout: AttachmentLayoutTypes.Carousel
});
```
