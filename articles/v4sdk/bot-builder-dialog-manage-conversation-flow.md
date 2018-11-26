---
title: Implementar fluxo de conversa sequencial | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa simples com diálogos no SDK do Construtor de Bot para Node.js.
keywords: fluxo de conversa simples, fluxo de conversa sequencial, diálogos, prompts, cascatas, conjunto de diálogos
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e774d6360968e5059588dbdb476cfd1f35fb464e
ms.sourcegitcommit: 6cb37f43947273a58b2b7624579852b72b0e13ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52288825"
---
# <a name="implement-sequential-conversation-flow"></a>Implementar fluxo de conversa sequencial

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Você pode gerenciar fluxos de conversa simples e complexos usando a biblioteca de caixas de diálogo. Em uma interação simples, o bot percorre uma sequência fixa de etapas e a conversa termina. Neste artigo, usamos um _diálogo em cascata_, alguns _prompts_ e um _conjunto de diálogos_ para criar uma interação simples que faz uma série de perguntas ao usuário.

## <a name="prerequisites"></a>Pré-requisitos
- [Emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- O código neste artigo se baseia no exemplo de **multi-turn-prompt**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/cs-multi-prompts-sample) ou [JS](https://aka.ms/js-multi-prompts-sample).
- Conhecimento das [Noções básicas do bot](bot-builder-basics.md), [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md), [estado da caixa de diálogo](bot-builder-dialog-state.md) e do arquivo [.bot](bot-file-basics.md).


As seções a seguir representam as etapas que você executaria para implementar diálogos simples para a maioria dos bots:

## <a name="configure-your-bot"></a>Configurar seu bot

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Inicializaremos o acessador de propriedade de estado para o estado de diálogo do bot no código de configuração do arquivo **Startup.cs**.

Definimos uma classe `MultiTurnPromptsBotAccessors` para conter os objetos de gerenciamento de estado e os acessadores de propriedades de estado do bot.
Aqui, chamamos apenas partes do código.

```csharp
public class MultiTurnPromptsBotAccessors
{
    // Initializes a new instance of the class.
    public MultiTurnPromptsBotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }

    public IStatePropertyAccessor<DialogState> ConversationDialogState { get; set; }
    public IStatePropertyAccessor<UserProfile> UserProfile { get; set; }

    public ConversationState ConversationState { get; }
    public UserState UserState { get; }
}
```

Registramos a classe de acessadores no método `ConfigureServices` da classe `Statup`.
Novamente, chamamos apenas partes do código.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<MultiTurnPromptsBotAccessors>(sp =>
    {
        // We need to grab the conversationState we added on the options in the previous step
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        var userState = options.State.OfType<UserState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new MultiTurnPromptsBotAccessors(conversationState, userState)
        {
            ConversationDialogState = conversationState.CreateProperty<DialogState>("DialogState"),
            UserProfile = userState.CreateProperty<UserProfile>("UserProfile"),
        };

        return accessors;
    });
}
```

Por meio da injeção de dependência, os acessadores estarão disponíveis para o código do construtor do bot.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No arquivo **index.js**, definimos os objetos de gerenciamento de estado.
Aqui, chamamos apenas partes do código.

```javascript
// Import required bot services. See https://aka.ms/bot-services to learn more about the different part of a bot.
const { BotFrameworkAdapter, ConversationState, MemoryStorage, UserState } = require('botbuilder');

// Define the state store for your bot. See https://aka.ms/about-bot-state to learn more about using MemoryStorage.
// A bot requires a state storage system to persist the dialog and user state between messages.
const memoryStorage = new MemoryStorage();

// Create conversation state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);

