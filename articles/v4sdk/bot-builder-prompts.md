---
title: Coletar entrada do usuário usando um prompt de diálogo | Microsoft Docs
description: Saiba como solicitar informações dos usuários usando a biblioteca Diálogos no SDK do Bot Builder.
keywords: prompts, prompt, entrada do usuário, diálogos, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, solicitar novamente, validação
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/02/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ec0cc5e942ed66c8683f8b0cc92ba7df2e36db42
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645666"
---
# <a name="gather-user-input-using-a-dialog-prompt"></a>Coletar entrada do usuário usando um prompt de diálogo

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A coleta de informações através da postagem de perguntas é uma das principais formas de um bot interagir com os usuários. É possível fazer isso diretamente usando o método _enviar atividade_ do objeto [contexto do turno](~/v4sdk/bot-builder-basics.md#defining-a-turn) e, em seguida, processar a mensagem recebida seguinte como a resposta. No entanto, o SDK do Bot Builder fornece uma [biblioteca de diálogos](bot-builder-concept-dialog.md) que disponibiliza métodos projetados para facilitar a realização de perguntas e para garantir que a resposta corresponda a um tipo de dado específico ou atenda às regras de validação personalizadas. Este tópico fornece detalhes sobre como usar objetos de prompt para pedir informações a um usuário.

## <a name="prompt-types"></a>Tipos de prompt

Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicitará a entrada, em seguida, ele retornará o valor válido ou reiniciará na parte superior com um novo prompt.

A biblioteca de diálogos oferece uma série de prompts básicos, cada um deles usado para coletar um tipo de resposta.

| Prompt | DESCRIÇÃO | Retornos |
|:----|:----|:----|
| _Prompt de anexo_ | Solicita um ou mais anexos, como um documento ou uma imagem. | Uma coleção de objetos _attachment_. |
| _Prompt de escolha_ | Solicita a escolha dentre um conjunto de opções. | Um objeto _found choice_. |
| _Prompt de confirmação_ | Solicita uma confirmação. | Um valor booliano. |
| _Prompt de data e hora_ | Solicita uma data e hora. | Uma coleção de objetos _date-time resolution_. |
| _Prompt de número_ | Solicita um número. | Um valor numérico. |
| _Texto do prompt_ | Solicita a entrada de texto geral. | Uma cadeia de caracteres. |

A biblioteca também inclui um _prompt OAuth_ para obter um _token OAuth_ que acessa outro aplicativo em nome do usuário. Para obter mais informações sobre autenticação, confira como [adicionar autenticação ao seu bot](bot-builder-authentication.md).

Os prompts básicos podem interpretar a entrada de linguagem natural, como "dez" ou "uma dúzia" para um número, ou "amanhã" ou "sextas-feira às 10h" para uma data e hora.

## <a name="using-prompts"></a>Usando prompts

Um diálogo pode usar um prompt somente se o diálogo e o prompt estiverem no mesmo conjunto de diálogos.

1. Defina um acessador de propriedade de estado para o estado do diálogo.
1. Crie um conjunto de diálogos.
1. Crie seus prompts e adicione-os ao conjunto de diálogos.
1. Crie um diálogo que usa seus prompts e adicione-o ao conjunto de diálogos.
1. Na caixa de diálogo, adicione chamadas para os prompts e para recuperar os resultados do prompt.

Este artigo discute como criar seus prompts e como chamá-los de um diálogo em cascata.
Para obter mais informações sobre as caixas de diálogo em geral, confira o artigo [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md).
Para ver uma discussão sobre um bot completo que usa diálogos e prompts, confira como [usar as caixas de diálogo para gerenciar o fluxo da conversa simples](bot-builder-dialog-manage-conversation-flow.md).

Você pode usar o mesmo prompt em várias etapas dentro de um diálogo e em vários diálogos no mesmo conjunto diálogos.
No entanto, você associa a validação personalizada com um prompt no momento da inicialização.
Portanto, se você precisar de validação diferente para o mesmo tipo de prompt, você precisará de várias instâncias do tipo de prompt, cada um com seu próprio código de validação.

### <a name="create-a-prompt"></a>Criar um prompt

Para solicitar uma entrada ao usuário, defina um prompt usando uma das classes internas, como _text prompt_, e adicione-o ao seu conjunto de diálogos.

* O prompt tem uma ID fixa. (Os identificadores devem ser exclusivos dentro de um conjunto de diálogos).
* O prompt pode ter um validador personalizado. (Confira [validação personalizada](#custom-validation).)
* Para alguns prompts, você pode especificar uma _localidade padrão_.

Em geral, crie e adicione prompts e diálogos ao seu conjunto de diálogos ao inicializar seu bot. Então, o conjunto diálogos, poderá resolver a ID do prompt quando o bot recebe entradas do usuário.

Por exemplo, o código a seguir cria prompts de dois textos e adiciona-os a um conjunto de diálogos existente. O segundo prompt de texto faz referência a um método de validação que não é mostrado aqui.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aqui, `_dialogs` contém um conjunto de diálogos existente, e `NameValidator` é um método de validação.

```csharp
_dialogs.Add(new TextPrompt("nickNamePrompt"));
_dialogs.Add(new TextPrompt("namePrompt", NameValidator));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aqui, `this.dialogs` contém um conjunto de diálogos existente, e `NameValidator` é uma função de validação.

```javascript
this.dialogs.add(new TextPrompt('nickNamePrompt'));
this.dialogs.add(new TextPrompt('namePrompt', NameValidator));
```

---

#### <a name="locales"></a>Localidades

A localidade é usada para determinar o comportamento específico do idioma dos prompts **choice**, **confirm**, **date-time** e **number**. Para qualquer entrada do usuário:

* Se o canal forneceu uma propriedade de _localidade_ na mensagem do usuário, então essa é usada.
* Caso contrário, se a _localidade padrão_ do prompt for definida, fornecendo-a ao chamar o construtor do prompt ou configurando-a mais tarde, então essa é a usada.
* Caso contrário, o inglês ("en-us") é usado como a localidade.

> [!NOTE]
> A localidade é um código ISO 639 de 2, 3 ou 4 caracteres que representa um idioma ou uma família de idiomas.

### <a name="call-a-prompt-from-a-waterfall-dialog"></a>Chamar um prompt de um diálogo em cascata

Depois que um prompt é adicionado, chame-o em uma etapa de um diálogo em cascata e obtenha o resultado do prompt na etapa do diálogo a seguir.
Para chamar um prompt de dentro de uma etapa de cascata, chame o método do _prompt_ do objeto do _contexto da etapa de cascata_. O primeiro parâmetro é a ID do prompt a ser usado e o segundo parâmetro contém as opções do prompt, como o texto usado para pedir entradas ao usuário.

Suponha que o usuário está interagindo com um bot, que tem um diálogo em cascata ativo, e a próxima etapa no diálogo usa um prompt.

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
      * Se a entrada não for válida, o prompt é reiniciado, fazendo com que ele solicite entradas novamente e que o conjunto de etapas seja repetido no próximo turno.
      * Caso contrário, o prompt termina e retorna um objeto do _resultado de turno do diálogo_ para o diálogo pai. O controle passa para a próxima etapa do seu diálogo em cascata, com o resultado do prompt disponível na propriedade _resultado_ do contexto da etapa de cascata.

<!--
> [!NOTE]
> A waterfall step delegate takes a _waterfall step context_ parameter and returns a _dialog turn result_.
> A prompt's result is contained within the prompt's return value (a dialog turn result object) when it ends.
> The waterfall dialog provides the return value in the waterfall step context parameter when it calls the next waterfall step.
-->

Quando um prompt retorna, a propriedade _resultado_ do contexto da etapa de cascata é definida como o valor de retorno do prompt.

Este exemplo mostra as duas etapas consecutivas de cascata. O primeiro usa o prompt para solicitar ao usuário seu nome. O segundo obtém o valor de retorno do prompt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aqui, `name` é a ID de um prompt de texto, e `NameStepAsync` e `GreetingStepAsync` são dois delegados de etapa consecutivos de um diálogo em cascata.

```csharp
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // ...

    // Prompt for the user's name.
    return await stepContext.PromptAsync(
        "name",
         new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") },
         cancellationToken);
}

private static async Task<DialogTurnResult> GreetingStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the user's name from the prompt result.
    string name = (string)stepContext.Result;
    await stepContext.Context.SendActivityAsync(
        MessageFactory.Text($"Pleased to meet you, {name}."),
         cancellationToken);

    // ...
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aqui, `name` é a ID de um prompt de texto, e `nameStep` e `greetingStep` são duas funções do diálogo em cascata.

```javascript
async nameStep(step) {
    // ...

    return await step.prompt('name', 'Please enter your name.');
}

async greetingStep(step) {
    // Get the user's name from the prompt result.
    const name = step.result;
    await step.context.sendActivity(`Pleased to meet you, ${name}.`);

    // ...
}
```

---

### <a name="call-a-prompt-from-the-bots-turn-handler"></a>Chamar um prompt do manipulador de turnos do bot

É possível chamar um prompt diretamente do seu manipulador de turnos, usando o método _prompt_ do contexto do diálogo.
Você precisaria chamar o método _continuar diálogo_ do contexto do diálogo no próximo turno e examinar seu valor de retorno, que é um objeto _dialog turn result_. Para obter um exemplo de como fazer isso, consulte o exemplo de validações do prompt ([C#](https://aka.ms/cs-prompt-validation-sample) | [JS](https://aka.ms/js-prompt-validation-sample)) ou confira como [solicitar entradas ao usuário usando seus próprio prompts](bot-builder-primitive-prompts.md) para obter uma abordagem alternativa.

## <a name="prompt-options"></a>Opções de prompt

O segundo parâmetro do método de _prompt_ leva um objeto _prompt options_, que tem as propriedades a seguir.

| Propriedade | DESCRIÇÃO |
| :--- | :--- |
| _prompt_ | A atividade inicial para enviar o usuário, para solicitar sua entrada. |
| _prompt de nova tentativa_ | A atividade de enviar o usuário se a sua primeira entrada não tiver sido validada. |
| _opções_ | Uma lista de opções para o usuário escolher, para ser usado com um prompt de escolha. |

Em geral, as propriedades de prompt e de nova tentativa de prompt são atividads, embora exista alguma variação sobre como isso é manipulado em linguagens de programação diferentes.

Você sempre deve especificar a atividade de prompt inicial para enviar ao usuário.

Especificar um prompt de nova tentativa é útil quando é possível que a entrada do usuário falhe na validação, seja porque está em um formato que o prompt não pode analisar, como "amanhã" para um prompt numérico, ou a entrada falha em um critério de validação. Nesse caso, se nenhum prompt de nova tentativa foi fornecido, o prompt usará a atividade de prompt inicial para voltar a solicitar a entrada ao usuário.

Para um prompt de escolha, você sempre deve fornecer a lista de escolhas disponíveis.

Este exemplo mostra como usar um prompt de escolha, fornecendo todas as três propriedades. O método de _cor favorita_ é usado como uma etapa em um diálogo em cascata e nosso conjunto de diálogos contém o diálogo em cascata e um prompt de escolha com uma ID de `colorChoice`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private static async Task<DialogTurnResult> FavoriteColorAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // ...

    return await stepContext.PromptAsync(
        "colorChoice",
        new PromptOptions {
            Prompt = MessageFactory.Text("Please choose a color."),
            RetryPrompt = MessageFactory.Text("Sorry, please choose a color from the list."),
            Choices = ChoiceFactory.ToChoices(new List<string> { "blue", "green", "red" }),
        },
        cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No SDK do JavaScript, você pode fornecer uma cadeia de caracteres para ambas as propriedades `prompt` e `retryPrompt`. O prompt converte-as em atividades de mensagem para você.

```javascript
async favoriteColor(step) {
    // ...

    return await step.prompt('colorChoice', {
        prompt: 'Please choose a color:',
        retryPrompt: 'Sorry, please choose a color from the list.',
        choices: [ 'red', 'green', 'blue' ]
    });
}
```

---

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

### <a name="setup"></a>Configuração

Precisamos configurar um pouco antes de adicionar o nosso código de validação.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No arquivo **.cs** do seu bot, defina uma classe interna para obter informações de reserva.

```csharp
public class Reservation
{
    public int Size { get; set; }

    public string Date { get; set; }
}
```

Em **BotAccessors.cs**, adicione um acessador de propriedade de estado para os dados de reserva.

```csharp
public class BotAccessors
{
    public BotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    public static string DialogStateAccessorKey { get; } = "BotAccessors.DialogState";
    public static string ReservationAccessorKey { get; } = "BotAccessors.Reservation";

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }
    public IStatePropertyAccessor<ReservationBot.Reservation> ReservationAccessor { get; set; }

    public ConversationState ConversationState { get; }
}
```

Em **Startup.cs**, atualize `ConfigureServices` para definir os acessadores.

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
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(BotAccessors.DialogStateAccessorKey),
            ReservationAccessor = conversationState.CreateProperty<ReservationBot.Reservation>(BotAccessors.ReservationAccessorKey),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Se não for necessária nenhuma alteração no código de serviço HTTP para JavaScript, podemos deixar nosso arquivo **index.js** como está.

Em **bot.js**, atualize as instruções necessárias e adicione identificadores para os acessadores de propriedade de estado.

```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, NumberPrompt, DateTimePrompt, DialogTurnStatus } = require('botbuilder-dialogs');

// Define identifiers for our state property accessors.
const DIALOG_STATE_ACCESSOR = 'dialogStateAccessor';
const RESERVATION_ACCESSOR = 'reservationAccessor';
```

---

No arquivo bot, adicione os identificadores de nossos diálogos e prompts.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Define identifiers for our dialogs and prompts.
private const string ReservationDialog = "reservationDialog";
private const string PartySizePrompt = "partyPrompt";
private const string ReservationDatePrompt = "reservationDatePrompt";
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Define identifiers for our dialogs and prompts.
const RESERVATION_DIALOG = 'reservationDialog';
const PARTY_SIZE_PROMPT = 'partySizePrompt';
const RESERVATION_DATE_PROMPT = 'reservationDatePrompt';
```

---

### <a name="define-the-prompts-and-dialogs"></a>Definir os prompts e diálogos

No código do construtor do bot, crie o conjunto de diálogo, adicione os prompts e adicione o diálogo de reserva.
Incluímos a validação personalizada quando criamos os prompts. Em seguida, implementaremos as funções de validação.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public ReservationBot(BotAccessors accessors, ILoggerFactory loggerFactory)
{
    // ...
    _accessors = accessors ?? throw new System.ArgumentNullException(nameof(accessors));

    // Create the dialog set and add the prompts, including custom validation.
    _dialogSet = new DialogSet(_accessors.DialogStateAccessor);
    _dialogSet.Add(new NumberPrompt<int>(PartySizePrompt, PartySizeValidatorAsync));
    _dialogSet.Add(new DateTimePrompt(ReservationDatePrompt, DateValidatorAsync));

    // Define the steps of the waterfall dialog and add it to the set.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        PromptForPartySizeAsync,
        PromptForReservationDateAsync,
        AcknowledgeReservationAsync,
    };
    _dialogSet.Add(new WaterfallDialog(ReservationDialog, steps));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
constructor(conversationState) {
    // Creates our state accessor properties.
    // See https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_ACCESSOR);
    this.reservationAccessor = conversationState.createProperty(RESERVATION_ACCESSOR);
    this.conversationState = conversationState;

    // Create the dialog set and add the prompts, including custom validation.
    this.dialogSet = new DialogSet(this.dialogStateAccessor);
    this.dialogSet.add(new NumberPrompt(PARTY_SIZE_PROMPT, partySizeValidator));
    this.dialogSet.add(new DateTimePrompt(RESERVATION_DATE_PROMPT, dateValidator));

    // Define the steps of the waterfall dialog and add it to the set.
    this.dialogSet.add(new WaterfallDialog(RESERVATION_DIALOG, [
        this.promptForPartySize.bind(this),
        this.promptForReservationDate.bind(this),
        this.acknowledgeReservation.bind(this),
    ]));
}
```

---

### <a name="implement-validation-code"></a>Implementar o código de validação

Implemente o validador de tamanho de grupo. Limitaremos as reservas a grupos de 6 a 20 pessoas.

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
    // Check whether the input could be recognized as date.
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

O prompt de data e hora retorna uma lista ou uma matriz das possíveis _resoluções de data e hora_ que correspondem à entrada do usuário. Por exemplo, 9:00 pode significar 9 AM ou 9 PM e domingo também é ambíguo. Além disso, uma resolução de data e hora pode representar uma data, uma hora, uma data e hora ou um intervalo. O prompt de data e hora usa [Microsoft/Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) para analisar a entrada do usuário.

Implemente o validador de data da reserva. Limitaremos as reservas para uma hora ou mais a partir da hora atual. Estamos mantendo a primeira resolução que corresponde aos nossos critérios e limpando o restante.

Esse código de validação não é exaustivo. Ele funciona melhor para a entrada que analisa uma data e hora. Ele demonstra algumas das opções de validação de um prompt de data e hora e sua implementação dependerá de quais informações você está tentando coletar do usuário.

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

### <a name="implement-the-dialog-steps"></a>Implemente as etapas de diálogo

Use os prompts que adicionamos ao conjunto de diálogos. Adicionamos validação às solicitações quando as criamos no construtor do bot. Na primeira vez que o prompt solicitar a entrada do usuário, ele enviará a atividade do _prompt_ entre as opções fornecidas. Se a validação falhar, ele envia a atividade de _repetição do prompt_ para pedir ao usuário uma entrada diferente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// First step of the main dialog: prompt for party size.
private async Task<DialogTurnResult> PromptForPartySizeAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.PromptAsync(
        PartySizePrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text("How many people is the reservation for?"),
            RetryPrompt = MessageFactory.Text("How large is your party?"),
        },
        cancellationToken);
}

// Second step of the main dialog: record the party size and prompt for the
// reservation date.
private async Task<DialogTurnResult> PromptForReservationDateAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Record the party size information in the current dialog state.
    int size = (int)stepContext.Result;
    stepContext.Values["size"] = size;

    // Prompt for the reservation date. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.PromptAsync(
        ReservationDatePrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text("Great. When will the reservation be for?"),
            RetryPrompt = MessageFactory.Text("What time should we make your reservation for?"),
        },
        cancellationToken);
}

