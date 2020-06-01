---
title: Criar um bot usando diálogos adaptáveis – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa com diálogos adaptáveis no SDK do Bot Framework.
keywords: conversation flow, repeat, loop, menu, dialogs, prompts, adaptive, language generation
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/08/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 995660d69d7c8a76d9256b065ea63955cd237cf3
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566116"
---
# <a name="create-a-bot-using-adaptive-dialogs"></a>Criar um bot usando diálogos adaptáveis  

[!INCLUDE[applies-to](../includes/applies-to.md)]

Este artigo mostra como usar recursos de **Diálogo adaptável** e **Geração de Linguagem** para obter a mesma funcionalidade obtida com o modelo em cascata.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot][concept-basics], [gerenciamento de estado][concept-state] e [biblioteca de diálogos][concept-dialogs].
- Uma cópia da amostra de **solicitação de vários turnos** na versão prévia em [**C#** ][cs-sample] ou [**JavaScript**][js-sample].

### <a name="preliminary-steps-to-add-an-adaptive-dialog-to-a-bot"></a>Etapas preliminares para adicionar um diálogo adaptável a um bot

Você precisará seguir as etapas descritas abaixo para adicionar um diálogo adaptável a um bot.

1. Atualize todos os pacotes NuGet do Bot Builder para a versão 4.9.x.
1. Adicione o pacote `Microsoft.Bot.Builder.Dialogs.Adaptive` ao seu projeto de bot.
1. Atualize o adaptador de bot para adicionar o armazenamento e os objetos de estado de conversa e usuário a cada contexto de turno.
1. Use um gerenciador de diálogos no código do bot para iniciar ou continuar o diálogo raiz em cada turno.

## <a name="about-the-sample"></a>Sobre o exemplo

Esta amostra usa um diálogo adaptável, algumas solicitações e um diálogo de componente para criar uma interação simples que faz uma série de perguntas ao usuário. As perguntas são criadas por meio de modelos LG:

- Para o C#, definido em [RootDialog.lg](https://github.com/microsoft/BotBuilder-Samples/blob/vishwac/r9/js/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt/Dialogs/RootDialog.lg)
- Para JavaScript, definido em [userProfileDialog.lg](https://github.com/microsoft/BotBuilder-Samples/blob/vishwac/r9/js/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/dialogs/userProfileDialog.lg) (JavaScript).

O código usa um diálogo para percorrer estas etapas:

> [!div class="mx-tdCol2BreakAll"]
> | Etapas        | Modelo LG  |
> |:-------------|:-------------|
> | Perguntar ao usuário qual é seu modo de transporte | `ModeOfTransportPrompt` |
> | Perguntar o nome do usuário | `AskForName` |
> | Perguntar se o usuário deseja fornecer a idade | `AgeConfirmPrompt` |
> | Se a resposta for Sim, perguntar a idade dele | Solicitação de `AskForAge` com validação para aceitar somente idades maiores que 0 e menores que 150 |
> | Perguntar se as informações coletadas estão “OK” | Solicitação de `ConfirmPrompt` |

Finalmente, se ele responder sim, exibir as informações coletadas; caso contrário, dizer ao usuário que as informações dele não serão mantidas.

## <a name="create-the-main-dialog"></a>Criar diálogo principal

# <a name="c"></a>[C#](#tab/csharp)

Para usar os diálogos, instale o pacote NuGet **Microsoft.Bot.Builder.Dialogs.Adaptive**.

O bot interage com o usuário por meio do `RootDialog`. Quando o `RootDialog` do bot é criado, o `AdaptiveDialog` é definido como o diálogo principal. Em seguida, o bot usa o `DialogManager.OnTurnAync` para executar o diálogo.

![Diálogo raiz](media/bot-builder-root-dialog-adaptive.png)

**Dialogs/RootDialog.cs**

O código começa criando uma instância da classe `RootDialog` que, por sua vez, cria uma instância do `AdaptiveDialog`. Neste momento, as `WelcomeUserSteps` e as `OnBeginDialogSteps` a seguir são adicionadas ao diálogo.
O diálogo criado é adicionado ao `DialogSet`, e o nome é salvo no estado do diálogo. Por fim, o nome do diálogo inicial a ser executado é atribuído à `InitialDialogId`. Observe a definição de `paths` referenciando o arquivo `RootDialog.lg` que contém os modelos LG usados na criação do diálogo adaptável.

```csharp
public RootDialog()
    : base(nameof(RootDialog))
{
    string[] paths = { ".", "Dialogs", "RootDialog.LG" };
    string fullPath = Path.Combine(paths);
    // Create instance of adaptive dialog.
    var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
    {
        // These steps are executed when this Adaptive Dialog begins
        Triggers = new List<OnCondition>()
        {
            // Add a rule to welcome user
            new OnConversationUpdateActivity()
            {
                Actions = WelcomeUserSteps()
            },

            // Respond to user on message activity
            new OnUnknownIntent()
            {
                Actions = OnBeginDialogSteps()
            }
        },
        Generator = new TemplateEngineLanguageGenerator(Templates.ParseFile(fullPath))
    };

    // Add named dialogs to the DialogSet. These names are saved in the dialog state.
    AddDialog(rootDialog);

    // The initial child Dialog to run.
    InitialDialogId = nameof(AdaptiveDialog);
}
```

<!--
[!code-csharp[RootDialog snippet](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=18-49&highlight=6-25)]
-->

O diálogo raiz é um diálogo de componente:

```csharp
public class RootDialog : ComponentDialog
```

<!--
[!code-csharp[RootDialog snippet](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=16&highlight=1)]
-->

Observe também:

- O gerador de modelo LG é adicionado ao diálogo adaptável para habilitar o uso dos **modelos LG**.
- Os dois gatilhos são adicionados, com as ações sendo fornecidas pelos dois métodos auxiliares.

Em `WelcomeUserSteps`, o método fornece as ações a serem executadas quando o gatilho for acionado. As ações `Foreach` iteram na lista `membersAdded` para saudar o usuário adicionado à conversa.

> [!NOTE]
> No contexto de diálogos adaptáveis e gatilhos, todos os diálogos são ações válidas e os tipos de ação (`Foreach`, `IfCondition`, `SendActivity`) são todos diálogos.
> Alguns canais enviam dois eventos de atualização de conversa: um para o bot adicionado à conversa e outro para o usuário.
> O código filtra os casos em que o próprio bot é o destinatário da mensagem. Para obter mais informações, confira [Atividades categorizadas por canal](https://docs.microsoft.com/azure/bot-service/bot-service-channels-reference?view=azure-bot-service-4.0#welcome).

```csharp
private static List<Dialog> WelcomeUserSteps()
{
    return new List<Dialog>()
    {
        // Iterate through membersAdded list and greet user added to the conversation.
        new Foreach()
        {
            ItemsProperty = "turn.activity.membersAdded",
            Actions = new List<Dialog>()
            {
                // Note: Some channels send two conversation update events - one for the Bot added to the conversation and another for user.
                // Filter cases where the bot itself is the recipient of the message.
                new IfCondition()
                {
                    Condition = "$foreach.value.name != turn.activity.recipient.name",
                    Actions = new List<Dialog>()
                    {
                        new SendActivity("Hello, I'm the multi-turn prompt bot. Please send a message to get started!")
                    }
                }
            }
        }
    };

}
```

<!--
[!code-csharp[RootDialog snippet](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=51-75&highlight=13-20)]
-->

As `OnBeginDialogSteps` implementam as **etapas** usadas pelo diálogo. Elas definem as solicitações usando os modelos LG do arquivo `RootDialog.lg`. O código abaixo mostra como a solicitação `Name` é criada.

A ação `IfCondition` usa uma expressão adaptável para solicitar a idade do usuário ou enviar uma mensagem de reconhecimento, dependendo da resposta à pergunta anterior. Novamente, ela usa modelos LG para formatar as solicitações e as mensagens.

```csharp
private static List<Dialog> OnBeginDialogSteps()
{
    return new List<Dialog>()
    {
        // Ask for user's age and set it in user.userProfile scope.
        new TextInput()
        {
            Prompt = new ActivityTemplate("${ModeOfTransportPrompt()}"),
            // Set the output of the text input to this property in memory.
            Property = "user.userProfile.Transport"
        },
        new TextInput()
        {
            Prompt = new ActivityTemplate("${AskForName()}"),
            Property = "user.userProfile.Name"
        },
        // SendActivity supports full language generation resolution.
        // See here to learn more about language generation
        // https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation
        new SendActivity("${AckName()}"),
        new ConfirmInput()
        {
            Prompt = new ActivityTemplate("${AgeConfirmPrompt()}"),
            Property = "turn.ageConfirmation"
        },
        new IfCondition()
        {
            // All conditions are expressed using the common expression language.
            // See https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/common-expression-language to learn more
            Condition = "turn.ageConfirmation == true",
            Actions = new List<Dialog>()
            {
                 new NumberInput()
                 {
                     Prompt = new ActivityTemplate("${AskForAge()}"),
                     Property = "user.userProfile.Age",
                     // Add validations
                     Validations = new List<BoolExpression>()
                     {
                         // Age must be greater than or equal 1
                         "int(this.value) >= 1",
                         // Age must be less than 150
                         "int(this.value) < 150"
                     },
                     InvalidPrompt = new ActivityTemplate("${AskForAge.invalid()}"),
                     UnrecognizedPrompt = new ActivityTemplate("${AskForAge.unRecognized()}")
                 },
                 new SendActivity("${UserAgeReadBack()}")
            },
            ElseActions = new List<Dialog>()
            {
                new SendActivity("${NoName()}")
            }
        },
        new ConfirmInput()
        {
            Prompt = new ActivityTemplate("${ConfirmPrompt()}"),
            Property = "turn.finalConfirmation"
        },
        // Use LG template to come back with the final read out.
        // This LG template is a great example of what logic can be wrapped up in LG sub-system.
        new SendActivity("${FinalUserProfileReadOut()}"), // examines turn.finalConfirmation
        new EndDialog()
    };
}

```

<!--
[!code-csharp[RootDialog snippet](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=77-141&highlight=12-16,31-58)]
-->

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar os diálogos, o projeto precisa instalar o pacote npm **botbuilder-dialogs-adaptive**.
A amostra descreve como usar recursos de **Diálogo adaptável** e **Geração de Linguagem** para obter a mesma funcionalidade do modelo em cascata.

Na inicialização, o `UserProfileDialog` do diálogo principal é inicializado. O bot o utiliza para interagir com o usuário.

**dialogs/userProfileDialog.js**

O código começa criando uma instância da classe `UserProfileDialog ` que, por sua vez, cria uma instância do diálogo raiz do `AdaptiveDialog`. Neste momento, as etapas do diálogo são criadas com os modelos de **Geração de Linguagem**.

As `OnBeginDialog` implementam as **etapas** usadas pelo diálogo. Elas definem as solicitações usando os modelos LG do arquivo `userProfileDialog.lg`.

```javascript
constructor() {
    super('userProfileDialog');
    const lgFile = Templates.parseFile(path.join(__dirname, 'userProfileDialog.lg'));
    const userProfileAdaptiveDialog = new AdaptiveDialog(ROOT_DIALOG).configure({
        generator: new TemplateEngineLanguageGenerator(lgFile),
        triggers: [
            new OnBeginDialog(
    ...............

    });
    this.addDialog(userProfileAdaptiveDialog);
    this.initialDialogId = ROOT_DIALOG;
}
```

<!--
[!code-javascript[userProfileDialog constructor](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/dialogs/userProfileDialog.js?range=11-17)]

[!code-javascript[userProfileDialog constructor](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/dialogs/userProfileDialog.js?range=92-97)]
-->

---

## <a name="register-the-adaptive-dialog"></a>Registrar o diálogo adaptável

Para permitir o uso do diálogo adaptável, o código de inicialização precisa registrar o diálogo conforme mostrado nas linhas realçadas abaixo, junto com outros serviços.

# <a name="c"></a>[C#](#tab/csharp)

**Startup.cs**

Você registra os diálogos adaptáveis na classe `Startup`, juntamente com outros serviços.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Register dialog. This sets up memory paths for adaptive.
    ComponentRegistration.Add(new DialogsComponentRegistration());

    // Register adaptive component
    ComponentRegistration.Add(new AdaptiveComponentRegistration());

    // Register to use language generation.
    ComponentRegistration.Add(new LanguageGenerationComponentRegistration());

    // Create the credential provider to be used with the Bot Framework Adapter.
    services.AddSingleton<ICredentialProvider, ConfigurationCredentialProvider>();

    // Create the Bot Framework Adapter with error handling enabled.
    services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();

    // Create the storage we'll be using for User and Conversation state. (Memory is great for testing purposes.)
    services.AddSingleton<IStorage, MemoryStorage>();

    // Create the User state. (Used in this bot's Dialog implementation.)
    services.AddSingleton<UserState>();

    // Create the Conversation state. (Used by the Dialog system itself.)
    services.AddSingleton<ConversationState>();

    // The Dialog that will be run by the bot.
    services.AddSingleton<RootDialog>();

    // Create the bot. the ASP Controller is expecting an IBot.
    services.AddSingleton<IBot, DialogBot<RootDialog>>();
}
```

<!--

[!code-csharp[ConfigureServices](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt/Startup.cs?range=21-54&highlight=5-18)]

-->

# <a name="javascript"></a>[JavaScript](#tab/javascript)


**index.js**

O código cria o diálogo de componente e os serviços em `index.js`. Especialmente:

- O diálogo de componente raiz.
- Serviços básicos para bot: um provedor de credenciais, um adaptador e a implantação do bot.
- Serviços para gerenciamento de estado: armazenamento, estado do usuário e estado da conversa.

Importe os serviços de bot necessários e a classe de diálogo de componente `userProfileDialog`.

```javascript
// Import required bot services.
// See https://aka.ms/bot-services to learn more about the different parts of a bot.
const { BotFrameworkAdapter, ConversationState, MemoryStorage, UserState } = require('botbuilder');

// Import our custom bot class that provides a turn handling function.
const { DialogBot } = require('./bots/dialogBot');
const { UserProfileDialog } = require('./dialogs/userProfileDialog');
```

Crie um estado de conversa com o provedor de armazenamento na memória.

```javascript

// A bot requires a state storage system to persist the dialog and user state between messages.
const memoryStorage = new MemoryStorage();

// Create conversation state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);
```

Crie o diálogo principal e o bot.

```javascript

const dialog = new UserProfileDialog();
const bot = new DialogBot(conversationState, userState, dialog);
```

Ouça as solicitações de entrada e encaminhe a mensagem para o manipulador principal do bot.

```javascript

// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        // Route the message to the bot's main handler.
        await bot.run(context);
    });