// Create the main dialog, which serves as the bot's main handler.
const bot = new MultiTurnBot(conversationState, userState);
```

O construtor do bot criará os acessadores de propriedade de estado para o bot: `this.dialogState` e `this.userProfile`.

---

## <a name="update-the-bot-turn-handler-to-call-the-dialog"></a>Atualizar o manipulador de turnos do bot para chamar o diálogo

Para executar o diálogo, o manipulador de turnos do bot precisa criar um contexto de diálogo para o conjunto de diálogos que contém os diálogos do bot. Um bot poderia definir vários conjuntos de diálogos, mas, como regra geral, você deve definir apenas um para o seu bot. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O diálogo é executado no manipulador de turnos do bot. Primeiro, o manipulador cria um `DialogContext` e continua o diálogo ativo, ou inicia um novo diálogo, conforme apropriado. Em seguida, o manipulador salva o estado do usuário e da conversa no final do turno.

Na classe `MultiTurnPromptsBot`, definimos uma propriedade `_dialogs` que contém o conjunto de diálogos, do qual podemos gerar um contexto de diálogo. Novamente, estamos mostrando aqui apenas parte do código do manipulador de turnos.

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    // ...
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Run the DialogSet - let the framework identify the current state of the dialog from
        // the dialog stack and figure out what (if any) is the active dialog.
        var dialogContext = await _dialogs.CreateContextAsync(turnContext, cancellationToken);
        var results = await dialogContext.ContinueDialogAsync(cancellationToken);

        // If the DialogTurnStatus is Empty we should start a new dialog.
        if (results.Status == DialogTurnStatus.Empty)
        {
            await dialogContext.BeginDialogAsync("details", null, cancellationToken);
        }
    }

    // ...
    // Save the dialog state into the conversation state.
    await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);

    // Save the user profile updates into the user state.
    await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código do bot usa algumas das classes na biblioteca de diálogos.

```javascript
const { ChoicePrompt, DialogSet, NumberPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
```

O diálogo é executado no manipulador de turnos do bot. Primeiro, o manipulador cria um `DialogContext` (`dc`) e continua o diálogo ativo ou inicia um novo diálogo, conforme apropriado. Em seguida, o manipulador salva o estado do usuário e da conversa no final do turno.

A classe `MultiTurnBot` é definida no arquivo **bot.js**. O construtor para essa classe adiciona uma propriedade `dialogs` ao conjunto de diálogos, do qual geramos um contexto de diálogo. Este bot coleta os dados de usuário uma vez, usando o diálogo `WHO_ARE_YOU`. Depois que o perfil do usuário é preenchido, o bot usa o diálogo `HELLO_USER` para responder. Novamente, estamos mostrando aqui apenas parte do código do manipulador de turnos.

```javascript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Create a dialog context object.
        const dc = await this.dialogs.createContext(turnContext);

        const utterance = (turnContext.activity.text || '').trim().toLowerCase();

        // ...
        // If the bot has not yet responded, continue processing the current dialog.
        await dc.continueDialog();

        // Start the sample dialog in response to any other input.
        if (!turnContext.responded) {
            const user = await this.userProfile.get(dc.context, {});
            if (user.name) {
                await dc.beginDialog(HELLO_USER);
            } else {
                await dc.beginDialog(WHO_ARE_YOU);
            }
        }
    }

    // ...
    // Save changes to the user state.
    await this.userState.saveChanges(turnContext);

    // End this turn by saving changes to the conversation state.
    await this.conversationState.saveChanges(turnContext);
}
```

---

No manipulador de turnos do bot, podemos criar um contexto de diálogo para o conjunto de diálogos. O contexto de diálogo acessa o cache de estado do bot, lembrando-se efetivamente onde na conversa o último turno parou.

Se houver um diálogo ativo, o método  _continuar diálogo_ do contexto de diálogo avança, usando a entrada do usuário que disparou esse turno; caso contrário, o bot chama método _iniciar diálogo_ do contexto de diálogo para iniciar um diálogo.

Por fim, podemos chamar o método _salvar alterações_ nos objetos de gerenciamento de estado para manter as alterações que ocorreram nesse turno.

### <a name="about-dialog-and-bot-state"></a>Sobre o estado do diálogo e do bot

Neste bot, definimos dois acessadores de propriedade de estado:

* Um deles criado dentro do estado de conversa para a propriedade de estado do diálogo. O estado do diálogo controla onde o usuário está dentro dos diálogos de um conjunto de diálogos, e é atualizado pelo contexto de diálogo, por exemplo, quando chamamos os métodos para iniciar ou continuar um diálogo.
* Um deles criado dentro do estado do usuário para a propriedade de perfil do usuário. O bot o utiliza para rastrear as informações que ele tem sobre o usuário, e podemos gerenciar explicitamente esse estado no código do nosso bot.

Os métodos _get_ e _set_ de um acessador de propriedade de estado obtêm e definem o valor da propriedade no cache do objeto de gerenciamento de estado. O cache é preenchido na primeira vez em que o valor de uma propriedade de estado é solicitado em um turno, mas deve ser mantido explicitamente. Para manter as alterações nas duas propriedades de estado, podemos chamar o método _salvar alterações_ do objeto de gerenciamento de estado correspondente.

## <a name="initialize-your-bot-and-define-your-dialog"></a>Inicializar seu bot e definir seu diálogo

Nossa conversa simples é modelada por uma série de perguntas ao usuário. As versões em C# e JavaScript têm etapas um pouco diferentes:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. Pergunte o nome deles.
1. Pergunte se eles estão dispostos a fornecer sua idade.
1. Nesse caso, pergunte a idade; caso contrário, pule essa etapa.
1. Pergunte se as informações coletadas estão corretas.
1. Envie uma mensagem de status e finalize.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para o diálogo `who_are_you`:

1. Pergunte o nome deles.
1. Pergunte se eles estão dispostos a fornecer sua idade.
1. Nesse caso, pergunte a idade; caso contrário, pule essa etapa.
1. Envie uma mensagem de status e finalize.

Para o diálogo `hello_user`:

1. Exiba as informações do usuário coletadas pelo bot.

---

Lembre-se do seguinte ao definir suas próprias etapas em cascata.

* Cada turno do bot representa a entrada do usuário, seguida por uma resposta do bot. Assim, você pede ao usuário a entrada no final de uma etapa em cascata, e recebe a resposta na próxima etapa em cascata.
* Cada prompt é, efetivamente, um diálogo em duas etapas que apresenta seu prompt e executa um loop até que receba a entrada "válida". 

Neste exemplo, o diálogo é definido dentro do arquivo do bot e é inicializado no construtor do bot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Defina uma propriedade de instância para o conjunto de caixas de diálogo.

```csharp
// The DialogSet that contains all the Dialogs that can be used at runtime.
private DialogSet _dialogs;
```

Crie o conjunto de caixas de diálogo no construtor do bot, adicionando prompts e a caixa de diálogo de cascata ao conjunto.

```csharp
public MultiTurnPromptsBot(MultiTurnPromptsBotAccessors accessors)
{
    _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

    // The DialogSet needs a DialogState accessor, it will call it when it has a turn context.
    _dialogs = new DialogSet(accessors.ConversationDialogState);

    // This array defines how the Waterfall will execute.
    var waterfallSteps = new WaterfallStep[]
    {
        NameStepAsync,
        NameConfirmStepAsync,
        AgeStepAsync,
        ConfirmStepAsync,
        SummaryStepAsync,
    };

    // Add named dialogs to the DialogSet. These names are saved in the dialog state.
    _dialogs.Add(new WaterfallDialog("details", waterfallSteps));
    _dialogs.Add(new TextPrompt("name"));
    _dialogs.Add(new NumberPrompt<int>("age"));
    _dialogs.Add(new ConfirmPrompt("confirm"));
}
```

Neste exemplo, definimos cada etapa como um método separado. Você também pode definir as etapas em linha no construtor usando expressões lambda.

```csharp
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
    // Running a prompt here means the next WaterfallStep will be run when the users response is received.
    return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
}

