---
title: Gerenciar um fluxo de conversa simples com diálogos | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa simples com diálogos no SDK do Construtor de Bot para Node.js.
keywords: fluxo de conversa simples, diálogos, prompts, cascatas, conjunto de diálogos
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 9/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 07035c8f0dfc7473192d8c51667ed1f5cefbc555
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999384"
---
# <a name="manage-simple-conversation-flow-with-dialogs"></a>Gerenciar fluxo de conversa simples com diálogos

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Você pode gerenciar fluxos de conversa simples e complexos usando a biblioteca de caixas de diálogo. Em um fluxo de conversa simples, o usuário começa na primeira etapa de uma *cascata*, percorre até a última etapa e a conversa termina. Os [fluxos de conversas complexos](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) incluem branches e loop.

<!-- TODO: This paragraph belongs in a conceptual topic. -->

As caixas de diálogo são estruturas em seu bot que atuam como funções no programa do bot. Essas caixas criam as mensagens que o bot envia e realizam as tarefas computacionais necessárias. São projetadas para executar operações específicas em determinada ordem. Elas podem ser chamadas de maneiras diferentes, às vezes em resposta a um usuário, outras em resposta a alguns estímulos externos ou por outras caixas de diálogo.

O uso de caixas de diálogo permite que o desenvolvedor do bot oriente o fluxo da conversa. É possível criar várias caixas de diálogo e vinculá-las para criar um fluxo de conversa com o qual você deseja que seu bot lide. A biblioteca **Caixas de Diálogo** no SDK do Bot Builder inclui recursos integrados, como _prompts_, _caixas de diálogo em cascata_ e _caixas de diálogo de componentes_ para ajudar a gerenciar o fluxo de conversas. Você pode usar os prompts para solicitar aos usuários diferentes tipos de informações. Você pode usar uma cascata para combinar várias etapas em uma sequência. É possível usar caixas de diálogo de componentes para criar sistemas modulares de caixas que contêm várias subcaixas de diálogo.