```

<!--
[!code-javascript[index-import](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=7-13)]

Create conversation state with in-memory storage provider.

[!code-javascript[index-storage](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=49-54)]

Create the main dialog and the bot.

[!code-javascript[index-storage](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=57-58)]

Listen for incoming requests and route the message to the bot's main handler.

[!code-javascript[index-run](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=68-73)]
-->

---

## <a name="run-the-dialog"></a>Executar o diálogo

# <a name="c"></a>[C#](#tab/csharp)

**Bots/Dialogs.cs**

O `DialogManager.OnTurnAsync` executa o diálogo adaptável com atividades.
A implementação mostrada pode executar qualquer tipo de `Dialog`. O `ConversationState` é usado pelo sistema de diálogo. No entanto, o `UserState` não pode ter sido usado em uma implementação de diálogo. O método `DialogManager.OnTurnAsync` fica responsável por salvar o estado.

```csharp
public class DialogBot<T> : ActivityHandler where T : Dialog
    {
        protected readonly BotState ConversationState;
        protected readonly Dialog Dialog;
        protected readonly ILogger Logger;
        protected readonly BotState UserState;
        private DialogManager DialogManager;

        public DialogBot(ConversationState conversationState, UserState userState, T dialog, ILogger<DialogBot<T>> logger)
        {
            ConversationState = conversationState;
            UserState = userState;
            Dialog = dialog;
            Logger = logger;
            DialogManager = new DialogManager(Dialog);
        }

        public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
        {
            Logger.LogInformation("Running dialog with Activity.");
            await DialogManager.OnTurnAsync(turnContext, cancellationToken: cancellationToken).ConfigureAwait(false);
        }
    }
}

