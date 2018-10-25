---
title: Manter os dados de usuário | Microsoft Docs
description: Saiba como salvar dados de estado do usuário para o armazenamento no SDK do Construtor de Bot.
keywords: manter os dados de usuário, armazenamento, dados de conversa
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/19/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 61e86ce9536bc5d77dc7bd411054b2f65bce8dd9
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326553"
---
# <a name="persist-user-data"></a>Manter os dados de usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Quando o bot pede dados entrada aos usuários, é provável que você deseje manter algumas informações no armazenamento de algum formulário. O SDK do Bot Builder permite que você armazene entradas do usuário usando o *armazenamento na memória* ou o armazenamento do banco de dados como o *CosmosDB*. Os tipos de armazenamento local são usados principalmente durante testes ou na criação de protótipos do bot. No entanto, os tipos de armazenamento persistentes, como o armazenamento de banco de dados, são melhores para bots de produção.

Este tópico mostrará como definir o objeto de armazenamento e salvar entradas de usuário no objeto de armazenamento de modo que ele possa ser mantido. Usaremos um diálogo para perguntar ao usuário o nome dele, se ainda não tivermos o nome. Independentemente do tipo de armazenamento que você optar por usar, o processo para vinculá-lo e manter os dados é o mesmo. O código neste tópico usa o `CosmosDB` como o local de armazenamento para manter os dados.

## <a name="prerequisites"></a>Pré-requisitos

Dependendo do ambiente de desenvolvimento que você desejar usar, serão necessários determinados recursos.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

