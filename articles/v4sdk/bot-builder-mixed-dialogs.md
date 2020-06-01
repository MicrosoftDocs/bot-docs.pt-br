---
title: Criar um bot usando diálogos adaptáveis, de componente e em cascata – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa com diálogos convencionais e adaptáveis no SDK do Bot Framework.
keywords: fluxo de conversa, diálogos, diálogos de componente, diálogos personalizados, diálogos em cascata, diálogos adaptáveis
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/07/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d3fe92f39f8aac7549bddced93b01f4723081f44
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566106"
---
# <a name="create-a-bot-using-adaptive-component-waterfall-and-custom-dialogs"></a>Criar um bot usando diálogos adaptáveis, de componente, em cascata e personalizados

[!INCLUDE[applies-to](../includes/applies-to.md)]

Todos os diálogos derivam de uma classe de _diálogo_ base.
Se você usar o _gerenciador de diálogos_ para executar o diálogo raiz, todas as classes de diálogo poderão trabalhar juntas.
Este artigo mostra como usar diálogos de componente, em cascata, personalizados e adaptáveis em um só bot.

Ele se concentra no código que permite que esses diálogos funcionem juntos. Confira as [informações adicionais](#additional-information) para obter artigos que abordam cada tipo de diálogo mais detalhadamente.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento das [noções básicas sobre bots][bot-basics], do [gerenciamento de estado][concept-state], da [biblioteca de diálogos][about-dialogs] e dos [diálogos adaptáveis][about-adaptive-dialogs].

<!--
- A copy of the **waterfall or custom dialog with adaptive** sample in either [**C#**][cs-sample], [**JavaScript** (preview)][js-sample]
-->

### <a name="preliminary-steps-to-add-an-adaptive-dialog-to-a-bot"></a>Etapas preliminares para adicionar um diálogo adaptável a um bot

Você precisará seguir as etapas descritas abaixo para adicionar um diálogo adaptável a um bot.
Essas etapas são abordadas mais detalhadamente em como [Criar um bot usando diálogos adaptáveis][basic-adaptive-how-to].

#### <a name="c"></a>[C#](#tab/csharp)

1. Atualize todos os pacotes NuGet do Bot Builder para a versão 4.9.x.
1. Adicione o pacote `Microsoft.Bot.Builder.Dialogs.Adaptive` ao seu projeto de bot.
1. Atualize o adaptador de bot para adicionar o armazenamento e os objetos de estado de conversa e usuário a cada contexto de turno.
1. Use um gerenciador de diálogos no código do bot para iniciar ou continuar o diálogo raiz em cada turno.

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Atualize todos os pacotes npm do Bot Builder para a versão 4.9.x.
1. Adicione o pacote `botbuilder-dialogs-adaptive` ao seu projeto de bot.
1. No manipulador no turno do bot:
   1. Crie um gerenciador de diálogos.
   1. Defina as propriedades de armazenamento e de estado do usuário e da conversa do gerenciador de diálogos.
   1. Use o gerenciador de diálogos para iniciar ou continuar o diálogo raiz.

---

## <a name="about-the-sample"></a>Sobre o exemplo

A título de ilustração, esta amostra combina vários tipos de diálogos juntos em um só bot.
Ela não demonstra as melhores práticas de criação do fluxo de conversa.
A amostra:

- Define uma classe de diálogo de _preenchimento de slot_ personalizada.
- Cria um diálogo de componente raiz:
  - Um diálogo em cascata gerencia o fluxo de conversa de nível superior.
  - Juntos, um diálogo adaptável e dois diálogos de preenchimento de slot personalizados gerenciam o restante do fluxo de conversa.

> [!div class="mx-imgBorder"]
> ![fluxo de diálogo](media/adaptive-mixed-dialogs-flow.png)

Os diálogos personalizados de preenchimento de slot aceitam uma lista de propriedades (slots a serem preenchidos). Cada diálogo personalizado solicitará os valores ausentes até que todos os slots sejam preenchidos.
A amostra _associa_ uma propriedade ao diálogo adaptável para permitir que o diálogo adaptável também preencha os slots.

Este artigo se concentra em como os vários tipos de diálogos funcionam juntos.
Para obter informações sobre como configurar o bot para usar diálogos adaptáveis, confira como [Criar um bot usando diálogos adaptáveis][basic-adaptive-how-to].
Para obter mais informações sobre como usar diálogos adaptáveis para coletar a entrada do usuário, confira [Sobre as entradas em diálogos adaptáveis][about-input-dialogs].

## <a name="the-custom-slot-filling-dialogs"></a>Os diálogos personalizados de preenchimento de slot

Um diálogo personalizado é qualquer diálogo derivado de uma das classes de diálogos no SDK e que substitua um ou mais métodos de diálogos básicos: _iniciar diálogo_, _continuar diálogo_, _retomar diálogo_ ou _encerrar diálogo_.

Ao criar o diálogo de preenchimento de slot, forneça uma lista de definições para os _slots_ que o diálogo preencherá.
O diálogo substitui os métodos iniciar, continuar e retomar diálogo para solicitar de maneira iterativa que o usuário preencha cada slot.
Quando todos os slots são preenchidos, o diálogo é encerrado e retorna as informações coletadas.

Cada definição de slot inclui o nome da solicitação de diálogo com a qual as informações serão coletadas.

O diálogo raiz cria dois diálogos de preenchimento de slot, um para coletar o nome completo do usuário e outro para coletar o endereço. Ele também cria a _solicitação de texto_ que esses dois diálogos usam para preencher os slots.

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\SlotDetails.cs**

A classe `SlotDetails` descreve as informações a serem coletadas e a solicitação com a qual ela será coletada.

<!-- [!code-csharp[slot details](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/SlotDetails.cs?range=9-39)]
-->

```csharp
/// <summary>
/// A list of SlotDetails defines the behavior of our SlotFillingDialog.
/// This class represents a description of a single 'slot'. It contains the name of the property we want to gather
/// and the id of the corresponding dialog that should be used to gather that property. The id is that used when the
/// dialog was added to the current DialogSet. Typically this id is that of a prompt but it could also be the id of
/// another slot dialog.
/// </summary>
public class SlotDetails
{
    public SlotDetails(string name, string dialogId, string prompt = null, string retryPrompt = null)
        : this(name, dialogId, new PromptOptions
        {
            Prompt = MessageFactory.Text(prompt),
            RetryPrompt = MessageFactory.Text(retryPrompt),
        })
    {
    }

    public SlotDetails(string name, string dialogId, PromptOptions options)
    {
        Name = name;
        DialogId = dialogId;
        Options = options;
    }

    public string Name { get; set; }

    public string DialogId { get; set; }

    public PromptOptions Options { get; set; }
}
```

**Dialogs\SlotFillingDialog.cs**

A classe `SlotFillingDialog` é derivada da classe `Dialog` base.

Ela acompanha os valores coletados, qual slot é solicitado por último e os detalhes dos slots a serem preenchidos.

<!-- [!code-csharp[slot-filling fields and constructor](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/SlotFillingDialog.cs?range=23-37)]
-->

```csharp
// Custom dialogs might define their own custom state.
// Similarly to the Waterfall dialog we will have a set of values in the ConversationState. However, rather than persisting
// an index we will persist the last property we prompted for. This way when we resume this code following a prompt we will
// have remembered what property we were filling.
private const string SlotName = "slot";
private const string PersistedValues = "values";

// The list of slots defines the properties to collect and the dialogs to use to collect them.
private readonly List<SlotDetails> _slots;

public SlotFillingDialog(string dialogId, List<SlotDetails> slots)
    : base(dialogId)
{
    _slots = slots ?? throw new ArgumentNullException(nameof(slots));
}
```

A lógica principal para coletar informações ausentes está no método auxiliar `RunPromptAsync`. Quando todas as informações forem coletadas, ela encerrará o diálogo e retornará as informações.

<!-- [!code-csharp[slot-filling RunPromptAsync](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/SlotFillingDialog.cs?range=121-151&highlight=23-24,28-29)]
-->

```csharp
/// <summary>
/// This helper function contains the core logic of this dialog. The main idea is to compare the state we have gathered with the
/// list of slots we have been asked to fill. When we find an empty slot we execute the corresponding prompt.
/// </summary>
/// <param name="dialogContext">A handle on the runtime.</param>
/// <param name="cancellationToken">The cancellation token.</param>
/// <returns>A DialogTurnResult indicating the state of this dialog to the caller.</returns>
private Task<DialogTurnResult> RunPromptAsync(DialogContext dialogContext, CancellationToken cancellationToken)
{
    var state = GetPersistedValues(dialogContext.ActiveDialog);

    // Run through the list of slots until we find one that hasn't been filled yet.
    var unfilledSlot = _slots.FirstOrDefault((item) => !state.ContainsKey(item.Name));

    // If we have an unfilled slot we will try to fill it
    if (unfilledSlot != null)
    {
        // The name of the slot we will be prompting to fill.
        dialogContext.ActiveDialog.State[SlotName] = unfilledSlot.Name;

        // If the slot contains prompt text create the PromptOptions.

        // Run the child dialog
        return dialogContext.BeginDialogAsync(unfilledSlot.DialogId, unfilledSlot.Options, cancellationToken);
    }
    else
    {
        // No more slots to fill so end the dialog.
        return dialogContext.EndDialogAsync(state);
    }
}
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/slotDetails.js**

A classe `SlotDetails` descreve as informações a serem coletadas e a solicitação com a qual ela será coletada.

<!-- [!code-javascript[slot details](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotDetails.js?range=4-26)]
-->

```javascript
class SlotDetails {
    /**
     * SlotDetails is a small class that defines a "slot" to be filled in a SlotFillingDialog.
     * @param {string} name The field name used to store user's response.
     * @param {string} promptId A unique identifier of a Dialog or Prompt registered on the DialogSet.
     * @param {string} prompt The text of the prompt presented to the user.
     * @param {string} reprompt (optional) The text to present if the user responds with an invalid value.
     */
    constructor(name, promptId, prompt, reprompt) {
        this.name = name;
        this.promptId = promptId;
        if (prompt && reprompt) {
            this.options = {
                prompt: prompt,
                retryPrompt: reprompt
            };
        } else {
            this.options = {
                prompt: prompt
            };
        }
    }
}
```

**dialogs/slotFillingDialog.js**

A classe `SlotFillingDialog` estende a classe `Dialog` base.

<!-- [!code-javascript[slot-filling constants](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotFillingDialog.js?range=7-12)]

[!code-javascript[slot-filling constructor](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotFillingDialog.js?range=15-30)]
-->

```javascript
// Custom dialogs might define their own custom state.
// Similarly to the Waterfall dialog we will have a set of values in the ConversationState. However, rather than persisting
// an index we will persist the last property we prompted for. This way when we resume this code following a prompt we will
// have remembered what property we were filling.
const SlotName = 'slot';
const PersistedValues = 'values';

/**
 * This is an example of implementing a custom Dialog class. This is similar to the Waterfall dialog in the framework;
 * however, it is based on a Dictionary rather than a sequential set of functions. The dialog is defined by a list of 'slots',
 * each slot represents a property we want to gather and the dialog we will be using to collect it. Often the property
 * is simply an atomic piece of data such as a number or a date. But sometimes the property is itself a complex object, in which
 * case we can use the slot dialog to collect that compound property.
 * @param {string} dialogId A unique identifier for this dialog.
 * @param {Array} slots An array of SlotDetails that define the required slots.
 */
constructor(dialogId, slots) {
    super(dialogId);

    if (!slots) throw new Error('[SlotFillingDialog]: Missing parameter. slots parameter is required');

    this.slots = slots;
}
```

A lógica principal para coletar informações ausentes está no método auxiliar `RunPromptAsync`.
Ela acompanha os valores coletados, qual slot é solicitado por último e os detalhes dos slots a serem preenchidos.
Quando todas as informações forem coletadas, ela encerrará o diálogo e retornará as informações.

<!-- [!code-javascript[slot-filling runPrompt](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotFillingDialog.js?range=84-109)]
-->

```javascript
/**
 * This helper function contains the core logic of this dialog. The main idea is to compare the state we have gathered with the
 * list of slots we have been asked to fill. When we find an empty slot we execute the corresponding prompt.
 * @param {DialogContext} dc
 */
async runPrompt(dc) {
    // runPrompt finds the next slot to fill, then calls the appropriate prompt to fill it.
    const state = dc.activeDialog.state;
    const values = state[PersistedValues];

    // Run through the list of slots until we find one that hasn't been filled yet.
    const unfilledSlot = this.slots.filter(function(slot) { return !Object.keys(values).includes(slot.name); });

    // If we have an unfilled slot we will try to fill it
    if (unfilledSlot.length) {
        state[SlotName] = unfilledSlot[0].name;

        // If the slot contains prompt text create the PromptOptions.

        // Run the child dialog
        return await dc.beginDialog(unfilledSlot[0].promptId, unfilledSlot[0].options);
    } else {
        // No more slots to fill so end the dialog.
        return await dc.endDialog(dc.activeDialog.state);
    }
}
```

---

Para obter mais informações sobre a implementação de diálogos personalizados, confira a discussão do diálogo _cancelar e ajudar_ em como [Tratar as interrupções do usuário][interruptions-how-to].

## <a name="the-root-component-dialog"></a>O diálogo de componente raiz

O diálogo raiz:

- Define todos os slots a serem preenchidos, para si mesmo, para os dois diálogos de preenchimento de slot e para o diálogo adaptável.
- Cria um acessador de propriedade de estado do usuário no qual as informações coletadas serão salvas.
- Cria o diálogo adaptável, os dois diálogos de preenchimento de slot, um diálogo em cascata e as solicitações a serem usadas com os diálogos de preenchimento de slot e em cascata.
- Define o diálogo em cascata como o diálogo inicial a ser executado quando o componente for iniciado pela primeira vez.

O diálogo em cascata agregará todas as informações coletadas e salvará o estado do usuário.

Os diálogos em cascata e adaptável são descritos nas seções a seguir.

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\RootDialog.cs**

A classe `RootDialog` é um `ComponentDialog`. Ela define a propriedade de estado do usuário na qual as informações coletadas serão salvas.

<!-- [!code-csharp[class and constructor opening](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=25-32)]
-->

```csharp
public class RootDialog : ComponentDialog
{
    private IStatePropertyAccessor<JObject> _userStateAccessor;

    public RootDialog(UserState userState)
        : base("root")
    {
        _userStateAccessor = userState.CreateProperty<JObject>("result");
```

O construtor cria um `adaptiveSlotFillingDialog`de diálogo adaptável. Em seguida, ele cria e adiciona o restante dos diálogos usados e adiciona o diálogo adaptável.

<!-- [!code-csharp[add dialogs plus closing](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=121-139)]
-->

```csharp
        // Add the various dialogs that will be used to the DialogSet.
        AddDialog(new SlotFillingDialog("address", address_slots));
        AddDialog(new SlotFillingDialog("fullname", fullname_slots));
        AddDialog(new TextPrompt("text"));
        AddDialog(new NumberPrompt<int>("number", defaultLocale: Culture.English));
        AddDialog(new NumberPrompt<float>("shoesize", ShoeSizeAsync, defaultLocale: Culture.English));

        // We will instead have adaptive dialog do the slot filling by invoking the custom dialog
        // AddDialog(new SlotFillingDialog("slot-dialog", slots));

        // Add adaptive dialog
        AddDialog(adaptiveSlotFillingDialog);

        // Defines a simple two step Waterfall to test the slot dialog.
        AddDialog(new WaterfallDialog("waterfall", new WaterfallStep[] { StartDialogAsync, DoAdaptiveDialog, ProcessResultsAsync }));

        // The initial child Dialog to run.
        InitialDialogId = "waterfall";
    }
    //...
}
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/rootDialog.js**

A classe `RootDialog` estende `ComponentDialog`. Ela define a propriedade de estado do usuário na qual as informações coletadas serão salvas.

<!-- [!code-javascript[class and constructor opening](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=16-23)]
-->

```javascript
const ROOT_DIALOG = 'RootDialog';
const ADAPTIVE_DIALOG = 'AdaptiveDialog';

class RootDialog extends ComponentDialog {
    constructor(userState) {
        super(ROOT_DIALOG);

        this.userStateAccessor = userState.createProperty('result');
```

O construtor cria um `adaptiveSlotFillingDialog`de diálogo adaptável. Em seguida, ele cria e adiciona o restante dos diálogos usados e adiciona o diálogo adaptável.

<!-- [!code-javascript[add dialogs plus closing](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=89-111)]
-->

```javascript
        // Add the various dialogs that will be used to the DialogSet.
        this.addDialog(new SlotFillingDialog('address', addressSlots));
        this.addDialog(new SlotFillingDialog('fullname', fullnameSlots));
        this.addDialog(new TextPrompt('text'));
        this.addDialog(new NumberPrompt('number')); // PromptCultureModels.English.locale
        this.addDialog(new NumberPrompt('shoesize', this.shoeSizeValidator)); // PromptCultureModels.English.locale

        // We will instead have adaptive dialog do the slot filling by invoking the custom dialog
        // AddDialog(new SlotFillingDialog("slot-dialog", slots));

        // Add adaptive dialog
        this.addDialog(adaptiveSlotFillingDialog);

        // Defines a simple two step Waterfall to test the slot dialog.
        this.addDialog(new WaterfallDialog('waterfall', [
            this.startDialog.bind(this),
            this.doAdaptiveDialog.bind(this),
            this.processResults.bind(this)
        ]));

        // The initial child Dialog to run.
        this.initialDialogId = 'waterfall';
    }
    //...
}
```

---

## <a name="the-waterfall-dialog"></a>O diálogo em cascata

O diálogo em cascata contém três etapas:

1. Inicia o diálogo de preenchimento de slot "fullname", que coletará e retornará o nome completo do usuário.
1. Registra o nome do usuário e inicia o diálogo adaptável, que coletará o restante das informações do usuário.
1. Grava as informações do usuário no acessador de propriedade de estado do usuário e resume para o usuário as informações coletadas.

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\RootDialog.cs**

<!-- [!code-csharp[Waterfall steps](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=159-199)]
-->

```csharp
private async Task<DialogTurnResult> StartDialogAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Start the child dialog. This will just get the user's first and last name.
    return await stepContext.BeginDialogAsync("fullname", null, cancellationToken);
}

private async Task<DialogTurnResult> DoAdaptiveDialog(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    object adaptiveOptions = null;
    if (stepContext.Result is IDictionary<string, object> result && result.Count > 0)
    {
        adaptiveOptions = new { fullname = result };
    }
    // begin the adaptive dialog. This in-turn will get user's age, shoe-size using adaptive inputs and subsequently
    // call the custom slot filling dialog to fill user address.
    return await stepContext.BeginDialogAsync(nameof(AdaptiveDialog), adaptiveOptions, cancellationToken);
}

private async Task<DialogTurnResult> ProcessResultsAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // To demonstrate that the slot dialog collected all the properties we will echo them back to the user.
    if (stepContext.Result is IDictionary<string, object> result && result.Count > 0)
    {
        // Now the waterfall is complete, save the data we have gathered into UserState.
        // This includes data returned by the adaptive dialog.
        var obj = await _userStateAccessor.GetAsync(stepContext.Context, () => new JObject());
        obj["data"] = new JObject
        {
            { "fullname",  $"{result["fullname"]}" },
            { "shoesize", $"{result["shoesize"]}" },
            { "address", $"{result["address"]}" },
        };

        await stepContext.Context.SendActivityAsync(MessageFactory.Text(obj["data"]["fullname"].Value<string>()), cancellationToken);
        await stepContext.Context.SendActivityAsync(MessageFactory.Text(obj["data"]["shoesize"].Value<string>()), cancellationToken);
        await stepContext.Context.SendActivityAsync(MessageFactory.Text(obj["data"]["address"].Value<string>()), cancellationToken);
    }

    // Remember to call EndAsync to indicate to the runtime that this is the end of our waterfall.
    return await stepContext.EndDialogAsync();
}
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/rootDialog.js**

<!-- [!code-javascript[Waterfall steps](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=113-145)]
-->

```javascript
async startDialog(step) {
    // Start the child dialog. This will just get the user's first and last name.
    return await step.beginDialog('fullname');
}

async doAdaptiveDialog(step) {
    let adaptiveOptions;
    if (step.result) {
        adaptiveOptions = { fullname: step.result.values };
    }

    // begin the adaptive dialog. This in-turn will get user's age, shoe-size using adaptive inputs and subsequently
    // call the custom slot filling dialog to fill user address.
    return await step.beginDialog(ADAPTIVE_DIALOG, adaptiveOptions);
}

// This is the second step of the WaterfallDialog.
// It receives the results of the SlotFillingDialog and displays them.
async processResults(step) {
    // Each "slot" in the SlotFillingDialog is represented by a field in step.result.values.
    // The complex that contain subfields have their own .values field containing the sub-values.
    const values = step.result;

    const fullname = values.fullname;
    await step.context.sendActivity(`Your name is ${ fullname.first } ${ fullname.last }.`);

    await step.context.sendActivity(`You wear a size ${ values.shoesize } shoes.`);

    const address = values.address.values;
    await step.context.sendActivity(`Your address is: ${ address.street }, ${ address.city } ${ address.zip }`);

    return await step.endDialog();
}
```

---

## <a name="the-adaptive-dialog"></a>O diálogo adaptável

O diálogo adaptável define um gatilho que é executado quando o diálogo é iniciado. O gatilho executará estas ações:

1. Usa um diálogo de entrada para solicitar a idade do usuário.
1. Usa um diálogo de entrada para solicitar o tamanho do calçado do usuário.
1. Inicia o diálogo de preenchimento de slot "address" para coletar o endereço do usuário.
1. Define o valor do resultado do gatilho e é encerrado.

Como nenhuma outra ação será colocada na fila, o diálogo adaptável também será encerrado e retornará esse valor do resultado.

O diálogo adaptável usa um gerador de linguagem para formatar o texto e incluir os valores do bot e do estado do diálogo. (Confira como usar [Geradores em diálogos adaptáveis][lg-in-adaptive] para obter mais informações.)

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\RootDialog.cs**

<!-- [!code-csharp[adaptive dialog and triggers](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=60-119&hihglight=3,7,10)]
-->

```csharp
// define adaptive dialog
var adaptiveSlotFillingDialog = new AdaptiveDialog();
adaptiveSlotFillingDialog.Id = nameof(AdaptiveDialog);

// Set a language generator
// You can see other adaptive dialog samples to learn how to externalize generation resources into .lg files.
adaptiveSlotFillingDialog.Generator = new TemplateEngineLanguageGenerator();

// add set of actions to perform when the adaptive dialog begins.
adaptiveSlotFillingDialog.Triggers.Add(new OnBeginDialog()
{
    Actions = new List<Dialog>()
    {
        // any options passed into adaptive dialog is automatically available under dialog.xxx
        // get user age
        new NumberInput()
        {
            Property = "dialog.userage",

            // use information passed in to the adaptive dialog.
            Prompt = new ActivityTemplate("Hello ${dialog.fullname.first}, what is your age?"),
            Validations = new List<BoolExpression>()
            {
                "int(this.value) >= 1",
                "int(this.value) <= 150"
            },
            InvalidPrompt = new ActivityTemplate("Sorry, ${this.value} does not work. Looking for age to be between 1-150. What is your age?"),
            UnrecognizedPrompt = new ActivityTemplate("Sorry, I did not understand ${this.value}. What is your age?"),
            MaxTurnCount = 3,
            DefaultValue = "=30",
            DefaultValueResponse = new ActivityTemplate("Sorry, this is not working. For now, I'm setting your age to ${this.defaultValue}"),
            AllowInterruptions = false
        },
        new NumberInput()
        {
            Property = "dialog.shoesize",
            Prompt = new ActivityTemplate("Please enter your shoe size."),
            InvalidPrompt = new ActivityTemplate("Sorry ${this.value} does not work. You must enter a size between 0 and 16. Half sizes are acceptable."),
            Validations = new List<BoolExpression>()
            {
                // size can only between 0-16
                "int(this.value) >= 0 && int(this.value) <= 16",
                // can only full or half size
                "isMatch(string(this.value), '^[0-9]+(\\.5)*$')"
            },
            AllowInterruptions = false
        },
        // get address - adaptive is calling the custom slot filling dialog here.
        new BeginDialog()
        {
            Dialog = "address",
            ResultProperty = "dialog.address"
        },
        // return everything under dialog scope. 
        new EndDialog()
        {
            Value = "=dialog"
        }
    }
}) ;
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/rootDialog.js**

<!-- [!code-javascript[adaptive dialog and triggers](~/../botbuilder-samples-adaptive/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=39-87)]
-->

```javascript
// define adaptive dialog
const adaptiveSlotFillingDialog = new AdaptiveDialog(ADAPTIVE_DIALOG);

// Set a language generator
// You can see other adaptive dialog samples to learn how to externalize generation resources into .lg files.
adaptiveSlotFillingDialog.generator = new TemplateEngineLanguageGenerator();

// add set of actions to perform when the adaptive dialog begins.
adaptiveSlotFillingDialog.triggers = [
    new OnBeginDialog([
        // any options passed into adaptive dialog is automatically available under dialog.xxx
        // get user age
        new NumberInput().configure({
            property: new StringExpression('dialog.userage'),
            // use information passed in to the adaptive dialog.
            prompt: new ActivityTemplate('Hello ${dialog.fullname.first}, what is your age?'),
            validations: [
                'int(this.value) >= 1',
                'int(this.value) <= 150'
            ],
            invalidPrompt: new ActivityTemplate('Sorry, ${this.value} does not work. Looking for age to be between 1-150. What is your age?'),
            unrecognizedPrompt: new ActivityTemplate('Sorry, I did not understand ${this.value}. What is your age?'),
            maxTurnCount: new NumberExpression(3),
            defaultValue: new ValueExpression('=30'),
            defaultValueResponse: new ActivityTemplate("Sorry, this is not working. For now, I'm setting your age to ${this.defaultValue}"),
            allowInterruptions: new BoolExpression(false)
        }),
        new NumberInput().configure({
            property: new StringExpression('dialog.shoesize'),
            prompt: new ActivityTemplate('Please enter your shoe size.'),
            invalidPrompt: new ActivityTemplate('Sorry ${this.value} does not work. You must enter a size between 0 and 16. Half sizes are acceptable.'),
            validations: [
                // size can only between 0-16
                'int(this.value) >= 0 && int(this.value) <= 16',
                // can only full or half size
                "isMatch(string(this.value), '^[0-9]+(\\.5)*$')"
            ],
            allowInterruptions: new BoolExpression(false)
        }),
        new BeginDialog().configure({
            dialog: new DialogExpression('address'),
            resultProperty: new StringExpression('dialog.address')
        }),
        // return everything under dialog scope.
        new EndDialog().configure({
            value: new ValueExpression('=dialog')
        })
    ])
];
```

---

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte-se ao bot e responda às solicitações: nome e sobrenome, tamanho do calçado, rua, cidade e CEP.
1. O bot exibirá as informações coletadas.
1. Envie ao bot qualquer mensagem para iniciar o processo novamente.

## <a name="additional-information"></a>Informações adicionais

Para obter mais informações sobre como usar cada tipo de diálogo, confira:

| Tipo de diálogo | Artigo
|:-|:-
| Diálogos adaptáveis e de entrada | [Criar um bot usando diálogos adaptáveis][basic-adaptive-how-to].
| Diálogos de componente | [Gerenciar a complexidade da caixa de diálogo][component-how-to]
| Diálogos personalizados | [Manipular interrupções do usuário][interruptions-how-to]
| Diálogos de solicitação e em cascata | [Implementar o fluxo de conversa sequencial][basic-dialog-how-to]

<!--
## Next steps
> [!div class="nextstepaction"]
> [TBD](tbd.md)
-->

<!-- Footnote-style links -->

[bot-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[about-dialogs]: bot-builder-concept-dialog.md
[about-adaptive-dialogs]: bot-builder-adaptive-dialog-Introduction.md
[about-input-dialogs]: bot-builder-concept-adaptive-dialog-inputs.md

[lg-in-adaptive]: bot-builder-concept-adaptive-dialog-generators.md

[basic-adaptive-how-to]: bot-builder-dialogs-adaptive.md
[basic-dialog-how-to]: bot-builder-dialog-manage-conversation-flow.md
[component-how-to]: bot-builder-compositcontrol.md
[interruptions-how-to]: bot-builder-howto-handle-user-interrupt.md

[cs-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/vishwac/r9/js/experimental/adaptive-dialog/csharp_dotnetcore/04.waterfall-or-custom-dialog-with-adaptive
[js-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/vishwac/r9/js/experimental/adaptive-dialog/javascript_nodejs/19.custom-dialogs