```

<!--
[!code-csharp[ConfigureServices](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt/Bots/DialogBot.cs?range=18-41&highlight=21)]
-->

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/dialogBot.js**

O `DialogBot` estende o `ActivityHandler` e executa o diálogo adaptável com atividades.
As informações de estado contidas pelo `conversationState` e pelos `userState` são armazenadas para uso do `dialogManager`.

```javascript
class DialogBot extends ActivityHandler {
    /**
     *
     * @param {Dialog} dialog
     */
    constructor(conversationState, userState, dialog) {
        super();
        if (!conversationState) throw new Error('[DialogBot]: Missing parameter. conversationState is required');
        if (!userState) throw new Error('[DialogBot]: Missing parameter. userState is required');
        if (!dialog) throw new Error('[DialogBot]: Missing parameter. dialog is required');

        this.dialogManager = new DialogManager(dialog);
        this.dialogManager.conversationState = conversationState;
        this.dialogManager.userState = userState;

        this.onTurn(async (context, next) => {
            console.log('Running dialog with activity.');

            await this.dialogManager.onTurn(context);

            await next();
        });
    }
}
```

<!--
[!code-javascript[DialogBot](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/bots/dialogBot.js?range=7-30&highlight=13-14,19-21)]
-->

---

> [!NOTE]
> O armazenamento de memória é usado somente para testes e não deve ser usado na produção.
> Certifique-se de usar um tipo persistente de armazenamento para um bot de produção.

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o Emulador, conecte-se ao seu bot e envie mensagens conforme mostrado abaixo.

![Execução de exemplo do diálogo de prompt de vários turnos](../media/emulator-v4/multi-turn-prompt-adaptive-sample.png)

<!--
## Next steps

> [!div class="nextstepaction"]
> [Create a bot using adaptive, component, waterfall, and custom dialogs](bot-builder-mixed-dialogs.md)
-->

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/vishwac/r9/js/experimental/adaptive-dialog/csharp_dotnetcore/01.multi-turn-prompt
[js-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/vishwac/r9/js/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt
