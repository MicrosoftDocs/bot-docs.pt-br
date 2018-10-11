---
title: Gravar diretamente no armazenamento | Microsoft Docs
description: Saiba como gravar diretamente no armazenamento com o SDK do Bot Builder para .NET.
keywords: armazenamento, ler e gravar, armazenamento de memória, eTag
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/14/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0bbb507484840ab1fb0dc7c209b5d7ca8e9c9cfb
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2018
ms.locfileid: "46708142"
---
# <a name="write-directly-to-storage"></a>Gravar diretamente no armazenamento

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Você pode ler e gravar diretamente em seu objeto de armazenamento sem usar middleware ou objeto de contexto. Isso pode ser apropriado para dados que o bot usa, originados de uma fonte fora do fluxo de conversa do bot. Por exemplo, digamos que o bot permite que o usuário solicite o boletim meteorológico e o bot recupera o boletim meteorológico para uma data específica, lendo-o a partir de um banco de dados externo. O conteúdo do banco de dados de clima não depende das informações do usuário ou do contexto da conversa, portanto, é possível lê-lo diretamente do armazenamento em vez de usar o gerenciador de estado. Os exemplos de código neste artigo mostram como ler e gravar dados no armazenamento usando o **Armazenamento de Memória**, o **Cosmos DB**, o **Armazenamento de Blobs** e o **Armazenamento de Transcrições do Blob do Azure**. 

## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/en-us/free/) antes de começar.
- Instalar o Bot Framework [Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)

## <a name="memory-storage"></a>Armazenamento de memória

Primeiro, criaremos um bot que vai ler e gravar dados no Armazenamento de Memória. O armazenamento de memória é usado somente para testes e não deve ser usado na produção. Defina o armazenamento como Cosmos DB ou Armazenamento de Blobs antes de publicar seu bot.

#### <a name="build-a-basic-bot"></a>Crie um bot básico

