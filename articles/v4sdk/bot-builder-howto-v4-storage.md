---
title: Gravar diretamente no armazenamento | Microsoft Docs
description: Saiba como gravar diretamente no armazenamento com V4 do SDK do Bot Builder para .NET.
keywords: armazenamento, ler e gravar, armazenamento de memória, eTag
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/2/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 76f8976aefe3d4fefcffc46e691dbd0b35e41ec7
ms.sourcegitcommit: 1abc32353c20acd103e0383121db21b705e5eec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42756511"
---
# <a name="write-directly-to-storage"></a>Gravar diretamente no armazenamento

<!--
 Note for V4: You can write directly to storage without using the state manager. Therefore, this topic isn't called "managing state". State is in a separate topic.
 ## Manage state data by writing directly to storage
-->
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

É possível gravar diretamente no armazenamento sem usar o objeto de contexto ou middleware, lendo e gravando diretamente no objeto de armazenamento.

Este exemplo de código mostra como ler e gravar dados no armazenamento. Neste exemplo o armazenamento é um arquivo, mas é possível facilmente alterar o código para inicializar o objeto de armazenamento e usar o armazenamento de memória em vez do Armazenamento de Tabelas do Azure. 

# <a name="ctabcsharpechorproperty"></a>[C#](#tab/csharpechorproperty)
Definiremos um objeto e usaremos `IStorage.Write` e `IStorage.Read` para salvar e recuperar o estado. 

O exemplo a seguir adiciona todas as mensagens do usuário a uma lista. A estrutura de dados que contém a lista é salva em um arquivo dentro do diretório que você fornece ao construtor `FileStorage`.

Inicie com o modelo do Visual Studio do EchoBot no SDK do BotBuilder V4.
Edite o arquivo `EchoBot.cs` . Adicione o seguinte usando instruções e substitua a definição de classe `EchoBot`.

```csharp
using System.Collections.Generic;
using System.Linq;
```

```csharp
// In the constructor initialize file storage
public class EchoBot : IBot
{
    private readonly FileStorage _myStorage;

    public EchoBot()
    {
        _myStorage = new FileStorage(System.IO.Path.GetTempPath());
    }

    // Add a class for storing a log of utterances (text of messages) as a list
    public class UtteranceLog : IStoreItem
    {
        // A list of things that users have said to the bot
        public List<string> UtteranceList { get; private set; } = new List<string>();

        // The number of conversational turns that have occurred        
        public int TurnNumber { get; set; } = 0;

        public string eTag { get; set; } = "*";
    }

    // Replace the OnTurn in EchoBot.cs with the following:
    public async Task OnTurn(ITurnContext context)
    {
        var activityType = context.Activity.Type;

        await context.SendActivity($"Activity type: {context.Activity.Type}.");

        if (activityType == ActivityTypes.Message)
        {
            // *********** begin (create or add to log of messages)
            var utterance = context.Activity.Text;
            bool restartList = false;

            if (utterance.Equals("restart"))
            {
                restartList = true;
            }

            // Attempt to read the existing property bag
            UtteranceLog logItems = null;
            try
            {
                logItems = _myStorage.Read<UtteranceLog>("UtteranceLog").Result?.FirstOrDefault().Value;
            }
            catch (System.Exception ex)
            {
                await context.SendActivity(ex.Message);
            }

            // If the property bag wasn't found, create a new one
            if (logItems is null)
            {
                try
                {
                    // add the current utterance to a new object.
                    logItems = new UtteranceLog();
                    logItems.UtteranceList.Add(utterance);

                    await context.SendActivity($"The list is now: {string.Join(", ",logItems.UtteranceList)}");

                    var changes = new KeyValuePair<string, object>[]
                    {
                        new KeyValuePair<string, object>("UtteranceLog", logItems)
                    };
                    await _myStorage.Write(changes);
                }
                catch (System.Exception ex)
                {
                    await context.SendActivity(ex.Message);
                }
            }
            // logItems.ContainsKey("UtteranceLog") == true, we were able to read a log from storage
            else
            {
                // Modify its property
                if (restartList)
                {
                    logItems.UtteranceList.Clear();
                }
                else
                {
                    logItems.UtteranceList.Add(utterance);
                    logItems.TurnNumber++;
                }

                await context.SendActivity($"The list is now: {string.Join(", ", logItems.UtteranceList)}");

                var changes = new KeyValuePair<string, object>[]
                {
                        new KeyValuePair<string, object>("UtteranceLog", logItems)
                };
                await _myStorage.Write(changes);
            }
        }

        return;
    }
}
```
# <a name="javascripttabjsechoproperty"></a>[JavaScript](#tab/jsechoproperty)

