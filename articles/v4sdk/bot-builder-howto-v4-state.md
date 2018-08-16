---
title: Salvar estado usando conversa e propriedades do usuário | Microsoft Docs
description: Saiba como salvar e recuperar dados com V4 do SDK do Construtor de Bot para .NET.
keywords: estado da conversa, estado do usuário, middleware de estado, fluxo da conversa, armazenamento de arquivos, armazenamento de tabelas do azure
author: ivorb
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/03/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 16df371b1cabb4b3eb47d1f491a5d45e26627d38
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352845"
---
# <a name="save-state-using-conversation-and-user-properties"></a>Salvar estado usando conversa e propriedades do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Para o bot salvar o estado do usuário e a conversa, primeiro inicialize o middleware do gerenciador de estado e, em seguida, use as propriedades de estado de conversa e usuário.
Para obter mais informações sobre como esse estado é usado, consulte [Estado e armazenamento](./bot-builder-storage-concept.md).

## <a name="initialize-state-manager-middleware"></a>Inicializar o middleware do gerenciador de estado

No SDK, você precisa inicializar o adaptador de bot para usar o middleware do gerenciador de estado antes de poder usar os repositórios de propriedades de conversa ou usuário. _Estado da conversa_ é usado para propriedades de conversa, e _estado de usuário_ é usado para as propriedades do usuário. (As propriedades de estado do usuário podem ser acessadas em várias conversas.) O middleware do gerenciador de estado fornece uma abstração que permite acessar propriedades usando um repositório de objeto ou chave-valor simples, independente do tipo de armazenamento subjacente. O gerenciador de estado é responsável por gravar dados no armazenamento e gerenciar simultaneidade, caso o tipo de armazenamento subjacente esteja na memória, armazenamento de arquivos ou Armazenamento de Tabelas do Azure.


# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Para ver como `ConversationState` é inicializado, consulte `Startup.cs` na amostra Microsoft.Bot.Samples.EchoBot AspNetCore.

```csharp
services.AddBot<EchoBot>(options =>
{
    options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

    IStorage dataStore = new MemoryStorage();
    options.Middleware.Add(new ConversationState<EchoState>(dataStore));
});
```

Na linha `options.Middleware.Add(new ConversationState<EchoState>(dataStore));`, `ConversationState` é o objeto do gerenciador de estado de conversa, que é adicionado ao bot como middleware. O parâmetro de tipo `EchoState` é o tipo que representa como você deseja que as informações de estado da conversa a ser armazenado. Um bot pode usar qualquer tipo de classe para dados de estado do usuário ou de conversa.

A implementação de `EchoState` está em `EchoBot.cs`:
```csharp
public class EchoState
{
    public int TurnNumber { get; set; }
}
``` 

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Definir o provedor de armazenamento e atribuí-lo ao gerenciador de estado que você deseja usar.
Você pode usar o mesmo provedor de armazenamento para middleware de gerenciamento `ConversationState` e `UserState`.
Em seguida, você usaria a biblioteca `BotStateSet` para conectar-se à camada de middleware que irá gerenciar a persistência de dados para você.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState, BotStateSet } = require('botbuilder');
const restify = require('restify');