// Third step of the main dialog: return the collected party size and reservation date.
private async Task<DialogTurnResult> AcknowledgeReservationAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Retrieve the reservation date.
    DateTimeResolution resolution = (stepContext.Result as IList<DateTimeResolution>).First();
    string time = resolution.Value ?? resolution.Start;

    // Send an acknowledgement to the user.
    await stepContext.Context.SendActivityAsync(
        "Thank you. We will confirm your reservation shortly.",
        cancellationToken: cancellationToken);

    // Return the collected information to the parent context.
    Reservation reservation = new Reservation
    {
        Date = time,
        Size = (int)stepContext.Values["size"],
    };
    return await stepContext.EndDialogAsync(reservation, cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async promptForPartySize(stepContext) {
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.prompt(
        PARTY_SIZE_PROMPT, {
            prompt: 'How many people is the reservation for?',
            retryPrompt: 'How large is your party?'
        });
}

async promptForReservationDate(stepContext) {
    // Record the party size information in the current dialog state.
    stepContext.values.size = stepContext.result;

    // Prompt for the reservation date. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.prompt(
        RESERVATION_DATE_PROMPT, {
            prompt: 'Great. When will the reservation be for?',
            retryPrompt: 'What time should we make your reservation for?'
        });
}

async acknowledgeReservation(stepContext) {
    // Retrieve the reservation date.
    const resolution = stepContext.result[0];
    const time = resolution.value || resolution.start;

    // Send an acknowledgement to the user.
    await stepContext.context.sendActivity(
        'Thank you. We will confirm your reservation shortly.');

    // Return the collected information to the parent context.
    return await stepContext.endDialog({ date: time, size: stepContext.values.size });
}
```

---

### <a name="update-the-turn-handler"></a>Atualizar o manipulador de turnos

Atualize o manipulador de turnos do bot para iniciar o diálogo e aceitar um valor de retorno no diálogo quando ele for concluído.

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
        default:
            break;
    }
}
```

---

You can use the similar techniques to validate prompt responses for any of the prompt types.

## <a name="handling-prompt-results"></a>Manipulando os resultados do prompt

O que você faz com o resultado do prompt depende da razão pela qual você solicitou a informação ao usuário. As opções incluem:

* Use as informações para controlar o fluxo do seu diálogo, como quando o usuário responde a um prompt de confirmação ou escolha.
* Armazene em cache as informações no estado do diálogo, como a configuração de um valor na propriedade de _valores_ do contexto da etapa em cascata e, em seguida, retorne às informações coletadas quando o diálogo terminar.
* Salve as informações de estado do bot. Isso exigiria a criação de um diálogo para ter acesso aos acessadores de propriedade de estado do bot.

## <a name="additional-resources"></a>Recursos adicionais
Para saber mais sobre vários prompts, confira os exemplos em [[C#](https://aka.ms/cs-multi-prompts-sample) ou [JS](https://aka.ms/js-multi-prompts-sample)].

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implementar o fluxo de conversa sequencial básico](bot-builder-dialog-manage-conversation-flow.md)