Neste artigo, usamos _conjuntos de caixas de diálogo_ para criar um fluxo de conversa que contém prompts e cascatas. Usaremos o código do exemplo de **prompt de vários turnos** [[C#](https://aka.ms/cs-multi-prompts-sample)|[JS](https://aka.ms/js-multi-prompts-sample)].

Para ter uma visão geral das caixas de diálogo, consulte a [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md) e o [estado das caixas de diálogo](bot-builder-dialog-state.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para usar as caixas de diálogo em geral, é necessário o pacote do NuGet `Microsoft.Bot.Builder.Dialogs` de seu projeto ou solução.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para usar as caixas de diálogo em geral, você precisa da biblioteca `botbuilder-dialogs` que pode ser baixada do NPM.

---

## <a name="using-dialogs-to-guide-the-user-through-steps"></a>Usando diálogos para orientar o usuário por etapas

Neste exemplo, criamos uma caixa de diálogo com várias etapas para solicitar informações ao usuário usando um conjunto de caixas de diálogo.

### <a name="create-a-dialog-with-waterfall-steps"></a>Criar um diálogo com etapas de cascata

Um **WaterfallDialog** é uma implementação específica de uma caixa de diálogo geralmente usada para coletar informações do usuário ou orientá-lo em uma série de tarefas. Cada etapa da conversa é implementada como uma função. Em cada etapa, um bot [solicita que o usuário insira uma entrada](bot-builder-prompts.md), aguarda uma resposta e depois passa o resultado para a próxima etapa. O resultado da primeira função é passado como um argumento para a função seguinte, e assim por diante.

Por exemplo, o exemplo de código a seguir define uma matriz de representantes que retrata as etapas de uma **cascata**. Após cada prompt, o bot reconhece a entrada do usuário. Há várias maneiras de manter a entrada coletada em uma caixa de diálogo. Consulte [Manter dados do usuário](bot-builder-tutorial-persist-user-inputs.md) para algumas das opções.

Este exemplo grava informações diretamente no perfil do usuário conforme elas são coletadas na caixa de diálogo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Neste exemplo, a caixa de diálogo de cascata é definida no arquivo do bot.

Consulte os namespaces usados nesse arquivo.

```csharp
using System;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Schema;
```

Defina uma propriedade de instância para o conjunto de caixas de diálogo.

```csharp
/// <summary>
/// The <see cref="DialogSet"/> that contains all the Dialogs that can be used at runtime.
/// </summary>
private DialogSet _dialogs;
```

Crie o conjunto de caixas de diálogo no construtor do bot, adicionando prompts e a caixa de diálogo de cascata ao conjunto.

```csharp
/// <summary>
/// Initializes a new instance of the <see cref="MultiTurnPromptsBot"/> class.
/// </summary>
/// <param name="accessors">A class containing <see cref="IStatePropertyAccessor{T}"/> used to manage state.</param>
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

Em seguida, defina cada etapa como um método separado. Você também pode definir as etapas em linha usando expressões lambda.

```csharp
/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
    // Running a prompt here means the next WaterfallStep will be run when the users response is received.
    return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
}

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

A caixa de diálogo é executada no manipulador de turnos do bot que, primeiro, cria um contexto da caixa de diálogo e continua ou inicia a caixa conforme apropriado, em seguida, salva a conversa e o estado do usuário no final do turno.

```csharp
// Run the DialogSet - let the framework identify the current state of the dialog from
// the dialog stack and figure out what (if any) is the active dialog.
var dialogContext = await _dialogs.CreateContextAsync(turnContext, cancellationToken);
var results = await dialogContext.ContinueDialogAsync(cancellationToken);

// If the DialogTurnStatus is Empty we should start a new dialog.
if (results.Status == DialogTurnStatus.Empty)
{
    await dialogContext.BeginDialogAsync("details", null, cancellationToken);
}
```

```csharp
// Save the dialog state into the conversation state.
await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);

// Save the user profile updates into the user state.
await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Neste exemplo, a caixa de diálogo de cascata é definida no arquivo **bot.js**.

Importe os objetos necessários para o código.

```javascript
const { ActivityTypes } = require('botbuilder');
const { ChoicePrompt, DialogSet, NumberPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
```

Defina e crie o conjunto de caixas de diálogo no construtor do bot adicionando prompts e a caixa de diálogo de cascata ao conjunto.

```javascript
/**
*
* @param {ConversationState} conversationState A ConversationState object used to store the dialog state.
* @param {UserState} userState A UserState object used to store values specific to the user.
*/
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

Em seguida, defina cada etapa como um método separado. Você também pode definir as etapas em linha usando expressões lambda.

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

A caixa de diálogo é executada no manipulador de turnos do bot que, primeiro, cria um contexto da caixa de diálogo e continua ou inicia a caixa conforme apropriado, em seguida, salva a conversa e o estado do usuário no final do turno.

```javascript
// Create a dialog context object.
const dc = await this.dialogs.createContext(turnContext);
```

```javascript
// If the bot has not yet responded, continue processing the current dialog.
await dc.continueDialog();
```

```javascript
// Start the sample dialog in response to any other input.
if (!turnContext.responded) {
    const user = await this.userProfile.get(dc.context, {});
    if (user.name) {
        await dc.beginDialog(HELLO_USER);
    } else {
        await dc.beginDialog(WHO_ARE_YOU);
    }
}
```

```javascript
// Save changes to the user state.
await this.userState.saveChanges(turnContext);

// End this turn by saving changes to the conversation state.
await this.conversationState.saveChanges(turnContext);
```

---

## <a name="dialog-context-and-waterfall-step-context-objects"></a>Contexto da caixa de diálogo e objetos de contexto da etapa de cascata

Use o objeto de contexto da caixa de diálogo para interagir com um conjunto de caixas de diálogo de dentro do manipulador de turnos do bot.
Use o objeto de contexto da etapa de cascata para interagir com um conjunto de caixas de diálogo de dentro da etapa de cascata.

## <a name="to-start-a-dialog"></a>Para iniciar uma caixa de diálogo

Para iniciar uma caixa de diálogo, passe a *dialogId* que você deseja iniciar para o método _beginDialog_, _prompt_ ou _replaceDialog_ do contexto da caixa. O método _beginDialog_ efetua o push da caixa de diálogo para o topo da pilha e o método _replaceDialog_ remove a caixa de diálogo atual da pilha e efetua o push da caixa substituta na pilha.

O método _prompt_ do contexto da caixa de diálogo é um método auxiliar que recebe argumentos e desenvolve as opções apropriadas para o prompt, em seguida, inicia a caixa de diálogo do prompt. Para saber mais sobre avisos, veja [Solicitar informações ao usuário](bot-builder-prompts.md).

## <a name="to-end-a-dialog"></a>Para encerrar uma caixa de diálogo

O método _end dialog_ encerra uma caixa de diálogo removendo-a da pilha e retorna um resultado opcional para a caixa de diálogo mãe.

É uma prática recomendada chamar explicitamente o método _endDialog_ no final da caixa de diálogo.

## <a name="to-clear-the-dialog-stack"></a>Para limpar a pilha de caixas de diálogo

Caso você queira remover todas as caixas de diálogo da pilha, é possível limpar a pilha de caixas chamando o método _cancel all dialogs_ do contexto da caixa de diálogo.

## <a name="to-repeat-a-dialog"></a>Para repetir uma caixa de diálogo

Para repetir uma caixa de diálogo, use o método _replace dialog_ que remove a caixa atual da pilha, efetua o push da caixa de diálogo substituta para o topo da fila e a inicia. Essa é uma ótima maneira de tratar [fluxos de conversa complexos](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) e uma boa técnica para gerenciar os menus.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar os fluxos de conversa simples, veremos como pode aproveitar o método _replace dialog_ para lidar com os fluxos de conversa complexos.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo de conversas complexo](bot-builder-dialog-manage-complex-conversation-flow.md)