// Create adapter
const adapter = new BotFrameworkAdapter({ 
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

// Add conversation state middleware.
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// Alternatively, use both conversation and user state middleware.
// const storage = new MemoryStorage;
// const conversationState = new ConverstationState(storage);
// const userState = new UserState(storage);
// adapter.use(new BotStateSet(conversationState, userState));
```    
---

> [!NOTE] 
> Armazenamento de dados na memória destina-se somente para teste. Esse armazenamento é volátil e temporário. Os dados serão limpos sempre que o bot é reiniciado. Ver [armazenamento de arquivos](#file-storage) e [armazenamento de tabelas do Azure](#azure-table-storage) mais adiante neste artigo para configurar outros meios de armazenamento subjacente para o estado da conversa e o estado do usuário. 

### <a name="configuring-state-manager-middleware"></a>Configurar o middleware do gerenciador de estado

Ao inicializar o middleware de estado, um parâmetro opcional de _configurações de estado_ permite que você altere o comportamento padrão de como as propriedades são salvas. As configurações padrão são:

* Persistir propriedades além do tempo de vida do contexto de turno.
* Se mais de uma instância do bot grava uma propriedade, permitir que a última instância do bot substitua o anterior.

## <a name="use-conversation-and-user-state-properties"></a>Usar as propriedades de conversa e estado do usuário 
<!-- middleware and message context properties -->

Depois que o middleware do gerenciador de estado tiver sido configurado, você pode obter as propriedades de estado de conversa e estado de usuário do objeto de contexto.
<!-- Changes are written to storage before the `SendActivity()` pipeline completes. -->

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Você pode ver como isso funciona usando a amostra `Microsoft.Bot.Samples.EchoBot` no SDK do Construtor de Bot. 

No manipulador `OnTurn`, `context.GetConversationState` obtém estado de conversa para acessar os dados que você definiu, e você pode modificar o estado por meio das propriedades (nesse caso, incrementando `TurnNumber`).

```csharp
public async Task OnTurn(ITurnContext context)
{
    // This bot is only handling Messages
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context
        var state = context.GetConversationState<EchoState>();

        // Bump the turn count. 
        state.TurnCount++;

        // Echo back to the user whatever they typed.
        await context.SendActivity($"Turn {state.TurnCount}: You sent '{context.Activity.Text}'");
    }
}
```   

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Você pode ver como isso funciona usando o EchoBot gerado da amostra do gerador Yeoman.

Este exemplo de código mostra como você pode armazenar um contador de turno para o estado da conversa.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({ 
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;
            await context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```
---

## <a name="using-conversation-state-to-direct-conversation-flow"></a>Usando o estado de conversa para direcionar o fluxo da conversa

Ao criar um fluxo da conversa, é útil definir um sinalizador de estado para direcionar o fluxo da conversa. O sinalizador pode ser um simples tipo **Boolean** ou um tipo que inclui o nome do tópico atual. O sinalizador pode ajudar a rastrear onde você está em uma conversa. Por exemplo, um sinalizador de tipo **Boolean** pode lhe dizer se você está em uma conversa ou não. Enquanto uma propriedade de nome do tópico pode informar a você a conversa na qual você está no momento.

O exemplo a seguir usa uma propriedade booliana _solicitaram nome_ para sinalizar quando o bot solicitou o nome para seu usuário. Quando a seguinte mensagem é recebida, o bot verifica a propriedade. Se a propriedade é definida como `true`, o bot sabe que solicitaram o nome do usuário e interpreta a mensagem de entrada como um nome para salvar como uma propriedade de usuário.


# <a name="ctabcsharp"></a>[C#](#tab/csharp)
```csharp
public class ConversationInfo
{
    public bool haveAskedNameFlag { get; set; }
    public bool haveAskedNumberFlag { get; set; }
}

public class UserInfo
{
    public string name { get; set; }
    public string telephoneNumber { get; set; }
    public bool done { get; set; }
}

public async Task OnTurn(ITurnContext context)
{
    // Get state objects. Default objects are created if they don't already exist.
    var convo = ConversationState<ConversationInfo>.Get(context);
    var user = UserState<UserInfo>.Get(context);

    if (context.Activity.Type is ActivityTypes.Message)
    {
        if (string.IsNullOrEmpty(user.name) && !convo.haveAskedNameFlag)
        {
            // Ask for the name.
            await context.SendActivity("Hello. What's your name?");

            // Set flag to show we've asked for the name. We save this out so the
            // context object for the next turn of the conversation can check haveAskedName
            convo.haveAskedNameFlag = true;
        }
        else if (!convo.haveAskedNumberFlag)
        {
            // Save the name.
            var name = context.Activity.AsMessageActivity().Text;
            user.name = name;
            convo.haveAskedNameFlag = false; // Reset flag

            // Ask for the phone number. You might want a flag to track this, too.
            await context.SendActivity($"Hello, {name}. What's your telephone number?");
            convo.haveAskedNumberFlag = true;
        }
        else if (convo.haveAskedNumberFlag)
        {
            // save the telephone number
            var telephonenumber = context.Activity.AsMessageActivity().Text;

            user.telephoneNumber = telephonenumber;
            convo.haveAskedNumberFlag = false; // Reset flag
            await context.SendActivity($"Got it. I'll call you later.");
        }
    }
}
```

Para configurar o estado do usuário para que ela possa ser retornada por `UserState<UserInfo>.Get(context)`, adicione o middleware de estado do usuário. Por exemplo, no `Startup.cs` do ASP .NET Core EchoBot, alterar o código em ConfigureServices.cs:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        
        IStorage dataStore = new MemoryStorage();
        options.Middleware.Add(new ConversationState<ConversationInfo>(dataStore));
        options.Middleware.Add(new UserState<UserInfo>(dataStore));
    });
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

**app.js**

