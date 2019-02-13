---
title: Reutilizar diálogos | Microsoft Docs
description: Saiba como modularizar a lógica de bot usando o contêiner de diálogo no SDK do Bot Framework para Node.js e C#.
keywords: controle composto, lógica de bot modular
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 01/16/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b72ffa951e176a174dd8b00e69229b27bf28a360
ms.sourcegitcommit: 32615b88e4758004c8c99e9d564658a700c7d61f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55711990"
---
# <a name="reuse-dialogs"></a>Reutilizar diálogos

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Imagine que você está criando um bot de hotel que lida com várias tarefas como cumprimentar o usuário, reservar uma mesa de jantar, pedir comida, configurar um alarme, exibir o boletim meteorológico atual e muitas outras. É possível lidar com cada uma dessas tarefas no bot usando um objeto de diálogo; no entanto, isso pode tornar o código de diálogo muito grande e desorganizado.

Você pode transformar partes de seu fluxo de conversa em diálogos de componente, dividindo um grande conjunto de diálogos em pedaços mais gerenciáveis. Isso pode simplificar o código, facilitar sua depuração e permitir que várias equipes trabalhem no bot simultaneamente.
Você também pode criar uma biblioteca de diálogos de componente para reutilização em outros conjuntos de diálogos e bots.

Neste exemplo, criaremos um bot de hotel que combina diálogos de componente de check-in, reserva de mesa e serviço de despertador.

Este artigo se baseia nas informações sobre como gerenciar fluxos de conversa [simples](bot-builder-dialog-manage-conversation-flow.md) e [complexos](bot-builder-dialog-manage-complex-conversation-flow.md).

## <a name="create-your-project"></a>Criar seu projeto

Vamos começar com o modelo de bot de eco e incluir a biblioteca de diálogos no projeto.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Começaremos com um modelo de **EchoBot**. Para obter instruções, consulte o [início rápido para .NET](../dotnet/bot-builder-dotnet-sdk-quickstart.md).

Para usar diálogos, instale o pacote do NuGet `Microsoft.Bot.Builder.Dialogs` do projeto ou solução.
Depois, consulte a biblioteca de diálogos usando as instruções em seus arquivos de código, conforme o necessário.

Renomeie o arquivo **EchoBotWithCounter.cs** como **HotelBot.cs** e renomeie a classe como **HotelBot**.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Vamos começar com o modelo **Echo**. Para obter instruções, confira o [início rápido para JavaScript](../javascript/bot-builder-javascript-quickstart.md).

A biblioteca `botbuilder-dialogs` pode ser baixada do npm. Para instalar a biblioteca `botbuilder-dialogs`, execute o seguinte comando npm:

```cmd
npm install --save botbuilder-dialogs
```

---

## <a name="managing-state"></a>Gerenciar estado

