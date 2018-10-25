---
title: Solicitar informações dos usuários usando a biblioteca de Diálogos | Microsoft Docs
description: Saiba como solicitar informações dos usuários usando a biblioteca Diálogos no SDK do Construtor de Bot para Node.js.
keywords: prompts, diálogos, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, reprompt, validação
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 9/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 16ef274bc7e8301825e574c566a49d53f01115c1
ms.sourcegitcommit: aef7d80ceb9c3ec1cfb40131709a714c42960965
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49383121"
---
# <a name="prompt-users-for-input-using-the-dialogs-library"></a>Solicitar informações dos usuários usando a biblioteca Diálogos

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A coleta de informações através da postagem de perguntas é uma das principais formas de um bot interagir com os usuários. É possível fazer isso diretamente usando o método _enviar atividade_ do objeto [contexto do turno](~/v4sdk/bot-builder-basics.md#defining-a-turn) e, em seguida, processar a mensagem recebida seguinte como a resposta. No entanto, o SDK do Bot Builder fornece uma biblioteca de **diálogos** que disponibiliza métodos projetados para facilitar a realização de perguntas e para garantir que a resposta corresponda a um tipo de dado específico ou atenda às regras de validação personalizadas. Este tópico fornece detalhes sobre como usar **solicitações** para pedir informações a um usuário.

Este artigo descreve como usar prompts dentro de uma caixa de diálogo. Para saber mais sobre como usar diálogos em geral, veja [como usar os diálogos para gerenciar o fluxo da conversa simples](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prompt-types"></a>Tipos de prompt

A biblioteca de diálogos oferece uma série de tipos de prompts diferentes e cada um deles é usado para coletar um tipo de resposta.

| Prompt | DESCRIÇÃO |
|:----|:----|
| **AttachmentPrompt** | Solicitar ao usuário um anexo, como um documento ou uma imagem. |
| **ChoicePrompt** | Solicita que o usuário para escolher um conjunto de opções. |
| **ConfirmPrompt** | Solicita que o usuário confirme sua ação. |
| **DatetimePrompt** | Solicita ao usuário para uma data e hora. Os usuários podem responder usando linguagem natural como "Amanhã às 20h" ou "Sexta-feira às 10h". O SDK do Bot Framework usa a entidade pré-construída `builtin.datetimeV2`LUIS. Para obter mais informações, consulte [builtin.datetimev2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-entities#builtindatetimev2). |
| **NumberPrompt** | Solicitar ao usuário um número. O usuário pode responder com "10" ou "dez". Se a resposta for "dez", por exemplo, o prompt converterá a resposta em um número e retornará `10` como resultado. |
| **TextPrompt** | Solicitar ao usuário uma sequência de texto. |

## <a name="add-references-to-prompt-library"></a>Adicione referências para solicitar a biblioteca

Para obter a biblioteca de **diálogos**, adicione o pacote de **botbuilder-dialogs** ao bot. Nós abordamos os diálogos em [como usar diálogos para gerenciar o fluxo de conversas simples](bot-builder-dialog-manage-conversation-flow.md), mas usaremos diálogos para nossos prompts.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Instale o pacote **Microsoft.Bot.Builder.Dialogs** do NuGet.

Em seguida, inclua a referência à biblioteca no seu código do bot.

```cs
using Microsoft.Bot.Builder.Dialogs;
```

Configure o estado do diálogo da conversa usando acessadores. Não nos aprofundaremos muito neste código, mas é possível obter mais informações no artigo sobre o [estado](bot-builder-howto-v4-state.md).

Nas opções de bot em **Startup.cs**, primeiro defina seus objetos de estado e adicione o singleton para fornecer a classe de acessador ao construtor de bot. A classe do `BotAccessor` apenas armazena o estado da conversa e do usuário, juntamente com acessadores para cada um desses itens. A definição completa de classe é fornecida no exemplo vinculado no final deste artigo. 

```cs
    services.AddBot<MultiTurnPromptsBot>(options =>
    {
        InitCredentialProvider(options);

        // Create and add conversation state.
        var convoState = new ConversationState(dataStore);
        options.State.Add(convoState);

        // Create and add user state.
        var userState = new UserState(dataStore);
        options.State.Add(userState);
    });

    services.AddSingleton(sp =>
    {
        // We need to grab the conversationState we added on the options in the previous step
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        if (options == null)
        {
            throw new InvalidOperationException("BotFrameworkOptions must be configured prior to setting up the State Accessors");
        }

        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        if (conversationState == null)
        {
            throw new InvalidOperationException("ConversationState must be defined and added before adding conversation-scoped state accessors.");
        }

        var userState = options.State.OfType<UserState>().FirstOrDefault();
        if (userState == null)
        {
            throw new InvalidOperationException("UserState must be defined and added before adding user-scoped state accessors.");
        }

        // The dialogs will need a state store accessor. Creating it here once (on-demand) allows the dependency injection
        // to hand it to our IBot class that is create per-request.
        var accessors = new BotAccessors(conversationState, userState)
        {
            ConversationDialogState = conversationState.CreateProperty<DialogState>("DialogState"),
            UserProfile = userState.CreateProperty<UserProfile>("UserProfile"),
        };

        return accessors;
    });
```

Em seguida, no código do bot, defina os seguintes objetos para o conjunto de diálogos.

```cs
    private readonly BotAccessors _accessors;

    /// <summary>
    /// The <see cref="DialogSet"/> that contains all the Dialogs that can be used at runtime.
    /// </summary>
    private DialogSet _dialogs;

    /// <summary>
    /// Initializes a new instance of the <see cref="MultiTurnPromptsBot"/> class.
    /// </summary>
    /// <param name="accessors">A class containing <see cref="IStatePropertyAccessor{T}"/> used to manage state.</param>
    public MultiTurnPromptsBot(BotAccessors accessors)
    {
        _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

        // The DialogSet needs a DialogState accessor, it will call it when it has a turn context.
        _dialogs = new DialogSet(accessors.ConversationDialogState);

        // ...
        // other constructor items
        // ...
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Crie um bot em JavaScript usando o modelo de eco. Para saber mais, confira o [Início rápido do JavaScript](../javascript/bot-builder-javascript-quickstart.md).

Instale o pacote de diálogo do npm:

```cmd
npm install --save botbuilder-dialogs
```

Para usar **diálogos** no seu bot, inclua-o no código do bot.

1. No arquivo de seu **bot.js**, adicione o seguinte.

    ```javascript
    // Import components from the dialogs library.
    const { DialogSet, TextPrompt, WaterfallDialog } = require("botbuilder-dialogs");

    // Name for the dialog state property accessor.
    const DIALOG_STATE_PROPERTY = 'dialogState';

    // Define the names for the prompts and dialogs for the dialog set.
    const TEXT_PROMPT = 'textPrompt';
    const MAIN_DIALOG = 'mainDialog';
    ```

    O _conjunto de diálogos_ conterá os diálogos para este bot, e usaremos o _prompt de texto_ para pedir a entrada ao usuário. Também será necessário um acessador de propriedade de estado de diálogo que o conjunto de diálogos pode usar para controlar seu estado.

1. atualize o código do construtor do seu bot. Adicionaremos mais sobre isso em breve.

    ```javascript
      constructor(conversationState) {
        // Track the conversation state object.
        this.conversationState = conversationState;

        // Create a state property accessor for the dialog set.
        this.dialogState = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    }
    ```

---

## <a name="prompt-the-user"></a>Solicitar que o usuário

Para solicitar uma entrada ao usuário, defina um prompt usando uma das classes internas, como **TextPrompt**, adicione-o ao seu conjunto de diálogos e atribua a ele uma ID de diálogo.

Depois que um prompt é adicionado, use-o em um diálogo em cascata de duas etapas. Um diálogo de *cascata* é uma maneira de definir uma sequência de etapas. Vários prompts podem ser encadeados juntos para criar conversas de várias etapas. Para saber mais, veja a seção [como usar diálogos](bot-builder-dialog-manage-conversation-flow.md#using-dialogs-to-guide-the-user-through-steps) de [gerenciar o fluxo de conversas simples com diálogos](bot-builder-dialog-manage-conversation-flow.md).

Por exemplo, o seguinte diálogo solicita ao usuário seu nome e, em seguida, usa a resposta para cumprimentá-lo. Na primeira vez, o diálogo solicita o nome ao usuário. A resposta do usuário é passada como um parâmetro para a segunda função da etapa que processa a entrada e envia a saudação personalizada.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Cada solicitação que você usa na sua caixa de diálogo também recebe um nome, usado pela caixa de diálogo ou pelo seu bot para acessar o prompt. Em todos esses exemplos, estamos expondo os IDs de prompt como constantes.

No construtor do bot, adicione definições para sua cascata de duas etapas e o prompt para o diálogo que será usado. Aqui estamos adicionando-os como funções independentes, mas eles podem, caso você prefira, ser definidos como um lambda embutido.

```csharp
 public MultiTurnPromptsBot(BotAccessors accessors)
{
    _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

    // The DialogSet needs a DialogState accessor, it will call it when it has a turn context.
    _dialogs = new DialogSet(accessors.ConversationDialogState);

    // This array defines how the Waterfall will execute.
    var waterfallSteps = new WaterfallStep[]
    {
        NameStepAsync,
        SayHiAsync,
    };

    _dialogs.Add(new WaterfallDialog("details", waterfallSteps));
    _dialogs.Add(new TextPrompt("name"));
}
```

Em seguida, defina as duas etapas de cascata em seu bot. No caso do prompt de texto, você está especificando a ID do *nome* do `TextPrompt` definido acima. Observe que os nomes dos métodos correspondem aos nomes da `WaterfallStep[]` acima. Exemplos futuros mostrados aqui não incluirão esse código, mas, para ver mais etapas, você precisará adicionar o nome do método na ordem correta em `WaterfallStep[]`.

```cs
    private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
        // Running a prompt here means the next WaterfallStep will be run when the users response is received.
        return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
    }

    private static async Task<DialogTurnResult> SayHiAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync($"Hi {stepContext.Result}");

        return await stepContext.EndDialogAsync(cancellationToken);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. No construtor do bot, criar o conjunto de diálogos e adicione um prompt de texto e um diálogo em cascata.

    ```javascript
    // Create the dialog set, and add the prompt and the waterfall dialog.
    this.dialogs = new DialogSet(this.dialogState)
        .add(new TextPrompt(TEXT_PROMPT))
        .add(new WaterfallDialog(MAIN_DIALOG, [
            async (step) => {
                // The results of this prompt will be passed to the next step.
                return await step.prompt(TEXT_PROMPT, 'What is your name?');
            },
            async (step) => {
                // The result property contains the result from the previous step.
                const userName = step.result;
                await step.context.sendActivity(`Hi ${userName}!`);
                return await step.endDialog();
            }
        ]));
    ```

1. Atualize o manipulador de turnos do bot para executar o diálogo.

    ```javascript
    async onTurn(turnContext) {
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        if (turnContext.activity.type === ActivityTypes.Message) {
            // Create a dialog context for the dialog set.
            const dc = await this.dialogs.createContext(turnContext);
            // Continue the dialog if it's active.
            await dc.continueDialog();
            if (!turnContext.responded) {
                // Otherwise, start the dialog.
                await dc.beginDialog(MAIN_DIALOG);
            }
        } else {
            // Send a default message for activity types that we don't handle.
            await turnContext.sendActivity(`[${turnContext.activity.type} event detected]`);
        }
        // Save state changes
        await this.conversationState.saveChanges(turnContext);
        }
    }
    ```

---

> [!NOTE]
> Para iniciar um diálogo, obtenha um contexto de diálogo e use seu método _começar diálogo_. Para saber mais, veja [usar diálogos para gerenciar o fluxo de conversa simples](./bot-builder-dialog-manage-conversation-flow.md).

## <a name="reusable-prompts"></a>Prompts reutilizáveis

Um prompt pode ser reutilizado para fazer perguntas diferentes desde que as respostas sejam do mesmo tipo. Por exemplo, o código de exemplo acima definiu um prompt de texto e o usou para perguntar ao usuário seu nome. Você também pode usar o mesmo prompt para pedir ao usuário outra cadeia de caracteres de texto como "Onde você trabalha?".

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No exemplo, a ID do prompt de texto, *nome*, não é útil para a legibilidade do código. No entanto, o fato de sua ID de solicitação poder ser tudo o que você escolher é um bom exemplo.

Agora, nossos métodos incluem um terceiro passo para perguntar onde nosso usuário trabalha.

```cs
    private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
        // Running a prompt here means the next WaterfallStep will be run when the users response is received.
        return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
    }

    private static async Task<DialogTurnResult> WorkAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync($"Hi {stepContext.Result}!");

        return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Where do you work?") }, cancellationToken);
    }

    private static async Task<DialogTurnResult> SayHiAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync($"{stepContext.Result} is a cool place!");

        return await stepContext.EndDialogAsync(cancellationToken);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor do bot, modifique a cascata para fazer uma segunda pergunta.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new TextPrompt(TEXT_PROMPT))
    .add(new WaterfallDialog(MAIN_DIALOG, [
    async (step) => {
        // Ask the user for their name.
        return await step.prompt(TEXT_PROMPT, 'What is your name?');
    },
    async (step) => {
        // Acknowledge their response and ask for their place of work.
        const userName = step.result;
        return await step.prompt(TEXT_PROMPT, `Hi ${userName}; where do you work?`);
    },
    async (step) => {
        // Acknowledge their response and exit the dialog.
        const workPlace = step.result;
        await step.context.sendActivity(`${workPlace} is a cool place!`);
        return await step.endDialog();
    }
    ]));
```

---

Se você precisar usar vários prompts diferentes, ofereça um *dialogId* exclusivo para cada um deles. Cada caixa de diálogo ou prompt adicionado a um conjunto de diálogos precisa de uma ID exclusiva. Você também pode criar vários diálogos do **prompt** do mesmo tipo. Por exemplo, você poderia criar dois diálogos **TextPrompt** para o exemplo acima:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
_dialogs.Add(new WaterfallDialog("details", waterfallSteps));
_dialogs.Add(new TextPrompt("name"));
_dialogs.Add(new TextPrompt("workplace"));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Por exemplo, você poderia substituir isto.

```javascript
.add(new TextPrompt(TEXT_PROMPT))
```

Pelo seguinte.

```javascript
.add(new TextPrompt('namePrompt'))
.add(new TextPrompt('workPlacePrompt'))
```

E, em seguida, atualize as etapas em cascata respectivas para usar esses prompts por seus respectivos nomes.

---

Por uma questão de reusabilidade de código, a definição de um único `TextPrompt` funcionaria para todos esses prompts porque eles pedem um texto como uma resposta. A capacidade de nomear diálogos é útil quando você precisa aplicar regras de validação diferentes à entrada dos prompts. Vamos dar uma olhada em como você pode validar as respostas do prompt usando um `NumberPrompt`.

## <a name="specify-prompt-options"></a>Especificar opções de prompt

Quando você usa um prompt em uma etapa da caixa de diálogo, também pode fornecer opções de prompt, como uma string de nova solicitação.

Especificar uma string de nova solicitação é útil quando a entrada do usuário pode falhar em satisfazer um prompt, seja porque está em um formato que o prompt não pode analisar, como "amanhã" para um prompt numérico, ou a entrada falha em um critério de validação. O prompt de número pode interpretar uma ampla variedade de entradas, como "doze" ou "um quarto", além de "12" e "0,25".

A localidade é um parâmetro opcional em determinados prompts, como **NumberPrompt**. Apesar de não ser necessário, isso pode ajudar o prompt a analisar a entrada com maior precisão.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O código a seguir adicionaria um prompt numérico a um conjunto de diálogos existente, **_dialogs**.

```csharp
_dialogs.Add(new NumberPrompt<int>("age"));
```

Em uma etapa da caixa de diálogo, o código a seguir solicitaria ao usuário uma entrada e forneceria uma string de aviso para ser usada se a entrada não puder ser interpretada como um número.

```csharp
return await stepContext.PromptAsync(
    "age",
    new PromptOptions {
        Prompt = MessageFactory.Text("Please enter your age."),
        RetryPrompt = MessageFactory.Text("I didn't get that. Please enter a valid age."),
    },
    cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Importe a classe `NumberPrompt` da biblioteca de diálogos.

```javascript
const { NumberPrompt } = require("botbuilder-dialogs");
```

Use o prompt de número em sua cascata de diálogos, especificando as cadeias de caracteres inicial e de nova tentativa.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new NumberPrompt('partySize'))
    .add(new WaterfallDialog(MAIN_DIALOG, [
    async (step) => {
        // Ask the user for their party size.
        return await step.prompt('partySize', {
            prompt: 'How many people in your party?',
            retryPrompt: 'Sorry, please specify the number of people in your party.'
        });
    },
    async (step) => {
        // Acknowledge their response and exit the dialog.
        const partySize = step.result;
        await step.context.sendActivity(`That's a party of ${partySize}, thanks.`);
        return await step.endDialog();
    }
]));
```

---

O prompt de escolha possui um parâmetro adicional necessário: a lista de opções disponíveis para o usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Quando usamos o **ChoicePrompt** para pedir ao usuário que escolha entre um conjunto de opções, temos que fornecer o prompt com esse conjunto de opções, fornecido dentro de um objeto **PromptOptions**. Aqui, usamos o **ChoiceFactory** para converter uma lista de opções em um formato apropriado.

```csharp
private static async Task<DialogTurnResult> FavoriteColorAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    await stepContext.Context.SendActivityAsync($"Hi {stepContext.Result}!");

    return await stepContext.PromptAsync(
        "color",
        new PromptOptions {
            Prompt = MessageFactory.Text("What's your favorite color?"),
            Choices = ChoiceFactory.ToChoices(new List<string> { "blue", "green", "red" }),
        },
        cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Importe a classe `NumberPrompt` da biblioteca de diálogos.

```javascript
const { ChoicePrompt } = require("botbuilder-dialogs");
```

Use o prompt de opções em sua cascata de diálogos, especificando as opções disponíveis.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
const list = ['green', 'blue', 'red', 'yellow'];
this.dialogs = new DialogSet(this.dialogState)
    .add(new ChoicePrompt('choicePrompt'))
    .add(new WaterfallDialog(MAIN_DIALOG, [
    async (step) => {
        // Ask the user for their party size.
        return await step.prompt('choicePrompt', {
            prompt: 'Please choose a color:',
            retryPrompt: 'Sorry, please choose a color from the list.',
            choices: list
        });
    },
    async (step) => {
        // Acknowledge their response and exit the dialog.
        const choice = step.result;
        await step.context.sendActivity(`That's ${choice.value}, thanks.`);
        return await step.endDialog();
    }
]));
```

---

## <a name="validate-a-prompt-response"></a>Validar uma resposta imediata

Você pode validar uma resposta do prompt antes de retornar o valor para a próxima etapa da **cascata**. Por exemplo, para validar um **NumberPrompt** em um intervalo de números entre **6** e **20**, você incluiria uma função de validação semelhante a esta:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Alterar quando o prompt é adicionado ao conjunto de diálogos para incluir a função de validador

```cs
_dialogs.Add(new NumberPrompt<int>("partySize", PartySizeValidatorAsync));
```

Em seguida, a validação é definida como seu próprio método, indicando true ou false, dependendo se ela passou na validação ou não. Se o retorno for false, será feita uma nova solicitação ao usuário.

```cs
private Task<bool> PartySizeValidatorAsync(PromptValidatorContext<int> promptContext, CancellationToken cancellationToken)
{
    var result = promptContext.Recognized.Value;

    if (result < 6 || result > 20)
    {
        return Task.FromResult(false);
    }

    return Task.FromResult(true);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione um método de validação ao criar o prompt.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new NumberPrompt('partySizePrompt', async (promptContext) =>                                                 {
        // Check to make sure a value was recognized.
        if (promptContext.recognized.succeeded) {
            const value = promptContext.recognized.value;
            try {
                if (value < 6) {
                    throw new Error('Party size too small.');
                } else if (value > 20) {
                    throw new Error('Party size too big.')
                } else {
                    return true; // Indicate that this is a valid value.
                }
            } catch (err) {
                await promptContext.context.sendActivity(`${err.message} <br/>Please provide a valid number between 6 and 20.`);
                return false; // Indicate that this is invalid.
            }
        } else {
            return false;
        }
    }))
    .add(new WaterfallDialog(MAIN_DIALOG, [
        async (step) => {
            // Ask the user for their party size.
            return await step.prompt('partySizePrompt', {
                prompt: 'How large is your party?',
                retryPrompt: 'Sorry, please specify a size between 6 and 20.'
            });
        },
        async (step) => {
            // Acknowledge their response and exit the dialog.
            const size = step.result;
            await step.context.sendActivity(`That's a party of ${size}, thanks.`);
            return await step.endDialog();
        }
    ]));
```

---

Da mesma forma, se você quiser validar uma resposta **DatetimePrompt** para uma data e hora no futuro, poderá ter uma lógica de validação semelhante a esta:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    private Task<bool> DateTimeValidatorAsync(PromptValidatorContext<IList<DateTimeResolution>> prompt, CancellationToken cancellationToken)
    {
        if (prompt.Recognized.Succeeded)
        {
            var resolution = prompt.Recognized.Value.First();

            // Verify that the Timex received is within the desired bounds, compared to today.
            var now = DateTime.Now;
            DateTime.TryParse(resolution.Value, out var time);

            if (time < now)
            {
                return Task.FromResult(false);
            }

            return Task.FromResult(true);
        }

        return Task.FromResult(false);
    }
```

```csharp
_dialogs.Add(new DateTimePrompt("date", DateTimeValidatorAsync));
```

Outros exemplos podem ser encontrados em nosso [repositório de amostras](https://aka.ms/bot-samples-readme).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const { DateTimePrompt } = require("botbuilder-dialogs");
```

```JavaScript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new DateTimePrompt('dateTimePrompt', async (promptContext) => {
        try {
            if (!promptContext.recognized.succeeded) { throw new Error('Value not recognized.') }
            const values = promptContext.recognized.value;
            if (!Array.isArray(values) || values.length < 0) { throw new Error('Value missing.'); }
            if ((values[0].type !== 'datetime') && (values[0].type !== 'date')) { throw new Error('Unsupported type.'); }
            const now = new Date();
            const value = new Date(values[0].value);
            if (value.getTime() < now.getTime()) { throw new Error('Value in the past.') }

            // update the return value of the prompt to be a real date object
            promptContext.recognized.value = [value];
            return true; // indicate valid
        } catch (err) {
            await promptContext.context.sendActivity(`${err} Please specify a date or a date and time in the future, like tomorrow at 9am.`);
            return false; // indicate invalid
        }
    }))
    .add(new WaterfallDialog(MAIN_DIALOG, [
        async (step) => {
            // Ask the user for their party size.
            return await step.prompt('dateTimePrompt', 'When would you like to schedule that for?');
        },
        async (step) => {
            // Acknowledge their response and exit the dialog.
            const time = step.result;
            await step.context.sendActivity(`That's ${time}, thanks.`);
            return await step.endDialog();
        }
    ]));
```

Outros exemplos podem ser encontrados em nosso [repositório de amostras](https://aka.ms/bot-samples-readme).

---

> [!TIP]
> Os prompts de data e hora podem ser resolvidos em algumas datas diferentes se o usuário fornecer uma resposta ambígua. Dependendo do que você está usando, você pode querer verificar todas as resoluções fornecidas pelo resultado do prompt, em vez de apenas o primeiro.

You can use the similar techniques to validate prompt responses for any of the prompt types.

## <a name="save-user-data"></a>Salvar dados do usuário

Quando você solicita a entrada do usuário, você tem várias opções sobre como lidar com essa entrada. Por exemplo, você pode consumir e descartar a entrada, pode salvá-la em uma variável global, pode salvá-la em um recipiente de armazenamento volátil ou em memória, pode salvá-la em um arquivo ou pode salvá-la em um arquivo externo. base de dados. Para obter mais informações sobre como salvar dados do usuário, consulte [ Gerenciar dados do usuário ](bot-builder-howto-v4-state.md).

## <a name="additional-resources"></a>Recursos adicionais

Para ver um exemplo completo com a utilização de alguns desses prompts, confira o Bot de prompt de vários turnos para [C#](https://aka.ms/cs-multi-prompts-sample) ou [JavaScript](https://aka.ms/js-multi-prompts-sample).

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como solicitar uma entrada ao usuário, ele aprimora o código bot e a experiência do usuário, gerenciando vários fluxos de conversas por meio de caixas de diálogo.

> [!div class="nextstepaction"]
> [Gerenciar fluxo de conversa simples com diálogos](bot-builder-dialog-manage-conversation-flow.md)