private async Task<DialogTurnResult> NameConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the current profile object from user state.
    var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

    // Update the profile.
    userProfile.Name = (string)stepContext.Result;

    // We can send messages to the user at any point in the WaterfallStep.
    await stepContext.Context.SendActivityAsync(MessageFactory.Text($"Thanks {stepContext.Result}."), cancellationToken);

    // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
    return await stepContext.PromptAsync("confirm", new PromptOptions { Prompt = MessageFactory.Text("Would you like to give your age?") }, cancellationToken);
}

private async Task<DialogTurnResult> AgeStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    if ((bool)stepContext.Result)
    {
        // User said "yes" so we will be prompting for the age.

        // Get the current profile object from user state.
        var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

        // WaterfallStep always finishes with the end of the Waterfall or with another dialog, here it is a Prompt Dialog.
        return await stepContext.PromptAsync("age", new PromptOptions { Prompt = MessageFactory.Text("Please enter your age.") }, cancellationToken);
    }
    else
    {
        // User said "no" so we will skip the next step. Give -1 as the age.
        return await stepContext.NextAsync(-1, cancellationToken);
    }
}


private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the current profile object from user state.
    var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

    // Update the profile.
    userProfile.Age = (int)stepContext.Result;

    // We can send messages to the user at any point in the WaterfallStep.
    if (userProfile.Age == -1)
    {
        await stepContext.Context.SendActivityAsync(MessageFactory.Text($"No age given."), cancellationToken);
    }
    else
    {
        // We can send messages to the user at any point in the WaterfallStep.
        await stepContext.Context.SendActivityAsync(MessageFactory.Text($"I have your age as {userProfile.Age}."), cancellationToken);
    }

    // WaterfallStep always finishes with the end of the Waterfall or with another dialog, here it is a Prompt Dialog.
    return await stepContext.PromptAsync("confirm", new PromptOptions { Prompt = MessageFactory.Text("Is this ok?") }, cancellationToken);
}

