---
title: Expirar um serviço de bot de conversa
description: Saiba como expirar a conversa de um usuário com um bot.
keywords: expirar, tempo limite
author: ericdahlvang
ms.author: ericdahlvang
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/14/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e207aa011d03db30c68615440cb588e3e8c0d125
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89364384"
---
# <a name="expire-a-conversation"></a>Expirar uma conversa

[!INCLUDE[applies-to](../includes/applies-to.md)]

Às vezes, um bot precisa reiniciar uma conversa desde o início.  Por exemplo, se um usuário não responder após um determinado período de tempo.  Este artigo descreve dois métodos para expirar uma conversa:

- Acompanhe a última vez em que uma mensagem foi recebida de um usuário e limpe o estado se o tempo for maior do que um comprimento pré-configurado ao receber a próxima mensagem do usuário. Para obter mais informações, consulte a seção [expiração de interação do usuário](#user-interaction-expiration) .
- Use um recurso de camada de armazenamento, como Cosmos DB TTL (vida útil), para limpar automaticamente o estado após um período de tempo pré-configurado. Para obter mais informações, consulte a seção [expiração de armazenamento](#storage-expiration) .

<!-- 
NOTE: in the future, provide guidance on an azure function queue or time trigger

- Track the last time a message was received from a user, and run a Web Job or Azure Function to clear the state and/or proactively message the user. See [Proactive Expiration](#proactive-expiration)
-->

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Conhecimento de [noções básicas de bot][concept-basics], [gerenciamento de estado][concept-state] e [biblioteca de diálogos][concept-dialogs].
- Uma cópia do **exemplo de prompt de vários turnos** em [**C#** ][cs-sample], [**JavaScript**][js-sample] ou [**Python**][python-sample].

## <a name="about-this-sample"></a>Sobre este exemplo

O código de exemplo neste artigo começa com a estrutura de um bot com vários folheios e estende a funcionalidade desse bot adicionando código adicional (fornecido nas seções a seguir). Esse código estendido demonstra como limpar o estado da conversa após um determinado período de tempo aprovado.

## <a name="user-interaction-expiration"></a>Expiração da interação do usuário

Esse tipo de conversa de expiração é realizado com a adição de uma propriedade _hora do último acesso_ ao estado de conversa do bot. Esse valor de propriedade é comparado com a hora atual dentro do _manipulador de atividade_ antes de processar atividades.

> [!NOTE]
> Este exemplo usa um tempo limite de 30 segundos para facilitar o teste desse padrão.

# <a name="c"></a>[C#](#tab/csharp)

**appsettings.json**

Primeiro, adicione uma `ExpireAfterSeconds` configuração para appsettings.jsem:

```json
{
  "MicrosoftAppId": "",
  "MicrosoftAppPassword": "",
  "ExpireAfterSeconds": 30
}
```

**Bots\DialogBot.cs**

Em seguida, adicione os `ExpireAfterSeconds` `LastAccessedTimeProperty` campos, e `DialogStateProperty` à classe bot e inicialize-os no construtor do bot. Além disso, adicione um `IConfiguration` parâmetro ao construtor com o qual recuperar o `ExpireAfterSeconds` valor.

Observe que, em vez de criar o acessador de propriedade de estado da caixa de diálogo embutido no `OnMessageActivityAsync` método, você está criando e gravando-o no momento da inicialização. O bot precisará do acessador de propriedade de estado não apenas para executar a caixa de diálogo, mas também para limpar o estado da caixa de diálogo.

```csharp
protected readonly int ExpireAfterSeconds;
protected readonly IStatePropertyAccessor<DateTime> LastAccessedTimeProperty;
protected readonly IStatePropertyAccessor<DialogState> DialogStateProperty;

// Existing fields omitted...

public DialogBot(IConfiguration configuration, ConversationState conversationState, UserState userState, T dialog, ILogger<DialogBot<T>> logger)
{
    ConversationState = conversationState;
    UserState = userState;
    Dialog = dialog;
    Logger = logger;

    TimeoutSeconds = configuration.GetValue<int>("ExpireAfterSeconds");
    DialogStateProperty = ConversationState.CreateProperty<DialogState>(nameof(DialogState));
    LastAccessedTimeProperty = ConversationState.CreateProperty<DateTime>(nameof(LastAccessedTimeProperty));
}
```

Por fim, adicione o código ao método do bot `OnTurnAsync` para limpar o estado da caixa de diálogo se a conversa for muito antiga.

```csharp
public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default)
{
    // Retrieve the property value, and compare it to the current time.
    var lastAccess = await LastAccessedTimeProperty.GetAsync(turnContext, () => DateTime.UtcNow, cancellationToken).ConfigureAwait(false);
    if ((DateTime.UtcNow - lastAccess) >= TimeSpan.FromSeconds(ExpireAfterSeconds))
    {
        // Notify the user that the conversation is being restarted.
        await turnContext.SendActivityAsync("Welcome back!  Let's start over from the beginning.").ConfigureAwait(false);

        // Clear state.
        await ConversationState.ClearStateAsync(turnContext, cancellationToken).ConfigureAwait(false);
    }

    await base.OnTurnAsync(turnContext, cancellationToken).ConfigureAwait(false);

    // Set LastAccessedTime to the current time.
    await LastAccessedTimeProperty.SetAsync(turnContext, DateTime.UtcNow, cancellationToken).ConfigureAwait(false);

    // Save any state changes that might have occurred during the turn.
    await ConversationState.SaveChangesAsync(turnContext, false, cancellationToken).ConfigureAwait(false);
    await UserState.SaveChangesAsync(turnContext, false, cancellationToken).ConfigureAwait(false);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**.env**

Primeiro, adicione uma `ExpireAfterSeconds` Configuração a. env:

```json
MicrosoftAppId=
MicrosoftAppPassword=
ExpireAfterSeconds=30
```

**bots\dialogBot.js**

Em seguida, adicione campos `DialogBot` e atualize o construtor. Adicione campos locais para `expireAfterSeconds` e `lastAccessedTimeProperty` .

Adicione `expireAfterSeconds` como um parâmetro ao construtor e crie o necessário `StatePropertyAccessor` :

```javascript
constructor(expireAfterSeconds, conversationState, userState, dialog) {

    // Existing code omitted...

    this.lastAccessedTimeProperty = this.conversationState.createProperty('LastAccessedTime');
    this.expireAfterSeconds = expireAfterSeconds;

    // Existing code omitted...
}
```

Adicione código ao `run` método bot:

```javascript
async run(context) {
    // Retrieve the property value, and compare it to the current time.
    const now = new Date();
    const lastAccess = new Date(await this.lastAccessedTimeProperty.get(context, now.toISOString()));
    if (now !== lastAccess && ((now.getTime() - lastAccess.getTime()) / 1000) >= this.expireAfterSeconds) {
        // Notify the user that the conversation is being restarted.
        await context.sendActivity("Welcome back!  Let's start over from the beginning.");

        // Clear state.
        await this.conversationState.clear(context);
    }

    await super.run(context);

    // Set LastAccessedTime to the current time.
    await this.lastAccessedTimeProperty.set(context, now.toISOString());

    // Save any state changes. The load happened during the execution of the Dialog.
    await this.conversationState.saveChanges(context, false);
    await this.userState.saveChanges(context, false);
}
```

**index.js**

Por fim, atualize `index.js` para enviar o `ExpireAfterSeconds` parâmetro para `DialogBot` :

```javascript
const bot = new DialogBot(process.env.ExpireAfterSeconds, conversationState, userState, dialog);
```

## <a name="python"></a>[Python](#tab/python)

**config.py**

Primeiro, adicione uma `ExpireAfterSeconds` Configuração a config.py:

```python
PORT = 3978
APP_ID = os.environ.get("MicrosoftAppId", "")
APP_PASSWORD = os.environ.get("MicrosoftAppPassword", "")
EXPIRE_AFTER_SECONDS = os.environ.get("ExpireAfterSeconds", 30)
```

**bots \ dialog_bot. py**

Em seguida, adicione campos `DialogBot` e atualize o construtor. Adicione campos locais para `expire_after_seconds` e `last_accessed_time_property` .

Adicione `expire_after_seconds` como um parâmetro ao construtor e crie o necessário `StatePropertyAccessor` :

```python
def __init__(
    self,
    expire_after_seconds: int,
    conversation_state: ConversationState,
    user_state: UserState,
    dialog: Dialog,
):
    # Existing code omitted...

    self.expire_after_seconds = expire_after_seconds
    self.dialog_state_property = conversation_state.create_property("DialogState")
    self.last_accessed_time_property = conversation_state.create_property("LastAccessedTime")
    self.conversation_state = conversation_state
    self.user_state = user_state
    self.dialog = dialog
```

Altere `on_message_activity` para que ele use `dialog_state_property` :

```python
async def on_message_activity(self, turn_context: TurnContext):
    await DialogHelper.run_dialog(
        self.dialog,
        turn_context,
        self.dialog_state_property,
    )
```

Adicione código ao `on_turn` método bot:

```python
async def on_turn(self, turn_context: TurnContext):
    # Retrieve the property value, and compare it to the current time.
    now_seconds = int(time.time())
    last_access = int(
        await self.last_accessed_time_property.get(turn_context, now_seconds)
    )
    if now_seconds != last_access and (
        now_seconds - last_access >= self.expire_after_seconds
    ):
        # Notify the user that the conversation is being restarted.
        await turn_context.send_activity(
            "Welcome back!  Let's start over from the beginning."
        )

        # Clear state.
        await self.conversation_state.clear_state(turn_context)
        await self.conversation_state.save_changes(turn_context, True)

    await super().on_turn(turn_context)

    # Set LastAccessedTime to the current time.
    await self.last_accessed_time_property.set(turn_context, now_seconds)

    # Save any state changes that might have ocurred during the turn.
    await self.conversation_state.save_changes(turn_context)
    await self.user_state.save_changes(turn_context)
```

**app.py**

Por fim, atualize `app.py` para enviar o `EXPIRE_AFTER_SECONDS` parâmetro para `DialogBot` :

```python
BOT = DialogBot(CONFIG.EXPIRE_AFTER_SECONDS, CONVERSATION_STATE, USER_STATE, DIALOG)
```

---

## <a name="storage-expiration"></a>Expiração do armazenamento

O Cosmos DB fornece um recurso TTL (vida útil) que permite excluir itens automaticamente de um contêiner após um determinado período de tempo.  Isso pode ser configurado de dentro do portal do Azure ou durante a criação do contêiner (usando os SDKs de Cosmos DB específicos do idioma).

O SDK do bot Framework não expõe uma configuração de TTL.  No entanto, a inicialização do contêiner pode ser substituída e o SDK do Cosmos DB pode ser usado para configurar o TTL antes da inicialização do armazenamento do bot Framework.

# <a name="c"></a>[C#](#tab/csharp)

Comece com uma cópia nova do exemplo de **solicitação de múltipla ativação** e adicione o `Microsoft.Bot.Builder.Azure` pacote NuGet ao projeto.

**appsettings.json**

Atualize appsettings.jsno para incluir opções de armazenamento de Cosmos DB:

```json
{
  "MicrosoftAppId": "",
  "MicrosoftAppPassword": "",

  "CosmosDbTimeToLive": 30,
  "CosmosDbEndpoint": "<endpoint-for-your-cosmosdb-instance>",
  "CosmosDbAuthKey": "<your-cosmosdb-auth-key>",
  "CosmosDbDatabaseId": "<your-database-id>",
  "CosmosDbUserStateContainerId": "<no-ttl-container-id>",
  "CosmosDbConversationStateContainerId": "<ttl-container-id>"
}
```

Observe os dois ContainerIds, um para `UserState` e um para `ConversationState` .  Isso ocorre porque estamos definindo uma vida útil padrão no `ConversationState` contêiner, mas não em `UserState` .

**CosmosDbStorageInitializerHostedService.cs**

Em seguida, crie uma `CosmosDbStorageInitializerHostedService` classe, que criará o contêiner com a vida útil configurada.

```csharp
// Add required using statements...

public class CosmosDbStorageInitializerHostedService : IHostedService
{
    readonly CosmosDbPartitionedStorageOptions _storageOptions;
    readonly int _cosmosDbTimeToLive;

    public CosmosDbStorageInitializerHostedService(IConfiguration config)
    {
        _storageOptions = new CosmosDbPartitionedStorageOptions()
        {
            CosmosDbEndpoint = config["CosmosDbEndpoint"],
            AuthKey = config["CosmosDbAuthKey"],
            DatabaseId = config["CosmosDbDatabaseId"],
            ContainerId = config["CosmosDbConversationStateContainerId"]
        };

        _cosmosDbTimeToLive = config.GetValue<int>("CosmosDbTimeToLive");
    }

    public async Task StartAsync(CancellationToken cancellationToken)
    {
        using (var client = new CosmosClient(
            _storageOptions.CosmosDbEndpoint,
            _storageOptions.AuthKey,
            _storageOptions.CosmosClientOptions ?? new CosmosClientOptions()))
        {
            // Create the contaier with the provided TTL
            var containerResponse = await client
                .GetDatabase(_storageOptions.DatabaseId)
                .DefineContainer(_storageOptions.ContainerId, "/id")
                .WithDefaultTimeToLive(_cosmosDbTimeToLive)
                .WithIndexingPolicy().WithAutomaticIndexing(false).Attach()
                .CreateIfNotExistsAsync(_storageOptions.ContainerThroughput)
                .ConfigureAwait(false);
        }
    }

    public Task StopAsync(CancellationToken cancellationToken) => Task.CompletedTask;
}
```

**Startup.cs**

Por fim, atualize `Startup.cs` para usar o inicializador de armazenamento e o cosmos DB para o estado:

```csharp
// Existing code omitted...

// commented out MemoryStorage, since we are using CosmosDbPartitionedStorage instead
// services.AddSingleton<IStorage, MemoryStorage>();

// Add the Initializer as a HostedService (so it is called during the app service startup)
services.AddHostedService<CosmosDbStorageInitializerHostedService>();

// Create the storage options for User state
var userStorageOptions = new CosmosDbPartitionedStorageOptions()
{
    CosmosDbEndpoint = Configuration["CosmosDbEndpoint"],
    AuthKey = Configuration["CosmosDbAuthKey"],
    DatabaseId = Configuration["CosmosDbDatabaseId"],
    ContainerId = Configuration["CosmosDbUserStateContainerId"]
};

// Create the User state. (Used in this bot's Dialog implementation.)
services.AddSingleton(new UserState(new CosmosDbPartitionedStorage(userStorageOptions)));

// Create the storage options for Conversation state
var conversationStorageOptions = new CosmosDbPartitionedStorageOptions()
{
    CosmosDbEndpoint = Configuration["CosmosDbEndpoint"],
    AuthKey = Configuration["CosmosDbAuthKey"],
    DatabaseId = Configuration["CosmosDbDatabaseId"],
    ContainerId = Configuration["CosmosDbConversationStateContainerId"]
};

// Create the Conversation state. (Used by the Dialog system itself.)
services.AddSingleton(new ConversationState(new CosmosDbPartitionedStorage(conversationStorageOptions)));

// Existing code omitted...
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Comece com uma cópia nova do exemplo de **solicitação de múltipla ativação** .

**.env**

Update. env para incluir opções de armazenamento de Cosmos DB:

```txt
MicrosoftAppId=
MicrosoftAppPassword=

CosmosDbTimeToLive=30
CosmosDbEndpoint=<endpoint-for-your-cosmosdb-instance>
CosmosDbAuthKey=<your-cosmosdb-auth-key>
CosmosDbDatabaseId=<your-database-id>
CosmosDbUserStateContainerId=<no-ttl-container-id>
CosmosDbConversationStateContainerId=<ttl-container-id>
```

Observe os dois ContainerIds, um para `UserState` e um para `ConversationState` .  Isso ocorre porque estamos definindo uma vida útil padrão no `ConversationState` contêiner, mas não `UserState` .

**project.json**

Em seguida, adicione o `botbuilder-azure` pacote NPM ao project.js.

```json
"dependencies": {
    "botbuilder": "~4.9.2",
    "botbuilder-dialogs": "~4.9.2",
    "botbuilder-azure": "~4.9.2",
    "dotenv": "^8.2.0",
    "path": "^0.12.7",
    "restify": "~8.5.1"
},
```

**index.js**

Adicione as instruções require necessárias para index.js:

```javascript
const { CosmosDbPartitionedStorage } = require('botbuilder-azure');
const { CosmosClient } = require('@azure/cosmos');
```

Substituir `MemoryStorage` `ConversationState` e `UserState` criar com:

```javascript
// const memoryStorage = new MemoryStorage();

// Storage options for Conversation State
const conversationStorageOptions = {
    cosmosDbEndpoint: process.env.CosmosDbEndpoint,
    authKey: process.env.CosmosDbAuthKey,
    databaseId: process.env.CosmosDbDatabaseId,
    containerId: process.env.CosmosDbConversationStateContainerId
};

// Create a cosmosClient, and set defaultTtl (with other properties)
var cosmosClient = new CosmosClient({
    endpoint: conversationStorageOptions.cosmosDbEndpoint,
    key: conversationStorageOptions.authKey,
    ...conversationStorageOptions.cosmosClientOptions,
});

// Create the container with the provided TTL.
Promise.resolve(cosmosClient
    .database(conversationStorageOptions.databaseId)
    .containers.createIfNotExists({
        id: conversationStorageOptions.containerId,
        partitionKey: {
            paths: ['/id']
        },
        defaultTtl: parseInt(process.env.CosmosDbTimeToLive, 10)
    }));

// Storage options for User State
const userStorageOptions = {
    cosmosDbEndpoint: process.env.CosmosDbEndpoint,
    authKey: process.env.CosmosDbAuthKey,
    databaseId: process.env.CosmosDbDatabaseId,
    containerId: process.env.CosmosDbUserStateContainerId
};

// Create state instances.
const conversationState = new ConversationState(new CosmosDbPartitionedStorage(conversationStorageOptions));
const userState = new UserState(new CosmosDbPartitionedStorage(userStorageOptions));
```

Por fim, execute NPM install antes de iniciar o bot.

```cmd
npm install
```

## <a name="python"></a>[Python](#tab/python)

Comece com uma cópia nova do exemplo de **solicitação de múltipla ativação** .

**config.py**

Atualize `config.py` para incluir opções de armazenamento Cosmos DB:

```python
PORT = 3978
APP_ID = os.environ.get("MicrosoftAppId", "")
APP_PASSWORD = os.environ.get("MicrosoftAppPassword", "")

COSMOSDB_TTL = os.environ.get("CosmosDbTimeToLive", 30)
COSMOSDB_ENDPOINT = os.environ.get("CosmosDbEndpoint", "<endpoint-for-your-cosmosdb-instance>")
COSMOSDB_AUTH_KEY = os.environ.get("CosmosDbAuthKey", "<your-cosmosdb-auth-key>")
COSMOSDB_DATABASE_ID = os.environ.get("CosmosDbDatabaseId", "<your-database-id>")
COSMOSDB_USER_STATE_CONTAINER_ID = os.environ.get("CosmosDbUserStateContainerId", "<no-ttl-container-id>")
COSMOSDB_CONVERSATION_STATE_CONTAINER_ID = os.environ.get("CosmosDbConversationStateContainerId", "<ttl-container-id>")
```

Observe os dois ContainerIds, um para `UserState` e um para `ConversationState` .  Isso ocorre porque estamos definindo uma vida útil padrão no `ConversationState` contêiner, mas não `UserState` .

**requirements.txt**

Em seguida, adicione o `botbuilder-azure` pacote a requirements.txt.

```txt
botbuilder-integration-aiohttp>=4.10.0
botbuilder-dialogs>=4.10.0
botbuilder-ai>=4.10.0
botbuilder-azure>=4.10.0
```

Em seguida, execute a instalação do Pip:

```cmd
pip install -r requirements.txt
```

**app.py**

```python
client = cosmos_client.CosmosClient(
    CONFIG.COSMOSDB_ENDPOINT, {"masterKey": CONFIG.COSMOSDB_AUTH_KEY},
)

containers = list(client.QueryContainers("dbs/" + CONFIG.COSMOSDB_DATABASE_ID, {
     "query": "SELECT * FROM r WHERE r.id=@id",
        "parameters": [
            {"name": "@id", "value": CONFIG.COSMOSDB_CONVERSATION_STATE_CONTAINER_ID}
        ],
    }))

if len(containers) < 1:
    new_container = client.CreateContainer(
        "dbs/" + CONFIG.COSMOSDB_DATABASE_ID,
        {
            "defaultTtl": CONFIG.COSMOSDB_TTL,
            "id": CONFIG.COSMOSDB_CONVERSATION_STATE_CONTAINER_ID,
            "partitionKey": {"paths": ["/id"], "kind": "Hash",},
        },
    )
```

---

Cosmos DB agora excluirá automaticamente os registros de estado da conversa após 30 segundos de inatividade.

<!-- 
## Proactive Expiration
add Azure Function with ConversationReference solution 
-->

Para obter mais informações, consulte [Configurar a vida útil no Azure Cosmos DB][cosmos-ttl]

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator][emulator-readme].
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte-se ao bot e envie uma mensagem para ele.
1. Após um dos prompts, aguarde 30 segundos antes de responder.

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[cs-sample]: https://aka.ms/cs-multi-prompts-sample
[js-sample]: https://aka.ms/js-multi-prompts-sample
[python-sample]: https://aka.ms/python-multi-prompts-sample

[cosmos-ttl]: https://docs.microsoft.com/azure/cosmos-db/how-to-time-to-live
[emulator-readme]: https://aka.ms/bot-framework-emulator-readme
