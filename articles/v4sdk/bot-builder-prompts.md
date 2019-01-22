---
title: Coletar entrada do usuário usando um prompt de diálogo | Microsoft Docs
description: Saiba como solicitar informações dos usuários usando a biblioteca Diálogos no SDK do Bot Framework.
keywords: prompts, prompt, entrada do usuário, diálogos, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, solicitar novamente, validação
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 57e43e6f0ad8673634bd8faafac79636a672eefd
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225841"
---
# <a name="gather-user-input-using-a-dialog-prompt"></a>Coletar entrada do usuário usando um prompt de diálogo

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A coleta de informações por meio da apresentação de perguntas é uma das principais formas de um bot interagir com os usuários. A biblioteca de *caixas de diálogo* torna mais fácil fazer as perguntas, bem como validar a resposta para garantir que ela corresponde a um tipo de dados específico ou atenda às regras de validação personalizada. Este tópico fornece detalhes sobre como criar e chamar prompts de uma caixa de diálogo em cascata.

## <a name="prerequisites"></a>Pré-requisitos

- O código neste artigo se baseia no exemplo **DialogPromptBot**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/dialog-prompt-cs) ou [JS](https://aka.ms/dialog-prompt-js).
- É necessário um entendimento básico sobre a [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md) e como [gerenciar conversas](bot-builder-dialog-manage-conversation-flow.md). 
- [Emulador do Bot Framework](https://github.com/Microsoft/BotFramework-Emulator) para teste.

## <a name="using-prompts"></a>Usando prompts

Um diálogo pode usar um prompt somente se o diálogo e o prompt estiverem no mesmo conjunto de diálogos. Você pode usar o mesmo prompt em várias etapas dentro de um diálogo e em vários diálogos no mesmo conjunto diálogos. No entanto, você associa a validação personalizada com um prompt no momento da inicialização. Para usar validação diferente para o mesmo tipo de prompt, você precisa de várias instâncias do tipo de prompt, cada um com seu próprio código de validação.

### <a name="define-a-state-property-accessor-for-the-dialog-state"></a>Defina um acessador de propriedade de estado para o estado do diálogo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo de prompt de caixas de diálogo usado neste artigo solicita ao usuário informações de reserva. Para gerenciar a data e o tamanho do grupo, definimos uma classe interna para obter informações de reserva no arquivo DialogPromptBot.cs.

```csharp
public class Reservation
{
    public int Size { get; set; }

    public string Date { get; set; }
}
```

Em seguida, adicionamos um acessador de propriedade de estado para os dados de reserva.

```csharp
public class DialogPromptBotAccessors
{
    public DialogPromptBotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    public static string DialogStateAccessorKey { get; } = "DialogPromptBotAccessors.DialogState";
    public static string ReservationAccessorKey { get; } = "DialogPromptBotAccessors.Reservation";

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }
    public IStatePropertyAccessor<DialogPromptBot.Reservation> ReservationAccessor { get; set; }

    public ConversationState ConversationState { get; }
}
```

Em Startup.cs, atualizamos o método `ConfigureServices` para definir os acessadores.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    // Create and register state accesssors.
    // Acessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        // ...

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState)
        {
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(DialogPromptBotAccessors.DialogStateAccessorKey),
            ReservationAccessor = conversationState.CreateProperty<DialogPromptBot.Reservation>(DialogPromptBotAccessors.ReservationAccessorKey),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Se não for necessária nenhuma alteração no código de serviço HTTP para JavaScript, podemos deixar nosso arquivo index.js como está.

Em bot.js, incluímos as `require` instruções necessárias para o bot de prompt da caixa de diálogo. 
```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, NumberPrompt, DateTimePrompt, ChoicePrompt, DialogTurnStatus } = require('botbuilder-dialogs');
```

Adicione os identificadores para os acessadores de propriedade de estado, as caixas de diálogo e os prompts.

```javascript
// Define identifiers for state property accessors.
const DIALOG_STATE_ACCESSOR = 'dialogStateAccessor';
const RESERVATION_ACCESSOR = 'reservationAccessor';

// Define identifiers for dialogs and prompts.
const RESERVATION_DIALOG = 'reservationDialog';
const PARTY_SIZE_PROMPT = 'partySizePrompt';
const LOCATION_PROMPT = 'locationPrompt';
const RESERVATION_DATE_PROMPT = 'reservationDatePrompt';
```

---

### <a name="create-a-dialog-set-and-prompts"></a>Crie um conjunto de caixas de diálogo e prompts

Em geral, crie e adicione os prompts e os diálogos ao conjunto de caixas de diálogo ao inicializar seu bot. Então, o conjunto diálogos, poderá resolver a ID do prompt quando o bot recebe entradas do usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Na classe `DialogPromptBot`, defina os identificadores para as caixas de diálogo, os prompts e o conjunto de caixas de diálogo.
```csharp
private const string ReservationDialog = "reservationDialog";
private const string PartySizePrompt = "partyPrompt";
private const string LocationPrompt = "locationPrompt";
private const string ReservationDatePrompt = "reservationDatePrompt";

private readonly DialogSet _dialogSet;
```

No construtor do bot, crie o conjunto de caixas de diálogo, adicione os prompts e adicione a caixa de diálogo de reserva. Incluímos a validação personalizada quando criamos os prompts e vamos implementar as funções de validação posteriormente.

```csharp
// The following code creates prompts and adds them to an existing dialog set. The DialogSet contains all the dialogs that can 
// be used at runtime. The prompts also references a validation method is not shown here.

public DialogPromptBot(DialogPromptBotAccessors accessors, ILoggerFactory loggerFactory)
{
   // ...

    // Create the dialog set and add the prompts, including custom validation.
    _dialogSet = new DialogSet(_accessors.DialogStateAccessor);
    _dialogSet.Add(new NumberPrompt<int>(PartySizePrompt, PartySizeValidatorAsync));
    _dialogSet.Add(new ChoicePrompt(LocationPrompt));
    _dialogSet.Add(new DateTimePrompt(ReservationDatePrompt, DateValidatorAsync));

    // Define the steps of the waterfall dialog and add it to the set.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        PromptForPartySizeAsync,
        PromptForLocationAsync,
        PromptForReservationDateAsync,
        AcknowledgeReservationAsync,
    };
    _dialogSet.Add(new WaterfallDialog(ReservationDialog, steps));


}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor, crie as propriedades de acessador de estado.

```javascript
constructor(conversationState) {
    // Creates our state accessor properties.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_ACCESSOR);
    this.reservationAccessor = conversationState.createProperty(RESERVATION_ACCESSOR);
    this.conversationState = conversationState;
    // ...
    }
```

Em seguida, crie o conjunto de caixas de diálogo e adicione os prompts, incluindo a validação personalizada.

```javascript
    // ...
    this.dialogSet = new DialogSet(this.dialogStateAccessor);
    this.dialogSet.add(new NumberPrompt(PARTY_SIZE_PROMPT, this.partySizeValidator));
    this.dialogSet.add(new ChoicePrompt (LOCATION_PROMPT));
    this.dialogSet.add(new DateTimePrompt(RESERVATION_DATE_PROMPT, this.dateValidator));
    // ...
```

Em seguida, defina as etapas da caixa de diálogo em cascata e a adicione ao conjunto.

```javascript
    // ...
    this.dialogSet.add(new WaterfallDialog(RESERVATION_DIALOG, [
    this.promptForPartySize.bind(this),
    this.promptForLocation.bind(this),
    this.promptForReservationDate.bind(this),
    this.acknowledgeReservation.bind(this),
 ]));
}
```

---

### <a name="implement-dialog-steps"></a>Implemente as etapas de diálogo

No arquivo principal do bot, podemos implementar cada uma de nossas etapas da caixa de diálogo em cascata. Depois que um prompt é adicionado, chame-o em uma etapa de um diálogo em cascata e obtenha o resultado do prompt na etapa do diálogo a seguir. Para chamar um prompt de dentro de uma etapa de cascata, chame o método do _prompt_ do objeto do _contexto da etapa de cascata_. O primeiro parâmetro é a ID do prompt a ser usado e o segundo parâmetro contém as opções do prompt, como o texto usado para pedir entradas ao usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No arquivo DialogPromptBot.cs, podemos implementar as etapas `PromptForPartySizeAsync`, `PromptForLocationAsync`, `PromptForReservationDateAsync` e `AcknowledgeReservationAsync` do diálogo em cascata.

Aqui estamos mostrando apenas `PromptForPartySizeAsync` e `PromptForLocationAsync` que são dois delegados de etapas consecutivas de uma caixa de diálogo em cascata.

```csharp
private async Task<DialogTurnResult> PromptForPartySizeAsync(WaterfallStepContext stepContext)
{
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    // If the input is not valid, the prompt is restarted, causing it to reprompt for input
    // and this set of steps is repeated next turn. Otherwise, the prompt ends and returns a _dialog turn result_ object 
    // to the parent dialog. Control passes to the next step of your waterfall dialog, with the result of the prompt 
    // available in the waterfall step context's _result_ property.
    return await stepContext.PromptAsync(
        PartySizePrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text("How many people is the reservation for?"),
            RetryPrompt = MessageFactory.Text("How large is your party?"),
        },
        cancellationToken);
}