private async Task<DialogTurnResult> SummaryStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    if ((bool)stepContext.Result)
    {
        // Get the current profile object from user state.
        var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

        // We can send messages to the user at any point in the WaterfallStep.
        if (userProfile.Age == -1)
        {
            await stepContext.Context.SendActivityAsync(MessageFactory.Text($"I have your name as {userProfile.Name}."), cancellationToken);
        }
        else
        {
            await stepContext.Context.SendActivityAsync(MessageFactory.Text($"I have your name as {userProfile.Name} and age as {userProfile.Age}."), cancellationToken);
        }
    }
    else
    {
        // We can send messages to the user at any point in the WaterfallStep.
        await stepContext.Context.SendActivityAsync(MessageFactory.Text("Thanks. Your profile will not be kept."), cancellationToken);
    }

    // WaterfallStep always finishes with the end of the Waterfall or with another dialog, here it is the end.
    return await stepContext.EndDialogAsync(cancellationToken: cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Neste exemplo, a caixa de diálogo de cascata é definida no arquivo **bot.js**.

Defina os identificadores para usar os acessadores de propriedade de estado, os prompts e os diálogos.

```javascript
const DIALOG_STATE_PROPERTY = 'dialogState';
const USER_PROFILE_PROPERTY = 'user';

const WHO_ARE_YOU = 'who_are_you';
const HELLO_USER = 'hello_user';

const NAME_PROMPT = 'name_prompt';
const CONFIRM_PROMPT = 'confirm_prompt';
const AGE_PROMPT = 'age_prompt';
```

Defina e crie o conjunto de diálogos no construtor do bot adicionando prompts e diálogos em cascata ao conjunto.
O `NumberPrompt` inclui validação personalizada para garantir que o usuário insira uma idade maior do que 0.

```javascript
constructor(conversationState, userState) {
    // Create a new state accessor property. See https://aka.ms/about-bot-state-accessors to learn more about bot state and state accessors.
    this.conversationState = conversationState;
    this.userState = userState;

    this.dialogState = this.conversationState.createProperty(DIALOG_STATE_PROPERTY);

    this.userProfile = this.userState.createProperty(USER_PROFILE_PROPERTY);

    this.dialogs = new DialogSet(this.dialogState);

    // Add prompts that will be used by the main dialogs.
    this.dialogs.add(new TextPrompt(NAME_PROMPT));
    this.dialogs.add(new ChoicePrompt(CONFIRM_PROMPT));
    this.dialogs.add(new NumberPrompt(AGE_PROMPT, async (prompt) => {
        if (prompt.recognized.succeeded) {
            if (prompt.recognized.value <= 0) {
                await prompt.context.sendActivity(`Your age can't be less than zero.`);
                return false;
            } else {
                return true;
            }
        }
        return false;
    }));

    // Create a dialog that asks the user for their name.
    this.dialogs.add(new WaterfallDialog(WHO_ARE_YOU, [
        this.promptForName.bind(this),
        this.confirmAgePrompt.bind(this),
        this.promptForAge.bind(this),
        this.captureAge.bind(this)
    ]));

    // Create a dialog that displays a user name after it has been collected.
    this.dialogs.add(new WaterfallDialog(HELLO_USER, [
        this.displayProfile.bind(this)
    ]));
}
```

Como os métodos de etapa do nosso diálogo consultam propriedades da instância, precisamos usar o método `bind` para que o objeto `this` seja resolvido corretamente dentro de cada método de etapa.

Neste exemplo, definimos cada etapa como um método separado. Você também pode definir as etapas em linha no construtor usando expressões lambda.

```javascript
// This step in the dialog prompts the user for their name.
async promptForName(step) {
    return await step.prompt(NAME_PROMPT, `What is your name, human?`);
}