Há muitas maneiras de configurar o gerenciamento de estado para um bot que usa diálogos compostos. Nesse bot, cada diálogo de componente retorna um objeto como o resultado do diálogo. O contexto de chamada gerencia os valores retornados. Para obter mais informações sobre gerenciamento de estado, consulte [Salvar estado usando propriedades do usuário e conversa](bot-builder-howto-v4-state.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Cada diálogo coletará algumas informações, e o manipulador de turnos ou o menu principal do bot salvará essas informações no estado do usuário. Vamos definir diálogos de componente para check-in, reserva de mesa e definição de despertador. Cada um deles retornará um objeto de uma classe pertinente. Adicione cada uma das classes a seguir ao seu projeto como um novo módulo de classe em C#.

```csharp
/// <summary>
/// User state information.
/// </summary>
public class UserInfo
{
    public GuestInfo Guest { get; set; }
    public TableInfo Table { get; set; }
    public WakeUpInfo WakeUp { get; set; }
}

/// <summary>
/// State information associated with the check-in dialog.
/// </summary>
public class GuestInfo
{
    public string Name { get; set; }
    public string Room { get; set; }
}

/// <summary>
/// State information associated with the reserve-table dialog.
/// </summary>
public class TableInfo
{
    public string Number { get; set; }
}

/// <summary>
/// State information associated with the wake-up call dialog.
/// </summary>
public class WakeUpInfo
{
    public string Time { get; set; }
}
```

Renomeie **EchoBotAccessors.cs** como **BotAccessors.cs** e atualize o nome da classe **BotAccessors**.

Em seguida, atualize o arquivo usando este código. Precisamos de acessadores para o estado do diálogo e para as informações do usuário que coletamos.

```csharp
using System;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

/// <summary>
/// Contains the state objects and the state property accessors for the bot.
/// </summary>
public class BotAccessors
{
    // The property accessor keys to use.
    public const string UserInfoAccessorName = "HotelBot.UserInfo";
    public const string DialogStateAccessorName = "HotelBot.DialogState";

    /// <summary>
    /// Initializes a new instance of the <see cref="BotAccessors"/> class.
    /// Contains the <see cref="ConversationState"/> and associated <see cref="IStatePropertyAccessor{T}"/>.
    /// </summary>
    /// <param name="conversationState">The state object that stores the counter.</param>
    public BotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }

    /// <summary>Gets or sets the state property accessor for the user information we're tracking.</summary>
    /// <value>Accessor for user information.</value>
    public IStatePropertyAccessor<UserInfo> UserInfoAccessor { get; set; }

    /// <summary>Gets or sets the state property accessor for the dialog state.</summary>
    /// <value>Accessor for the dialog state.</value>
    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }

    /// <summary>Gets the conversation state for the bot.</summary>
    /// <value>The conversation state for the bot.</value>
    public ConversationState ConversationState { get; }

    /// <summary>Gets the user state for the bot.</summary>
    /// <value>The user state for the bot.</value>
    public UserState UserState { get; }
}
```

No arquivo **Startup.cs**, atualize o código no método `ConfigureServices` que configura o estado e os acessadores de propriedade de estado do bot.

```csharp
using Microsoft.Bot.Builder.Dialogs;

public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<HotelBot>(options =>
    {
        //...

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, everything stored in memory will be gone.
        IStorage dataStore = new MemoryStorage();

        // Create conversation and user state objects.
        options.State.Add(new ConversationState(dataStore));
        options.State.Add(new UserState(dataStore));
    });

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        var userState = options.State.OfType<UserState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState, userState)
        {
            UserInfoAccessor = userState.CreateProperty<UserInfo>(BotAccessors.UserInfoAccessorName),
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(BotAccessors.DialogStateAccessorName),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Cada diálogo coletará algumas informações, e o manipulador de turnos ou o menu principal do bot salvará essas informações no estado do usuário. Vamos definir diálogos de componente para check-in, reserva de mesa e definição de despertador. Cada um retornará um objeto com propriedades adequadas. Vamos agregar essas propriedades no estado do usuário do bot.

No seu arquivo **bot.js**, atualize o construtor de bot para criar acessadores de propriedade de estado e acompanhar o estado do usuário e o estado do diálogo.

```javascript
// Define the identifiers for our state property accessors.
const DIALOG_STATE_PROPERTY = 'dialogStatePropertyAccessor';
const USER_INFO_PROPERTY = 'userInfoPropertyAccessor';

constructor(conversationState, userState) {
    // Record the conversation and user state management objects.
    this.conversationState = conversationState;
    this.userState = userState;

    // Create our state property accessors.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    this.userInfoAccessor = userState.createProperty(USER_INFO_PROPERTY);
}
```

No arquivo **index.js**, atualize as classes importadas da biblioteca `botbuilder` e o código que cria os objetos de estado e o bot:

```javascript
// Import required bot services.
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState } = require('botbuilder');
```

```javascript
// Define state store for your bot.
const memoryStorage = new MemoryStorage();

// Create conversation and user state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);