private async Task<DialogTurnResult> PromptForLocationAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Record the party size information in the current dialog state.
    int size = (int)stepContext.Result;
    stepContext.Values["size"] = size;

    return await stepContext.PromptAsync(
        "locationPrompt",
        new PromptOptions
        {
            Prompt = MessageFactory.Text("Please choose a location."),
            RetryPrompt = MessageFactory.Text("Sorry, please choose a location from the list."),
            Choices = ChoiceFactory.ToChoices(new List<string> { "Redmond", "Bellevue", "Seattle" }),
        },
        cancellationToken);
}
```

O exemplo acima mostra como usar um prompt de escolha, fornecendo todas as três propriedades. O método `PromptForLocationAsync` é usado como uma etapa em uma caixa de diálogo em cascata e nosso conjunto de caixas de diálogo contém a caixa de diálogo em cascata e um prompt de escolha com uma ID de `locationPrompt`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aqui, `PARTY_SIZE_PROMPT` e `LOCATION_PROMPT` são as IDs dos prompts e `promptForPartySize` e `promptForLocation` e são funções de duas etapas consecutivas de uma caixa de diálogo em cascata.

```javascript
async promptForPartySize(stepContext) {
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.prompt(
        PARTY_SIZE_PROMPT, {
            prompt: 'How many people is the reservation for?',
            retryPrompt: 'How large is your party?'
        });
}

