---
title: Reconhecer intenções e entidades com o LUIS | Microsoft Docs
description: Integre um bot ao LUIS para detectar a intenção do usuário e responder adequadamente disparando diálogos usando o SDK do Bot Builder para Node.js.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/28/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 82a4d0843a9aaab25779d833f2b1b1d2ab2516c2
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905098"
---
# <a name="recognize-intents-and-entities-with-luis"></a>Reconhecer intenções e entidades com o LUIS 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Este artigo usa o exemplo de um bot para fazer anotações, visando demonstrar como o Reconhecimento Vocal ([LUIS][LUIS]) ajuda o bot a responder adequadamente à entrada de idioma natural. Um bot detecta o que um usuário deseja fazer identificando a **intenção** desse usuário. A intenção é determinada por entradas textuais ou faladas, ou por **enunciados**. A intenção mapeia enunciados para ações que o bot executa, como chamar um diálogo. Um bot também pode precisar extrair **entidades**, que são palavras importantes em enunciados. Às vezes, entidades são necessárias para cumprir uma intenção. No exemplo de um bot de anotações, a entidade `Notes.Title` identifica o título de cada nota.

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Criar um bot de Reconhecimento vocal com o serviço de bot

1. No [portal do Azure](https://portal.azure.com), selecione **Criar novo recurso** na folha de menu e clique em **Ver tudo**.

    ![Criar novo recurso](../media/bot-builder-nodejs-use-luis/bot-service-creation.png)

2. Na caixa de pesquisa, pesquise **Bot do aplicativo Web**. 

    ![Criar novo recurso](../media/bot-builder-nodejs-use-luis/bot-service-selection.png)

3. Na folha **Serviço de Bot**, forneça as informações necessárias e clique em **Criar**. Isso cria e implanta o serviço de bot e o aplicativo LUIS no Azure. 
   * Defina **Nome do aplicativo** como o nome do bot. O nome é usado como o subdomínio quando o bot é implantado na nuvem (por exemplo, mynotesbot.azurewebsites.net). Esse nome também é usado como o nome do aplicativo LUIS associado ao bot. Copie-o para uso posterior, de modo a localizar o aplicativo LUIS associado ao bot.
   * Selecione a assinatura, o [grupo de recursos](/azure/azure-resource-manager/resource-group-overview), o plano do serviço de aplicativo e o [local](https://azure.microsoft.com/en-us/regions/).
   * Selecione o modelo **Reconhecimento vocal (Node.js)** para o campo **Modelo de bot**.

     ![Folha Serviço de bot](../media/bot-builder-nodejs-use-luis/bot-service-setting-callout-template.png)

   * Marque a caixa para confirmar os termos de serviço.

4. Confirme que o serviço de bot foi implantado.
    * Clique em Notificações (o ícone de sino que fica ao longo da borda superior do portal do Azure). A notificação será alterada de **Implantação iniciada** para **Implantação bem-sucedida**.
    * Depois que a notificação mudar para **Implantação bem-sucedida**, clique em **Ir para o recurso** nessa notificação.

## <a name="try-the-bot"></a>Experimentar o bot

Confirme que o bot foi implantado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique no botão **Ir para recurso** para abrir a folha de recursos do bot.

Depois que o bot está registrado, clique em **Teste no chat da Web** para abrir o painel do chat da Web. Digite "hello" no Webchat.

  ![Testar o bot no Webchat](../media/bot-builder-nodejs-use-luis/bot-service-web-chat.png)

O bot responde dizendo "Você atingiu a Saudação. Você disse: Olá". Isso confirma que o bot recebeu sua mensagem e a passou para um aplicativo LUIS padrão que ele criou. Este aplicativo LUIS padrão detectou uma intenção de Saudação.

## <a name="modify-the-luis-app"></a>Modificar o aplicativo LUIS

Faça logon no [https://www.luis.ai](https://www.luis.ai) usando a mesma conta usada para fazer logon no Azure. Clique em **Meus aplicativos**. Na lista de aplicativos, localize o aplicativo que começa com o nome especificado em **Nome do aplicativo** na folha **Serviço de Bot** quando você criou o Serviço de Bot. 

O aplicativo LUIS inicia com 4 intenções: Cancelar: Saudação, Ajuda e Nenhuma. <!-- picture -->

As etapas a seguir adicionam as intenções Note.Create, Note.ReadAloud e Note.Delete: 

1. Clique em **Domínios Pré-criados** no canto inferior esquerdo da página. Encontre o domínio **Note** e clique em **Adicionar domínio**.

2. Este tutorial não usa todas as intenções incluídas no domínio pré-criado **Note**. Na página **Intenções**, clique em cada um dos nomes de intenção a seguir e, em seguida, clique no botão **Excluir Intenção**.
   * Note.ShowNext
   * Note.DeleteNoteItem
   * Note.Confirm
   * Note.Clear
   * Note.CheckOffItem
   * Note.AddToNote

   As únicas intenções que devem permanecer no aplicativo LUIS são as seguintes: 
   * Note.ReadAloud
   * Note.Create
   * Note.Delete
   * Nenhum
   * Ajuda
   * Saudação
   * Cancelar

     ![intenções mostradas no aplicativo LUIS](../media/bot-builder-nodejs-use-luis/luis-intent-list.png)


3.  Clique no botão **Treinar** no canto superior direito para treinar o aplicativo.
4.  Clique em **PUBLISH** na barra de navegação superior para abrir a página **Publicar**. Clique no botão **Publicar no slot de produção**. Após a publicação bem-sucedida, um aplicativo LUIS será implantado na coluna **Ponto de extremidade**, na página **Publicar Aplicativo**, na linha que inicia com o Nome de Recurso Starter_Key. A URL tem um formato semelhante a este exemplo: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?subscription-key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx&timezoneOffset=0&verbose=true&q=`. A chave de ID e a assinatura do aplicativo nessa URL são os mesmos que LuisAppId e LuisAPIKey em * * Configurações do Serviço de Aplicativo > ApplicationSettings > Configurações do Aplicativo * *


## <a name="modify-the-bot-code"></a>Modificar o código do bot

Clique em **Criar** e, em seguida, clique em **Abrir editor de código online**.

   ![Abrir editor de código online](../media/bot-builder-nodejs-use-luis/bot-service-build.png)

No editor de código, abra `app.js`. Ele contém o seguinte código:

```javascript
var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/en-us/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})

```


> [!TIP] 
> Também é possível localizar o código de exemplo descrito neste artigo no [Exemplo de bot de anotações][NotesSample].



## <a name="edit-the-default-message-handler"></a>Editar o manipulador de mensagens padrão
O bot tem um manipulador de mensagens padrão. Edite-o para corresponder ao seguinte: 
```javascript
// Create your bot with a function to receive messages from the user.
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send("Hi... I'm the note bot sample. I can create new notes, read saved notes to you and delete notes.");

   // If the object for storing notes in session.userData doesn't exist yet, initialize it
   if (!session.userData.notes) {
       session.userData.notes = {};
       console.log("initializing userData.notes in default message handler");
   }
});
```

## <a name="handle-the-notecreate-intent"></a>Manipular a intenção Note.Create

Copie o código a seguir e cole-o no final do app.js:

```javascript
// CreateNote dialog
bot.dialog('CreateNote', [
    function (session, args, next) {
        // Resolve and store any Note.Title entity passed from LUIS.
        var intent = args.intent;
        var title = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');

        var note = session.dialogData.note = {
          title: title ? title.entity : null,
        };
        
        // Prompt for title
        if (!note.title) {
            builder.Prompts.text(session, 'What would you like to call your note?');
        } else {
            next();
        }
    },
    function (session, results, next) {
        var note = session.dialogData.note;
        if (results.response) {
            note.title = results.response;
        }

        // Prompt for the text of the note
        if (!note.text) {
            builder.Prompts.text(session, 'What would you like to say in your note?');
        } else {
            next();
        }
    },
    function (session, results) {
        var note = session.dialogData.note;
        if (results.response) {
            note.text = results.response;
        }
        
        // If the object for storing notes in session.userData doesn't exist yet, initialize it
        if (!session.userData.notes) {
            session.userData.notes = {};
            console.log("initializing session.userData.notes in CreateNote dialog");
        }
        // Save notes in the notes object
        session.userData.notes[note.title] = note;

        // Send confirmation to user
        session.endDialog('Creating note named "%s" with text "%s"',
            note.title, note.text);
    }
]).triggerAction({ 
    matches: 'Note.Create',
    confirmPrompt: "This will cancel the creation of the note you started. Are you sure?" 
}).cancelAction('cancelCreateNote', "Note canceled.", {
    matches: /^(cancel|nevermind)/i,
    confirmPrompt: "Are you sure?"
});
```

Quaisquer entidades no enunciado serão passadas para a caixa de diálogo usando o parâmetro `args`. A primeira etapa da [cascata][waterfall] chama [EntityRecognizer.findEntity][EntityRecognizer_findEntity] para obter o título da anotação de quaisquer entidades `Note.Title` na resposta do LUIS. Se o aplicativo LUIS não detectar uma entidade `Note.Title`, o bot solicita ao usuário o nome da anotação. A segunda etapa da cascata de solicita o texto a ser incluído na anotação. Assim que o bot obtém o texto da anotação, a terceira etapa usa [session.userData][session_userData] para salvar a anotação em um objeto `notes`, usando o título como a chave. Para obter mais informações sobre `session.UserData`, consulte [Gerenciar dados de estado](./bot-builder-nodejs-state.md). 



## <a name="handle-the-notedelete-intent"></a>Manipular a intenção Note.Delete
Assim como para a intenção `Note.Create`, o bot examina o parâmetro `args` para um título. Se nenhum título for detectado, o bot solicita ao usuário. O título é usado para pesquisar a anotação para excluir do `session.userData.notes`. 



Copie o código a seguir e cole-o no final do app.js:
```javascript
// Delete note dialog
bot.dialog('DeleteNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify that the title is in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to delete?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to delete.");
        }
    },
    function (session, results) {
        delete session.userData.notes[results.response.entity];        
        session.endDialog("Deleted the '%s' note.", results.response.entity);
    }
]).triggerAction({
    matches: 'Note.Delete'
}).cancelAction('cancelDeleteNote', "Ok - canceled note deletion.", {
    matches: /^(cancel|nevermind)/i
});
```

O código que manipula `Note.Delete` usa a função `noteCount` para determinar se o objeto `notes` contém anotações. 

Cole a função auxiliar `noteCount` no final do `app.js`.

[!code-js[Add a helper function that returns the number of notes (JavaScript)](../includes/code/node-basicNote.js#CountNotesHelper)]

## <a name="handle-the-notereadaloud-intent"></a>Manipular a intenção Note.ReadAloud

Copie o código a seguir e cole-o no `app.js` após o manipulador para `Note.Delete`:

```javascript
// Read note dialog
bot.dialog('ReadNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
           
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify it's in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to read?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to read.");
        }
    },
    function (session, results) {        
        session.endDialog("Here's the '%s' note: '%s'.", results.response.entity, session.userData.notes[results.response.entity].text);
    }
]).triggerAction({
    matches: 'Note.ReadAloud'
}).cancelAction('cancelReadNote', "Ok.", {
    matches: /^(cancel|nevermind)/i
});