O exemplo a seguir adiciona todas as mensagens do usuário a uma lista. A estrutura de dados que contém a lista é salva em um arquivo dentro do diretório que você fornece ao construtor `FileStorage`.

Cole o seguinte em `app.js`.
``` javascript
const { BotFrameworkAdapter, FileStorage, MemoryStorage, ConversationState, BotStateSet } = require('botbuilder');
const restify = require('restify');

// Create server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter.
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

// Add storage.
var storage = new FileStorage("C:/temp");
const conversationState = new ConversationState(storage);
adapter.use(new BotStateSet(conversationState));

// Listen for incoming activities.
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing.
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            let utterance = context.activity.text;
            let storeItems = await storage.read(["UtteranceLog"])
            try {
                // check result
                var utteranceLog = storeItems["UtteranceLog"];
                
                if (typeof (utteranceLog) != 'undefined') {
                    // log exists so we can write to it
                    storeItems["UtteranceLog"].UtteranceList.push(utterance);
                    
                    await storage.write(storeItems)
                    try {
                        context.sendActivity('Successful write.');
                    } catch (err) {
                        context.sendActivity(`Srite failed of UtteranceLog: ${err}`);
                    }

                } else {
                    context.sendActivity(`need to create new utterance log`);
                    storeItems["UtteranceLog"] = { UtteranceList: [`${utterance}`], "eTag": "*" }
                    await storage.write(storeItems)
                    try {
                        context.sendActivity('Successful write.');
                    } catch (err) {
                        context.sendActivity(`Write failed: ${err}`);
                    }
                }
            } catch (err) {
                context.sendActivity(`Read rejected. ${err}`);
            };

            await context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```
---

## <a name="manage-concurrency-using-etags"></a>Gerenciar simultaneidade usando eTags

No exemplo anterior, você definiu a propriedade `eTag` para `*`. O membro (marca da entidade) `eTag` do objeto de armazenamento é para gerenciar simultaneidade. O `eTag` indica o que deverá ser feito se outra instância do bot alterou o objeto no armazenamento onde o bot está gravando. 

<!-- define optimistic concurrency -->

### <a name="last-write-wins---allow-overwrites"></a>A última gravação prevalece - permite substituições

Defina a eTag como `*` para permitir que outras instâncias do bot substituam dados gravados anteriormente. Um valor da propriedade `eTag` do asterisco (`*`) indica que o último gravador prevalece. Ao criar um novo armazenamento de dados, é possível definir `eTag` de uma propriedade como `*` para indicar que os dados em gravação não foram salvos anteriormente, ou que você deseja que o último gravador substitua qualquer propriedade salva anteriormente. Se a simultaneidade não for um problema para o bot, definir a propriedade `eTag` como `*` a qualquer dado que você estiver gravando permitirá substituições.

Isso é mostrado no exemplo de código a seguir.

# <a name="ctabcsetagoverwrite"></a>[C#](#tab/csetagoverwrite)
Use a classe `UtteranceLog` que definimos anteriormente.
```csharp
// Add the current utterance to a new object and save it to storage.
logItems = new UtteranceLog();
logItems.UtteranceList.Add(utterance);
logItems.eTag = "*";

var changes = new KeyValuePair<string, object>[]
{
    new KeyValuePair<string, object>("UtteranceLog", logItems)
};
await _myStorage.Write(changes);

```
# <a name="javascripttabjstagoverwrite"></a>[JavaScript](#tab/jstagoverwrite)
Adicione uma nova expressão ao log e permita substituir.