```js
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState, BotStateSet } = require('botbuilder');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter (it's ok for MICROSOFT_APP_ID and MICROSOFT_APP_PASSWORD to be blank for now)  
const adapter = new BotFrameworkAdapter({ 
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

// Storage
const storage = new MemoryStorage();
const conversationState = new ConversationState(storage);
const userState  = new UserState(storage);
adapter.use(new BotStateSet(conversationState, userState));

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        const convo = conversationState.get(context);
        const user = userState.get(context);

        if (isMessage) {
            if(!user.name && !convo.haveAskedNameFlag){
                // Ask for the name.
                await context.sendActivity("What is your name?")
                // Set flag to show we've asked for the name. We save this out so the
                // context object for the next turn of the conversation can check haveAskedNameFlag
                convo.haveAskedNameFlag = true;
            } else if(convo.haveAskedNameFlag){
                // Save the name.
                user.name = context.activity.text;
                convo.haveAskedNameFlag = false; // Reset flag

                await context.sendActivity(`Hello, ${user.name}. What's your telephone number?`);
                convo.haveAskedNumberFlag = true; // Set flag
            } else if(convo.haveAskedNumberFlag){
                // save the phone number
                user.telephonenumber = context.activity.text;
                convo.haveAskedNumberFlag = false; // Reset flag
                await context.sendActivity(`Got it. I'll call you later.`);
            }
        }

        // ...
    });
});

```

---

Uma alternativa é usar o modelo _cascata_ de uma caixa de diálogo. A caixa de diálogo controla o estado da conversa para você, você não precisa criar sinalizadores para controlar seu estado. Para obter mais informações, consulte [Gerenciar uma conversa com caixas de diálogo](bot-builder-dialog-manage-conversation-flow.md).

## <a name="file-storage"></a>Armazenamento de arquivos

O provedor de armazenamento de memória usa o armazenamento na memória que é descartado quando o bot é reiniciado. É bom apenas para fins de teste. Se você deseja manter os dados, mas não quiser conectar seu bot até um banco de dados, você pode usar o provedor de armazenamento de arquivos. Enquanto esse provedor também está destinado para fins de teste, ele mantém os dados de estado em um arquivo para que você possa inspecioná-lo. Os dados são gravados para o arquivo usando o formato JSON.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Vá para `Startup.cs` no exemplo Microsoft.Bot.Samples.EchoBot-AspNetCore de e edite o código no método `ConfigureServices`.
```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        // Using file storage instead of in-memory storage.
        IStorage dataStore = new FileStorage(System.IO.Path.GetTempPath());
        options.Middleware.Add(new ConversationState<EchoState>(dataStore));
    });
}
``` 

Execute o código e permita que o eco echobot faça sua entrada algumas vezes.

Em seguida, vá para o diretório especificado por `System.IO.Path.GetTempPath()`. Você deve ver um arquivo cujo nome começa com "conversa". Abra-o e observe o JSON. O arquivo contém algo semelhante ao seguinte:
```json
{
  "$type": "Microsoft.Bot.Samples.Echo.EchoState, Microsoft.Bot.Samples.EchoBot",
  "TurnNumber": "3",
  "eTag": "ecfe2a23566b4b52b2fe697cffc59385"
}
```

O `$type` especifica o tipo da estrutura de dados que você está usando em seu bot para armazenar o estado da conversa. O campo `TurnNumber` corresponde da propriedade `TurnNumber` na classe `EchoState`. O campo `eTag` é herdado de `IStoreItem` e é um valor exclusivo que automaticamente é atualizado sempre que seu bot atualiza o estado da conversa.  O campo de eTag permite que seu bot habilite a simultaneidade otimista.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Para usar `FileStorage`, atualize seu exemplo de bot eco descrito na seção: [Usar propriedades de estado de usuário e a conversa](#use-conversation-and-user-state-properties) anterior. Certifique-se de que `storage` é definido como `FileStorage` em vez de `MemoryStorage` e exige `FileStorage` do construtor de bot. Essas são as únicas alterações necessárias. 

```javascript
// Storage
const storage = new FileStorage("c:/temp");
const conversationState = new ConversationState(storage);
const userState  = new UserState(storage);
adapter.use(new BotStateSet(conversationState, userState));
```

O provedor `FileStorage` usa um "caminho" como um parâmetro. Especificar um caminho permite localizar facilmente o arquivo com as informações persistentes do seu bot. Cada *conversa* terá um novo arquivo criado para ele. Assim, no *caminho*, você pode encontrar vários nomes de arquivo começando com `conversation!`. Você pode classificar por data para localizar mais facilmente a conversa mais recente. Por outro lado, você encontrará apenas um arquivo para o estado *usuário*. O nome do arquivo será iniciado com `user!`. A qualquer momento o estado de qualquer uma dessas alterações de objeto, o Gerenciador de estado atualizará o arquivo para refletir o que mudou.

Execute o bot e envie-o em algumas mensagens. Em seguida, localize o arquivo de armazenamento e abra-o. Aqui está como o conteúdo JSON deve aparecer para o bot de eco que controla o contador de turno.

```json
{
  "turnNumber": "3",
  "eTag": "322"
}
```
---

## <a name="azure-table-storage"></a>Armazenamento de tabelas do Azure

Você também pode usar o armazenamento de tabelas do Azure como sua mídia de armazenamento.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No exemplo de AspNetCore Microsoft.Bot.Samples.EchoBot, adicione uma referência para o pacote do NuGet `Microsoft.Bot.Builder.Azure`.

Em seguida, vá para `Startup.cs`, adicione uma instrução `using Microsoft.Bot.Builder.Azure;` e edite o código no método `ConfigureServices`.
```csharp
services.AddBot<EchoBot>(options =>
{
    options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
    // The parameters are the connection string and table name.
    // "UseDevelopmentStorage=true" is the connection string to use if you are using the Azure Storage Emulator.
    // Replace it with your own connection string if you're not using the emulator
    options.Middleware.Add(new ConversationState<EchoState>(new AzureTableStorage("UseDevelopmentStorage=true","conversationstatetable")));
    // you could also specify the cloud storage account instead of the connection string
    /* options.Middleware.Add(new ConversationState<EchoState>(
        new AzureTableStorage(WindowsAzure.Storage.CloudStorageAccount.DevelopmentStorageAccount, "conversationstatetable"))); */
    options.EnableProactiveMessages = true;
});
```
`UseDevelopmentStorage=true` é a cadeia de conexão que você pode usar com o [Azure Storage Emulator][AzureStorageEmulator]. Substitua-o por sua própria cadeia de conexão se você não estiver usando o emulador.

Se a tabela com o nome que você especificar no construtor para `AzureTableStorage` não existir, ela será criada.

<!-- 
TODO: step-by-step inspection of the stored table
-->

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

No `app.js` da amostra echobot, você pode criar ConversationState usando `AzureTableStorage`. 

```bash
npm install --save botbuilder-azure@preview
```

```javascript
const { BotFrameworkAdapter, FileStorage, MemoryStorage, ConversationState } = require('botbuilder');
const { TableStorage } = require('botbuilder-azure');

