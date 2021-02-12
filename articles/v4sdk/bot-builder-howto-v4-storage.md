---
title: Gravar diretamente no armazenamento – Serviço de Bot
description: Saiba como usar o SDK do bot Framework para .NET para gravar dados de bot diretamente em vários tipos de armazenamento persistente sem usar um Gerenciador de estado.
keywords: armazenamento, ler e gravar, armazenamento de memória, eTag
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/27/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 56554af53d941376411d7d87b74d3124686915f9
ms.sourcegitcommit: 22a92bc07c85f899b3b1dca4f19421bc302db23f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100272307"
---
# <a name="write-directly-to-storage"></a>Gravar diretamente no armazenamento

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Você pode ler e gravar diretamente em seu objeto de armazenamento sem usar middleware ou objeto de contexto. Isso pode ser apropriado para dados que seu bot usa para preservar a conversa ou dados originados de uma fonte fora do fluxo de conversa do bot. Nesse modelo de armazenamento de dados, os dados são lidos diretamente do armazenamento em vez de usar um gerenciador de estado. Os exemplos de código neste artigo mostram como ler e gravar dados no armazenamento usando **memória**, **Cosmos DB**, **blob do Azure** e armazenamento de **transcrição de blob do Azure** .

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Familiaridade com o artigo: Crie um bot localmente para [C#](../dotnet/bot-builder-dotnet-sdk-quickstart.md), [JavaScript](../javascript/bot-builder-javascript-quickstart.md)ou [python](../python/bot-builder-python-quickstart.md).
- Modelos do SDK do Bot Framework v4 para [Visual Studio (C#)](https://aka.ms/bot-vsix), [Node.js](https://nodejs.org) ou [Yeoman](http://yeoman.io).

[!INCLUDE [VSIX templates](~/includes/vsix-templates-versions.md)]

## <a name="about-this-sample"></a>Sobre este exemplo

<!-- Sent email to John Taylor on 1/20/21 about the need to create a sample bot to base
     this article on. As it is currently, this article starts with the EchoBot, then replaces everything in EchoBot.cs. The code demonstrates creating a list by saving the values the user enters into memory, then makes the required code changes to save it into a Cosmos DB database (then a Blob storage database). It is similar to echo bot in that it repeats what you enter, except that it echoes back a list of all previous entries as well. This type of article is difficult to maintain, if the underlying sample it is based off of (EchoBot) changes, it could cause things not to work and there is no great way to tie an article to a sample that is used in this way.  -->

O código de exemplo neste artigo começa com a estrutura de um bot de eco básico e estende a funcionalidade dele, adicionando códigos (fornecidos abaixo). Esse código estendido cria uma lista para preservar as entradas do usuário conforme elas são recebidas. Cada vez, a lista completa de entradas de usuário, salva na memória, é ecoada de volta para o usuário. A estrutura de dados que contém essa lista de entradas é modificada para salvar no armazenamento. Vários tipos de armazenamento são explorados conforme a funcionalidade adicional é adicionada a este código de exemplo.

## <a name="memory-storage"></a>Armazenamento de memória

O SDK do Bot Framework permite que você armazene entradas do usuário usando o armazenamento na memória. Como o armazenamento na memória é limpo cada vez que o bot é reiniciado, ele é mais adequado para fins de teste e não se destina ao uso em produção. Os tipos de armazenamento persistentes, como o armazenamento de banco de dados, são melhores para bots de produção. <!--Be sure to set storage to **Cosmos DB**, **Blob storage**, or **Azure Table storage** before publishing your bot.-->

## <a name="build-a-basic-bot"></a>Crie um bot básico

O restante deste tópico se baseia em um bot de Eco. O código de exemplo do bot de eco pode ser criado localmente seguindo as instruções de início rápido para criar o EchoBot em [C#](../dotnet/bot-builder-dotnet-sdk-quickstart.md), [JavaScript](../javascript/bot-builder-javascript-quickstart.md) ou [python](../python/bot-builder-python-quickstart.md).

### <a name="c"></a>[C#](#tab/csharp)

Substitua o código em **EchoBot.cs** pelo seguinte código:

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

      // Make empty local log-items list.
      UtteranceLog logItems = null;

      // See if there are previous messages saved in storage.
      try
      {
         string[] utteranceList = { "UtteranceLog" };
         logItems = _myStorage.ReadAsync<UtteranceLog>(utteranceList).Result?.FirstOrDefault().Value;
      }
      catch
      {
         // Inform the user an error occurred.
         await turnContext.SendActivityAsync("Sorry, something went wrong reading your stored messages!");
      }

      // If no stored messages were found, create and store a new entry.
      if (logItems is null)
      {
         // Add the current utterance to a new object.
         logItems = new UtteranceLog();
         logItems.UtteranceList.Add(utterance);

         // Set initial turn counter to 1.
         logItems.TurnNumber++;

         // Show user new user message.
         await turnContext.SendActivityAsync($"{logItems.TurnNumber}: The list is now: {string.Join(", ", logItems.UtteranceList)}");

         // Create dictionary object to hold received user messages.
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
            // Inform the user an error occurred.
            await turnContext.SendActivityAsync("Sorry, something went wrong storing your message!");
         }
      }
      // Else, our storage already contained saved user messages, add new one to the list.
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
            // Inform the user an error occurred.
            await turnContext.SendActivityAsync("Sorry, something went wrong storing your message!");
         }
      }
   }
}

