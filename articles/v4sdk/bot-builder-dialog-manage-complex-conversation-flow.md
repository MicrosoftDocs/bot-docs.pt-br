---
title: Criar fluxo de conversa avançado usando branches e loops | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa complexo com diálogos no SDK do Bot Builder.
keywords: fluxo de conversa complexo, repetição, loop, menu, diálogos, prompts, cascatas, conjunto de diálogos
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/28/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: da891fad019fc1725242404905565bb1072f8540
ms.sourcegitcommit: 4db09039effc5259feba163128f4e9a3ccdd3d0d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585887"
---
# <a name="create-advanced-conversation-flow-using-branches-and-loops"></a>Criar fluxo de conversa avançado usando ramificações e loops

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Neste artigo, mostraremos como gerenciar conversas complexas que geram ramificações e loops. Também mostraremos como passar argumentos entre partes diferentes do diálogo.

## <a name="prerequisites"></a>Pré-requisitos

- [Emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- O código neste artigo se baseia no exemplo de **diálogo complexo**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/cs-complex-dialog-sample) ou [JS](https://aka.ms/js-complex-dialog-sample).
- Conhecimento dos [conceitos básicos sobre bot](bot-builder-basics.md), [biblioteca de diálogo](bot-builder-concept-dialog.md), [estado de diálogo](bot-builder-dialog-state.md) e arquivos [.bot](bot-file-basics.md).

## <a name="about-the-sample"></a>Sobre o exemplo

Este exemplo representa um bot que pode inscrever usuários para avaliação de até duas empresas de uma lista.

- Ele pede o nome e a idade do usuário e depois cria _ramificações_ com base na idade do usuário.
  - Se o usuário for muito jovem, ele não pedirá que o usuário avalie nenhuma empresa.
  - Se o usuário tiver idade suficiente, ele começará a coletar as preferências de avaliação do usuário.
    - Ele permite que o usuário selecione uma empresa para avaliação.
    - Se o usuário escolher uma empresa, ele usa um _loop_ para permitir que ele escolha uma segunda empresa.
- Por fim, ele agradece a participação do usuário.

Ele usa dois diálogos em cascata e alguns prompts para gerenciar uma conversa complexa.

## <a name="configure-state-for-your-bot"></a>Configurar o estado do seu bot

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Defina informações do usuário que coletaremos.

```csharp
public class UserProfile
{
    public string Name { get; set; }
    public int Age { get; set; }

    //The list of companies the user wants to review.
    public List<string> CompaniesToReview { get; set; } = new List<string>();
}
```

Defina a classe para conter os objetos de gerenciamento de estado e os acessadores de propriedades de estado do bot.

```csharp
public class ComplexDialogBotAccessors
{
    public ComplexDialogBotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }
    public IStatePropertyAccessor<UserProfile> UserProfileAccessor { get; set; }

    public ConversationState ConversationState { get; }
    public UserState UserState { get; }
}
```

Crie o objeto de gerenciamento de estado e registre a classe de acessadores no método `ConfigureServices` da classe `Statup`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Register the bot.

    // Create conversation and user state management objects, using memory storage.
    IStorage dataStore = new MemoryStorage();
    var conversationState = new ConversationState(dataStore);
    var userState = new UserState(dataStore);

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<ComplexDialogBotAccessors>(sp =>
    {
        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new ComplexDialogBotAccessors(conversationState, userState)
        {
            DialogStateAccessor = conversationState.CreateProperty<DialogState>("DialogState"),
            UserProfileAccessor = userState.CreateProperty<UserProfile>("UserProfile"),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No arquivo **index.js**, definimos os objetos de gerenciamento de estado.

```javascript
const { BotFrameworkAdapter, MemoryStorage, UserState, ConversationState } = require('botbuilder');

// ...

// Define state store for your bot.
const memoryStorage = new MemoryStorage();

// Create user and conversation state with in-memory storage provider.
const userState = new UserState(memoryStorage);
const conversationState = new ConversationState(memoryStorage);

// Create the bot.
const myBot = new MyBot(conversationState, userState);
```

O construtor do bot criará os acessadores de propriedade de estado para o bot.

---

## <a name="initialize-your-bot"></a>Inicializar o bot

Crie um _conjunto de diálogos_ para o bot, ao qual adicionaremos todos os diálogos para este exemplo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Crie o conjunto de diálogos no construtor do bot adicionando os prompts e os dois diálogos em cascata ao conjunto.

Em seguida, definiremos cada etapa como um método separado. Nós os implementaremos na próxima seção.

```csharp
public class ComplexDialogBot : IBot
{
    // Define constants for the bot...

    // Define properties for the bot's accessors and dialog set.
    private readonly ComplexDialogBotAccessors _accessors;
    private readonly DialogSet _dialogs;

    // Initialize the bot and add dialogs and prompts to the dialog set.
    public ComplexDialogBot(ComplexDialogBotAccessors accessors)
    {
        _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

        // Create a dialog set for the bot. It requires a DialogState accessor, with which
        // to retrieve the dialog state from the turn context.
        _dialogs = new DialogSet(accessors.DialogStateAccessor);

        // Add the prompts we need to the dialog set.
        _dialogs
            .Add(new TextPrompt(NamePrompt))
            .Add(new NumberPrompt<int>(AgePrompt))
            .Add(new ChoicePrompt(SelectionPrompt));

        // Add the dialogs we need to the dialog set.
        _dialogs.Add(new WaterfallDialog(TopLevelDialog)
            .AddStep(NameStepAsync)
            .AddStep(AgeStepAsync)
            .AddStep(StartSelectionStepAsync)
            .AddStep(AcknowledgementStepAsync));

        _dialogs.Add(new WaterfallDialog(ReviewSelectionDialog)
            .AddStep(SelectionStepAsync)
            .AddStep(LoopStepAsync));
    }

    // Turn handler and other supporting methods...
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No arquivo **bot.js**, defina e crie o conjunto de diálogos no construtor do bot adicionando prompts e diálogos em cascata ao conjunto.

Em seguida, definiremos cada etapa como um método separado. Nós os implementaremos na próxima seção.

```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, TextPrompt, NumberPrompt, ChoicePrompt, DialogTurnStatus } = require('botbuilder-dialogs');

// Define constants for the bot...

class MyBot {
    constructor(conversationState, userState) {
        // Create the state property accessors and save the state management objects.
        this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
        this.userProfileAccessor = userState.createProperty(USER_PROFILE_PROPERTY);
        this.conversationState = conversationState;
        this.userState = userState;

        // Create a dialog set for the bot. It requires a DialogState accessor, with which
        // to retrieve the dialog state from the turn context.
        this.dialogs = new DialogSet(this.dialogStateAccessor);

        // Add the prompts we need to the dialog set.
        this.dialogs
            .add(new TextPrompt(NAME_PROMPT))
            .add(new NumberPrompt(AGE_PROMPT))
            .add(new ChoicePrompt(SELECTION_PROMPT));

        // Add the dialogs we need to the dialog set.
        this.dialogs.add(new WaterfallDialog(TOP_LEVEL_DIALOG)
            .addStep(this.nameStep.bind(this))
            .addStep(this.ageStep.bind(this))
            .addStep(this.startSelectionStep.bind(this))
            .addStep(this.acknowledgementStep.bind(this)));

        this.dialogs.add(new WaterfallDialog(REVIEW_SELECTION_DIALOG)
            .addStep(this.selectionStep.bind(this))
            .addStep(this.loopStep.bind(this)));
    }

    // Turn handler and other supporting methods...
}
```

---

## <a name="implement-the-steps-for-the-waterfall-dialogs"></a>Implemente as etapas para os diálogos em cascata

Agora, vamos implementar as etapas para nossos dois diálogos.

### <a name="the-top-level-dialog"></a>O diálogo de nível superior

O diálogo inicial principal tem quatro etapas:

1. Perguntar o nome do usuário.
1. Perguntar a idade do usuário.
1. Ramificação com base na idade do usuário.
1. Por fim, agradecer a participação do usuário e retornar as informações coletadas.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// The first step of the top-level dialog.
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Create an object in which to collect the user's information within the dialog.
    stepContext.Values[UserInfo] = new UserProfile();

    // Ask the user to enter their name.
    return await stepContext.PromptAsync(
        NamePrompt,
        new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") },
        cancellationToken);
}

// The second step of the top-level dialog.
private async Task<DialogTurnResult> AgeStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    // Set the user's name to what they entered in response to the name prompt.
    ((UserProfile)stepContext.Values[UserInfo]).Name = (string)stepContext.Result;

    // Ask the user to enter their age.
    return await stepContext.PromptAsync(
        AgePrompt,
        new PromptOptions { Prompt = MessageFactory.Text("Please enter your age.") },
        cancellationToken);
}

// The third step of the top-level dialog.
private async Task<DialogTurnResult> StartSelectionStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    // Set the user's age to what they entered in response to the age prompt.
    int age = (int)stepContext.Result;
    ((UserProfile)stepContext.Values[UserInfo]).Age = age;

    if (age < 25)
    {
        // If they are too young, skip the review-selection dialog, and pass an empty list to the next step.
        await stepContext.Context.SendActivityAsync(
            MessageFactory.Text("You must be 25 or older to participate."),
            cancellationToken);
        return await stepContext.NextAsync(new List<string>(), cancellationToken);
    }
    else
    {
        // Otherwise, start the review-selection dialog.
        return await stepContext.BeginDialogAsync(ReviewSelectionDialog, null, cancellationToken);
    }
}

// The final step of the top-level dialog.
private async Task<DialogTurnResult> AcknowledgementStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    // Set the user's company selection to what they entered in the review-selection dialog.
    List<string> list = stepContext.Result as List<string>;
    ((UserProfile)stepContext.Values[UserInfo]).CompaniesToReview = list ?? new List<string>();

    // Thank them for participating.
    await stepContext.Context.SendActivityAsync(
        MessageFactory.Text($"Thanks for participating, {((UserProfile)stepContext.Values[UserInfo]).Name}."),
        cancellationToken);

    // Exit the dialog, returning the collected user information.
    return await stepContext.EndDialogAsync(stepContext.Values[UserInfo], cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async nameStep(stepContext) {
    // Create an object in which to collect the user's information within the dialog.
    stepContext.values[USER_INFO] = {};

    // Ask the user to enter their name.
    return await stepContext.prompt(NAME_PROMPT, 'Please enter your name.');
}

async ageStep(stepContext) {
    // Set the user's name to what they entered in response to the name prompt.
    stepContext.values[USER_INFO].name = stepContext.result;

    // Ask the user to enter their age.
    return await stepContext.prompt(AGE_PROMPT, 'Please enter your age.');
}

async startSelectionStep(stepContext) {
    // Set the user's age to what they entered in response to the age prompt.
    stepContext.values[USER_INFO].age = stepContext.result;

    if (stepContext.result < 25) {
        // If they are too young, skip the review-selection dialog, and pass an empty list to the next step.
        await stepContext.context.sendActivity('You must be 25 or older to participate.');
        return await stepContext.next([]);
    } else {
        // Otherwise, start the review-selection dialog.
        return await stepContext.beginDialog(REVIEW_SELECTION_DIALOG);
    }
}

async acknowledgementStep(stepContext) {
    // Set the user's company selection to what they entered in the review-selection dialog.
    const list = stepContext.result || [];
    stepContext.values[USER_INFO].companiesToReview = list;

    // Thank them for participating.
    await stepContext.context.sendActivity(`Thanks for participating, ${stepContext.values[USER_INFO].name}.`);

    // Exit the dialog, returning the collected user information.
    return await stepContext.endDialog(stepContext.values[USER_INFO]);
}
```

---

### <a name="the-review-selection-dialog"></a>O diálogo de seleção da avaliação

O diálogo de seleção da avaliação tem duas etapas:

1. Pedir que o usuário escolha uma empresa para avaliação, ou escolha `done` para concluir.
1. Repetir esse diálogo ou sair, conforme apropriado.

Nesse design, o diálogo principal sempre precederá o diálogo de seleção da avaliação na pilha, e o diálogo de seleção da avaliação pode ser considerado um filho do diálogo principal.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// The first step of the review-selection dialog.
private async Task<DialogTurnResult> SelectionStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Continue using the same selection list, if any, from the previous iteration of this dialog.
    List<string> list = stepContext.Options as List<string> ?? new List<string>();
    stepContext.Values[CompaniesSelected] = list;

    // Create a prompt message.
    string message;
    if (list.Count is 0)
    {
        message = $"Please choose a company to review, or `{DoneOption}` to finish.";
    }
    else
    {
        message = $"You have selected **{list[0]}**. You can review an additional company, " +
            $"or choose `{DoneOption}` to finish.";
    }

    // Create the list of options to choose from.
    List<string> options = _companyOptions.ToList();
    options.Add(DoneOption);
    if (list.Count > 0)
    {
        options.Remove(list[0]);
    }

    // Prompt the user for a choice.
    return await stepContext.PromptAsync(
        SelectionPrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text(message),
            RetryPrompt = MessageFactory.Text("Please choose an option from the list."),
            Choices = ChoiceFactory.ToChoices(options),
        },
        cancellationToken);
}

// The final step of the review-selection dialog.
private async Task<DialogTurnResult> LoopStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Retrieve their selection list, the choice they made, and whether they chose to finish.
    List<string> list = stepContext.Values[CompaniesSelected] as List<string>;
    FoundChoice choice = (FoundChoice)stepContext.Result;
    bool done = choice.Value == DoneOption;

    if (!done)
    {
        // If they chose a company, add it to the list.
        list.Add(choice.Value);
    }

    if (done || list.Count is 2)
    {
        // If they're done, exit and return their list.
        return await stepContext.EndDialogAsync(list, cancellationToken);
    }
    else
    {
        // Otherwise, repeat this dialog, passing in the list from this iteration.
        return await stepContext.ReplaceDialogAsync(ReviewSelectionDialog, list, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async selectionStep(stepContext) {
    // Continue using the same selection list, if any, from the previous iteration of this dialog.
    const list = Array.isArray(stepContext.options) ? stepContext.options : [];
    stepContext.values[COMPANIES_SELECTED] = list;

    // Create a prompt message.
    let message;
    if (list.length === 0) {
        message = 'Please choose a company to review, or `' + DONE_OPTION + '` to finish.';
    } else {
        message = `You have selected **${list[0]}**. You can review an addition company, ` +
            'or choose `' + DONE_OPTION + '` to finish.';
    }

    // Create the list of options to choose from.
    const options = list.length > 0
        ? COMPANY_OPTIONS.filter(function (item) { return item !== list[0] })
        : COMPANY_OPTIONS.slice();
    options.push(DONE_OPTION);

    // Prompt the user for a choice.
    return await stepContext.prompt(SELECTION_PROMPT, {
        prompt: message,
        retryPrompt: 'Please choose an option from the list.',
        choices: options
    });
}

async loopStep(stepContext) {
    // Retrieve their selection list, the choice they made, and whether they chose to finish.
    const list = stepContext.values[COMPANIES_SELECTED];
    const choice = stepContext.result;
    const done = choice.value === DONE_OPTION;

    if (!done) {
        // If they chose a company, add it to the list.
        list.push(choice.value);
    }

    if (done || list.length > 1) {
        // If they're done, exit and return their list.
        return await stepContext.endDialog(list);
    } else {
        // Otherwise, repeat this dialog, passing in the list from this iteration.
        return await stepContext.replaceDialog(REVIEW_SELECTION_DIALOG, list);
    }
}
```

---

## <a name="update-the-bots-turn-handler"></a>Atualizar o manipulador de turnos do bot

O manipulador de turnos do bot repete o fluxo de conversa definido por esses diálogos.
Quando recebemos uma mensagem do usuário:

1. Continue o diálogo ativo, se houver um.
   - Se não houver um diálogo ativo, limparemos o perfil do usuário e iniciaremos o diálogo principal.
   - Se o dialogo ativo for concluído, coletaremos e salvaremos as informações retornadas e exibiremos uma mensagem de status.
   - Caso contrário, o diálogo ativo ainda estará na metade do processo, e não precisaremos fazer mais nada no momento.
1. Salve o estado da conversa, para que as atualizações do estado do diálogo sejam mantidas.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext == null)
    {
        throw new ArgumentNullException(nameof(turnContext));
    }

    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Run the DialogSet - let the framework identify the current state of the dialog from
        // the dialog stack and figure out what (if any) is the active dialog.
        DialogContext dialogContext = await _dialogs.CreateContextAsync(turnContext, cancellationToken);
        DialogTurnResult results = await dialogContext.ContinueDialogAsync(cancellationToken);
        switch (results.Status)
        {
            case DialogTurnStatus.Cancelled:
            case DialogTurnStatus.Empty:
                // If there is no active dialog, we should clear the user info and start a new dialog.
                await _accessors.UserProfileAccessor.SetAsync(turnContext, new UserProfile(), cancellationToken);
                await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
                await dialogContext.BeginDialogAsync(TopLevelDialog, null, cancellationToken);
                break;

            case DialogTurnStatus.Complete:
                // If we just finished the dialog, capture and display the results.
                UserProfile userInfo = results.Result as UserProfile;
                string status = "You are signed up to review "
                    + (userInfo.CompaniesToReview.Count is 0
                        ? "no companies"
                        : string.Join(" and ", userInfo.CompaniesToReview))
                    + ".";
                await turnContext.SendActivityAsync(status);
                await _accessors.UserProfileAccessor.SetAsync(turnContext, userInfo, cancellationToken);
                await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
                break;

            case DialogTurnStatus.Waiting:
                // If there is an active dialog, we don't need to do anything here.
                break;
        }

        await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
    }

    // Processes ConversationUpdate Activities to welcome the user.
    else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
    {
        // Welcome new users...
    }
    else
    {
        // Give a default reply for all other activity types...
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Run the DialogSet - let the framework identify the current state of the dialog from
        // the dialog stack and figure out what (if any) is the active dialog.
        const dialogContext = await this.dialogs.createContext(turnContext);
        const results = await dialogContext.continueDialog();
        switch (results.status) {
            case DialogTurnStatus.cancelled:
            case DialogTurnStatus.empty:
                // If there is no active dialog, we should clear the user info and start a new dialog.
                await this.userProfileAccessor.set(turnContext, {});
                await this.userState.saveChanges(turnContext);
                await dialogContext.beginDialog(TOP_LEVEL_DIALOG);
                break;
            case DialogTurnStatus.complete:
                // If we just finished the dialog, capture and display the results.
                const userInfo = results.result;
                const status = 'You are signed up to review '
                    + (userInfo.companiesToReview.length === 0 ? 'no companies' : userInfo.companiesToReview.join(' and '))
                    + '.';
                await turnContext.sendActivity(status);
                await this.userProfileAccessor.set(turnContext, userInfo);
                await this.userState.saveChanges(turnContext);
                break;
            case DialogTurnStatus.waiting:
                // If there is an active dialog, we don't need to do anything here.
                break;
        }
        await this.conversationState.saveChanges(turnContext);
    } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate) {
        // Welcome new users...
    } else {
        // Give a default reply for all other activity types...
    }
}
```

---

## <a name="test-your-dialog"></a>Testar seu diálogo

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para o exemplo em [C#](https://aka.ms/cs-complex-dialog-sample) ou em [JS](https://aka.ms/js-complex-dialog-sample).
1. Use o emulador para testar o bot, conforme mostrado abaixo.

![teste de exemplo de diálogo complexo](~/media/emulator-v4/test-complex-dialog.png)

## <a name="additional-resources"></a>Recursos adicionais

Para obter uma introdução sobre como implementar um diálogo, confira [implementar fluxo da conversa sequencial](bot-builder-dialog-manage-conversation-flow.md), que usa um único diálogo em cascata e alguns prompts para criar uma interação simples que faz ao usuário uma série de perguntas.

A biblioteca Diálogos inclui uma validação básica de prompts. Você também pode adicionar uma validação personalizada. Para obter mais informações, confira [coletar entrada do usuário usando um prompt de diálogo](bot-builder-prompts.md).

Para simplificar o código do seu diálogo e reutilizá-lo em vários bots, defina as partes de um conjunto de diálogos como uma classe separada.
Para saber mais, confira [reutilizar diálogos](bot-builder-compositcontrol.md).

## <a name="next-steps"></a>Próximas etapas

Você pode aprimorar os bots para que reajam a uma entrada adicional, como "ajuda" ou "cancelar", que pode interromper o fluxo normal da conversa.

> [!div class="nextstepaction"]
> [Manipular interrupções do usuário](bot-builder-howto-handle-user-interrupt.md)