// ...

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

// Add conversation state middleware
// The parameters are the connection string and table name.
// "UseDevelopmentStorage=true" is the connection string to use if you are using the Azure Storage Emulator.
// Replace it with your own connection string if you're not using the emulator
var azureStorage = new TableStorage({ tableName: "TestAzureTable1", storageAccountOrConnectionString: "UseDevelopmentStorage=true"})

// You can alternatively use your account name and table name
// var azureStorage = new TableStorage({tableName: "TestAzureTable2", storageAccessKey: "V3ah0go4DLkMtQKUPC6EbgFeXnE6GeA+veCwDNFNcdE6rqSVE/EQO/kjfemJaitPwtAkmR9lMKLtcvgPhzuxZg==", storageAccountOrConnectionString: "storageaccount"});

const conversationState = new ConversationState(azureStorage);
adapter.use(conversationState);

```

`UseDevelopmentStorage=true` é a cadeia de conexão que você pode usar com o [Azure Storage Emulator][AzureStorageEmulator].
Se a tabela com o nome que você especificar no construtor para `AzureTableStorage` não existir, ela será criada.

---

Para examinar os dados de estado de conversa que são salvos, execute o exemplo e, em seguida, abra a tabela usando o [Azure Storage explorer][AzureStorageExplorer].

![dados de estado de conversa echobot no armazenamento do Azure Storage Explorer](media/how-to-state/echostate-azure-storage-explorer.png)


A **chave de partição** é uma chave gerada exclusivamente para a conversa atual. Se você reiniciar o bot ou iniciar uma nova conversa, a nova conversa receberá sua própria linha com sua própria chave de partição. A estrutura de dados `EchoState` é serializada para JSON e salva na coluna **Json** da tabela do Azure. 

```json
{
    "$type":"Microsoft.Bot.Samples.Echo.AspNetCore.EchoState, Microsoft.Bot.Samples.EchoBot-AspNetCore",
    "TurnNumber":2,
    "LastMessage":"second message",
    "eTag":"*"
}
```

Os campos `$type` e `eTag` são adicionados pelo SDK do Construtor de Bot. Para obter mais informações sobre as eTags, consulte [Gerenciando a simultaneidade otimista](bot-builder-howto-v4-storage.md#manage-concurrency-using-etags)


## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como usar o estado para ajudá-lo a ler e gravar dados de bot para o armazenamento, vamos dar uma olhada em como você pode ler e gravar diretamente no armazenamento.

> [!div class="nextstepaction"]
> [Como gravar diretamente no armazenamento](bot-builder-howto-v4-storage.md).

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre armazenamento, consulte [armazenamento no SDK do Construtor de Bot](bot-builder-storage-concept.md)

<!-- Links -->
[AzureStorageEmulator]: https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator
[AzureStorageExplorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