```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar o arquivo de configuração **. env** , o bot precisa de um pacote extra incluído. Caso ainda não o tenha instalado, obtenha o pacote dotnet do npm:

```console
npm install --save dotenv
```

Modifique o código em **index.js** que cria a caixa de diálogo principal. A linha de código existente para criar a caixa de diálogo principal é `const myBot = new EchoBot();` que ela precisa ser atualizada para habilitar a passagem de um objeto de armazenamento para o `EchoBot` Construtor, para que você possa armazenar a entrada do usuário na memória interna do bot:

Primeiro, você precisará adicionar uma referência a `MemoryStorage` no `botbuilder` :

```javascript
const { MemoryStorage } = require('botbuilder');
```

Em seguida, crie o objeto de armazenamento de memória e passe-o para o `EchoBot` Construtor:

```javascript
const myStorage = new MemoryStorage();
const myBot = new EchoBot(myStorage);
```

Isso passa um `MemoryStorage` objeto para o `EchoBot` Construtor. Você alterará isso posteriormente para passar um objeto de _armazenamento_ de _Cosmos DB_ ou BLOB.

Em seguida, substitua o código em **bot.js** pelo seguinte código:

```javascript
const { ActivityHandler, MemoryStorage } = require('botbuilder');
const restify = require('restify');

// Process incoming requests - adds storage for messages.
class EchoBot extends ActivityHandler {
    constructor(myStorage) {
        super();
        this.storage = myStorage;
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        this.onMessage(async turnContext => { console.log('this gets called (message)');
        await turnContext.sendActivity(`You said '${ turnContext.activity.text }'`);
        // Save updated utterance inputs.
        await logMessageText(this.storage, turnContext);
    });
        this.onConversationUpdate(async turnContext => { console.log('this gets called (conversation update)');
        await turnContext.sendActivity('Welcome, enter an item to save to your list.'); });
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
                await turnContext.sendActivity(`${numStored}: The list is now: ${storedString}`);
            } catch (err) {
                await turnContext.sendActivity(`Write failed of UtteranceLogJS: ${err}`);
            }
        }
        else{
            await turnContext.sendActivity(`Creating and saving new utterance log`);
            var turnNumber = 1;
            storeItems["UtteranceLogJS"] = { UtteranceList: [`${utterance}`], "eTag": "*", turnNumber }
            // Gather info for user message.
            var storedString = storeItems.UtteranceLogJS.UtteranceList.toString();
            var numStored = storeItems.UtteranceLogJS.turnNumber;

            try {
                await storage.write(storeItems)
                await turnContext.sendActivity(`${numStored}: The list is now: ${storedString}`);
            } catch (err) {
                await turnContext.sendActivity(`Write failed: ${err}`);
            }
        }
    }
    catch (err){
        await turnContext.sendActivity(`Read rejected. ${err}`);
    }
}

module.exports.EchoBot = EchoBot;

```

### <a name="python"></a>[Python](#tab/python)

Substitua o código em **echo_bot. py** pelo código a seguir:

```python
from botbuilder.core import ActivityHandler, TurnContext, StoreItem, MemoryStorage


class UtteranceLog(StoreItem):
    """
    Class for storing a log of utterances (text of messages) as a list.
    """

    def __init__(self):
        super(UtteranceLog, self).__init__()
        self.utterance_list = []
        self.turn_number = 0
        self.e_tag = "*"


class EchoBot(ActivityHandler):
    """
    Represents a bot saves and echoes back user input.
    """

    def __init__(self):
        self.storage = MemoryStorage()

    async def on_message_activity(self, turn_context: TurnContext):
        utterance = turn_context.activity.text

        # read the state object
        store_items = await self.storage.read(["UtteranceLog"])

        if "UtteranceLog" not in store_items:
            # add the utterance to a new state object.
            utterance_log = UtteranceLog()
            utterance_log.utterance_list.append(utterance)
            utterance_log.turn_number = 1
        else:
            # add new message to list of messages existing state object.
            utterance_log: UtteranceLog = store_items["UtteranceLog"]
            utterance_log.utterance_list.append(utterance)
            utterance_log.turn_number = utterance_log.turn_number + 1

        # Show user list of utterances.
        await turn_context.send_activity(f"{utterance_log.turn_number}: "
                                         f"The list is now: {','.join(utterance_log.utterance_list)}")

        try:
            # Save the user message to your Storage.
            changes = {"UtteranceLog": utterance_log}
            await self.storage.write(changes)
        except Exception as exception:
            # Inform the user an error occurred.
            await turn_context.send_activity("Sorry, something went wrong storing your message!")