* [Instale o Visual Studio 2015 ou superior](https://www.visualstudio.com/downloads/).
* [Instale o modelo do BotBuilder V4](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

* [Instale o Visual Studio Code](https://www.visualstudio.com/downloads/).
* [Instale o Node.js v8.5 ou superior](https://nodejs.org/en/).
* [Instale o Yeoman](http://yeoman.io/).
* Instale o gerador de modelos do Botbuilder do Node.js v4.

    ```shell
    npm install generator-botbuilder
    ```

---

Este tutorial utiliza os seguintes pacotes.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Começaremos de um modelo básico de EchoBot. Para obter instruções, consulte o [início rápido para .NET](~/dotnet/bot-builder-dotnet-quickstart.md).

Instale esses pacotes adicionais a partir do gerenciador de pacotes do NuGet.

* **Microsoft.Bot.Builder.Azure**
* **Microsoft.Bot.Builder.Dialogs**

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Começaremos de um modelo básico de EchoBot. Para obter instruções, confira o [início rápido para JavaScript](~/javascript/bot-builder-javascript-quickstart.md).

Instale esses pacotes npm adicionais.

```cmd
npm install --save botbuilder-dialogs
npm install --save botbuilder-azure
```

---

Para testar o bot criado neste tutorial, você precisará instalar o [Emulador do BotFramework](https://github.com/Microsoft/BotFramework-Emulator).

## <a name="create-a-cosmosdb-service-and-update-your-application-settings"></a>Criar um serviço do CosmosDB e atualizar as configurações do aplicativo

Para configurar um serviço do CosmosDB e um banco de dados, siga as instruções de [como usar o CosmosDB](bot-builder-howto-v4-storage.md#using-cosmos-db). As etapas estão resumidas aqui:

1. Em uma nova janela do navegador, entre no <a href="http://portal.azure.com/" target="_blank">Portal do Azure</a>.
1. Clique em **Criar um recurso > Bancos de dados > Azure Cosmos DB**.
1. Na página **Nova conta**, forneça um nome exclusivo no campo **ID**. No caso da **API**, escolha **SQL** e forneça as informações de **Assinatura**, **Local** e **Grupo de recursos**.
1. Clique em **Criar**.

Em seguida, adicione uma coleção a esse serviço para usar com este bot.

Registre a ID do banco de dados e a ID da coleção que você usou para adicionar a coleção. Registre também o URI e a chave primária das configurações de chaves da coleção, pois precisaremos deles para conectar nosso bot ao serviço.

### <a name="update-your-application-settings"></a>Atualizar as configurações do aplicativo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Atualize seu arquivo **appsettings.json** para incluir as informações de conexão para o CosmosDB.

```csharp
{
  // Settings for CosmosDB.
  "CosmosDB": {
    "DatabaseID": "<your-database-identifier>",
    "CollectionID": "<your-collection-identifier>",
    "EndpointUri": "<your-CosmosDB-endpoint>",
    "AuthenticationKey": "<your-primary-key>"
  }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Na pasta do projeto, localize o arquivo **.env** e adicione essas entradas com seus dados específicos do Cosmos para ele.

**.env**

```text
DB_SERVICE_ENDPOINT=<your-CosmosDB-endpoint>
AUTH_KEY=<authentication key>
DATABASE=<your-primary-key>
COLLECTION=<your-collection-identifier>
```

Em seguida, no arquivo **index.js** principal do bot, substitua `storage` para usar `CosmosDbStorage` em vez de `MemoryStorage`. Durante o tempo de execução, as variáveis de ambiente serão obtidas e preencherão esses campos.

```javascript
const storage = new CosmosDbStorage({
    serviceEndpoint: process.env.DB_SERVICE_ENDPOINT,
    authKey: process.env.AUTH_KEY, 
    databaseId: process.env.DATABASE,
    collectionId: process.env.COLLECTION
});
```

---

## <a name="create-storage-state-manager-and-state-property-accessor-objects"></a>Criar armazenamento, gerenciador de estado e objetos do acessador de propriedades do estado

Os bots usam o gerenciamento de estado e os objetos de armazenamento para gerenciar e manter o estado. O gerenciador fornece uma camada de abstração que permite acessar propriedades de estado usando um acessador de propriedades de estado, independente do tipo de armazenamento subjacente. Use o gerenciador de estado para gravar dados no armazenamento.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

### <a name="define-a-class-for-your-user-data"></a>Definir uma classe para seus dados de usuário

Renomeie o arquivo **CounterState.cs** como **UserData.cs** e renomeie a classe **CounterState** como **UserData**.

Atualize essa classe para manter os dados que serão coletados.

```csharp
/// <summary>
/// Class for storing persistent user data.
/// </summary>
public class UserData
{
    public string Name { get; set; }
}
```

### <a name="define-a-class-for-your-state-and-state-property-accessor-objects"></a>Definir uma classe para o estado e os objetos do acessador de propriedades do estado

Renomeie o arquivo **EchoBotAccessors.cs** como **BotAccessors.cs** e renomeie a classe **EchoBotAccessors** como **BotAccessors**.

Atualize essa classe para armazenar os objetos de estado e acessadores de propriedades de estado necessários ao bot.

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using System;

public class BotAccessors
{
    public UserState UserState { get; }

    public ConversationState ConversationState { get; }

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }

    public IStatePropertyAccessor<UserData> UserDataAccessor { get; set; }

    public BotAccessors(UserState userState, ConversationState conversationState)
    {
        this.UserState = userState
            ?? throw new ArgumentNullException(nameof(userState));

        this.ConversationState = conversationState
            ?? throw new ArgumentNullException(nameof(conversationState));
    }
}
```

### <a name="update-the-startup-code-for-your-bot"></a>Atualizar o código de inicialização para o bot

No arquivo **Startup.cs**, atualize as instruções “using”.

```csharp
using System;
using System.Linq;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Integration;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Bot.Configuration;
using Microsoft.Bot.Connector.Authentication;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Options;
```

No método `ConfigureServices`, atualize a chamada de adição de bot começando pelo local onde você cria o objeto de armazenamento de backup e, em seguida, registre seu objeto de acessadores de bot.

Precisamos do estado da conversa do objeto `DialogState` para rastrear o estado do diálogo. Estamos registrando singletons para o acessador de propriedades de estado do diálogo e o conjunto de diálogos que nosso bot usará. O bot criará seu próprio acessador de propriedades de estado para o estado do usuário.

O acessador `BotAccessors` é uma maneira eficiente de gerenciar o armazenamento de vários objetos de estado do seu bot.

```cs
public void ConfigureServices(IServiceCollection services)
{
    // Register your bot.
    services.AddBot<UserDataBot>(options =>
    {
        // ...

        // Use persistent storage and create state management objects.
        var cosmosSettings = Configuration.GetSection("CosmosDB");
        IStorage storage = new CosmosDbStorage(
            new CosmosDbStorageOptions
            {
                DatabaseId = cosmosSettings["DatabaseID"],
                CollectionId = cosmosSettings["CollectionID"],
                CosmosDBEndpoint = new Uri(cosmosSettings["EndpointUri"]),
                AuthKey = cosmosSettings["AuthenticationKey"],
            });
        options.State.Add(new ConversationState(storage));
        options.State.Add(new UserState(storage));
    });

    // Register the bot's state and state property accessor objects.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var userState = options.State.OfType<UserState>().FirstOrDefault();
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();

        return new BotAccessors(userState, conversationState)
        {
            UserDataAccessor = userState.CreateProperty<UserData>("UserDataBot.UserData"),
            DialogStateAccessor = conversationState.CreateProperty<DialogState>("UserDataBot.DialogState"),
        };
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

### <a name="update-your-server-code"></a>Atualizar o código do servidor

No arquivo **index.js** do projeto, atualize as seguintes instruções require.

```javascript
// Import required bot services.
const { BotFrameworkAdapter, ConversationState, UserState } = require('botbuilder');
const { CosmosDbStorage } = require('botbuilder-azure');
```

Para este tutorial, usaremos o `UserState` para armazenar dados. É necessário criar um novo objeto `userState` e atualizar esta linha de código para passar um segundo parâmetro para a classe `MainDialog`.

```javascript
// Create conversation state with in-memory storage provider.
const conversationState = new ConversationState(storage);
const userState = new UserState(storage);

// Create the main dialog.
const bot = new MyBot(conversationState, userState);
```

Se encontrarmos um erro geral, limpe o estado do usuário e da conversa.

```javascript
// Catch-all for errors.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${error}`);
    // Send a message to the user
    context.sendActivity(`Oops. Something went wrong!`);
    // Clear out state
    await conversationState.load(context);
    await conversationState.clear(context);
    await userState.load(context);
    await userState.clear(context);
    // Save state changes.
    await conversationState.saveChanges(context);
    await userState.saveChanges(context);
};
```

E atualize o loop do servidor HTTP para chamar nosso objeto de bot.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        // Route to main dialog.
        await bot.onTurn(context);
    });
});
```

### <a name="update-your-bot-logic"></a>Atualizar a lógica do seu bot

Na classe `MyBot`, exija as bibliotecas necessárias para o bot funcionar. Neste tutorial, vamos usar a biblioteca de **Diálogos**.

```javascript
// Required packages for this bot
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, TextPrompt, NumberPrompt } = require('botbuilder-dialogs');

```

Atualize o construtor `MyBot` da classe para aceitar um segundo parâmetro, `userState`. Além disso, atualize o construtor para definir os estados, os diálogos e os prompts necessários para este tutorial. Nesse caso, estamos definindo uma cascata de duas etapas em que a _etapa 1_ pergunta o nome ao usuário e a _etapa 2_ retorna a resposta do usuário. Cabe à lógica principal do bot manter essas informações.

```javascript
constructor(conversationState, userState) {

    // creates a new state accessor property.
    this.conversationState = conversationState;
    this.userState = userState;

    this.dialogState = this.conversationState.createProperty('dialogState');
    this.userDataAccessor = this.userState.createProperty('userData');

    this.dialogs = new DialogSet(this.dialogState);

    // Add prompts
    this.dialogs.add(new TextPrompt('textPrompt'));

    // Add a waterfall dialog to collect and return the user's name.
    this.dialogs.add(new WaterfallDialog('greetings', [
        async function (step) {
            return await step.prompt('textPrompt', "What is your name?");
        },
        async function (step) {
            return await step.endDialog(step.result);
        }
    ]));
}
```

---

No momento de salvar os dados do usuário, você tem algumas opções. O SDK fornece alguns objetos de estado com escopos diferentes que podem ser escolhidos. Aqui, estamos usando o estado da conversa para gerenciar o objeto de estado do diálogo e o estado do usuário para gerenciar os dados do usuário.

Para obter mais informações sobre o armazenamento e o estado em geral, confira [armazenar](bot-builder-howto-v4-storage.md) e [como gerenciar o estado da conversa e do usuário](bot-builder-howto-v4-state.md).

## <a name="create-a-greeting-dialog"></a>Criar um diálogo de saudação

Usaremos um diálogo para coletar o nome do usuário. Para simplificar este cenário, o diálogo retornará o nome do usuário e o bot gerenciará o objeto de dados do usuário e o estado.

Crie uma classe **GreetingsDialog** e inclua o código a seguir.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

/// <summary>Defines a dialog for collecting a user's name.</summary>
public class GreetingsDialog : DialogSet
{
    /// <summary>The ID of the main dialog.</summary>
    public const string MainDialog = "main";

    /// <summary>The ID of the text prompt to use in the dialog.</summary>
    private const string TextPrompt = "textPrompt";

    /// <summary>Creates a new instance of this dialog set.</summary>
    /// <param name="dialogState">The dialog state property accessor to use for dialog state.</param>
    public GreetingsDialog(IStatePropertyAccessor<DialogState> dialogState)
        : base(dialogState)
    {
        // Add the text prompt to the dialog set.
        Add(new TextPrompt(TextPrompt));

        // Define the main dialog and add it to the set.
        Add(new WaterfallDialog(MainDialog, new WaterfallStep[]
        {
            async (stepContext, cancellationToken) =>
            {
                // Ask the user for their name.
                return await stepContext.PromptAsync(TextPrompt, new PromptOptions
                {
                    Prompt = MessageFactory.Text("What is your name?"),
                }, cancellationToken);
            },
            async (stepContext, cancellationToken) =>
            {
                // Assume that they entered their name, and return the value.
                return await stepContext.EndDialogAsync(stepContext.Result, cancellationToken);
            },
        }));
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Confira a seção acima em que o diálogo é criado no construtor do `MainDialog`.

---

Para saber mais sobre diálogos, confira [como solicitar entradas](bot-builder-prompts.md) e [como gerenciar o fluxo de conversas simples](bot-builder-dialog-manage-conversation-flow.md).

## <a name="update-your-bot-to-use-the-dialog-and-user-state"></a>Atualizar seu bot para usar o estado do usuário e o diálogo

Discutiremos a criação de bots e o gerenciamento de entradas do usuário separadamente.

### <a name="add-the-dialog-and-a-user-state-accessor"></a>Adicionar o diálogo e um acessador de estado do usuário

É necessário acompanhar a instância do diálogo e o acessador de propriedades de estado para os dados do usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Adicione o código para inicializar nosso bot.

```cs
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Schema;

/// <summary>Defines the bot for the persisting user data tutorial.</summary>
public class UserDataBot : IBot
{
    /// <summary>The bot's state and state property accessor objects.</summary>
    private BotAccessors Accessors { get; }

    /// <summary>The dialog set that has the dialog to use.</summary>
    private GreetingsDialog GreetingsDialog { get; }

    /// <summary>Create a new instance of the bot.</summary>
    /// <param name="options">The options to use for our app.</param>
    /// <param name="greetingsDialog">An instance of the dialog set.</param>
    public UserDataBot(BotAccessors botAccessors)
    {
        // Retrieve the bot's state and accessors.
        Accessors = botAccessors;

        // Create the greetings dialog.
        GreetingsDialog = new GreetingsDialog(Accessors.DialogStateAccessor);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Confira a seção acima em que os acessadores de estado são definidos no construtor do `MainDialog`.

---

### <a name="update-the-turn-handler"></a>Atualizar o manipulador de turnos

O manipulador de turnos saudará o usuário quando ele entrar pela primeira vez na conversa e responderá sempre que ele enviar uma mensagem ao bot. Se em algum momento o bot não souber o nome do usuário, ele iniciará o diálogo de saudação no qual solicitará o nome. Quando o diálogo for concluído, salvaremos seu nome no estado do usuário usando um objeto de estado gerado pelo nosso acessador de propriedades do estado. Quando o turno terminar, o acessador e o gerenciador de estado gravarão as alterações no objeto para armazenamento.

Também adicionaremos suporte para a atividade _excluir dados de usuário_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Atualizar o método `OnTurnAsync` do bot.

```cs
/// <summary>Handles incoming activities to the bot.</summary>
/// <param name="turnContext">The context object for the current turn.</param>
/// <param name="cancellationToken">A cancellation token that can be used by other objects
/// or threads to receive notice of cancellation.</param>
/// <returns>A task that represents the work queued to execute.</returns>
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    // Retrieve user data from state.
    UserData userData = await Accessors.UserDataAccessor.GetAsync(turnContext, () => new UserData());

    // Establish context for our dialog from the turn context.
    DialogContext dc = await GreetingsDialog.CreateContextAsync(turnContext);

    // Handle conversation update, message, and delete user data activities.
    switch (turnContext.Activity.Type)
    {
        case ActivityTypes.ConversationUpdate:

            // Greet any user that is added to the conversation.
            IConversationUpdateActivity activity = turnContext.Activity.AsConversationUpdateActivity();
            if (activity.MembersAdded.Any(member => member.Id != activity.Recipient.Id))
            {
                if (userData.Name is null)
                {
                    // If we don't already have their name, start a dialog to collect it.
                    await turnContext.SendActivityAsync("Welcome to the User Data bot.");
                    await dc.BeginDialogAsync(GreetingsDialog.MainDialog);
                }
                else
                {
                    // Otherwise, greet them by name.
                    await turnContext.SendActivityAsync($"Hi {userData.Name}! Welcome back to the User Data bot.");
                }
            }

            break;

        case ActivityTypes.Message:

            // If there's a dialog running, continue it.
            if (dc.ActiveDialog != null)
            {
                var dialogTurnResult = await dc.ContinueDialogAsync();
                if (dialogTurnResult.Status == DialogTurnStatus.Complete
                    && dialogTurnResult.Result is string name
                    && !string.IsNullOrWhiteSpace(name))
                {
                    // If it completes successfully and returns a valid name, save the name and greet the user.
                    userData.Name = name;
                    await turnContext.SendActivityAsync($"Pleased to meet you {userData.Name}.");
                }
            }
            else if (userData.Name is null)
            {
                // Else, if we don't have the user's name yet, ask for it.
                await dc.BeginDialogAsync(GreetingsDialog.MainDialog);
            }
            else
            {
                // Else, echo the user's message text.
                await turnContext.SendActivityAsync($"{userData.Name} said, '{turnContext.Activity.Text}'.");
            }

            break;

        case ActivityTypes.DeleteUserData:

            // Delete the user's data.
            userData.Name = null;
            await turnContext.SendActivityAsync("I have deleted your user data.");

            break;
    }

    // Update the user data in the turn's state cache.
    await Accessors.UserDataAccessor.SetAsync(turnContext, userData, cancellationToken);

    // Persist any changes to storage.
    await Accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
    await Accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atualizar o manipulador `onTurn` do bot.

```javascript
async onTurn(turnContext) {
    const dc = await this.dialogs.createContext(turnContext); // Create dialog context
    const userData = await this.userDataAccessor.get(turnContext, {});

    switch (turnContext.activity.type) {
        case ActivityTypes.ConversationUpdate:
            if (turnContext.activity.membersAdded[0].name !== 'Bot') {
                if (userData.name) {
                    await turnContext.sendActivity(`Hi ${userData.name}! Welcome back to the User Data bot.`);
                }
                else {
                    // send a "this is what the bot does" message
                    await turnContext.sendActivity('Welcome to the User Data bot.');
                    await dc.beginDialog('greetings');
                }
            }
            break;
        case ActivityTypes.Message:
            // If there is an active dialog running, continue it
            if (dc.activeDialog) {
                var turnResult = await dc.continueDialog();
                if (turnResult.status == "complete" && turnResult.result) {
                    // If it completes successfully and returns a value, save the name and greet the user.
                    userData.name = turnResult.result;
                    await this.userDataAccessor.set(turnContext, userData);
                    await turnContext.sendActivity(`Pleased to meet you ${userData.name}.`);
                }
            }
            // Else, if we don't have the user's name yet, ask for it.
            else if (!userData.name) {
                await dc.beginDialog('greetings');
            }
            // Else, echo the user's message text.
            else {
                await turnContext.sendActivity(`${userData.name} said, ${turnContext.activity.text}.`);
            }
            break;
        case ActivityTypes.DeleteUserData:
            // Delete the user's data.
            // Note: You can use the Emulator to send this activity.
            userData.name = null;
            await this.userDataAccessor.set(turnContext, userData);
            await turnContext.sendActivity("I have deleted your user data.");
            break;
    }

    // Save changes to the conversation and user states.
    await this.conversationState.saveChanges(turnContext);
    await this.userState.saveChanges(turnContext);
}
```

---

## <a name="start-your-bot-in-visual-studio"></a>Iniciar seu bot no Visual Studio

Compile e execute seu aplicativo.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador.
2. Escolha o arquivo .bot localizado no diretório em que você criou a solução do Visual Studio.

## <a name="interact-with-your-bot"></a>Interagir com o bot

Envie uma mensagem para seu bot e o bot responderá com uma mensagem.
![Emulador em execução](../media/emulator-v4/emulator-running.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar conversa e estado do usuário](bot-builder-howto-v4-state.md)