```javascript
storeItems["UtteranceLog"] = { UtteranceList: [`${utterance}`], "eTag": "*" }
await storage.write(storeItems)
```
---

### <a name="maintain-concurrency-and-prevent-overwrites"></a>Manter simultaneidade e evitar substituições
Use um valor diferente de `*` para `eTag`, se quiser impedir o acesso simultâneo a uma propriedade para evitar substituir alterações de outra instância do bot. O bot receberá uma resposta de erro com a mensagem `etag conflict key=` ao tentar salvar dados de estado e se `eTag` não for o mesmo que estiver no armazenamento. <!-- To control concurrency of data that is stored using `IStorage`, the BotBuilder SDK checks the entity tag (ETag) for `Storage.Write()` requests. -->

Por padrão, o armazenamento verificará a propriedade `eTag` de um objeto de armazenamento quanto à igualdade sempre que um bot gravar esse item e, em seguida, atualizará para um novo valor exclusivo após cada gravação. Se a propriedade `eTag` na gravação não corresponder com `eTag` no armazenamento, isso significa que outro bot ou thread alterou os dados. 

Por exemplo, digamos que você queira que o bot edite uma nota salva, mas não quer que o bot substitua as alterações feitas por outra instância do bot. Se outra instância do bot tiver feito edições, você quer que o usuário edite a versão com as atualizações mais recentes.

# <a name="ctabcsetag"></a>[C#](#tab/csetag)
Primeiro, crie uma classe que implemente `IStoreItem`.
```csharp
public class Note : IStoreItem
{
    public string Name { get; set; }
    public string Contents { get; set; }
    public string eTag { get; set; }
}
```
Em seguida, crie uma nota inicial criando um objeto de armazenamento e adicione o objeto ao armazenamento.
```csharp
// create a note for the first time, with a non-null, non-* eTag.
var note = new Note { Name = "Shopping List", Contents = "eggs", eTag = "x" };

var changes = new KeyValuePair<string, object>[]
{
    new KeyValuePair<string, object>("Note", note)
};
await NoteStore.Write(changes);
```
Então, acesse e atualize a nota mais tarde, mantendo `eTag` que foi lido no armazenamento.
```csharp
var note = NoteStore.Read<Note>("Note").Result.FirstOrDefault().Value;

if (note != null)
{
    note.Contents += ", bread";
    var changes = new KeyValuePair<string, object>[]
    {
        new KeyValuePair<string, object>("Note1", note)
    };
    await NoteStore.Write(changes);
}
```
Se a nota foi atualizada no armazenamento antes de gravar as alterações, a chamada para `Write` lançará uma exceção.

# <a name="javascripttabjsetag"></a>[JavaScript](#tab/jsetag)

Primeiro, crie objeto `myNote`.
```javascript
var myNote = {
    name: "Shopping List",
    contents: "eggs",
    eTag: "*"
}
```
Em seguida, inicialize um objeto `changes`, adicione as *notas* ao objeto e grave-o no armazenamento.

```javascript
// Write a note
var changes = {};
changes["Note"] = myNote;
await storage.write(changes);
```
Então, acesse e atualize a nota mais tarde, mantendo `eTag` que foi lido no armazenamento.
```javascript
// Read in a note
var note = await storage.read(["Note"]);
try {
    // Someone has updated the note. Need to update our local instance to match
    if(myNote.eTag != note.eTag){
        myNote = note;
    }

    // Add any updates to the note and write back out
    myNote.contents += ", bread";   // Add to the note
    changes["Note"] = note;
    await storage.write(changes); // Write the changes back to storage
    try {
        context.sendActivity('Successful write.');
    } catch (err) {
        context.sendActivity(`Write failed: ${err}`);
    }
}
catch (err) {
    context.sendActivity(`Unable to read the Note: ${err}`);
}
```
Se a nota foi atualizada no armazenamento antes de gravar as alterações, a chamada para `Write` lançará uma exceção.

---