```

---

### <a name="start-your-bot"></a>Inicie seu bot

Execute o bot localmente.

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

Instale o [emulador](https://aka.ms/bot-framework-emulator-readme) do bot Framework em seguida, inicie o emulador e conecte-se ao bot no emulador:

1. Selecione o link **criar nova configuração de bot** na guia de **boas-vindas** do emulador.
2. Preencha os campos para se conectar ao seu bot considerando as informações na página da Web exibida ao iniciar o bot.

### <a name="interact-with-your-bot"></a>Interagir com o bot

Envie uma mensagem ao bot. Ele listará as mensagens recebidas.

![Bot de armazenamento de teste do emulador](./media/emulator-direct-storage-test.png)

O restante deste artigo demonstrará como salvar o armazenamento persistente em vez da memória interna do bot.

## <a name="using-cosmos-db"></a>Usar o Cosmos DB

>[!IMPORTANT]
> A classe de _armazenamento do Cosmos DB_ foi preterida. Os contêineres criados originalmente com CosmosDbStorage não tinham nenhum conjunto de chaves de partição e receberam a chave de partição padrão de _ \/ _partitionKey_.
>
> Contêineres criados com _Cosmos DB armazenamento_ podem ser usados com _Cosmos DB armazenamento particionado_. Para obter mais informações, leia [Particionamento no Azure Cosmos DB](/azure/cosmos-db/partitioning-overview).
>
> Observe também que, diferentemente do armazenamento de Cosmos DB herdado, o armazenamento particionado Cosmos DB não cria automaticamente um banco de dados dentro de sua conta de Cosmos DB. Você precisa [criar um novo banco de dados manualmente](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal), mas ignorar a criação manual de um contêiner, pois o _CosmosDbPartitionedStorage_ criará o contêiner para você.

Agora que você usou o armazenamento de memória, vamos atualizar o código para usar o Azure Cosmos DB. O Cosmos DB é o banco de dados multimodelo globalmente distribuído da Microsoft. O Azure Cosmos DB permite que você dimensione a taxa de transferência e o armazenamento de maneira elástica e independente em qualquer número de regiões geográficas do Azure. Ele oferece garantias de taxa de transferência, disponibilidade, latência e consistência com contratos de nível de serviço (SLAs) abrangentes.

### <a name="set-up-a-cosmos-db-resource"></a>Configurar um recurso de Cosmos DB

Para usar o Cosmos DB em seu bot, você precisará criar um recurso de banco de dados antes de mexer no código. Para obter uma descrição detalhada do Cosmos DB banco de dados e criação de aplicativos, consulte o guia de início rápido para [.net](/azure/cosmos-db/create-sql-api-dotnet-v4), [Node.js](/azure/cosmos-db/create-sql-api-nodejs) ou [python](/azure/cosmos-db/create-sql-api-python).

### <a name="create-your-database-account"></a>Criar sua conta de banco de dados

1. Vá para o [portal do Azure](https://portal.azure.com) para criar uma conta do Azure Cosmos DB. Pesquise pelo **Azure Cosmos DB** e selecione-o.
1. Na página **Azure Cosmos DB** , selecione **novo** para abrir a página **criar Azure Cosmos DB conta** .

    ![Criar uma conta de banco de dados do Cosmos DB](./media/create-cosmosdb-database.png)

1. Forneça valores para os seguintes campos:
    1. **Assinatura**. Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos.
    1. **Grupo de recursos**. Selecione um grupo de recursos existente ou selecione **criar novo** e insira um nome para um novo grupo de recursos.
    1. **Nome da conta**. Insira um nome para identificar a conta do Azure Cosmos. Já que _documents.Azure.com_ é acrescentado ao nome que você fornece para criar o URI, use um nome exclusivo. Observe as seguintes diretrizes:
        - O nome deve ser exclusivo em todo o Azure.
        - O nome deve ter entre três e 31 caracteres.
        - O nome pode incluir apenas letras minúsculas, números e o caractere de hífen (-).
    1. **API**. Selecionar **núcleo (SQL)**
    1. **Localização**. Selecione um local mais próximo de seus usuários para conceder a eles o acesso mais rápido aos dados.
1. Selecione **Examinar + criar**.
1. Depois de validado, selecione **criar**.

A criação da conta leva alguns minutos. Aguarde até que o portal exiba a página _Parabéns! Sua conta do Azure Cosmos DB foi criada_.

### <a name="add-a-database"></a>Adicionar um banco de dados
<!---
>[!IMPORTANT]
> Unlike the legacy _Cosmos DB storage_, which has now been deprecated, the _Cosmos DB partitioned storage_ does not automatically create a database within your Cosmos DB account.
-->

> [!NOTE]
> Você não deve criar o contêiner por conta própria. Quando o bot criar seu respectivo cliente interno do Cosmos DB, ele também criará o contêiner para você. Isso garante que ele esteja configurado corretamente para armazenar o estado do bot.

1. Navegue até a página de **Data Explorer** dentro de sua conta de Cosmos DB recém-criada e, em seguida, escolha **novo banco de dados** na lista suspensa **novo contêiner** . Um painel será aberto no lado direito da janela, no qual você poderá inserir os detalhes do novo banco de dados.

    ![Criar Cosmos DB imagem de recurso do banco de dados](./media/create-cosmosdb-database-resource.png)

1. Insira uma ID para o novo banco de dados e, opcionalmente, defina a taxa de transferência (você pode alterar isso posteriormente) e, finalmente, selecione **OK** para criar seu banco de dados. Ao configurar seu bot, anote essa ID de banco de dados para uso posterior.
1. Agora que criou uma conta do Cosmos DB e um banco de dados, você precisa copiar alguns dos valores para integrar seu novo banco de dados ao bot.  Para recuperá-los, navegue até a guia **Chaves** na seção de configurações do banco de dados da sua conta do Cosmos DB.  Nessa página, você precisará do **URI** (_ponto de extremidade Cosmos DB_) e da **chave primária** (_chave de autorização_).

    ![Chaves do Cosmos DB](./media/cosmos-db-keys-legend.png)

Agora você deve ter uma conta de Cosmos DB com um banco de dados e os seguintes valores prontos para uso nas configurações de bot.

- URI
- Chave primária
- ID do banco de dados

### <a name="add-cosmos-db-configuration-information"></a>Adicionar informações de configuração de Cosmos DB

Use os detalhes que você anotou na parte anterior deste artigo para definir o ponto de extremidade, a chave de autorização e a ID do banco de dados.  Por fim, você precisa escolher um nome apropriado para o contêiner que será criado no banco de dados para armazenar o estado do bot. No exemplo abaixo, o contêiner Cosmos DB criado será denominado "bot-Storage".

### <a name="c"></a>[C#](#tab/csharp)

Adicione as informações a seguir ao arquivo de configuração.

**appsettings.json**

```json
"CosmosDbEndpoint": "<your-CosmosDb-URI>",
"CosmosDbAuthKey": "<your-primary-key>",
"CosmosDbDatabaseId": "<your-database-id>",
"CosmosDbContainerId": "bot-storage"
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Adicione as informações a seguir ao arquivo **. env** .