// This step captures the user's name, then prompts whether or not to collect an age.
async confirmAgePrompt(step) {
    const user = await this.userProfile.get(step.context, {});
    user.name = step.result;
    await this.userProfile.set(step.context, user);
    await step.prompt(CONFIRM_PROMPT, 'Do you want to give your age?', ['yes', 'no']);
}

// This step checks the user's response - if yes, the bot will proceed to prompt for age.
// Otherwise, the bot will skip the age step.
async promptForAge(step) {
    if (step.result && step.result.value === 'yes') {
        return await step.prompt(AGE_PROMPT, `What is your age?`,
            {
                retryPrompt: 'Sorry, please specify your age as a positive number or say cancel.'
            }
        );
    } else {
        return await step.next(-1);
    }
}

// This step captures the user's age.
async captureAge(step) {
    const user = await this.userProfile.get(step.context, {});
    if (step.result !== -1) {
        user.age = step.result;
        await this.userProfile.set(step.context, user);
        await step.context.sendActivity(`I will remember that you are ${ step.result } years old.`);
    } else {
        await step.context.sendActivity(`No age given.`);
    }
    return await step.endDialog();
}

// This step displays the captured information back to the user.
async displayProfile(step) {
    const user = await this.userProfile.get(step.context, {});
    if (user.age) {
        await step.context.sendActivity(`Your name is ${ user.name } and you are ${ user.age } years old.`);
    } else {
        await step.context.sendActivity(`Your name is ${ user.name } and you did not share your age.`);
    }
    return await step.endDialog();
}
```

---

Este exemplo atualiza o estado de perfil do usuário a partir do diálogo. Essa prática pode ser útil para um bot simples, mas não funcionará se você quiser reutilizar um diálogo entre os bots.

Há várias opções para manter as etapas de diálogo e o estado de bot separados. Por exemplo, após o diálogo reunir todas as informações, você pode:

* Usar o método _terminar diálogo_ para fornecer os dados coletados como valor de retorno ao contexto-pai. Pode ser o manipulador de turnos do bot ou um diálogo ativo anterior na pilha de diálogos. É assim que as classes de prompt são criadas.
* Gere uma solicitação para um serviço apropriado. Isso pode funcionar bem se seu bot atuar como um front-end para um serviço maior.

## <a name="test-your-dialog"></a>Testar seu diálogo

Compile e execute o bot localmente, depois, interaja com seu bot usando o Emulador.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. O bot envia uma mensagem de saudação inicial em resposta à atividade de atualização de conversa na qual o usuário é adicionado à conversa.
1. Insira `hi` ou outra entrada. Como ainda não há um diálogo ativo neste turno, o bot inicia o diálogo `details`.
   * O bot envia o primeiro prompt do diálogo e aguarda mais entrada.
1. Responda às perguntas conforme o bot as faz, avançando através do diálogo.
1. A última etapa do diálogo envia uma mensagem `Thanks`, com base em suas entradas.
   * Quando o diálogo termina, ele é removido da pilha de diálogos e o bot deixa de ter um diálogo ativo.
1. Insira `hi` ou outra entrada para iniciar o diálogo novamente.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. O bot envia uma mensagem de saudação inicial em resposta à atividade de atualização de conversa na qual o usuário é adicionado à conversa.
1. Insira `hi` ou outra entrada. Como ainda não há um diálogo ativo neste turno, e nenhum perfil de usuário, o bot inicia o diálogo `who_are_you`.
   * O bot envia o primeiro prompt do diálogo e aguarda mais entrada.
1. Responda às perguntas conforme o bot as faz, avançando através do diálogo.
1. A última etapa do diálogo envia uma breve mensagem de confirmação.
1. Insira `hi` ou outra entrada.
   * O bot inicia o diálogo `hello_user` de uma etapa, que exibe informações de dados coletados e termina imediatamente.

---

## <a name="additional-resources"></a>Recursos adicionais
Você pode contar com a validação interna para cada tipo de prompt, conforme mostrado aqui, ou pode adicionar sua própria validação personalizada ao prompt. Para obter mais informações, confira [coletar entrada do usuário usando um prompt de diálogo](bot-builder-prompts.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar o fluxo de conversa de avanço usando ramificações e loops](bot-builder-dialog-manage-complex-conversation-flow.md)
