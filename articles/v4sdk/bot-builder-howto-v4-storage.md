---
title: Gravar diretamente no armazenamento | Microsoft Docs
description: Saiba como gravar diretamente no armazenamento com o SDK do Bot Framework para .NET.
keywords: armazenamento, ler e gravar, armazenamento de memória, eTag
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1213e7d0c03fd6faa3768fac85a59bf4c7b5b28a
ms.sourcegitcommit: a1eaa44f182a7210197bd793250907df00e9edab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68756980"
---
# <a name="write-directly-to-storage"></a>Gravar diretamente no armazenamento

[!INCLUDE[applies-to](../includes/applies-to.md)]

Você pode ler e gravar diretamente em seu objeto de armazenamento sem usar middleware ou objeto de contexto. Isso pode ser apropriado para dados que seu bot usa para preservar a conversa ou dados originados de uma fonte fora do fluxo de conversa do bot. Nesse modelo de armazenamento de dados, os dados são lidos diretamente do armazenamento em vez de usar um gerenciador de estado. Os exemplos de código neste artigo mostram como ler e gravar dados no armazenamento usando o **Armazenamento de Memória**, o **Cosmos DB**, o **Armazenamento de Blobs** e o **Armazenamento de Transcrições do Blob do Azure**. 

## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Familiaridade com o artigo: Crie um bot localmente para [dotnet](https://aka.ms/bot-framework-www-c-sharp-quickstart) ou [nodeJS](https://aka.ms/bot-framework-www-node-js-quickstart).
- Modelo SDK do Bot Framework v4 para o [Modelo C#](https://aka.ms/bot-vsix) ou [nodeJS](https://nodejs.org) e [yeoman](http://yeoman.io).

## <a name="about-this-sample"></a>Sobre este exemplo
O código de exemplo neste artigo começa com a estrutura de um bot de eco básico e estende a funcionalidade dele, adicionando códigos (fornecidos abaixo). Esse código estendido cria uma lista para preservar as entradas do usuário conforme elas são recebidas. A cada turno, a lista completa de entradas do usuário é ecoada de volta para o usuário. A estrutura de dados que contém essa lista de entradas é então salva no armazenamento ao final desse turno. Vários tipos de armazenamento são explorados à medida que outras funcionalidades são adicionadas a este código de exemplo.

## <a name="memory-storage"></a>Armazenamento de memória

O SDK do Bot Framework permite que você armazene entradas do usuário usando o armazenamento na memória. O armazenamento de memória é usado somente para testes e não deve ser usado na produção. Os tipos de armazenamento persistentes, como o armazenamento de banco de dados, são melhores para bots de produção. Defina o armazenamento como Cosmos DB ou Armazenamento de Blobs antes de publicar seu bot.

#### <a name="build-a-basic-bot"></a>Crie um bot básico

O restante deste tópico se baseia em um bot de Eco. O código de exemplo do bot Echo pode ser criado localmente, seguindo as instruções do Guia de Início Rápido para a criação de um [EchoBot C#](https://aka.ms/bot-framework-www-c-sharp-quickstart) ou um [EchoBot JS](https://aka.ms/bot-framework-www-node-js-quickstart).

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**EchoBot.cs**
```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

// Represents a bot saves and echoes back user input.
public class EchoBot : ActivityHandler
{
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
     
   // Echo back user input.
   protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
   {
      // preserve user input.
      var utterance = turnContext.Activity.Text;  
      // make empty local logitems list.
      UtteranceLog logItems = null;
          
      // see if there are previous messages saved in storage.
      try
      {
         string[] utteranceList = { "UtteranceLog" };
         logItems = _myStorage.ReadAsync<UtteranceLog>(utteranceList).Result?.FirstOrDefault().Value;
      }
      catch
      {
         // Inform the user an error occured.
         await turnContext.SendActivityAsync("Sorry, something went wrong reading your stored messages!");
      }
         
      // If no stored messages were found, create and store a new entry.
      if (logItems is null)
      {
         // add the current utterance to a new object.
         logItems = new UtteranceLog();
         logItems.UtteranceList.Add(utterance);
         // set initial turn counter to 1.
         logItems.TurnNumber++;

         // Show user new user message.
         await turnContext.SendActivityAsync($"{logItems.TurnNumber}: The list is now: {string.Join(", ", logItems.UtteranceList)}");

         // Create Dictionary object to hold received user messages.
         var changes = new Dictionary<string, object>();
         {
            changes.Add("UtteranceLog", logItems);
         }
         try
         {
            // Save the user message to your Storage.
            await _myStorage.WriteAsync(changes, cancellationToken);
         }
         catch
         {
            // Inform the user an error occured.
            await turnContext.SendActivityAsync("Sorry, something went wrong storing your message!");
         }
      }
      // Else, our Storage already contained saved user messages, add new one to the list.
      else
      {
         // add new message to list of messages to display.
         logItems.UtteranceList.Add(utterance);
         // increment turn counter.
         logItems.TurnNumber++;
         
         // show user new list of saved messages.
         await turnContext.SendActivityAsync($"{logItems.TurnNumber}: The list is now: {string.Join(", ", logItems.UtteranceList)}");
         
         // Create Dictionary object to hold new list of messages.
         var changes = new Dictionary<string, object>();
         {
            changes.Add("UtteranceLog", logItems);
         };
         
         try
         {
            // Save new list to your Storage.
            await _myStorage.WriteAsync(changes,cancellationToken);
         }
         catch
         {
            // Inform the user an error occured.
            await turnContext.SendActivityAsync("Sorry, something went wrong storing your message!");
         }
      }
      ...  // OnMessageActivityAsync( )
   }
}

```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para usar o arquivo de configuração .env, o bot precisará da inclusão de mais um pacote. Caso ainda não o tenha instalado, obtenha o pacote dotnet do npm:

```powershell
npm install --save dotenv
```

**bot.js**
```javascript
const { ActivityHandler, MemoryStorage } = require('botbuilder');
const restify = require('restify');

// Add memory storage.
var storage = new MemoryStorage();

// Process incoming requests - adds storage for messages.
class MyBot extends ActivityHandler {
    constructor() {
        super();
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        this.onMessage(async turnContext => { console.log('this gets called (message)'); 
        await turnContext.sendActivity(`You said '${ turnContext.activity.text }'`); 
        // Save updated utterance inputs.
        await logMessageText(storage, turnContext);
    });
        this.onConversationUpdate(async turnContext => { console.log('this gets called (conversation update)'); 
        await turnContext.sendActivity('[conversationUpdate event detected]'); });
    }
}

// This function stores new user messages. Creates new utterance log if none exists.
async function logMessageText(storage, turnContext) {
    let utterance = turnContext.activity.text;
    // debugger;
    try {
        // Read from the storage.
        let storeItems = await storage.read(["UtteranceLogJS"])
        // Check the result.
        var UtteranceLogJS = storeItems["UtteranceLogJS"];
        if (typeof (UtteranceLogJS) != 'undefined') {
            // The log exists so we can write to it.
            storeItems["UtteranceLogJS"].turnNumber++;
            storeItems["UtteranceLogJS"].UtteranceList.push(utterance);
            // Gather info for user message.
            var storedString = storeItems.UtteranceLogJS.UtteranceList.toString();
            var numStored = storeItems.UtteranceLogJS.turnNumber;

            try {
                await storage.write(storeItems)
                turnContext.sendActivity(`${numStored}: The list is now: ${storedString}`);
            } catch (err) {
                turnContext.sendActivity(`Write failed of UtteranceLogJS: ${err}`);
            }
        }
        else{
            turnContext.sendActivity(`Creating and saving new utterance log`);
            var turnNumber = 1;
            storeItems["UtteranceLogJS"] = { UtteranceList: [`${utterance}`], "eTag": "*", turnNumber }
            // Gather info for user message.
            var storedString = storeItems.UtteranceLogJS.UtteranceList.toString();
            var numStored = storeItems.UtteranceLogJS.turnNumber;

            try {
                await storage.write(storeItems)
                turnContext.sendActivity(`${numStored}: The list is now: ${storedString}`);
            } catch (err) {
                turnContext.sendActivity(`Write failed: ${err}`);
            }
        }
    }
    catch (err){
        turnContext.sendActivity(`Read rejected. ${err}`);
    }
}

module.exports.MyBot = MyBot;

```
---

### <a name="start-your-bot"></a>Inicie seu bot
Execute o bot localmente.

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
- Instale o Bot Framework [Emulator](https://aka.ms/bot-framework-emulator-readme), depois inicie o emulador e conecte-se ao bot no emulador:

1. Clique no link **Criar nova configuração de bot** na guia “Boas-vindas” do emulador. 
2. Preencha os campos para se conectar ao seu bot considerando as informações na página da Web exibida ao iniciar o bot.

### <a name="interact-with-your-bot"></a>Interagir com o bot
Envie uma mensagem ao bot. Ele listará as mensagens recebidas.

![Bot de armazenamento de teste do emulador](./media/emulator-direct-storage-test.png)

## <a name="using-cosmos-db"></a>Usar o Cosmos DB
Agora que você usou o armazenamento de memória, vamos atualizar o código para usar o Azure Cosmos DB. O Cosmos DB é o banco de dados multimodelo globalmente distribuído da Microsoft. O Azure Cosmos DB permite que você dimensione a taxa de transferência e o armazenamento de maneira elástica e independente em qualquer número de regiões geográficas do Azure. Ele oferece garantias de taxa de transferência, disponibilidade, latência e consistência com contratos de nível de serviço (SLAs) abrangentes. 

### <a name="set-up"></a>Configurar
Para usar o Cosmos DB em seu bot, você precisará configurar algumas coisas antes de mexer no código. Para obter uma descrição detalhada da criação do banco de dados Cosmos DB e do aplicativo, acesse a documentação para [dotnet do Cosmos DB](https://aka.ms/Bot-framework-create-dotnet-cosmosdb) ou [nodejs do Cosmos DB](https://aka.ms/Bot-framework-create-nodejs-cosmosdb).

### <a name="create-your-database-account"></a>Criar sua conta de banco de dados

1. Em uma nova janela do navegador, entre no [Portal do Azure](http://portal.azure.com).

![Criar banco de dados do Cosmos DB](./media/create-cosmosdb-database.png)

2. Clique em **Criar um recurso > Bancos de Dados > Azure Cosmos DB**

![Página da nova conta do Cosmos DB](./media/cosmosdb-new-account-page.png)

3. Na **Página da nova conta**, forneça as informações de **Assinatura**, **Grupo de recursos**. Crie um nome exclusivo para o campo **Nome da conta** — isso acabará se tornando parte do seu nome de URL de acesso de dados. Para **API**, selecione **Core(SQL)** e forneça um **Local** próximo para melhorar os tempos de acesso de dados.
4. Depois clique em **Revisar + Criar**.
5. Depois que a validação tiver sido bem-sucedida, clique em **Criar**.

A criação da conta leva alguns minutos. Aguarde até que o portal exiba a página de parabéns! Página Sua conta do Azure Cosmos DB foi criada.

### <a name="add-a-collection"></a>Adicionar uma coleção

![Adicionar coleção do Azure Cosmos DB](./media/add_database_collection.png)

1. Clique em **Configurações > Nova Coleção**. A área **Adicionar Coleção** é exibida à direita, talvez seja necessário rolar para a direita para vê-la. Devido a atualizações recentes para o Cosmos DB, certifique-se de adicionar uma única chave de partição: _/id_. Essa chave evitará erros de consulta entre partições.

![Cosmos DB](./media/cosmos-db-sql-database.png)

2. Sua nova coleção de banco de dados, "bot-cosmos-sql-db", com uma ID de coleção "bot-storage." Usaremos esses valores em nosso exemplo de codificação abaixo.

![Chaves do Cosmos DB](./media/comos-db-keys.png)

3. O URI e a chave do ponto de extremidade estão disponíveis na guia **Chaves** das configurações de seu banco de dados. Esses valores serão necessários para configurar o código mais adiante neste artigo. 

### <a name="add-configuration-information"></a>Adicionar informações de configuração
Nossos dados de configuração para adição do armazenamento do Cosmos DB são curtos e simples; você poderá adicionar outros parâmetros de configuração usando esses mesmos métodos à medida que seu bot for ficando mais complexo. Este exemplo usa os nomes de banco de dados e coleção do Cosmos DB do exemplo acima.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**EchoBot.cs**
```csharp
public class EchoBot : ActivityHandler
{
   private const string CosmosServiceEndpoint = "<your-cosmos-db-URI>";
   private const string CosmosDBKey = "<your-cosmos-db-account-key>";
   private const string CosmosDBDatabaseName = "bot-cosmos-sql-db";
   private const string CosmosDBCollectionName = "bot-storage";
   ...
   
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione as informações a seguir ao arquivo `.env`.

**.env**
```javascript
DB_SERVICE_ENDPOINT="<your-Cosmos-db-URI>"
AUTH_KEY="<your-cosmos-db-account-key>"
DATABASE="<bot-cosmos-sql-db>"
COLLECTION="<bot-storage>"
```
---

#### <a name="installing-packages"></a>Instalar pacotes
Verifique se você possui os pacotes necessários para o Cosmos DB

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```powershell
Install-Package Microsoft.Bot.Builder.Azure
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Você pode adicionar referências ao botbuilder-azure em seu projeto por meio do npm.
>**Observação**: esse pacote npm se baseia em uma instalação do Python existente no seu computador de desenvolvimento. Caso não tenha o Python instalado, você pode encontrar os recursos de instalação no seu computador aqui: [Python.org](https://www.python.org/downloads/)

```powershell
npm install --save botbuilder-azure 
```

Caso ainda não o tenha instalado, obtenha o pacote do dotnet do npm para acessar as configurações de arquivo do `.env`.

```powershell
npm install --save dotenv
```
---

### <a name="implementation"></a>Implementação 

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

O código de exemplo a seguir é executado usando o mesmo código de bot do exemplo de [armazenamento de memória](#memory-storage) fornecido acima.
O trecho de código a seguir mostra uma implementação de armazenamento do Cosmos DB para '_myStorage_' que substitui o armazenamento de memória local. O Armazenamento de memória é comentado e substituído por uma referência ao Cosmos DB.

**EchoBot.cs**
```csharp

using System;
...
using Microsoft.Bot.Builder.Azure;
...
public class EchoBot : ActivityHandler
{
   // Create local Memory Storage - commented out.
   // private static readonly MemoryStorage _myStorage = new MemoryStorage();

   // Replaces Memory Storage with reference to Cosmos DB.
   private static readonly CosmosDbStorage _myStorage = new CosmosDbStorage(new CosmosDbStorageOptions
   {
      AuthKey = CosmosDBKey,
      CollectionId = CosmosDBCollectionName,
      CosmosDBEndpoint = new Uri(CosmosServiceEndpoint),
      DatabaseId = CosmosDBDatabaseName,
   });
   
   ...
}

```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código de exemplo a seguir é semelhante ao [armazenamento de memória](#memory-storage), mas com pequenas alterações.

Requer o `CosmosDbStorage` do `botbuilder-azure` e a configuração do dotenv para ler o arquivo `.env`.

**bot.js**

```javascript
const { CosmosDbStorage } = require("botbuilder-azure");
```
Comentário sobre o Armazenamento de Memória, substitua-o com referência ao Cosmos DB.

**bot.js**
```javascript
// initialized to access values in .env file.
var dotenv = require('dotenv');
dotenv.load();

// Create local Memory Storage - commented out.
// var storage = new MemoryStorage();

// Create access to CosmosDb Storage - this replaces local Memory Storage.
var storage = new CosmosDbStorage({
    serviceEndpoint: process.env.DB_SERVICE_ENDPOINT, 
    authKey: process.env.AUTH_KEY, 
    databaseId: process.env.DATABASE,
    collectionId: process.env.COLLECTION
})

```
---

## <a name="start-your-bot"></a>Inicie seu bot
Execute o bot localmente.

## <a name="test-your-bot-with-bot-framework-emulator"></a>Testar o bot usando o Bot Framework Emulator
Agora inicie o Bot Framework Emulator e conecte-se ao seu bot:

1. Clique no link **Criar nova configuração de bot** na guia “Boas-vindas” do emulador. 
2. Preencha os campos para se conectar ao seu bot considerando as informações na página da Web exibida ao iniciar o bot.

## <a name="interact-with-your-bot"></a>Interagir com o bot
Envie uma mensagem ao bot e o bot listará as mensagens recebidas por ele.
![Emulador em execução](./media/emulator-direct-storage-test.png)


### <a name="view-your-data"></a>Ver seus dados
Depois de executar o bot e salvar suas informações, poderemos exibir os dados armazenados no portal do Azure na guia **Data Explorer**. 

![Exemplo do Data Explorer](./media/data_explorer.PNG)


## <a name="using-blob-storage"></a>Usar o armazenamento de blobs 
O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários.

### <a name="create-your-blob-storage-account"></a>Criar sua conta de Armazenamento de Blobs
Para usar o Armazenamento de Blobs em seu bot, você precisará configurar algumas coisas antes de mexer no código.
1. Em uma nova janela do navegador, entre no [Portal do Azure](http://portal.azure.com).

![Criar o Armazenamento de Blobs](./media/create-blob-storage.png)

2. Clique em **Criar um recurso > Armazenamento > Conta de armazenamento - blob, arquivo, tabela, fila**

![Página da nova conta de Armazenamento de Blobs](./media/blob-storage-new-account.png)

3. Na página **Nova conta**, insira o **Nome** da conta de armazenamento, selecione **Armazenamento de Blobs** como o **Tipo de conta** e forneça as informações de **Local**, **Grupo de recursos** e **Assinatura**.  
4. Depois clique em **Revisar + Criar**.
5. Depois que a validação tiver sido bem-sucedida, clique em **Criar**.

### <a name="create-blob-storage-container"></a>Criar contêiner de Armazenamento de Blobs
Depois de criar sua conta de Armazenamento de Blobs, abra esta conta 
1. selecionando o recurso.
2. Agora abra usando o Gerenciador de Armazenamento (versão prévia).

![Criar contêiner de Armazenamento de Blobs](./media/create-blob-container.png)

3. Clique com botão direito do mouse em CONTÊINERES DE BLOBS e selecione _Criar contêiner de blobs_.
4. Adicione um nome. Você usará esse nome para o valor "your-blob-storage-container-name" para fornecer acesso à sua conta de Armazenamento de Blobs.

#### <a name="add-configuration-information"></a>Adicionar informações de configuração
Localize as chaves do Armazenamento de Blobs necessárias para configurá-lo para o bot, conforme mostrado acima:
1. No portal do Azure, abra sua conta do Armazenamento de Blobs e selecione **Configurações > Chaves de acesso**.

![Localizar chaves do Armazenamento de Blobs](./media/find-blob-storage-keys.png)

Você usará a chave1 _Cadeia de conexão_ como o valor "your-blob-storage-account-string" para fornecer acesso à sua conta de Armazenamento de Blobs.

#### <a name="installing-packages"></a>Instalar pacotes
Caso ainda não os tenha instalado para usar o Cosmos DB, instale os pacotes a seguir.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```powershell
Install-Package Microsoft.Bot.Builder.Azure
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione referências ao botbuilder-azure no seu projeto por meio do npm.
>**Observação**: esse pacote npm se baseia em uma instalação do Python existente no seu computador de desenvolvimento. Caso não tenha o Python instalado, você pode encontrar os recursos de instalação no seu computador aqui: [Python.org](https://www.python.org/downloads/)

```powershell
npm install --save botbuilder-azure 
```

Caso ainda não o tenha instalado, obtenha o pacote do dotnet do npm para acessar as configurações de arquivo do `.env`.

```powershell
npm install --save dotenv
```
---

### <a name="implementation"></a>Implementação 

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**EchoBot.cs**
```csharp
using Microsoft.Bot.Builder.Azure;
```
Atualize a linha de código que aponta "_myStorage_" para a conta do Armazenamento de Blobs existente.

**EchoBot.cs**
```csharp
private static readonly AzureBlobStorage _myStorage = new AzureBlobStorage("<your-blob-storage-account-string>", "<your-blob-storage-container-name>");
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione as informações a seguir ao arquivo `.env`.

**.env**
```javascript
BLOB_NAME="<your-blob-storage-container-name>"
BLOB_STRING="<your-blob-storage-account-string>"
```

Atualize o arquivo `bot.js` conforme descrito a seguir. Requer o `BlobStorage` do `botbuilder-azure`.

**bot.js**
```javascript
const { BlobStorage } = require("botbuilder-azure");
```

Caso não tenha adicionado o código para carregar o arquivo `.env` para implementar o armazenamento do Cosmos DB, adicione-o aqui.

```javascript
// initialized to access values in .env file.
var dotenv = require('dotenv');
dotenv.load();
```
Agora, atualize seu código para apontar o "_armazenamento_" para sua conta de Armazenamento de Blobs existente, comentando definições anteriores de armazenamento e adicionando o seguinte.

**bot.js**
```javascript
var storage = new BlobStorage({
    containerName: process.env.BLOB_NAME,
    storageAccountOrConnectionString: process.env.BLOB_STRING
});
```
---

Quando o armazenamento estiver definido para apontar para a conta do Armazenamento de Blobs, o código do bot armazenará e recuperará dados do Armazenamento de Blobs.

## <a name="start-your-bot"></a>Inicie seu bot
Execute o bot localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Criar nova configuração de bot** na guia “Boas-vindas” do emulador. 
2. Preencha os campos para se conectar ao seu bot considerando as informações na página da Web exibida ao iniciar o bot.

## <a name="interact-with-your-bot"></a>Interagir com o bot
Envie uma mensagem ao bot, e ele listará as mensagens recebidas.

![Bot de armazenamento de teste do emulador](./media/emulator-direct-storage-test.png)

### <a name="view-your-data"></a>Ver seus dados
Depois de executar o bot e salvar suas informações, poderemos exibi-lo na guia **Gerenciador de Armazenamento** no portal do Azure.

## <a name="blob-transcript-storage"></a>Armazenamento de transcrição de blobs
O armazenamento de transcrições do Blob do Azure fornece uma opção de armazenamento especializado que permite salvar e recuperar facilmente conversas do usuário como transcrições gravadas. O armazenamento de transcrições de blob do Azure é bastante útil para capturar respostas do usuário automaticamente e examiná-las durante a depuração de desempenho do seu bot.

### <a name="set-up"></a>Configurar
O armazenamento de transcrições do Blob do Azure pode usar a mesma conta de Armazenamento de Blobs criada seguindo as etapas detalhadas nas seções "_Criar sua conta de Armazenamento de Blobs_" e "_Adicionar informações de configuração_" acima. Agora, adicionamos um contêiner para manter nossas transcrições.

![Criar contêiner de transcrição](./media/create-blob-transcript-container.png)

1. Abra uma conta de Armazenamento de Blobs do Azure.
1. Clique em _Gerenciador de Armazenamento_.
1. Clique com botão direito do mouse em _CONTÊINERES DE BLOBS_ e selecione _Criar contêiner de blobs_.
1. Insira um nome para o contêiner de transcrição e selecione _OK_. (Nomeamos como mybottranscripts)

### <a name="implementation"></a>Implementação 
O código a seguir conecta o ponteiro de armazenamento de transcrição `_myTranscripts` à sua nova conta de armazenamento de transcrições do Blob do Azure. Para criar esse link com um novo nome de contêiner, <your-blob-transcript-container-name>, crie um novo contêiner no Armazenamento de Blobs para manter os arquivos de transcrição.

**echoBot.cs**
```csharp
using Microsoft.Bot.Builder.Azure;

public class EchoBot : ActivityHandler
{
   ...
   
   private readonly AzureBlobTranscriptStore _myTranscripts = new AzureBlobTranscriptStore("<your-blob-transcript-storage-account-string>", "<your-blob-transcript-container-name>");
   
   ...
}

```

### <a name="store-user-conversations-in-azure-blob-transcripts"></a>Armazenar conversas do usuário em transcrições do Blob do Azure
Quando um contêiner de blob fica disponível para armazenar transcrições, você pode começar a armazenar as conversas dos usuários com seu bot. Essas conversas poderão ser usadas posteriormente como uma ferramenta de depuração para ver como os usuários interagem com o bot. Cada _Reiniciar conversa_ do emulador inicia a criação de uma nova lista de conversa de transcrição. O código a seguir preserva as entradas de conversas do usuário em um arquivo de transcrição armazenado.
- A transcrição atual é salva usando `LogActivityAsync`.
- Transcrições salvas são recuperadas usando `ListTranscriptsAsync`.
Nesse código de exemplo, a ID de cada transcrição de código armazenada é salva em uma lista chamada "storedTranscripts". Posteriormente, essa lista é usada para gerenciar a quantidade de transcrições armazenadas de blobs que podemos manter.

**echoBot.cs**
```csharp

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    await _myTranscripts.LogActivityAsync(turnContext.Activity);

    List<string> storedTranscripts = new List<string>();
    PagedResult<Microsoft.Bot.Builder.TranscriptInfo> pagedResult = null;
    var pageSize = 0;
    do
    {
       pagedResult = await _myTranscripts.ListTranscriptsAsync("emulator", pagedResult?.ContinuationToken);
       pageSize = pagedResult.Items.Count();

       // transcript item contains ChannelId, Created, Id.
       // save the channelIds found by "ListTranscriptsAsync" to a local list.
       foreach (var item in pagedResult.Items)
       {
          storedTranscripts.Add(item.Id);
       }
    } while (pagedResult.ContinuationToken != null);
    
    ...
}

```

### <a name="manage-stored-blob-transcripts"></a>Gerenciar transcrições armazenadas de blobs
Embora as transcrições armazenadas possam ser usadas como uma ferramenta de depuração, ao longo do tempo, a quantidade de transcrições armazenadas poderá ficar maior do que você consegue preservar. O código adicional incluído abaixo usa `DeleteTranscriptAsync` para remover tudo, exceto os três últimos itens de transcrição recuperados do seu armazenamento de transcrições de blobs.

**echoBot.cs**
```csharp

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    await _myTranscripts.LogActivityAsync(turnContext.Activity);

    List<string> storedTranscripts = new List<string>();
    PagedResult<Microsoft.Bot.Builder.TranscriptInfo> pagedResult = null;
    var pageSize = 0;
    do
    {
       pagedResult = await _myTranscripts.ListTranscriptsAsync("emulator", pagedResult?.ContinuationToken);
       pageSize = pagedResult.Items.Count();

       // transcript item contains ChannelId, Created, Id.
       // save the channelIds found by "ListTranscriptsAsync" to a local list.
       foreach (var item in pagedResult.Items)
       {
          storedTranscripts.Add(item.Id);
       }
    } while (pagedResult.ContinuationToken != null);
    
    // Manage the size of your transcript storage.
    for (int i = 0; i < pageSize; i++)
    {
       // Remove older stored transcripts, save just the last three.
       if (i < pageSize - 3)
       {
          string thisTranscriptId = storedTranscripts[i];
          try
          {
             await _myTranscripts.DeleteTranscriptAsync("emulator", thisTranscriptId);
           }
           catch (System.Exception ex)
           {
              await turnContext.SendActivityAsync("Debug Out: DeleteTranscriptAsync had a problem!");
              await turnContext.SendActivityAsync("exception: " + ex.Message);
           }
       }
    }
    ...
}

```

O link a seguir fornece mais informações referentes ao [Armazenamento de transcrições de blobs do Azure](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.azure.azureblobtranscriptstore) 

## <a name="additional-information"></a>Informações adicionais

### <a name="manage-concurrency-using-etags"></a>Gerenciar simultaneidade usando eTags
Em nosso exemplo de código do bot, definimos a propriedade `eTag` de cada `IStoreItem` como `*`. O membro (marca da entidade) `eTag` do objeto de repositório é usado no Cosmos DB para gerenciar a simultaneidade. `eTag` indica ao banco dados o que fazer caso outra instância do bot altere o objeto no mesmo armazenamento onde o bot está gravando. 

<!-- define optimistic concurrency -->

#### <a name="last-write-wins---allow-overwrites"></a>A última gravação prevalece - permite substituições
Um valor da propriedade `eTag` do asterisco (`*`) indica que o último gravador prevalece. Ao criar um novo armazenamento de dados, é possível definir `eTag` de uma propriedade como `*` para indicar que os dados em gravação não foram salvos anteriormente, ou que você deseja que o último gravador substitua qualquer propriedade salva anteriormente. Se a simultaneidade não for um problema para o bot, a definição da propriedade `eTag` como `*` para qualquer dado que você esteja gravando viabilizará as substituições.

#### <a name="maintain-concurrency-and-prevent-overwrites"></a>Manter simultaneidade e evitar substituições
Ao armazenar seus dados no Cosmos DB, use um valor diferente de `*` para `eTag` se quiser impedir o acesso simultâneo a uma propriedade e evitar a substituição de alterações por outra instância do bot. O bot receberá uma resposta de erro com a mensagem `etag conflict key=` ao tentar salvar dados de estado se a `eTag` não tiver o mesmo valor da `eTag` em armazenamento. <!-- To control concurrency of data that is stored using `IStorage`, the BotBuilder SDK checks the entity tag (ETag) for `Storage.Write()` requests. -->

Por padrão, o armazenamento do Cosmos DB verificará a propriedade `eTag` de um objeto de armazenamento quanto à igualdade sempre que um bot gravar esse item e, em seguida, atualizará para um novo valor exclusivo após cada gravação. Se a propriedade `eTag` na gravação não corresponder com `eTag` no armazenamento, isso significa que outro bot ou thread alterou os dados. 

Por exemplo, digamos que você queira que o bot edite uma nota salva, mas não quer que o bot substitua as alterações feitas por outra instância do bot. Se outra instância do bot tiver feito edições, você quer que o usuário edite a versão com as atualizações mais recentes.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Primeiro, crie uma classe que implemente `IStoreItem`.

**EchoBot.cs**
```csharp
public class Note : IStoreItem
{
    public string Name { get; set; }
    public string Contents { get; set; }
    public string ETag { get; set; }
}
```

Em seguida, crie uma nota inicial criando um objeto de armazenamento e adicione o objeto ao armazenamento.

**EchoBot.cs**
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

**EchoBot.cs**
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


### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione uma função auxiliar ao final do seu bot para gravar uma nota de exemplo em um armazenamento de dados.
Primeiro, crie o objeto `myNoteData`.

**bot.js**
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

**bot.js**
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
     var list = changes["Note"].contents;
     await context.sendActivity(`Successful created a note: ${list}`);
} catch (err) {
     await context.sendActivity(`Could not create note: ${err}`);
}
```

A função auxiliar é acessada de dentro da lógica do bot ao adicionar a seguinte chamada:

**bot.js**
```javascript
// Save a note with etag.
await createSampleNote(storage, turnContext);
```

Depois de criada, para recuperar e atualizar a observação mais tarde, criamos outra função auxiliar que pode ser acessada quando o usuário digita "atualizar observação".

**bot.js**
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
             var list = note["Note"].contents;
             await context.sendActivity(`Successfully updated note: ${list}`);
        } catch (err) {            
             console.log(`Write failed: ${err}`);
        }
    }
    catch (err) {
        await context.sendActivity(`Unable to read the Note: ${err}`);
    }
}
```

Essa função auxiliar é acessada de dentro da lógica do bot ao adicionar a seguinte chamada:

**bot.js**
```javascript
// Update a note with etag.
await updateSampleNote(storage, turnContext);
```

Se a observação foi atualizada no armazenamento por outro usuário antes da tentativa de fazer write-back das alterações, o valor `eTag` não corresponderá mais e a chamada para `write` gerará uma exceção.

---

Para manter a simultaneidade, sempre leia uma propriedade do armazenamento e modifique a propriedade lida para que `eTag` seja mantido. Ao ler os dados de usuário do armazenamento, a resposta conterá a propriedade eTag. Se você alterar os dados e gravar dados atualizados no armazenamento, a solicitação deverá incluir a propriedade eTag que especifica o mesmo valor lido anteriormente. No entanto, gravar um objeto com `eTag` definido para `*` permitirá que a gravação substitua quaisquer outras alterações.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como ler e gravar diretamente do armazenamento, veremos como é possível utilizar o gerenciador de estado para fazer isso.

> [!div class="nextstepaction"]
> [Salvar estado usando conversa e propriedades do usuário](bot-builder-howto-v4-state.md)