async promptForLocation(stepContext) {
    // Prompt for location
    return await stepContext.prompt(
        LOCATION_PROMPT, 'Select a location.', ['Redmond', 'Bellevue', 'Seattle']
    );
}
```

---

O segundo parâmetro do método de _prompt_ leva um objeto _prompt options_, que tem as propriedades a seguir.

| Propriedade | DESCRIÇÃO |
| :--- | :--- |
| _prompt_ | A atividade inicial para enviar o usuário, para solicitar sua entrada. |
| _prompt de nova tentativa_ | A atividade de enviar o usuário se a sua primeira entrada não tiver sido validada. |
| _opções_ | Uma lista de opções para o usuário escolher, para ser usado com um prompt de escolha. |

Em geral, as propriedades de prompt e de nova tentativa de prompt são atividads, embora exista alguma variação sobre como isso é manipulado em linguagens de programação diferentes.

Você sempre deve especificar a atividade de prompt inicial para enviar ao usuário.

Especificar um prompt de nova tentativa é útil para quando a entrada do usuário falha na validação, seja porque está em um formato que o prompt não pode analisar, como "amanhã" para um prompt numérico, ou a entrada falha em um critério de validação. Nesse caso, se nenhum prompt de nova tentativa foi fornecido, o prompt usará a atividade de prompt inicial para voltar a solicitar a entrada ao usuário.

Para um prompt de escolha, você sempre deve fornecer a lista de escolhas disponíveis.

## <a name="custom-validation"></a>Validação personalizada

Você pode validar uma resposta do prompt antes de retornar o valor para a próxima etapa da **cascata**. Uma função de validador tem um parâmetro de _contexto do validador de prompt_ e retorna um valor booleano que indica se a entrada passa na validação.

O contexto do validador de prompt inclui as seguintes propriedades:

| Propriedade | DESCRIÇÃO |
| :--- | :--- |
| _Contexto_ | O contexto de turnos atual para o bot. |
| _Reconhecido_ | Um _resultado do reconhecedor de prompts_ que contém informações sobre a entrada do usuário, conforme processado pelo reconhecedor. |

O resultado do reconhecedor de prompts tem as seguintes propriedades:

| Propriedade | DESCRIÇÃO |
| :--- | :--- |
| _Êxito_ | Indica se o reconhecedor foi capaz de analisar a entrada. |
| _Valor_ | O valor retornado do reconhecedor. Se necessário, o código de validação pode modificar esse valor. |

### <a name="implement-validation-code"></a>Implementar o código de validação

Você associa a validação personalizada com um prompt no momento da inicialização, no construtor do bot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// ...
_dialogSet = new DialogSet(_accessors.DialogStateAccessor);
_dialogSet.Add(new NumberPrompt<int>(PartySizePrompt, PartySizeValidatorAsync));
_dialogSet.Add(new ChoicePrompt(LocationPrompt));
_dialogSet.Add(new DateTimePrompt(ReservationDatePrompt, DateValidatorAsync));
// ...
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// ...
this.dialogSet = new DialogSet(this.dialogStateAccessor);
this.dialogSet.add(new NumberPrompt(PARTY_SIZE_PROMPT, this.partySizeValidator));
this.dialogSet.add(new ChoicePrompt (LOCATION_PROMPT));
this.dialogSet.add(new DateTimePrompt(RESERVATION_DATE_PROMPT, this.dateValidator));
// ...
```