```

O objeto `session.userData.notes` é passado como o terceiro argumento para `builder.Prompts.choice`, para que o prompt exiba uma lista de anotações para o usuário.

Agora que você já adicionou os manipuladores para as novas intenções, o código completo para `app.js` contém o seguinte:

```javascript
var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user.
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send("Hi... I'm the note bot sample. I can create new notes, read saved notes to you and delete notes.");

   // If the object for storing notes in session.userData doesn't exist yet, initialize it
   if (!session.userData.notes) {
       session.userData.notes = {};
       console.log("initializing userData.notes in default message handler");
   }
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// CreateNote dialog
bot.dialog('CreateNote', [
    function (session, args, next) {
        // Resolve and store any Note.Title entity passed from LUIS.
        var intent = args.intent;
        var title = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');

        var note = session.dialogData.note = {
          title: title ? title.entity : null,
        };
        
        // Prompt for title
        if (!note.title) {
            builder.Prompts.text(session, 'What would you like to call your note?');
        } else {
            next();
        }
    },
    function (session, results, next) {
        var note = session.dialogData.note;
        if (results.response) {
            note.title = results.response;
        }

        // Prompt for the text of the note
        if (!note.text) {
            builder.Prompts.text(session, 'What would you like to say in your note?');
        } else {
            next();
        }
    },
    function (session, results) {
        var note = session.dialogData.note;
        if (results.response) {
            note.text = results.response;
        }
        
        // If the object for storing notes in session.userData doesn't exist yet, initialize it
        if (!session.userData.notes) {
            session.userData.notes = {};
            console.log("initializing session.userData.notes in CreateNote dialog");
        }
        // Save notes in the notes object
        session.userData.notes[note.title] = note;

        // Send confirmation to user
        session.endDialog('Creating note named "%s" with text "%s"',
            note.title, note.text);
    }
]).triggerAction({ 
    matches: 'Note.Create',
    confirmPrompt: "This will cancel the creation of the note you started. Are you sure?" 
}).cancelAction('cancelCreateNote', "Note canceled.", {
    matches: /^(cancel|nevermind)/i,
    confirmPrompt: "Are you sure?"
});