O restante deste tópico se baseia em um bot de Echo. Você pode criar um tanto em [C#](../dotnet/bot-builder-dotnet-sdk-quickstart.md) quanto em [JS](../javascript/bot-builder-javascript-quickstart.md). Use o Bot Framework Emulator para se conectar ao bot, conversar com ele e testá-lo. O exemplo a seguir adiciona todas as mensagens do usuário a uma lista. A estrutura de dados que contém a lista é salva em seu armazenamento.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.TraceExtensions;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Schema;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

// Create local Memory Storage.
private static readonly MemoryStorage _myStorage = new MemoryStorage();

// Create cancellation token (used by Async Write operation).
public CancellationToken cancellationToken { get; private set; }

// Class for storing a log of utterances (text of messages) as a list.
public class UtteranceLog : IStoreItem
{
     // A list of things that users have said to the bot
     public List<string> UtteranceList { get; } = new List<string>();

     // The number of conversational turns that have occurred        
     public int TurnNumber { get; set; } = 0;

     // Create concurrency control where this is used.
     public string ETag { get; set; } = "*";
}

// Every Conversation turn for our Bot calls this method.
public async Task OnTurnAsync(ITurnContext context)
{
     
     var activityType = context.Activity.Type;
     // See if activity type for this turn is a message from the user.
     if (activityType == ActivityTypes.Message)
     {
         var utterance = context.Activity.Text;
         UtteranceLog logItems = null;
          
         // see if there are previous messages saved in sstorage.
         string[] utteranceList = { "UtteranceLog" };
         logItems = _myStorage.ReadAsync<UtteranceLog>(utteranceList).Result?.FirstOrDefault().Value;

         // If no stored messages were found, create and store a new entry.
         if (logItems is null)
         {
             logItems = new UtteranceLog();
         }
         
         // add new message to list of messages to display.
         logItems.UtteranceList.Add(utterance);
         // increment turn counter.
         logItems.TurnNumber++;
         
         // show user new list of saved messages.
         await context.SendActivityAsync($"The list is now: {string.Join(", ", logItems.UtteranceList)}");
         
         // Create Dictionary object to hold new list of messages.
         {
             changes.Add("UtteranceLog", logItems);
          };
          
          // Save new list to your Storage.
          await _myStorage.WriteAsync(changes,cancellationToken);
     }
     return;
}

```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const { BotFrameworkAdapter, ConversationState, BotStateSet, MemoryStorage } = require('botbuilder');
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

// Add memory storage.
var storage = new MemoryStorage();

const conversationState = new ConversationState(storage);
adapter.use(conversationState);

// Listen for incoming activity .
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing.
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            await logMessageText(storage, context);

            await context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});

async function logMessageText(storage, context) {
    let utterance = context.activity.text;
    try {
        // Read from the storage.
        let storeItems = await storage.read(["UtteranceLog"])
        // Check the result.
        var utteranceLog = storeItems["UtteranceLog"];

        if (typeof (utteranceLog) != 'undefined') {
            // The log exists so we can write to it.
            storeItems["UtteranceLog"].UtteranceList.push(utterance);

            try {
                await storage.write(storeItems)
                context.sendActivity('Successful write to utterance log.');
            } catch (err) {
                context.sendActivity(`Write failed of UtteranceLog: ${err}`);
            }

         } else {
            context.sendActivity(`need to create new utterance log`);
            storeItems["UtteranceLog"] = { UtteranceList: [`${utterance}`], "eTag": "*" }

            try {
                await storage.write(storeItems)
                context.sendActivity('Successful write to log.');
            } catch (err) {
                context.sendActivity(`Write failed: ${err}`);
            }
        }
    } catch (err) {
        context.sendActivity(`Read rejected. ${err}`);
    };
}
```

---

### <a name="start-your-bot"></a>Inicie seu bot
Execute o bot localmente.

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador. 
2. Selecione o arquivo .bot localizado no diretório em que você criou o projeto.

### <a name="interact-with-your-bot"></a>Interagir com o bot
Envie uma mensagem ao bot e o bot listará as mensagens recebidas por ele.
![Emulador em execução](../media/emulator-v4/emulator-running.png)

 
## <a name="using-cosmos-db"></a>Usar o Cosmos DB
Agora que você usou o armazenamento de memória, vamos atualizar o código para usar o Azure Cosmos DB. O Cosmos DB é o banco de dados multimodelo globalmente distribuído da Microsoft. O Azure Cosmos DB permite que você dimensione a taxa de transferência e o armazenamento de maneira elástica e independente em qualquer número de regiões geográficas do Azure. Ele oferece garantias de taxa de transferência, disponibilidade, latência e consistência com contratos de nível de serviço (SLAs) abrangentes. 

### <a name="set-up"></a>Configurar
Para usar o Cosmos DB em seu bot, você precisará configurar algumas coisas antes de mexer no código.

#### <a name="create-your-database-account"></a>Criar sua conta de banco de dados
1. Em uma nova janela do navegador, entre no [Portal do Azure](http://portal.azure.com).
2. Clique em **Criar um recurso > Bancos de Dados > Azure Cosmos DB**
3. Na página **Nova conta**, forneça um nome exclusivo no campo **ID**. No caso da **API**, escolha **SQL** e forneça as informações de **Assinatura**, **Local** e **Grupo de recursos**.
4. Em seguida, clique em **Criar**.

A criação da conta leva alguns minutos. Aguarde até que o portal exiba a página de parabéns! Página Sua conta do Azure Cosmos DB foi criada.

##### <a name="add-a-collection"></a>Adicionar uma coleção
1. Clique em **Configurações > Nova Coleção**. A área **Adicionar Coleção** é exibida à direita, talvez seja necessário rolar para a direita para vê-la.

![Adicionar coleção do Azure Cosmos DB](./media/add_database_collection.png)

2. Sua nova coleção de banco de dados, "bot-cosmos-sql-db", com uma ID de coleção "bot-storage." Usaremos esses valores em nosso exemplo de codificação abaixo.

![Cosmos DB](./media/cosmos-db-sql-database.png)

3. O URI e a chave do ponto de extremidade estão disponíveis na guia **Chaves** das configurações de seu banco de dados. Esses valores serão necessários para configurar o código mais adiante neste artigo. 

![Chaves do Cosmos DB](./media/comos-db-keys.png)

#### <a name="add-configuration-information"></a>Adicionar informações de configuração
Nossos dados de configuração para adição do armazenamento do Cosmos DB são curtos e simples; você poderá adicionar outros parâmetros de configuração usando esses mesmos métodos à medida que seu bot for ficando mais complexo. Este exemplo usa os nomes de banco de dados e coleção do Cosmos DB do exemplo acima.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private const string CosmosServiceEndpoint = "<your-cosmos-db-URI>";
private const string CosmosDBKey = "<your-cosmos-db-account-key>";
private const string CosmosDBDatabaseName = "bot-cosmos-sql-db";
private const string CosmosDBCollectionName = "bot-storage";
```


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione as informações a seguir ao arquivo `.env`. 
 
```javascript
ACTUAL_SERVICE_ENDPOINT=<your database URI>
ACTUAL_AUTH_KEY=<your database key>
DATABASE=Tasks
COLLECTION=Items
```
---

#### <a name="installing-packages"></a>Instalar pacotes
Verifique se você possui os pacotes necessários para o Cosmos DB

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```powershell
Install-Package Microsoft.Bot.Builder.Azure
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione referências a botbuilder-azure em seu projeto por meio do npm: -->


```powershell
npm install --save botbuilder-azure 
```

Para usar o arquivo de configuração .env, o bot precisará da inclusão de mais um pacote. Primeiro, obtenha o pacote de .NET do npm:

```powershell
npm install --save dotenv
```

---

### <a name="implementation"></a>Implementação 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O código de exemplo a seguir é executado usando o mesmo código de bot do exemplo de [armazenamento de memória](#memory-storage) fornecido acima.
O trecho de código a seguir mostra uma implementação de armazenamento do Cosmos DB para '_myStorage_' que substitui o armazenamento de memória local. 

```csharp
using Microsoft.Bot.Builder.Azure;

// Create access to Cosmos DB storage.
// Replaces Memory Storage with reference to Cosmos DB.
private static readonly CosmosDbStorage _myStorage = new CosmosDbStorage(new CosmosDbStorageOptions
{
   AuthKey = CosmosDBKey,
   CollectionId = CosmosDBCollectionName,
   CosmosDBEndpoint = new Uri(CosmosServiceEndpoint),
   DatabaseId = CosmosDBDatabaseName,
});
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código de exemplo a seguir é semelhante ao [armazenamento de memória](#memory-storage), mas com pequenas alterações.

Exija `CosmosDbStorage` do botbuilder-azure e configure dotenv para ler o arquivo `.env`.

**app.js**
```javascript
const { CosmosDbStorage } = require("botbuilder-azure");
require('dotenv').config()
```

Substitua o Armazenamento de Memória pela referência ao Cosmos DB.

```javascript
//Add CosmosDB 
const storage = new CosmosDbStorage({
    serviceEndpoint: process.env.ACTUAL_SERVICE_ENDPOINT, 
    authKey: process.env.ACTUAL_AUTH_KEY, 
    databaseId: process.env.DATABASE,
    collectionId: process.env.COLLECTION
})

const conversationState = new ConversationState(storage);
adapter.use(conversationState);
```

---

## <a name="start-your-bot"></a>Inicie seu bot
Execute o bot localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador. 
2. Selecione o arquivo .bot localizado no diretório em que você criou o projeto.

## <a name="interact-with-your-bot"></a>Interagir com o bot
Envie uma mensagem ao bot e o bot listará as mensagens recebidas por ele.
![Emulador em execução](../media/emulator-v4/emulator-running.png)


### <a name="view-your-data"></a>Ver seus dados
Depois de executar o bot e salvar suas informações, poderemos exibi-lo no portal do Azure na guia **Data Explorer**. 

![Exemplo do Data Explorer](./media/data_explorer.PNG)

### <a name="manage-concurrency-using-etags"></a>Gerenciar simultaneidade usando eTags
Em nosso exemplo de código do bot, definimos a propriedade `eTag` de cada `IStoreItem` como `*`. O membro (marca da entidade) `eTag` do objeto de repositório é usado no Cosmos DB para gerenciar a simultaneidade. `eTag` indica ao banco dados o que fazer caso outra instância do bot altere o objeto no mesmo armazenamento onde o bot está gravando. 

<!-- define optimistic concurrency -->

#### <a name="last-write-wins---allow-overwrites"></a>A última gravação prevalece - permite substituições
Um valor da propriedade `eTag` do asterisco (`*`) indica que o último gravador prevalece. Ao criar um novo armazenamento de dados, é possível definir `eTag` de uma propriedade como `*` para indicar que os dados em gravação não foram salvos anteriormente, ou que você deseja que o último gravador substitua qualquer propriedade salva anteriormente. Se a simultaneidade não for um problema para o bot, a definição da propriedade `eTag` como `*` para qualquer dado que você esteja gravando viabilizará as substituições.

#### <a name="maintain-concurrency-and-prevent-overwrites"></a>Manter simultaneidade e evitar substituições
Ao armazenar seus dados no Cosmos DB, use um valor diferente de `*` para `eTag` se quiser impedir o acesso simultâneo a uma propriedade e evitar a substituição de alterações por outra instância do bot. O bot receberá uma resposta de erro com a mensagem `etag conflict key=` ao tentar salvar dados de estado se a `eTag` não tiver o mesmo valor da `eTag` em armazenamento. <!-- To control concurrency of data that is stored using `IStorage`, the BotBuilder SDK checks the entity tag (ETag) for `Storage.Write()` requests. -->

Por padrão, o armazenamento do Cosmos DB verificará a propriedade `eTag` de um objeto de armazenamento quanto à igualdade sempre que um bot gravar esse item e, em seguida, atualizará para um novo valor exclusivo após cada gravação. Se a propriedade `eTag` na gravação não corresponder com `eTag` no armazenamento, isso significa que outro bot ou thread alterou os dados. 

Por exemplo, digamos que você queira que o bot edite uma nota salva, mas não quer que o bot substitua as alterações feitas por outra instância do bot. Se outra instância do bot tiver feito edições, você quer que o usuário edite a versão com as atualizações mais recentes.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Primeiro, crie uma classe que implemente `IStoreItem`.

```csharp
public class Note : IStoreItem
{
    public string Name { get; set; }
    public string Contents { get; set; }
    public string ETag { get; set; }
}
```

Em seguida, crie uma nota inicial criando um objeto de armazenamento e adicione o objeto ao armazenamento.

```csharp
// create a note for the first time, with a non-null, non-* ETag.
var note = new Note { Name = "Shopping List", Contents = "eggs", ETag = "x" };

var changes = Dictionary<string, object>();
{
    changes.Add("Note", note);
};
await NoteStore.WriteAsync(changes, cancellationToken);
```

Então, acesse e atualize a nota mais tarde, mantendo `eTag` que foi lido no armazenamento.

```csharp
var note = NoteStore.ReadAsync<Note>("Note").Result?.FirstOrDefault().Value;

if (note != null)
{
    note.Contents += ", bread";
    var changes = new Dictionary<string, object>();
    {
         changes.Add("Note1", note);
    };
    await NoteStore.WriteAsync(changes, cancellationToken);
}
```

Se a nota foi atualizada no armazenamento antes de gravar as alterações, a chamada para `Write` lançará uma exceção.


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione uma função auxiliar ao final do seu bot para gravar uma nota de exemplo em um armazenamento de dados.
Primeiro, crie o objeto `myNoteData`.

```javascript
// Helper function for writing a sample note to a data store
async function createSampleNote(storage, context) {
    var myNoteData = {
        name: "Shopping List",
        contents: "eggs",
        // If any Note file is already stored, the eTag field
        // must be set to "*" in order to allow writing without first reading the stored eTag
        // otherwise you'll likely get an exception indicating an eTag conflict. 
        eTag: "*"
    }
}
```

Na função auxiliar `createSampleNote`, inicie um objeto `changes`, adicione suas *notas* a ele e grave-o no armazenamento.

```javascript
// Write the note data to the "Note" key
var changes = {};
changes["Note"] = myNoteData;
// Creates a file named Note, if it doesn't already exist.
// specifying eTag= "*" will overwrite any existing contents.
// The act of writing to the file automatically updates the eTag property
// The first time you write to Note, the eTag is changed from *, and file contents will become:
//    {"name":"Shopping List","contents":"eggs","eTag":"1"}
try {
    await storage.write(changes);
    await context.sendActivity('Successful created a note.');
} catch (err) {
    await context.sendActivity(`Could not create note: ${err}`);
}
```

Em seguida, para acessar e atualizar a nota mais tarde, criaremos outra função auxiliar que pode ser acessada quando o usuário digita "atualizar nota".

```javascript
async function updateSampleNote(storage, context) {
    try {
        // Read in a note
        var note = await storage.read(["Note"]);
        console.log(`note.eTag=${note["Note"].eTag}\n note=${JSON.stringify(note)}`);
        // update the note that we just read
        note["Note"].contents += ", bread";
        console.log(`Updated note=${JSON.stringify(note)}`);

        try {
            await storage.write(note); // Write the changes back to storage
            await context.sendActivity('Successfully updated to note.');
        } catch (err) {            console.log(`Write failed: ${err}`);
        }
    }
    catch (err) {
        await context.sendActivity(`Unable to read the Note: ${err}`);
    }
}
```

Se a nota foi atualizada no armazenamento antes de gravar as alterações, a chamada para `write` lançará uma exceção.

---

Para manter a simultaneidade, sempre leia uma propriedade do armazenamento e modifique a propriedade lida para que `eTag` seja mantido. Ao ler os dados de usuário do armazenamento, a resposta conterá a propriedade eTag. Se você alterar os dados e gravar dados atualizados no armazenamento, a solicitação deverá incluir a propriedade eTag que especifica o mesmo valor lido anteriormente. No entanto, gravar um objeto com `eTag` definido para `*` permitirá que a gravação substitua quaisquer outras alterações.

## <a name="using-blob-storage"></a>Usar o armazenamento de blobs 
O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários.

### <a name="create-your-blob-storage-account"></a>Criar sua conta de Armazenamento de Blobs
Para usar o Armazenamento de Blobs em seu bot, você precisará configurar algumas coisas antes de mexer no código.
1. Em uma nova janela do navegador, entre no [Portal do Azure](http://portal.azure.com).
2. Clique em **Criar um recurso > Armazenamento > Conta de armazenamento - blob, arquivo, tabela, fila**
3. Na página **Nova conta**, insira o **Nome** da conta de armazenamento, selecione **Armazenamento de Blobs** como o **Tipo de conta** e forneça as informações de **Local**, **Grupo de recursos** e **Assinatura**.  
4. Em seguida, clique em **Criar**.

![Criar o Armazenamento de Blobs](./media/create-blob-storage.png)

#### <a name="add-configuration-information"></a>Adicionar informações de configuração

Localize as chaves do Armazenamento de Blobs necessárias para configurá-lo para o bot, conforme mostrado acima:
1. No portal do Azure, abra sua conta do Armazenamento de Blobs e selecione **Configurações > Chaves de acesso**.

![Localizar chaves do Armazenamento de Blobs](./media/find-blob-storage-keys.png)

Agora, usaremos duas dessas chaves para dar ao código acesso à nossa conta do Armazenamento de Blobs.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder.Azure;
```

Atualize a linha de código que aponta "_myStorage_" para a conta do Armazenamento de Blobs existente.

```csharp
private static readonly AzureBlobStorage _myStorage = new AzureBlobStorage("<your-blob-storage-account-string>", "<your-blob-storage-container-name>");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
```javascript
const mystorage = new BlobStorage({
   <youy_containerName>,
   <your_storageAccountOrConnectionString>,
   <your_storageAccessKey>
})
```
---

Quando "_myStorage_" estiver definido para apontar para a conta do Armazenamento de Blobs, o código de seu bot armazenará e recuperará dados do Armazenamento de Blobs.

## <a name="start-your-bot"></a>Inicie seu bot
Execute o bot localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador. 
2. Selecione o arquivo .bot localizado no diretório em que você criou o projeto.

## <a name="interact-with-your-bot"></a>Interagir com o bot
Envie uma mensagem ao bot e o bot listará as mensagens recebidas por ele.
![Emulador em execução](../media/emulator-v4/emulator-running.png)

### <a name="view-your-data"></a>Ver seus dados
Depois de executar o bot e salvar suas informações, poderemos exibi-lo na guia **Gerenciador de Armazenamento** no portal do Azure.

## <a name="blob-transcript-storage"></a>Armazenamento de transcrição de blobs
O armazenamento de transcrições do Blob do Azure fornece uma opção de armazenamento especializado que permite salvar e recuperar facilmente conversas do usuário como transcrições gravadas. O armazenamento de transcrições do Blob do Azure é particularmente útil para capturar respostas do usuário automaticamente e examiná-las durante a depuração de desempenho do bot.

### <a name="set-up"></a>Configurar
O armazenamento de transcrições do Blob do Azure pode usar a mesma conta de Armazenamento de Blobs criada seguindo as etapas detalhadas nas seções "_Criar sua conta de Armazenamento de Blobs_" e "_Adicionar informações de configuração_" acima. Para esta discussão, adicionamos um novo contêiner de blob, "_mybottranscripts_." 

### <a name="implementation"></a>Implementação 
O código a seguir conecta o ponteiro de armazenamento de transcrição "_myTranscripts_" à sua nova conta de armazenamento de transcrições do Blob do Azure. Linha única que conecta o ponteiro do armazenamento de transcrição "_myTranscripts_."

```csharp
using Microsoft.Bot.Builder.Azure;
private readonly AzureBlobTranscriptStore _myTranscripts = new AzureBlobTranscriptStore("<your-blob-storage-account-string>", "<your-blob-storage-account-name>");
```

### <a name="store-user-conversations-in-azure-blob-transcripts"></a>Armazenar conversas do usuário em transcrições do Blob do Azure
Quando um contêiner de blob fica disponível para armazenar transcrições, você pode começar a armazenar as conversas dos usuários com seu bot. Essas conversas poderão ser usadas como uma ferramenta de depuração para ver como os usuários estão interagindo com o bot. O código a seguir preserva as entradas de conversas do usuário para análise posterior.


```csharp
/// <summary>
/// Every Conversation turn for our EchoBot will call this method. 
/// </summary>
/// <param name="context">Turn scoped context containing all the data needed
/// for processing this conversation turn. </param>        
public async Task OnTurnAsync(ITurnContext context, CancellationToken cancellationToken = default(CancellationToken))
{
    var activityType = context.Activity.Type;

    await context.SendActivityAsync($"Activity type: {context.Activity.Type}.");

    // This bot is processing Messages
    if (activityType == ActivityTypes.Message)
    {
        // save user input into bot transcript storage for later debugging and review.
        await _myTranscripts.LogActivityAsync(context.Activity);
```


### <a name="find-all-stored-transcripts-for-your-channel"></a>Encontre todas as transcrições armazenadas de seu canal
Para ver quais dados estão armazenados, você pode usar o código a seguir para localizar programaticamente as "_ConversationIDs_" de todas as transcrições armazenadas. Ao usar o emulador de bot para testar seu código, a seleção de "_Reiniciar_"começa uma nova transcrição com uma nova "_ConversationID_."

```csharp
List<string> storedTranscripts = new List<string>();
PagedResult<Transcript> pagedResult = null;
var pageSize = 0;
do
{
    pagedResult = await _myTranscripts.ListTranscriptsAsync("emulator", pagedResult?.ContinuationToken);
    
    // transcript item contains ChannelId, Created, Id.
    // save the converasationIds (Id) found by "ListTranscriptsAsync" to a local list.
    foreach (var item in pagedResult.Items)
    {
         // Make sure we store an unescaped conversationId string.
         var strConversationId = item.Id;
         storedTranscripts.Add(Uri.UnescapeDataString(strConversationId));
    }
} while (pagedResult.ContinuationToken != null);
```


### <a name="retrieve-user-conversations-from-azure-blob-transcript-storage"></a>Recuperar conversas do usuário do armazenamento de transcrições do Blob do Azure
Após o armazenamento das transcrições das interações do bot no armazenamento de transcrições do Blob do Azure, você poderá recuperá-las programaticamente para teste ou depuração usando o método AzureBlobTranscriptStorage, "_GetTranscriptActivities_". O trecho de código a seguir recupera todas as transcrições de respostas de usuário que foram recebidas e armazenadas nas últimas 24 horas de cada transcrição armazenada.


```csharp
var numTranscripts = storedTranscripts.Count();
for (int i = 0; i < numTranscripts; i++)
{
    PagedResult<IActivity> pagedActivities = null;
    do
    {
        string thisConversationId = storedTranscripts[i];
        // Find all inputs in the last 24 hours.
        DateTime yesterday = DateTime.Now.AddDays(-1);
        // Retrieve iActivities for this transcript.
        pagedActivities = await _myTranscripts.GetTranscriptActivitiesAsync("emulator", thisConversationId, pagedActivities?.ContinuationToken, yesterday);
        foreach (var item in pagedActivities.Items)
        {
            // View as message and find value for key "text" :
            var thisMessage = item.AsMessageActivity();
            var userInput = thisMessage.Text;
         }
    } while (pagedActivities.ContinuationToken != null);
}
```

### <a name="remove-stored-transcripts-from-azure-blob-transcript-storage"></a>Remover transcrições armazenadas do armazenamento de transcrições do Blob do Azure
Depois que você terminar de usar dados de entrada do usuário para testar ou depurar o bot, as transcrições armazenadas poderão ser removidas de seu armazenamento de transcrição de blobs do Azure por programação. O trecho de código a seguir remove todas as transcrições armazenadas de seu armazenamento de transcrição do bot.


```csharp
for (int i = 0; i < numTranscripts; i++)
{
   // Remove all stored transcripts except the last one found.
   if (i > 0)
   {
       string thisConversationId = storedTranscripts[i];    
       await _myTranscripts.DeleteTranscriptAsync("emulator", thisConversationId);
    }
}
```


O link a seguir fornece mais informações referentes ao [Armazenamento de transcrições do Blob do Azure](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.azure.azureblobtranscriptstore)  

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como ler e gravar diretamente do armazenamento, veremos como é possível utilizar o gerenciador de estado para fazer isso.

> [!div class="nextstepaction"]
> [Salvar estado usando conversa e propriedades do usuário](bot-builder-howto-v4-state.md)