Para manter a simultaneidade, sempre leia uma propriedade do armazenamento e modifique a propriedade lida para que `eTag` seja mantido. Ao ler os dados de usuário do armazenamento, a resposta conterá a propriedade eTag. Se você alterar os dados e gravar dados atualizados no armazenamento, a solicitação deverá incluir a propriedade eTag que especifica o mesmo valor lido anteriormente. No entanto, gravar um objeto com `eTag` definido para `*` permitirá que a gravação substitua quaisquer outras alterações.

<!-- If the ETag specified in your `Storage.Write()` request matches the current value in the store, the server will save the data and specify a new eTag value in the body of the response, that indicates that the data has been updated. If the ETag specified in your Storage.Write() request does not match the current value in the store, the bot responds with an error indicating an eTag conflict, to indicate that the user's data in the store has changed since you last saved or retrieved it. -->

<!-- TODO: new snippet -->

<!-- jf: I think this section can be cut entirely.

## Save a conversation reference using storage

You can use IStorage to save BotBuilder SDK objects as well as user-defined data. This code snippet uses `Storage.Write()` to save a ConversationReference object for use in sending a proactive message later.

# [C#](#tab/csharpwriteconvref)
```csharp
            var reference = context.ConversationReference;
            var userId = reference.User.Id;

            // save the ConversationReference to a global variable of this class
            conversationReference = reference;

            StoreItems storeItems = new StoreItems();
            StoreItem conversationReferenceToStore = new StoreItem();
            // set the eTag to "*" to indicate you're overwriting previous data
            conversationReferenceToStore.eTag = "*";
            conversationReferenceToStore.Add("ref", reference);
            storeItems[$"ConversationReference/{userId}"] = conversationReferenceToStore;

            await storage.Write(storeItems).ConfigureAwait(false);

            SubscribeUser(userId);

            await context.SendActivity("Thank You! We will message you shortly.");
        }

public void SubscribeUser(string userId)
{
    CreateContextForUserAsync(userId, async (ITurnContext context) =>
    {
        await context.SendActivity("You've been notified.");
        await Task.Delay(2000);
    });
                
}
```
# [JavaScript](#tab/jswriteconvref)
```javascript
const { MemoryStorage } = require('botbuilder');

const storage = new MemoryStorage();

// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const utterances = (context.activity.text || '').trim().toLowerCase()
            if (utterances === 'subscribe') {
                const reference = context.activity;
                const userId = reference.id;
                const changes = {};
                changes['reference/' + userId] = reference;
                await storage.write(changes)
                await subscribeUser(userId)
                await context.sendActivity(`Thank You! We will message you shortly.`);
               
            } else{
                await context.sendActivity("Say 'subscribe'");
            }
    
        }
    });
});
```
---

To read the saved object from storage, call `Storage.Read()`.

# [C#](#tab/csharpread)
```csharp


        private async void CreateContextForUserAsync(String userId,Func<ITurnContext, Task> onReady)
        {
            var referenceKey = $"ConversationReference/{userId}";
            
            ConversationReference localRef = null;
            StoreItems conversationReferenceFromStorage = await storage.Read(referenceKey);
            foreach (var item in conversationReferenceFromStorage)
            {
                StoreItem value = item.Value as StoreItem;
                localRef = value["ref"];
            }
            
            await bot.CreateContext(this.conversationReference, onReady);

        }
```
# [JavaScript](#tab/jsread)
```javascript
async function subscribeUser(userId) {
    setTimeout(() => {
        createContextForUser(userId, (context) => {
            context.sendActivity("You have been notified");
        })
    }, 2000);
}

async function createContextForUser(userId, callback) {
    const referenceKey = 'reference/' + userId;
    var rows = await storage.read([referenceKey])
    var reference = await rows[referenceKey]
    await callback(adapter.createContext(reference))
          
}
```
---

-->

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como ler e gravar diretamente do armazenamento, veremos como é possível utilizar o gerenciador de estado para fazer isso.

> [!div class="nextstepaction"]
> [Salvar estado usando conversa e propriedades do usuário](bot-builder-howto-v4-state.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Conceito: Armazenamento no SDK do Bot Builder](bot-builder-storage-concept.md)