// Create the bot.
const myBot = new MyBot(conversationState, userState);
```

---

## <a name="about-component-dialogs"></a>Sobre os diálogos de componente

Com os diálogos de componente, é possível criar diálogos independentes para lidar com cenários específicos, como dividir um conjunto de diálogos grandes em partes mais gerenciáveis. Cada uma dessas partes tem seu próprio conjunto de diálogos e evita qualquer conflito de nome com o conjunto de diálogos que as contém.

Use o método _add dialog_ para adicionar diálogos e solicitações ao diálogo de componente.
O primeiro item que você adicionar com esse método será definido como o diálogo inicial, mas é possível alterar isso definindo explicitamente a propriedade `InitialDialogId` no construtor do diálogo de componente.
Ao iniciar um diálogo de componente, ele iniciará seu _diálogo inicial_.

## <a name="define-the-check-in-component-dialog"></a>Definir o diálogo do componente de check-in

Primeiramente, iniciaremos com um diálogo de check-in simples que perguntará ao usuário o nome e em qual quarto ficarão hospedados. Criamos uma classe `CheckInDialog` que estende `ComponentDialog`. Essa classe tem um construtor que define o nome do diálogo raiz, que definiremos como um diálogo de cascata com três etapas.

Estas são as etapas para o diálogo de check-in.

1. Pergunte o nome do hóspede.
1. Pergunte em qual quarto ele gostaria de ficar hospedado.
1. Envie uma mensagem de confirmação e conclua o diálogo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Adicione uma classe `CheckInDialog` ao projeto usando o código a seguir.

Ao longo desse diálogo, podemos gravar em um objeto de estado local, acessível por meio da propriedade `Values` do contexto da etapa. Quando o diálogo for concluído, o objeto de estado local será descartado. Portanto, devemos retornar um valor do diálogo que contém as informações de hóspede.

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

public class CheckInDialog : ComponentDialog
{
    private const string InitialId = "mainDialog";
    private const string GuestKey = nameof(CheckInDialog);
    private const string TextPrompt = "textPrompt";

    // You can start this from the parent using the ID assigned in the parent.
    public CheckInDialog(string id) : base(id)
    {
        InitialDialogId = InitialId;

        // Define the prompts used in this conversation flow.
        AddDialog(new TextPrompt(TextPrompt));

        // Define the conversation flow using a waterfall model.
        WaterfallStep[] waterfallSteps = new WaterfallStep[]
        {
            NameStepAsync,
            RoomStepAsync,
            FinalStepAsync,
        };
        AddDialog(new WaterfallDialog(InitialId, waterfallSteps));
    }

    private static async Task<DialogTurnResult> NameStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Clear the guest information and prompt for the guest's name.
        step.Values[GuestKey] = new GuestInfo();
        return await step.PromptAsync(
            TextPrompt,
            new PromptOptions
            {
                Prompt = MessageFactory.Text("What is your name?"),
            },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> RoomStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Save the name and prompt for the room number.
        string name = step.Result as string;
        ((GuestInfo)step.Values[GuestKey]).Name = name;
        return await step.PromptAsync(
            TextPrompt,
            new PromptOptions
            {
                Prompt = MessageFactory.Text($"Hi {name}. What room will you be staying in?"),
            },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> FinalStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Save the room number and "sign off".
        string room = step.Result as string;
        ((GuestInfo)step.Values[GuestKey]).Room = room;

        await step.Context.SendActivityAsync(
            "Great, enjoy your stay!",
            cancellationToken: cancellationToken);

        // End the dialog, returning the guest info.
        return await step.EndDialogAsync(
            (GuestInfo)step.Values[GuestKey],
            cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Crie um arquivo **checkInDialog.js** e adicione a ele uma classe `CheckInDialog` que estende `ComponentDialog`.

Ao longo desse diálogo, podemos gravar em um objeto de estado local, acessível por meio da propriedade `values` do contexto da etapa. Quando o diálogo for concluído, o objeto de estado local será descartado. Portanto, devemos retornar um valor do diálogo que contém as informações de hóspede.

```JavaScript
const { ComponentDialog, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

const initialId = 'mainDialog';

class CheckInDialog extends ComponentDialog {
    constructor(id) {
        super(id);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = initialId;

        // Define the prompts used in this conversation flow.
        this.addDialog(new TextPrompt('textPrompt'));

        // Define the conversation flow using a waterfall model.
        this.addDialog(new WaterfallDialog(initialId, [
            async function (step) {
                // Clear the guest information and prompt for the guest's name.
                step.values.guestInfo = {};
                return await step.prompt('textPrompt', "What is your name?");
            },
            async function (step) {
                // Save the name and prompt for the room number.
                step.values.guestInfo.userName = step.result;
                return await step.prompt('textPrompt', `Hi ${step.result}. What room will you be staying in?`);
            },
            async function (step) {
                // Save the room number and "sign off".
                step.values.guestInfo.roomNumber = step.result;
                await step.context.sendActivity(`Great! Enjoy your stay in room ${step.result}!`);

                // End the dialog, returning the guest info.
                return await step.endDialog(step.values.guestInfo);
            }
        ]));
    }
}

exports.CheckInDialog = CheckInDialog;
```

---

## <a name="define-the-reserve-table-and-wake-up-component-dialogs"></a>Definir os diálogos de componente de reserva de mesa e despertador

Uma vantagem de usar um diálogo de componente é a capacidade usar diálogos diferentes em conjunto. Como cada `DialogSet` mantém um conjunto exclusivo de `dialogs`, compartilhamento ou referência cruzada `dialogs` não pode ser feito facilmente. É aqui que entra o diálogo componente. Você pode encapsular aspectos complexos do fluxo da conversa nos diálogos de componente, o que pode facilitar a manutenção e o gerenciamento dos diálogos. Vamos criar os outros dois diálogos de componente: um para perguntar ao usuário qual mesa ele gostaria de reservar para o jantar e outro para criar um serviço de despertador. Novamente, usaremos uma classe separada para cada diálogo e cada diálogo estenderá o `ComponentDialog` principal.

Vamos projetá-los para aceitar as informações do hóspede nas opções do diálogo quando ele for iniciado.

Estas são as etapas para o diálogo de reserva de mesa.

1. Pergunte qual mesa reservar.
1. Envie uma mensagem de confirmação e conclua o diálogo, retornando o número da mesa.

Estas são as etapas para o diálogo de despertador.

1. Pergunte qual a hora definida para despertar.
1. Envie uma mensagem de confirmação e conclua o diálogo, retornando a hora do despertador.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Adicione uma classe `ReserveTableDialog` ao projeto usando o código a seguir.

Obteremos o nome do convidado do objeto de opções que é transmitido quando o diálogo é iniciado. Em seguida, vamos retornar um valor do diálogo; desta vez, o que contém o número da mesa.

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Choices;

public class ReserveTableDialog : ComponentDialog
{
    private const string InitialId = "mainDialog";
    private const string TablePrompt = "choicePrompt";

    public ReserveTableDialog(string id) : base(id)
    {
        InitialDialogId = InitialId;

        // Define the prompts used in this conversation flow.
        AddDialog(new ChoicePrompt(TablePrompt));

        // Define the conversation flow using a waterfall model.
        WaterfallStep[] waterfallSteps = new WaterfallStep[]
        {
                TableStepAsync,
                FinalStepAsync,
        };
        AddDialog(new WaterfallDialog(InitialId, waterfallSteps));
    }

    private static async Task<DialogTurnResult> TableStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        string greeting = step.Options is GuestInfo guest
                && !string.IsNullOrWhiteSpace(guest?.Name)
                ? $"Welcome {guest.Name}" : "Welcome";

        string prompt = $"{greeting}, How many diners will be at your table?";
        string[] choices = new string[] { "1", "2", "3", "4", "5", "6" };
        return await step.PromptAsync(
            TablePrompt,
            new PromptOptions
            {
                Prompt = MessageFactory.Text(prompt),
                Choices = ChoiceFactory.ToChoices(choices),
            },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> FinalStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // ChoicePrompt returns a FoundChoice object.
        string table = (step.Result as FoundChoice).Value;

        // Send a confirmation message.
        await step.Context.SendActivityAsync(
            $"Sounds great;  we will reserve a table for you for {table} diners.",
            cancellationToken: cancellationToken);

        // End the dialog, returning the table info.
        return await step.EndDialogAsync(
            new TableInfo { Number = table },
            cancellationToken);
    }
}
```

Adicione uma classe `SetAlarmDialog` ao projeto usando o código a seguir.

Vamos obter o número do quarto do hóspede do objeto de opções que é transmitido quando o diálogo é iniciado. Em seguida, vamos retornar um valor do diálogo, um que contenha a hora em que eles querem que o despertador seja definido.

```csharp
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

public class SetAlarmDialog : ComponentDialog
{
    private const string InitialId = "mainDialog";
    private const string AlarmPrompt = "dateTimePrompt";

    public SetAlarmDialog(string id) : base(id)
    {
        InitialDialogId = InitialId;

        // Define the prompts used in this conversation flow.
        // Ideally, we'd add validation to this prompt.
        AddDialog(new DateTimePrompt(AlarmPrompt));

        // Define the conversation flow using a waterfall model.
        WaterfallStep[] waterfallSteps = new WaterfallStep[]
        {
                AlarmStepAsync,
                FinalStepAsync,
        };

        AddDialog(new WaterfallDialog(InitialId, waterfallSteps));
    }

    private static async Task<DialogTurnResult> AlarmStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        string greeting = step.Options is GuestInfo guest
                && !string.IsNullOrWhiteSpace(guest?.Name)
                ? $"Hi {guest.Name}" : "Hi";

        string prompt = $"{greeting}. When would you like your alarm set for?";
        return await step.PromptAsync(
            AlarmPrompt,
            new PromptOptions { Prompt = MessageFactory.Text(prompt) },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> FinalStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Ambiguous responses can generate multiple results.
        var resolution = (step.Result as IList<DateTimeResolution>)?.FirstOrDefault();

        // Time ranges have a start and no value.
        var alarm = resolution.Value ?? resolution.Start;
        string roomNumber = (step.Options as GuestInfo)?.Room;

        // Send a confirmation message.
        await step.Context.SendActivityAsync(
            $"Your alarm is set to {alarm} for room {roomNumber}.",
            cancellationToken: cancellationToken);

        // End the dialog, returning the alarm info.
        return await step.EndDialogAsync(
            new WakeUpInfo { Time = alarm },
            cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Crie um arquivo **reserveTableDialog.js** e adicione a ele uma classe `ReserveTableDialog` que estende `ComponentDialog`.

Obteremos o nome do convidado do objeto de opções que é transmitido quando o diálogo é iniciado. Em seguida, vamos retornar um valor do diálogo; desta vez, o que contém o número da mesa.

```JavaScript
const { ComponentDialog, ChoicePrompt, WaterfallDialog } = require('botbuilder-dialogs');

const initialId = 'mainDialog';

class ReserveTableDialog extends ComponentDialog {
    constructor(id) {
        super(id);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = initialId;

        // Define the prompts used in this conversation flow.
        this.addDialog(new ChoicePrompt('choicePrompt'));

        // Define the conversation flow using a waterfall model.
        this.addDialog(new WaterfallDialog(initialId, [
            async function (step) {
                // Welcome the user and ask for their table preference.
                const greeting = step.options && step.options.userName ? `Welcome ${step.options.userName}` : `Welcome`;

                const promptOptions = {
                    prompt: `${greeting}, How many diners will be at your table?`,
                    reprompt: 'That was not a valid choice, please select a table size between 1 and 6 guests.',
                    choices: ['1', '2', '3', '4', '5', '6']
                };
                return await step.prompt('choicePrompt', promptOptions);
            },
            async function (step) {
                const choice = step.result;

                // Send a confirmation message.
                const tableNumber = choice.value;
                await step.context.sendActivity(`Sounds great, we will reserve a table for you for ${tableNumber} diners.`);

                // End the dialog, returning the table info.
                return await step.endDialog({ table: tableNumber });
            }
        ]));
    }
}

exports.ReserveTableDialog = ReserveTableDialog;
```

Crie um arquivo **setAlarmDialog.js** e adicione a ele uma classe `SetAlarmDialog` que estende `ComponentDialog`.

Vamos obter o número do quarto do hóspede do objeto de opções que é transmitido quando o diálogo é iniciado. Em seguida, vamos retornar um valor do diálogo, um que contenha a hora em que eles querem que o despertador seja definido.

```JavaScript
const { ComponentDialog, DateTimePrompt, WaterfallDialog } = require('botbuilder-dialogs');

const initialId = 'mainDialog';

class SetAlarmDialog extends ComponentDialog {
    constructor(id) {
        super(id);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = initialId;

        // Define the prompts used in this conversation flow.
        this.addDialog(new DateTimePrompt('datePrompt'));

        this.addDialog(new WaterfallDialog(initialId, [
            async function (step) {
                step.values.wakeUp = {};
                if (step.options && step.options.roomNumber) {
                    step.values.roomNumber = step.options.roomNumber;
                }

                const greeting = step.options && step.options.userName ? `Hi ${step.options.userName}` : `Hi`;
                return await step.prompt('datePrompt', `${greeting}. What time would you like your alarm to be set?`);
            },
            async function (step) {
                // Ambiguous responses can generate multiple results.
                const resolution = step.result[0];

                // Time ranges have a start and no value.
                const alarm = resolution.value ? resolution.value : resolution.start;
                const roomNumber = step.values.roomNumber;

                // Send a confirmation message.
                await step.context.sendActivity(`Your alarm is set to ${alarm} for room ${roomNumber}.`);

                // End the dialog, returning the alarm info.
                return await step.endDialog({ alarm: alarm });
            }]));

        // Defining the prompt used in this conversation flow
    }
}

exports.SetAlarmDialog = SetAlarmDialog;
```

---

## <a name="add-the-component-dialogs-to-the-bot"></a>Adicionar os diálogos de componente ao bot

Agora que definimos nossos três diálogos de componente, podemos usá-los em nosso bot.

- Todos os três diálogos são adicionados ao conjunto de diálogos principal do bot.
- Quando uma nova conversa inicia, não há um diálogo ativo e a lógica do bot assume a lógica.
- Se não temos informações do hóspede para o usuário, vamos iniciar o diálogo de check-in.
- Assim que tivermos informações do hóspede, o diálogo principal assume, permitindo várias vezes ao usuário iniciar o diálogo de reserva de mesa ou de despertador.

Vamos atualizar a lógica do manipulador de turnos do bot.

1. Obtenha o status do usuário.
1. Continue com os diálogos ativos.
1. Se um diálogo concluiu seu turno, deve ser o diálogo de check-in.
   1. Armazene as informações do hóspede.
   1. Inicie o diálogo principal.
1. Se o bot ainda não enviou uma mensagem para o usuário, não há nenhum diálogo ativo em execução.
    1. Se não temos informações do hóspede, inicie o diálogo de check-in.
    1. Caso contrário, inicie o diálogo principal.
1. Salve as alterações de estado que possam ter ocorrido.

Estas são as etapas para o diálogo principal.

1. Pergunte ao hóspede o que ele gostaria de fazer: reservar uma mesa ou definir hora para despertar.
1. Inicie o diálogo filho correspondente ou envie uma mensagem de _entrada não entendida_ e reinicie na primeira etapa.
1. Processe o valor retornado do diálogo filho e reinicie o diálogo principal.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No arquivo **HotelBot.cs**, atualize as instruções using.

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Schema;
using Microsoft.Extensions.Logging;
```

Atualize o código de inicialização e defina o conjunto de diálogos e o diálogo principal do bot.

```csharp
// Define the IDs for the dialogs in the bot's dialog set.
private const string MainDialogId = "mainDialog";
private const string CheckInDialogId = "checkInDialog";
private const string TableDialogId = "tableDialog";
private const string AlarmDialogId = "alarmDialog";

// Define the dialog set for the bot.
private readonly DialogSet _dialogs;

// Define the state accessors and the logger for the bot.
private readonly BotAccessors _accessors;
private readonly ILogger _logger;

/// <summary>
/// Initializes a new instance of the <see cref="HotelBot"/> class.
/// </summary>
/// <param name="accessors">Contains the objects to use to manage state.</param>
/// <param name="loggerFactory">A <see cref="ILoggerFactory"/> that is hooked to the Azure App Service provider.</param>
public HotelBot(BotAccessors accessors, ILoggerFactory loggerFactory)
{
    if (loggerFactory == null)
    {
        throw new System.ArgumentNullException(nameof(loggerFactory));
    }

    _logger = loggerFactory.CreateLogger<HotelBot>();
    _logger.LogTrace($"{nameof(HotelBot)} turn start.");
    _accessors = accessors ?? throw new System.ArgumentNullException(nameof(accessors));

    // Define the steps of the main dialog.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        MenuStepAsync,
        HandleChoiceAsync,
        LoopBackAsync,
    };

    // Create our bot's dialog set, adding a main dialog and the three component dialogs.
    _dialogs = new DialogSet(_accessors.DialogStateAccessor)
        .Add(new WaterfallDialog(MainDialogId, steps))
        .Add(new CheckInDialog(CheckInDialogId))
        .Add(new ReserveTableDialog(TableDialogId))
        .Add(new SetAlarmDialog(AlarmDialogId));
}

private static async Task<DialogTurnResult> MenuStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Present the user with a set of "suggested actions".
    List<string> menu = new List<string> { "Reserve Table", "Wake Up" };
    await stepContext.Context.SendActivityAsync(
        MessageFactory.SuggestedActions(menu, "How can I help you?"),
        cancellationToken: cancellationToken);
    return Dialog.EndOfTurn;
}

private async Task<DialogTurnResult> HandleChoiceAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Get the user's info. (Since the type factory is null, this will throw if state does not yet have a value for user info.)
    UserInfo userInfo = await _accessors.UserInfoAccessor.GetAsync(stepContext.Context, null, cancellationToken);

    // Check the user's input and decide which dialog to start.
    // Pass in the guest info when starting either of the child dialogs.
    string choice = (stepContext.Result as string)?.Trim()?.ToLowerInvariant();
    switch (choice)
    {
        case "reserve table":
            return await stepContext.BeginDialogAsync(TableDialogId, userInfo.Guest, cancellationToken);

        case "wake up":
            return await stepContext.BeginDialogAsync(AlarmDialogId, userInfo.Guest, cancellationToken);

        default:
            // If we don't recognize the user's intent, start again from the beginning.
            await stepContext.Context.SendActivityAsync(
                "Sorry, I don't understand that command. Please choose an option from the list.");
            return await stepContext.ReplaceDialogAsync(MainDialogId, null, cancellationToken);
    }
}

private async Task<DialogTurnResult> LoopBackAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Get the user's info. (Because the type factory is null, this will throw if state does not yet have a value for user info.)
    UserInfo userInfo = await _accessors.UserInfoAccessor.GetAsync(stepContext.Context, null, cancellationToken);

    // Process the return value from the child dialog.
    switch (stepContext.Result)
    {
        case TableInfo table:
            // Store the results of the reserve-table dialog.
            userInfo.Table = table;
            await _accessors.UserInfoAccessor.SetAsync(stepContext.Context, userInfo, cancellationToken);
            break;
        case WakeUpInfo alarm:
            // Store the results of the set-wake-up-call dialog.
            userInfo.WakeUp = alarm;
            await _accessors.UserInfoAccessor.SetAsync(stepContext.Context, userInfo, cancellationToken);
            break;
        default:
            // We shouldn't get here, since these are no other branches that get this far.
            break;
    }

    // Restart the main menu dialog.
    return await stepContext.ReplaceDialogAsync(MainDialogId, null, cancellationToken);
}
```

Além disso, atualize o manipulador de turnos do bot para usar o conjunto de diálogos.

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Establish dialog state from the conversation state.
        DialogContext dc = await _dialogs.CreateContextAsync(turnContext, cancellationToken);

        // Get the user's info.
        UserInfo userInfo = await _accessors.UserInfoAccessor.GetAsync(turnContext, () => new UserInfo(), cancellationToken);

        // Continue any current dialog.
        DialogTurnResult dialogTurnResult = await dc.ContinueDialogAsync();

        // Process the result of any complete dialog.
        if (dialogTurnResult.Status is DialogTurnStatus.Complete)
        {
            switch (dialogTurnResult.Result)
            {
                case GuestInfo guestInfo:
                    // Store the results of the check-in dialog.
                    userInfo.Guest = guestInfo;
                    await _accessors.UserInfoAccessor.SetAsync(turnContext, userInfo, cancellationToken);
                    break;
                default:
                    // We shouldn't get here, since the main dialog is designed to loop.
                    break;
            }
        }

        // Every dialog step sends a response, so if no response was sent,
        // then no dialog is currently active.
        else if (!turnContext.Responded)
        {
            if (string.IsNullOrEmpty(userInfo.Guest?.Name))
            {
                // If we don't yet have the guest's info, start the check-in dialog.
                await dc.BeginDialogAsync(CheckInDialogId, null, cancellationToken);
            }
            else
            {
                // Otherwise, start our bot's main dialog.
                await dc.BeginDialogAsync(MainDialogId, null, cancellationToken);
            }
        }

        // Save the new turn count into the conversation state.
        await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
        await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
    }
    else
    {
        await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Em nosso arquivo de bot, **bot.js**, precisamos importar não apenas as classes que usaremos do SDK, mas também as classes que definimos para nossos diálogos de componente.

```JavaScript
const { ActivityTypes, MessageFactory } = require('botbuilder');
const { DialogSet, WaterfallDialog, Dialog, DialogTurnStatus } = require('botbuilder-dialogs');

// Import our component dialogs.
const { CheckInDialog } = require("./checkInDialog");
const { ReserveTableDialog } = require("./reserveTableDialog");
const { SetAlarmDialog } = require("./setAlarmDialog");
```

Também precisaremos criar um conjunto de diálogos e adicionar todos os diálogos que usaremos a ele.

Estamos definindo as etapas da cascata do diálogo principal como funções na classe em vez de na linha. Estamos usando `bind()` nessas funções para que dentro da função, `this` seja resolvido corretamente.

Aqui está nosso construtor de bot atualizado.

```JavaScript
constructor(conversationState, userState) {
    // Record the conversation and user state management objects.
    this.conversationState = conversationState;
    this.userState = userState;

    // Create our state property accessors.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    this.userInfoAccessor = userState.createProperty(USER_INFO_PROPERTY);

    // Create our bot's dialog set, adding a main dialog and the three component dialogs.
    this.dialogs = new DialogSet(this.dialogStateAccessor)
        .add(new CheckInDialog('checkInDialog'))
        .add(new ReserveTableDialog('reserveTableDialog'))
        .add(new SetAlarmDialog('setAlarmDialog'))
        .add(new WaterfallDialog('mainDialog', [
            this.promptForChoice.bind(this),
            this.startChildDialog.bind(this),
            this.saveResult.bind(this)
    ]));
}
```

Abaixo do construtor de bot, adicione o código a seguir que implementa as etapas para o diálogo principal.

```JavaScript
async promptForChoice(step) {
    const menu = ["Reserve Table", "Wake Up"];
    await step.context.sendActivity(MessageFactory.suggestedActions(menu, 'How can I help you?'));
    return Dialog.EndOfTurn;
}

async startChildDialog(step) {
    // Get the user's info.
    const user = await this.userInfoAccessor.get(step.context);
    // Check the user's input and decide which dialog to start.
    // Pass in the guest info when starting either of the child dialogs.
    switch (step.result) {
        case "Reserve Table":
            return await step.beginDialog('reserveTableDialog', user.guestInfo);
            break;
        case "Wake Up":
            return await step.beginDialog('setAlarmDialog', user.guestInfo);
            break;
        default:
            await step.context.sendActivity("Sorry, I don't understand that command. Please choose an option from the list.");
            return await step.replaceDialog('mainDialog');
            break;
    }
}

async saveResult(step) {
    // Process the return value from the child dialog.
    if (step.result) {
        const user = await this.userInfoAccessor.get(step.context);
        if (step.result.table) {
            // Store the results of the reserve-table dialog.
            user.table = step.result.table;
        } else if (step.result.alarm) {
            // Store the results of the set-wake-up-call dialog.
            user.alarm = step.result.alarm;
        }
        await this.userInfoAccessor.set(step.context, user);
    }
    // Restart the main menu dialog.
    return await step.replaceDialog('mainDialog'); // Show the menu again
}
```

E, agora, atualize o manipulador de turnos do bot:

```JavaScript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        const user = await this.userInfoAccessor.get(turnContext, {});
        const dc = await this.dialogs.createContext(turnContext);
        const dialogTurnResult = await dc.continueDialog();
        if (dialogTurnResult.status === DialogTurnStatus.complete) {
            user.guestInfo = dialogTurnResult.result;
            await this.userInfoAccessor.set(turnContext, user);
            await dc.beginDialog('mainDialog');
        } else if (!turnContext.responded) {
            if (!user.guestInfo) {
                await dc.beginDialog('checkInDialog');
            } else {
                await dc.beginDialog('mainDialog');
            }
        }
        // Save state changes
        await this.conversationState.saveChanges(turnContext);
        await this.userState.saveChanges(turnContext);
    }
}
```

---

Como é possível constatar, os diálogos de componentes são adicionados ao diálogo principal do bot de uma forma semelhante a como adicionar [prompts](bot-builder-prompts.md) a um diálogo. Você pode adicionar tantos diálogos filho ao diálogo principal quanto desejar. Cada módulo adicionaria recursos e serviços adicionais que o bot pode oferecer aos usuários.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como usar diálogos de componente, veremos como usar o LUIS (Serviço Inteligente de Reconhecimento Vocal) para ajudar o bot a decidir quando iniciar os diálogos.

> [!div class="nextstepaction"]
> [Usar o LUIS para Reconhecimento Vocal](./bot-builder-howto-v4-luis.md)