// Delete note dialog
bot.dialog('DeleteNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify that the title is in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to delete?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to delete.");
        }
    },
    function (session, results) {
        delete session.userData.notes[results.response.entity];        
        session.endDialog("Deleted the '%s' note.", results.response.entity);
    }
]).triggerAction({
    matches: 'Note.Delete'
}).cancelAction('cancelDeleteNote', "Ok - canceled note deletion.", {
    matches: /^(cancel|nevermind)/i
});


// Read note dialog
bot.dialog('ReadNote', [
    function (session, args, next) {
        if (noteCount(session.userData.notes) > 0) {
           
            // Resolve and store any Note.Title entity passed from LUIS.
            var title;
            var intent = args.intent;
            var entity = builder.EntityRecognizer.findEntity(intent.entities, 'Note.Title');
            if (entity) {
                // Verify it's in our set of notes.
                title = builder.EntityRecognizer.findBestMatch(session.userData.notes, entity.entity);
            }
            
            // Prompt for note name
            if (!title) {
                builder.Prompts.choice(session, 'Which note would you like to read?', session.userData.notes);
            } else {
                next({ response: title });
            }
        } else {
            session.endDialog("No notes to read.");
        }
    },
    function (session, results) {        
        session.endDialog("Here's the '%s' note: '%s'.", results.response.entity, session.userData.notes[results.response.entity].text);
    }
]).triggerAction({
    matches: 'Note.ReadAloud'
}).cancelAction('cancelReadNote', "Ok.", {
    matches: /^(cancel|nevermind)/i
});