---

**Validador de tamanho do grupo**

Limitaremos as reservas a grupos de 6 a 20 pessoas.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private async Task<bool> PartySizeValidatorAsync(
    PromptValidatorContext<int> promptContext,
    CancellationToken cancellationToken)
{
    // Check whether the input could be recognized as an integer.
    if (!promptContext.Recognized.Succeeded)
    {
        await promptContext.Context.SendActivityAsync(
            "I'm sorry, I do not understand. Please enter the number of people in your party.",
            cancellationToken: cancellationToken);
        return false;
    }

    // Check whether the party size is appropriate.
    int size = promptContext.Recognized.Value;
    if (size < 6 || size > 20)
    {
        await promptContext.Context.SendActivityAsync(
            "Sorry, we can only take reservations for parties of 6 to 20.",
            cancellationToken: cancellationToken);
        return false;
    }

    return true;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async partySizeValidator(promptContext) {
    // Check whether the input could be recognized as an integer.
    if (!promptContext.recognized.succeeded) {
        await promptContext.context.sendActivity(
            "I'm sorry, I do not understand. Please enter the number of people in your party.");
        return false;
    }
    if (promptContext.recognized.value % 1 != 0) {
        await promptContext.context.sendActivity(
            "I'm sorry, I don't understand fractional people.");
        return false;
    }
    // Check whether the party size is appropriate.
    var size = promptContext.recognized.value;
    if (size < 6 || size > 20) {
        await promptContext.context.sendActivity(
            'Sorry, we can only take reservations for parties of 6 to 20.');
        return false;
    }

    return true;
}
```

---

**Validação de data e hora**

No validador de data de reserva, limitaremos as reservas a uma hora ou mais a partir da hora atual. Estamos mantendo a primeira resolução que corresponde aos nossos critérios e limpando o restante. O código de validação a seguir não é exaustivo e funciona melhor para a entrada que analisa uma data e hora. Ele demonstra algumas das opções de validação de um prompt de data e hora e sua implementação dependerá de quais informações você está tentando coletar do usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Validates whether the reservation date is appropriate.
// Reservations must be made at least an hour in advance.
private async Task<bool> DateValidatorAsync(
    PromptValidatorContext<IList<DateTimeResolution>> promptContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Check whether the input could be recognized as an integer.
    if (!promptContext.Recognized.Succeeded)
    {
        await promptContext.Context.SendActivityAsync(
            "I'm sorry, I do not understand. Please enter the date or time for your reservation.",
            cancellationToken: cancellationToken);
        return false;
    }

    // Check whether any of the recognized date-times are appropriate,
    // and if so, return the first appropriate date-time.
    DateTime earliest = DateTime.Now.AddHours(1.0);
    DateTimeResolution value = promptContext.Recognized.Value.FirstOrDefault(v =>
        DateTime.TryParse(v.Value ?? v.Start, out DateTime time) && DateTime.Compare(earliest,time) <= 0);
    if (value != null)
    {
        promptContext.Recognized.Value.Clear();
        promptContext.Recognized.Value.Add(value);
        return true;
    }

    await promptContext.Context.SendActivityAsync(
            "I'm sorry, we can't take reservations earlier than an hour from now.",
            cancellationToken: cancellationToken);
    return false;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async dateValidator(promptContext) {
    // Check whether the input could be recognized as an integer.
    if (!promptContext.recognized.succeeded) {
        await promptContext.context.sendActivity(
            "I'm sorry, I do not understand. Please enter the date or time for your reservation.");
        return false;
    }

    // Check whether any of the recognized date-times are appropriate,
    // and if so, return the first appropriate date-time.
    const earliest = Date.now() + (60 * 60 * 1000);
    let value = null;
    promptContext.recognized.value.forEach(candidate => {
        // TODO: update validation to account for time vs date vs date-time vs range.
        const time = new Date(candidate.value || candidate.start);
        if (earliest < time.getTime()) {
            value = candidate;
        }
    });
    if (value) {
        promptContext.recognized.value = [value];
        return true;
    }

    await promptContext.context.sendActivity(
        "I'm sorry, we can't take reservations earlier than an hour from now.");
    return false;
}
```

---

O prompt de data e hora retorna uma lista ou uma matriz das possíveis _resoluções de data e hora_ que correspondem à entrada do usuário. Por exemplo, 9:00 pode significar 9 AM ou 9 PM e domingo também é ambíguo. Além disso, uma resolução de data e hora pode representar uma data, uma hora, uma data e hora ou um intervalo. O prompt de data e hora usa [Microsoft/Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) para analisar a entrada do usuário.

### <a name="update-the-turn-handler"></a>Atualizar o manipulador de turnos

Atualize o manipulador de turnos do bot para iniciar o diálogo e aceitar um valor de retorno no diálogo quando ele for concluído. Suponha que o usuário esteja interagindo com um bot, o bot tem uma caixa de diálogo em cascata ativa e, na próxima etapa, o diálogo usa um prompt.

1. Quando o usuário envia uma mensagem para o bot, este faz o seguinte:
   1. O manipulador de turnos do bot cria um contexto diálogo e chamar seu método _continuar_.
   1. O controle passa para a próxima etapa no diálogo ativo, que neste caso é o diálogo em cascata.
   1. A etapa chama seu método de _prompt_ da etapa em cascata para solicitar entradas ao usuário.
   1. O contexto da etapa em cascata envia por push o prompt para a pilha e o inicia-o.
   1. O prompt envia uma atividade para o usuário para solicitar sua entrada.
1. Quando o usuário envia uma mensagem para o bot, ele faz o seguinte:
   1. O manipulador de turnos do bot cria um contexto diálogo e chamar seu método _continuar_.
   1. O controle passa para a próxima etapa no diálogo ativo, que é o segundo turno do prompt.
   1. O prompt valida a entrada do usuário.

**Manipulando os resultados do prompt**

O que você faz com o resultado do prompt depende da razão pela qual você solicitou a informação ao usuário. As opções incluem:

- Use as informações para controlar o fluxo do seu diálogo, como quando o usuário responde a um prompt de confirmação ou escolha.
- Armazene em cache as informações no estado do diálogo, como a configuração de um valor na propriedade de _valores_ do contexto da etapa em cascata e, em seguida, retorne às informações coletadas quando o diálogo terminar.
- Salve as informações de estado do bot. Isso exigiria a criação de um diálogo para ter acesso aos acessadores de propriedade de estado do bot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    switch (turnContext.Activity.Type)
    {
        // On a message from the user:
        case ActivityTypes.Message:

            // Get the current reservation info from state.
            Reservation reservation = await _accessors.ReservationAccessor.GetAsync(
                turnContext, () => null, cancellationToken);

            // Generate a dialog context for our dialog set.
            DialogContext dc = await _dialogSet.CreateContextAsync(turnContext, cancellationToken);

            if (dc.ActiveDialog is null)
            {
                // If there is no active dialog, check whether we have a reservation yet.
                if (reservation is null)
                {
                    // If not, start the dialog.
                    await dc.BeginDialogAsync(ReservationDialog, null, cancellationToken);
                }
                else
                {
                    // Otherwise, send a status message.
                    await turnContext.SendActivityAsync(
                        $"We'll see you {reservation.Date}.",
                        cancellationToken: cancellationToken);
                }
            }
            else
            {
                // Continue the dialog.
                DialogTurnResult dialogTurnResult = await dc.ContinueDialogAsync(cancellationToken);

                // If the dialog completed this turn, record the reservation info.
                if (dialogTurnResult.Status is DialogTurnStatus.Complete)
                {
                    reservation = (Reservation)dialogTurnResult.Result;
                    await _accessors.ReservationAccessor.SetAsync(
                        turnContext,
                        reservation,
                        cancellationToken);

                    // Send a confirmation message to the user.
                    await turnContext.SendActivityAsync(
                        $"Your party of {reservation.Size} is confirmed for {reservation.Date}.",
                        cancellationToken: cancellationToken);
                }
            }

            // Save the updated dialog state into the conversation state.
            await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
            break;

        // Handle other incoming activity types as appropriate to your bot.
        default:
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
            break;
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async onTurn(turnContext) {
    switch (turnContext.activity.type) {
        case ActivityTypes.Message:
            // Get the current reservation info from state.
            const reservation = await this.reservationAccessor.get(turnContext, null);

            // Generate a dialog context for our dialog set.
            const dc = await this.dialogSet.createContext(turnContext);

            if (!dc.activeDialog) {
                // If there is no active dialog, check whether we have a reservation yet.
                if (!reservation) {
                    // If not, start the dialog.
                    await dc.beginDialog(RESERVATION_DIALOG);
                }
                else {
                    // Otherwise, send a status message.
                    await turnContext.sendActivity(
                        `We'll see you ${reservation.date}.`);
                }
            }
            else {
                // Continue the dialog.
                const dialogTurnResult = await dc.continueDialog();

                // If the dialog completed this turn, record the reservation info.
                if (dialogTurnResult.status === DialogTurnStatus.complete) {
                    await this.reservationAccessor.set(
                        turnContext,
                        dialogTurnResult.result);

                    // Send a confirmation message to the user.
                    await turnContext.sendActivity(
                        `Your party of ${dialogTurnResult.result.size} is ` +
                        `confirmed for ${dialogTurnResult.result.date}.`);
                }
            }

            // Save the updated dialog state into the conversation state.
            await this.conversationState.saveChanges(turnContext, false);
            break;
        case ActivityTypes.EndOfConversation:
        case ActivityTypes.DeleteUserData:
            break;
        default:
            break;
    }
}
```

---

You can use the similar techniques to validate prompt responses for any of the prompt types.

## <a name="test-your-bot"></a>Testar seu bot

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para o exemplo em [C#](https://aka.ms/dialog-prompt-cs) ou em [JS](https://aka.ms/dialog-prompt-js).
2. Inicie o emulador e envie as mensagens conforme mostrado a seguir para testar o bot.

![exemplo de prompt de caixa de diálogo de teste](~/media/emulator-v4/test-dialog-prompt.png)

## <a name="additional-resources"></a>Recursos adicionais

Para chamar um prompt diretamente do seu manipulador de turnos, confira o exemplo de _validações de prompt_ em [C#](https://aka.ms/cs-prompt-validation-sample) ou [JS](https://aka.ms/js-prompt-validation-sample).

A biblioteca de caixas de diálogo também inclui um _prompt OAuth_ para obter um _token OAuth_ que acessa outro aplicativo em nome do usuário. Para obter mais informações sobre autenticação, confira como [adicionar autenticação](bot-builder-authentication.md) ao seu bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar o fluxo de conversa de avanço usando ramificações e loops](bot-builder-dialog-manage-complex-conversation-flow.md)