**.env**

```javascript
CosmosDbEndpoint="<your-CosmosDb-URI>"
CosmosDbAuthKey="<your-primary-key>"
CosmosDbDatabaseId="<your-database-id>"
CosmosDbContainerId="bot-storage"
```

### <a name="python"></a>[Python](#tab/python)

Adicione as informações a seguir ao arquivo de configuração.

**config.py**

```python
COSMOS_DB_URI="<your-CosmosDb-URI>"
COSMOS_DB_PRIMARY_KEY="your-primary-key"
COSMOS_DB_DATABASE_ID="<your-database-id>"
COSMOS_DB_CONTAINER_ID="bot-storage"
```

---

#### <a name="installing-cosmos-db-packages"></a>Instalando pacotes de Cosmos DB

Verifique se você tem os pacotes necessários para o Cosmos DB.

### <a name="c"></a>[C#](#tab/csharp)

Instale o pacote NuGet **Microsoft. bot. Builder. Azure** . Para obter mais informações sobre como usar o NuGet, consulte [instalar e gerenciar pacotes no Visual Studio usando o Gerenciador de pacotes NuGet ](/nuget/consume-packages/install-use-packages-visual-studio).

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Adicione referências a **botbuilder-Azure** usando NPM.
<!-- Email sent to Steven Gum and Josh Gummersall to validate the following note is correct. I was able to run through this scenario without Python installed, ao I am removing this.
> [!NOTE]
> This npm package relies on an installation of Python existing on your development machine. If you have not previously installed Python you can find installation resources for your machine at [python.org](https://www.python.org/downloads/).
 -->

```Console
npm install --save botbuilder-azure
```

Se ainda não estiver instalado, obtenha o pacote dotNet de NPM para acessar as configurações do arquivo **. env** .

```Console
npm install --save dotenv
```

### <a name="python"></a>[Python](#tab/python)

Você pode adicionar referências ao botbuilder-azure em seu projeto por meio do pip.

```Console
pip install botbuilder-azure
```

---

### <a name="cosmos-db-implementation"></a>Implementação de Cosmos DB

> [!NOTE]
> A versão 4.6 introduziu um novo provedor de armazenamento do Cosmos DB, a classe de _armazenamento particionado do Cosmos DB_. A classe de _armazenamento do Cosmos DB_ original foi preterida. Contêineres criados com _Cosmos DB armazenamento_ podem ser usados com _Cosmos DB armazenamento particionado_. Para obter mais informações, leia [Particionamento no Azure Cosmos DB](/azure/cosmos-db/partitioning-overview).
>
> Observe também que, diferentemente do armazenamento de Cosmos DB herdado, o armazenamento particionado Cosmos DB não cria automaticamente um banco de dados dentro de sua conta de Cosmos DB. Você precisa [criar um novo banco de dados manualmente](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal), mas ignorar a criação manual de um contêiner, pois o _CosmosDbPartitionedStorage_ criará o contêiner para você.

### <a name="c"></a>[C#](#tab/csharp)

O código de exemplo a seguir é executado usando o mesmo código de bot que o exemplo de [armazenamento de memória](#memory-storage) fornecido acima, com as exceções listadas aqui.
Os trechos de código a seguir mostram uma implementação de armazenamento de Cosmos DB para '_mystorage_' que substitui o armazenamento de memória local.

Primeiro, você precisa atualizar **Startup.cs** para fazer referência à biblioteca _do Azure do bot Builder_ :

```csharp
using Microsoft.Bot.Builder.Azure;
```

Em seguida, no `ConfigureServices` método em **Startup.cs**, crie o `CosmosDbPartitionedStorage` objeto. Isso será passado para o `EchoBot` Construtor por meio de injeção de dependência.

```csharp
// Use partitioned CosmosDB for storage, instead of in-memory storage.
services.AddSingleton<IStorage>(
    new CosmosDbPartitionedStorage(
        new CosmosDbPartitionedStorageOptions
        {
            CosmosDbEndpoint = Configuration.GetValue<string>("CosmosDbEndpoint"),
            AuthKey = Configuration.GetValue<string>("CosmosDbAuthKey"),
            DatabaseId = Configuration.GetValue<string>("CosmosDbDatabaseId"),
            ContainerId = Configuration.GetValue<string>("CosmosDbContainerId"),
            CompatibilityMode = false,
        }));
```

Em **EchoBot.cs** , altere a `_myStorage` declaração de variável `private static readonly MemoryStorage _myStorage = new MemoryStorage();` para o seguinte:

```csharp
// variable used to save user input to CosmosDb Storage.
private readonly IStorage _myStorage;
```

Em seguida, passe o `IStorage` objeto para o `EchoBot` Construtor:

```csharp
public EchoBot(IStorage storage)
{
    if (storage is null) throw new ArgumentNullException();
    _myStorage = storage;
}
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Primeiro, adicione código ao arquivo de **index.js** para permitir que você acesse os valores de seu arquivo **. env** que você inseriu anteriormente:

```javascript
// initialized to access values in .env file.
const ENV_FILE = path.join(__dirname, '.env');
require('dotenv').config({ path: ENV_FILE });
```

Em seguida, você precisará fazer alterações no **index.js** para usar Cosmos DB armazenamento particionado em vez do armazenamento interno das estruturas de bot. Observe que todas as alterações de código nesta seção são feitas em **index.js**.

Primeiro, adicione uma referência a `botbuilder-azure` no **index.js**. Isso dará acesso à `BlobStorage` API:

```javascript
const { CosmosDbPartitionedStorage } = require('botbuilder-azure');
```

Em seguida, crie o novo `CosmosDbPartitionedStorage` objeto:

```javascript
const myStorage = new CosmosDbPartitionedStorage({
    cosmosDbEndpoint: process.env.CosmosDbEndpoint,
    authKey: process.env.CosmosDbAuthKey,
    databaseId: process.env.CosmosDbDatabaseId,
    containerId: process.env.CosmosDbContainerId,
    compatibilityMode: false
});
```

Agora você pode comentar ou remover a `myStorage` declaração Const que você adicionou anteriormente, já que você não está mais salvando a entrada do usuário no armazenamento interno do bot frameworks, mas em vez de Cosmos DB:

```javascript
//const myStorage = new MemoryStorage();
const myBot = new EchoBot(myStorage);
```

### <a name="python"></a>[Python](#tab/python)

O código de exemplo a seguir é executado usando o mesmo código de bot que o exemplo de [armazenamento de memória](#memory-storage) fornecido acima, com as exceções listadas aqui.

`CosmosDbPartitionedStorage`E `CosmosDbPartitionedConfig` from `botbuilder-azure` são necessários para criar o objeto CosmosDBStorage.

**echo_bot. py**

```python
from botbuilder.azure import CosmosDbPartitionedStorage, CosmosDbPartitionedConfig
```

Para acessar as configurações no **config.py**, você irá importar `DefaultConfig` conforme mostrado abaixo:

```python
from config import DefaultConfig
CONFIG = DefaultConfig()
```

Comente o armazenamento de memória em `__init__` e substitua por uma referência ao Cosmos DB.  Use o URI (ponto de extremidade), a chave primária (chave de autorização), a ID do banco de dados e a ID do contêiner usados acima.

Em seguida, remova ou comente o código de armazenamento de memória no `__init__` e adicione uma referência às suas informações de Cosmos DB do **config.py**.

O trecho de código abaixo mostra uma implementação de Cosmos DB para ' armazenamento ' que substitui o armazenamento de memória local.

**echo_bot. py**

```python
def __init__(self):
    cosmos_config = CosmosDbPartitionedConfig(
        cosmos_db_endpoint=CONFIG.COSMOS_DB_URI,
        auth_key=CONFIG.COSMOS_DB_PRIMARY_KEY,
        database_id=CONFIG.COSMOS_DB_DATABASE_ID,
        container_id=CONFIG.COSMOS_DB_CONTAINER_ID,
        compatibility_mode = False
    )
    self.storage = CosmosDbPartitionedStorage(cosmos_config)
```

---

## <a name="start-your-cosmos-db-bot"></a>Iniciar o bot de Cosmos DB

Execute o bot localmente.

## <a name="test-your-cosmos-db-bot-with-bot-framework-emulator"></a>Testar o bot de Cosmos DB com o emulador do bot Framework

Agora inicie o emulador do bot Framework e conecte-se ao bot:

1. Selecione o link **criar uma nova configuração de bot** na guia de **boas-vindas** do emulador.
2. Preencha os campos para se conectar ao seu bot considerando as informações na página da Web exibida ao iniciar o bot.

## <a name="interact-with-your-cosmos-db-bot"></a>Interagir com o bot de Cosmos DB

Envie uma mensagem ao bot e o bot listará as mensagens recebidas por ele.
![Emulador em execução](./media/emulator-direct-storage-test.png)

### <a name="view-your-cosmos-db-data"></a>Exibir seus dados de Cosmos DB

Depois de executar o bot e salvar suas informações, poderemos exibir os dados armazenados no portal do Azure na guia **Data Explorer**.

![Exemplo do Data Explorer](./media/data_explorer.PNG)

## <a name="using-blob-storage"></a>Usar o armazenamento de blobs

O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários. Esta seção explica como criar uma conta e um contêiner de armazenamento de BLOBs do Azure e como fazer referência ao seu contêiner de armazenamento de BLOBs do bot.

Para obter informações adicionais sobre o armazenamento de BLOBs, consulte [o que é o armazenamento de BLOBs do Azure?](/azure/storage/blobs/storage-blobs-overview)

### <a name="create-your-blob-storage-account"></a>Criar sua conta de Armazenamento de Blobs

Para usar o Armazenamento de Blobs em seu bot, você precisará configurar algumas coisas antes de mexer no código.

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**.
1. Na seção em **destaque** da página **todos os serviços** , selecione **contas de armazenamento**.
1. Na página **contas de armazenamento** , selecione * * novo * * * *.

    ![A página Criar conta de armazenamento do blob](./media/blob-storage-new-account.png)

1. No campo **assinatura** , selecione a assinatura na qual criar a conta de armazenamento.
1. No campo **grupo de recursos** , selecione um grupo de recursos existente ou selecione **criar novo** e insira um nome para o novo grupo de recursos.
1. No campo **nome da conta de armazenamento** , insira um nome para a conta. Observe as seguintes diretrizes:
    - O nome deve ser exclusivo em todo o Azure.
    - O nome deve ter entre três e 24 caracteres.
    - O nome pode incluir apenas números e letras minúsculas.
1. No campo **local** , selecione um local para a conta de armazenamento ou use o local padrão.
1. Para o restante das configurações, configure o seguinte:
    - **Desempenho**: Standard. [Saiba mais sobre o desempenho](/azure/storage/common/storage-account-overview#performance-tiers).
    - **Tipo de conta**: BlobStorage. [Saiba mais sobre contas de armazenamento](/azure/storage/common/storage-account-create?tabs=azure-portal).
    - **Replicação**: Deixe a configuração padrão. [Saiba mais sobre redundância](/azure/storage/common/storage-redundancy).

1. Na seção **detalhes do projeto** da página **criar conta de armazenamento** , selecione os valores desejados para **assinatura** e **grupo de recursos**.
1. Na seção **detalhes da instância** da página **criar conta de armazenamento** , insira o **nome da conta de armazenamento** e, em seguida, selecione valores para **local**, **tipo de conta** e **replicação**.
1. Selecione **revisão + criar** para revisar as configurações da conta de armazenamento.
1. Depois de validado, selecione **criar**.

### <a name="create-blob-storage-container"></a>Criar contêiner de Armazenamento de Blobs

Depois que sua conta de armazenamento de BLOBs for criada, abra-a e, em seguida:

1. Selecione **Gerenciador de armazenamento (versão prévia)**.
1. Clique com o botão direito do mouse em **contêineres de blob**
1. Selecione **criar contêiner de blob** na lista suspensa.

    ![Criar contêiner de Armazenamento de Blobs](./media/create-blob-container.png)

1. Insira um nome no novo formulário de **contêiner** . Você usará esse nome para o valor de seu "_BLOB-Storage-container-Name_" para fornecer acesso à sua conta de armazenamento de BLOBs. Observe as seguintes diretrizes:
    - Esse nome pode conter apenas letras minúsculas, números e hifens.
    - Esse nome deve começar com uma letra ou um número.
    - Cada hífen deve ser precedido e seguido por um caractere não-hífen válido.
    - O nome deve ter entre três e 63 caracteres de comprimento.

#### <a name="add-blob-storage-configuration-information"></a>Adicionar informações de configuração de armazenamento de BLOBs

Localize as chaves de armazenamento de BLOBs necessárias para configurar o armazenamento de BLOBs para o bot, conforme mostrado acima:

1. No portal do Azure, abra sua conta de armazenamento de BLOBs e selecione **chaves de acesso** na seção **configurações** .

    ![Localizar chaves de armazenamento de BLOBs](./media/find-blob-storage-keys.png)

Use a **cadeia de conexão** como o valor para sua "_cadeia de conexão_" para fornecer acesso à sua conta de armazenamento de BLOBs.

### <a name="c"></a>[C#](#tab/csharp)

Adicione as informações a seguir ao arquivo de configuração.

**appsettings.json**

```json
"BlobConnectionString": "<your-blob-connection-string>",
"BlobContainerName": "<your-blob-container-name>",
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Adicione as informações a seguir ao arquivo **. env** .

**.env**

```javascript
BlobConnectionString="<your-blob-connection-string>"
BlobContainerName="<your-blob-container-name>"
```

### <a name="python"></a>[Python](#tab/python)

Adicione as informações a seguir ao arquivo de configuração. Use o mesmo nome de contêiner e cadeia de conexão usados ao criar seu [contêiner de armazenamento de BLOBs](#create-blob-storage-container).

**config.py**

```python
BLOB_CONNECTION_STRING="<your-blob-connection-string>"
BLOB_CONTAINER_NAME="<your-blob-container-name>"
```

---

#### <a name="installing-blob-storage-packages"></a>Instalando pacotes de armazenamento de BLOBs

Se não tiver sido instalado anteriormente, instale os pacotes a seguir.

### <a name="c"></a>[C#](#tab/csharp)

Instale o pacote NuGet **Microsoft. bot. Builder. Azure. BLOBs** . Para obter mais informações sobre como usar o NuGet, consulte [instalar e gerenciar pacotes no Visual Studio usando o Gerenciador de pacotes NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

<!--
```Console
Install-Package Microsoft.Bot.Builder.Azure.Blobs
```
-->

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Adicione referências ao botbuilder-azure no seu projeto por meio do npm.

> [!NOTE]
> Este pacote NPM depende de uma instalação do Python existente em seu computador de desenvolvimento. Se você não tiver instalado o Python anteriormente, poderá encontrar recursos de instalação para seu computador em [Python.org](https://www.python.org/downloads/)

```Console
npm install --save botbuilder-azure
```

Se ainda não estiver instalado, obtenha o pacote dotNet de NPM para acessar as configurações do arquivo **. env** .

```Console
npm install --save dotenv
```

### <a name="python"></a>[Python](#tab/python)

Você pode adicionar referências ao botbuilder-azure em seu projeto por meio do pip.

```Console
pip install botbuilder-azure
```

---

### <a name="blob-storage-implementation"></a>Implementação do armazenamento de BLOBs

O _armazenamento de BLOBs_ é usado para armazenar o estado do bot.

### <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> A partir da versão 4,10, `Microsoft.Bot.Builder.Azure.AzureBlobStorage` é preterida. Use o novo `Microsoft.Bot.Builder.Azure.Blobs.BlobsStorage` em seu lugar.

O código de exemplo a seguir é executado usando o mesmo código de bot que o exemplo de [armazenamento de memória](#memory-storage) fornecido acima, com as exceções listadas aqui.

Os trechos de código a seguir mostram uma implementação do armazenamento de BLOBs para o '_mystorage_' que substitui o armazenamento de memória local.

Primeiro, você precisa atualizar **Startup.cs** para fazer referência à biblioteca de _BLOBs do Azure para o bot Builder_ :

**Startup.cs**

```csharp
using Microsoft.Bot.Builder.Azure.Blobs;
```

Em seguida, no `ConfigureServices` método em **Startup.cs**, crie o `BlobsStorage` objeto, passando os valores de `appsettings.json` . Isso será passado para o `EchoBot` Construtor por meio de injeção de dependência.

```csharp
//Use Azure Blob storage, instead of in-memory storage.
services.AddSingleton<IStorage>(
    new BlobsStorage(
        Configuration.GetValue<string>("dataConnectionString"),
        Configuration.GetValue<string>("containerName")
        ));
```

Agora, primeiro você precisa atualizar **EchoBot.cs** para fazer referência à biblioteca de _BLOBs do Azure para o bot Builder_ :

**EchoBot.cs**

```csharp
using Microsoft.Bot.Builder.Azure.Blobs;
```

Em seguida, remova ou comente a linha de código que cria a variável MemoryStorage ' private estático ReadOnly MemoryStorage _myStorage = New MemoryStorage (); ' e crie uma nova variável que será usada para salvar a entrada do usuário no armazenamento de BLOBs.

**EchoBot.cs**

```csharp
// variable used to save user input to CosmosDb Storage.
private readonly IStorage _myStorage;
```

Em seguida, passe o `IStorage` objeto para o `EchoBot` Construtor:

```csharp
public EchoBot(IStorage storage)
{
    if (storage is null) throw new ArgumentNullException();
    _myStorage = storage;
}
```

Quando o armazenamento estiver definido para apontar para sua conta de armazenamento de BLOBs, o código de bot agora armazenará e recuperará dados do armazenamento de BLOBs.

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Todas as alterações de código nesta seção são feitas em **index.js**.

Primeiro, adicione o código para permitir que você acesse os valores de seu arquivo **. env** que você inseriu anteriormente:

```javascript
// initialized to access values in .env file.
const ENV_FILE = path.join(__dirname, '.env');
require('dotenv').config({ path: ENV_FILE });
```

Em seguida, você precisará fazer as alterações usar o armazenamento de BLOB em vez do armazenamento interno das estruturas de bot.

Em seguida, adicione uma referência a `botbuilder-azure` . Isso dará acesso à API de armazenamento de BLOBs:

```javascript
const { BlobStorage } = require("botbuilder-azure");
```

Agora você pode modificar seu código para usar `BlobStorage` modificando sua `myStorage` declaração da seguinte maneira:

```javascript
const myStorage = new BlobStorage({
    containerName: process.env.BlobContainerName,
    storageAccountOrConnectionString: process.env.BlobConnectionString
});
```

Quando o armazenamento estiver definido para apontar para sua conta de armazenamento de BLOBs, o código de bot agora armazenará e recuperará dados do armazenamento de BLOBs.

### <a name="python"></a>[Python](#tab/python)

O código de exemplo a seguir é executado usando o mesmo código de bot que o exemplo de [armazenamento de memória](#memory-storage) fornecido acima, com as exceções listadas aqui.

Isso exigirá `BlobStorage` e `BlobStorageSettings` do `botbuilder-azure` .

**echo_bot. py**

```python
from botbuilder.azure import BlobStorage, BlobStorageSettings
```

Para acessar as configurações no **config.py**, você irá importar `DefaultConfig` conforme mostrado abaixo:

```python
from config import DefaultConfig
CONFIG = DefaultConfig()
```

Em seguida, remova ou comente o código de armazenamento de memória no `__init__` e adicione uma referência às informações de armazenamento de BLOBs em **config.py**.

O trecho de código abaixo mostra uma implementação do armazenamento de BLOBs que substitui o armazenamento de memória local.

**echo_bot. py**

```python
def __init__(self):
    blob_settings = BlobStorageSettings(
        connection_string=CONFIG.BLOB_CONNECTION_STRING,
        container_name=CONFIG.BLOB_CONTAINER_NAME
    )
    self.storage = BlobStorage(blob_settings)
```

---

Quando o armazenamento estiver definido para apontar para sua conta de armazenamento de BLOBs, o código de bot agora armazenará e recuperará dados do armazenamento de BLOBs.

## <a name="start-your-blob-storage-bot"></a>Iniciar o bot do armazenamento de BLOBs

Execute o bot localmente.

## <a name="start-the-emulator-and-connect-your-blob-storage-bot"></a>Iniciar o emulador e conectar o bot do armazenamento de BLOBs

Em seguida, inicie o emulador e conecte-se ao bot no emulador:

1. Selecione o link **criar nova configuração de bot** na guia "bem-vindo" do emulador.
2. Preencha os campos para se conectar ao seu bot considerando as informações na página da Web exibida ao iniciar o bot.

## <a name="interact-with-your-blob-storage-bot"></a>Interagir com o bot do armazenamento de BLOBs

Envie uma mensagem ao bot, e ele listará as mensagens recebidas.

![Bot de armazenamento de teste do emulador](./media/emulator-direct-storage-test.png)

### <a name="view-your-blob-storage-data"></a>Exibir seus dados de armazenamento de BLOBs

Depois de executar o bot e salvar suas informações, poderemos exibi-lo na guia **Gerenciador de Armazenamento** no portal do Azure.

## <a name="blob-transcript-storage"></a>Armazenamento de transcrição de blobs

O armazenamento de transcrições do Blob do Azure fornece uma opção de armazenamento especializado que permite salvar e recuperar facilmente conversas do usuário como transcrições gravadas. O armazenamento de transcrições de blob do Azure é bastante útil para capturar respostas do usuário automaticamente e examiná-las durante a depuração de desempenho do seu bot.

> [!NOTE]
> No momento, o Python não oferece suporte ao _armazenamento de transcrição de blob do Azure_.
> Embora o JavaScript dê suporte ao armazenamento de transcrição de BLOB, as instruções a seguir são apenas para C#.

### <a name="set-up-a-blob-transcript-storage-container"></a>Configurar um contêiner de armazenamento de transcrição de BLOB

O armazenamento de transcrições do Blob do Azure pode usar a mesma conta de Armazenamento de Blobs criada seguindo as etapas detalhadas nas seções "_Criar sua conta de Armazenamento de Blobs_" e "_Adicionar informações de configuração_" acima. Agora, adicionamos um contêiner para manter nossas transcrições.

![Criar contêiner de transcrição](./media/create-blob-transcript-container.png)

1. Abra uma conta de Armazenamento de Blobs do Azure.
1. Selecione **Gerenciador de armazenamento**.
1. Clique com botão direito do mouse em _CONTÊINERES DE BLOBS_ e selecione **Criar contêiner de blobs**.
1. Insira um nome para seu contêiner de transcrição e, em seguida, selecione **OK**. (Inserimos _mybottranscripts_)

### <a name="blob-transcript-storage-implementation"></a>Implementação de armazenamento de transcrição de BLOB

O código a seguir conecta o ponteiro de armazenamento de transcrição `_myTranscripts` à sua nova conta de armazenamento de transcrições do Blob do Azure. Para criar esse link com um novo nome de contêiner, \<your-blob-transcript-container-name> , cria um novo contêiner dentro do armazenamento de BLOBs para manter seus arquivos de transcrição.

O _armazenamento de transcrição de blob_ é projetado para armazenar transcrições de bot.

> [!NOTE]
> A partir da versão 4,10, `Microsoft.Bot.Builder.Azure.AzureBlobTranscriptStore` é preterida. Use o novo `Microsoft.Bot.Builder.Azure.Blobs.BlobsTranscriptStore` em seu lugar.

**echoBot.cs**

```csharp
using Microsoft.Bot.Builder.Azure.Blobs;

public class EchoBot : ActivityHandler
{
   ...

   private readonly BlobsTranscriptStore _myTranscripts = new BlobsTranscriptStore("<your-azure-storage-connection-string>", "<your-blob-transcript-container-name>");

   ...
}

```

### <a name="store-user-conversations-in-azure-blob-transcripts"></a>Armazenar conversas do usuário em transcrições do Blob do Azure

Quando um contêiner de blob fica disponível para armazenar transcrições, você pode começar a armazenar as conversas dos usuários com seu bot. Essas conversas poderão ser usadas posteriormente como uma ferramenta de depuração para ver como os usuários interagem com o bot. Cada _conversa de reinicialização_ do emulador inicia a criação de uma nova lista de conversa de transcrição. O código a seguir preserva as entradas de conversas do usuário em um arquivo de transcrição armazenado.

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

Consulte [armazenamento de transcrição de blob do Azure](/dotnet/api/microsoft.bot.builder.azure.blobs.blobstranscriptstore) para obter mais informações sobre a classe.

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

### <a name="c"></a>[C#](#tab/csharp)

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

### <a name="javascript"></a>[JavaScript](#tab/javascript)

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

### <a name="python"></a>[Python](#tab/python)

Primeiro, crie uma classe que implemente `StoreItem`.

**echo_bot. py**

```python
class Note(StoreItem):
    def __init__(self, name: str, contents: str, e_tag="*"):
        super(Note, self).__init__()
        self.name = name
        self.contents = contents
        self.e_tag = e_tag
```

Em seguida, crie uma nota inicial criando um objeto de armazenamento e adicione o objeto ao armazenamento.

**echo_bot. py**

```python
# create a note for the first time, with a non-null, non-* ETag.
changes = {"Note": Note(name="Shopping List", contents="eggs", e_tag="x")}

await self.storage.write(changes)
```

Então, acesse e atualize a nota mais tarde, mantendo `eTag` que foi lido no armazenamento.

**echo_bot. py**

```python
store_items = await self.storage.read(["Note"])
    note = store_items["Note"]
    note.contents = note.contents + ", bread"

    changes = {"Note": note}
    await self.storage.write(changes)
```

Se a nota foi atualizada no armazenamento antes de gravar as alterações, a chamada para `write` lançará uma exceção.

---

Para manter a simultaneidade, sempre leia uma propriedade do armazenamento e modifique a propriedade lida para que `eTag` seja mantido. Ao ler os dados de usuário do armazenamento, a resposta conterá a propriedade eTag. Se você alterar os dados e gravar dados atualizados no armazenamento, a solicitação deverá incluir a propriedade eTag que especifica o mesmo valor lido anteriormente. No entanto, gravar um objeto com `eTag` definido para `*` permitirá que a gravação substitua quaisquer outras alterações.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como ler e gravar diretamente do armazenamento, veremos como é possível utilizar o gerenciador de estado para fazer isso.

> [!div class="nextstepaction"]
> [Salvar estado usando conversa e propriedades do usuário](bot-builder-howto-v4-state.md)