// Helper function to count the number of notes stored in session.userData.notes
function noteCount(notes) {

    var i = 0;
    for (var name in notes) {
        i++;
    }
    return i;
}
```

## <a name="test-the-bot"></a>Testar o bot

No portal do Azure, clique em **Testar no Webchat** para testar o bot. Tente digitar mensagens, como "Criar uma anotação", "ler minhas anotações" e "excluir anotações" para chamar as intenções que você adicionou a ao bot.
   ![Testar o bot de anotações no Webchat](../media/bot-builder-nodejs-use-luis/bot-service-test-notebot.png)

> [!TIP]
> Se você achar que seu bot nem sempre reconhece a intenção ou entidades corretas, melhore o desempenho do seu aplicativo LUIS dando mais declarações de exemplo para treiná-lo. É possível treinar novamente seu aplicativo LUIS sem nenhuma modificação no código do seu bot. Confira [Add example utterances](/azure/cognitive-services/LUIS/add-example-utterances) (Adicionar declarações de exemplo) e [treinar e testar seu aplicativo LUIS](/azure/cognitive-services/LUIS/train-test).


## <a name="next-steps"></a>Próximas etapas

Ao tentar o bot, é possível ver que o reconhecedor pode disparar a interrupção do diálogo atualmente ativo. Permitir e manipular as interrupções é um design flexível que leva em conta o que os usuários realmente fazem. Saiba mais sobre as diversas ações que você pode associar a uma intenção reconhecida.

> [!div class="nextstepaction"]
> [Manipular ações do usuário](bot-builder-nodejs-dialog-actions.md)


[LUIS]: https://www.luis.ai/

[intentDialog]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: /azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/en-us/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html
