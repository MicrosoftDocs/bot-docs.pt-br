---
title: Manipular interrupções do usuário | Microsoft Docs
description: Saiba como manipular as interrupções do usuário e orientar o fluxo da conversa.
keywords: interromper, interrupções, mudando o tópico, interrupção
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 04/18/2019
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9089334823c1c57c8ace48531c767c3f966b3355
ms.sourcegitcommit: aea57820b8a137047d59491b45320cf268043861
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59905009"
---
# <a name="handle-user-interruptions"></a>Manipular interrupções do usuário

[!INCLUDE[applies-to](../includes/applies-to.md)]

O tratamento de interrupções é um aspecto importante de um bot robusto.

Embora talvez você ache que os usuários seguirão o fluxo da conversa definido passo a passo, é muito provável que eles mudem de ideia ou façam uma pergunta no meio do processo, em vez de responderem à pergunta. Nessas situações, como o bot lida com a entrada do usuário? Como é a experiência do usuário? Como você mantém os dados de estado do usuário? Tratar interrupções significa ter certeza de que seu bot esteja preparado para lidar com situações como essa.

Não há nenhuma resposta correta para essas perguntas, pois cada situação é única para o cenário que o bot foi projetado para manipular. Neste tópico, exploraremos algumas maneiras comuns de manipular as interrupções do usuário e ofereceremos sugestões de como implementá-las no bot.

## <a name="handle-expected-interruptions"></a>Manipular as interrupções esperadas

Um fluxo da conversa de procedimento tem um conjunto principal de etapas pelo qual você deseja orientar o usuário, e as ações do usuário que se desviam dessas etapas são interrupções potenciais. Em um fluxo normal, há interrupções que podem ser previstas.

**Reserva de mesas** Em um bot de reserva de mesas, as principais etapas podem ser a solicitação ao usuário de uma data e uma hora, o tamanho do grupo de pessoas e o nome da reserva. Nesse processo, algumas interrupções esperadas que você pode prever podem incluir:

* `cancel`: para sair do processo.
* `help`: para fornecer diretrizes adicionais sobre esse processo.
* `more info`: para dar dicas e sugestões ou fornecer maneiras alternativas de reservar uma mesa (por exemplo: um endereço de email ou um número de telefone para contato).
* `show list of available tables`: se essa é uma opção, mostra uma lista de tabelas disponíveis para a data e a hora solicitadas pelo usuário.

**Pedido de jantar** Em um bot de pedido de jantar, as principais etapas são o fornecimento de uma lista de itens de menu e a permissão para que o usuário adicione itens ao carrinho. Nesse processo, algumas interrupções esperadas que você pode prever podem incluir:

* `cancel`: para sair do processo de pedido.
* `more info`: para fornecer detalhes nutricionais sobre cada item de menu.
* `help`: para fornecer ajuda sobre como usar o sistema.
* `process order`: para processar o pedido.

Você pode fornecer isso para o usuário como uma lista de **ações sugeridas** ou como uma dica para que o usuário esteja, pelo menos, ciente de quais comandos ele pode enviar que serão reconhecidos pelo bot.

Por exemplo, no fluxo de pedido de jantar, você pode fornecer as interrupções esperadas, juntamente com os itens de menu. Nesse caso, os itens de menu são enviados como uma matriz de `choices`.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

Vamos definir o conjunto de diálogos como uma subclasse de **DialogSet**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Choices;

public class OrderDinnerDialogs : DialogSet
{
    public OrderDinnerDialogs(IStatePropertyAccessor<DialogState> dialogStateAccessor)
        : base(dialogStateAccessor)
    {
    }
}
```

Definiremos algumas classes internas para descrever o menu.

```cs
/// <summary>
/// Contains information about an item on the menu.
/// </summary>
public class DinnerItem
{
    public string Description { get; set; }

    public double Price { get; set; }
}

/// <summary>
/// Describes the dinner menu, including the items on the menu and options for
/// interrupting the ordering process.
/// </summary>
public class DinnerMenu
{
    /// <summary>Gets the items on the menu.</summary>
    public static Dictionary<string, DinnerItem> MenuItems { get; } = new Dictionary<string, DinnerItem>
    {
        ["Potato salad"] = new DinnerItem { Description = "Potato Salad", Price = 5.99 },
        ["Tuna sandwich"] = new DinnerItem { Description = "Tuna Sandwich", Price = 6.89 },
        ["Clam chowder"] = new DinnerItem { Description = "Clam Chowder", Price = 4.50 },
    };

    /// <summary>Gets all the "interruptions" the bot knows how to process.</summary>
    public static List<string> Interrupts { get; } = new List<string>
    {
        "More info", "Process order", "Help", "Cancel",
    };

    /// <summary>Gets all of the valid inputs a user can make.</summary>
    public static List<string> Choices { get; }
        = MenuItems.Select(c => c.Key).Concat(Interrupts).ToList();
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Começaremos de um modelo básico de EchoBot. Para obter instruções, confira o [início rápido para JavaScript](~/javascript/bot-builder-javascript-quickstart.md).

A biblioteca `botbuilder-dialogs` pode ser baixada no NPM. Para instalar a biblioteca `botbuilder-dialogs`, execute o seguinte comando npm:

```cmd
npm install --save botbuilder-dialogs
```

Em seu arquivo **bot.js**, faça referência às classes as quais faremos referência e defina identificadores que usaremos para o diálogo.

```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, ChoicePrompt, WaterfallDialog, DialogTurnStatus } = require('botbuilder-dialogs');

// Name for the dialog state property accessor.
const DIALOG_STATE_PROPERTY = 'dialogStateProperty';

// Name of the order-prompt dialog.
const ORDER_PROMPT = 'orderingDialog';

// Name for the choice prompt for use in the dialog.
const CHOICE_PROMPT = 'choicePrompt';
```

Defina as opções que queremos exibir em nosso diálogo de pedido.

```javascript
// The options on the dinner menu, including commands for the bot.
const dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50",
        "Process order", "Cancel", "More info", "Help"],
    "Potato Salad - $5.99": {
        description: "Potato Salad",
        price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        description: "Tuna Sandwich",
        price: 6.89
    },
    "Clam Chowder - $4.50": {
        description: "Clam Chowder",
        price: 4.50
    }
}
```

---

Na lógica de ordenação, você pode verificá-los usando uma correspondência de cadeia de caracteres ou expressões regulares.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

Primeiro, precisamos definir um auxiliar para acompanhar nossos pedidos.

```cs
/// <summary>Helper class for storing the order.</summary>
public class Order
{
    public double Total { get; set; } = 0.0;

    public List<DinnerItem> Items { get; set; } = new List<DinnerItem>();

    public bool ReadyToProcess { get; set; } = false;

    public bool OrderProcessed { get; set; } = false;
}
```

Adicione algumas constantes para acompanhar as IDs que precisaremos.

```csharp
/// <summary>The ID of the top-level dialog.</summary>
public const string MainDialogId = "mainMenu";

/// <summary>The ID of the choice prompt.</summary>
public const string ChoicePromptId = "choicePrompt";

/// <summary>The ID of the order card value, tracked inside the dialog.</summary>
public const string OrderCartId = "orderCart";
```

Atualize o construtor para adicionar um prompt de escolha e nosso diálogo em cascata. Também definiremos os métodos que implementam as etapas em cascata.

```cs
public OrderDinnerDialogs(IStatePropertyAccessor<DialogState> dialogStateAccessor)
    : base(dialogStateAccessor)
{
    // Add a choice prompt for the dialog.
    Add(new ChoicePrompt(ChoicePromptId));

    // Define and add the main waterfall dialog.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        PromptUserAsync,
        ProcessInputAsync,
    };

    Add(new WaterfallDialog(MainDialogId, steps));
}

/// <summary>
/// Defines the first step of the main dialog, which is to ask for input from the user.
/// </summary>
/// <param name="stepContext">The current waterfall step context.</param>
/// <param name="cancellationToken">The cancellation token.</param>
/// <returns>The task to perform.</returns>
private async Task<DialogTurnResult> PromptUserAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Initialize order, continuing any order that was passed in.
    Order order = (stepContext.Options is Order oldCart && oldCart != null)
        ? new Order
        {
            Items = new List<DinnerItem>(oldCart.Items),
            Total = oldCart.Total,
            ReadyToProcess = oldCart.ReadyToProcess,
            OrderProcessed = oldCart.OrderProcessed,
        }
        : new Order();

    // Set the order cart in dialog state.
    stepContext.Values[OrderCartId] = order;

    // Prompt the user.
    return await stepContext.PromptAsync(
        "choicePrompt",
        new PromptOptions
        {
            Prompt = MessageFactory.Text("What would you like for dinner?"),
            RetryPrompt = MessageFactory.Text(
                "I'm sorry, I didn't understand that. What would you like for dinner?"),
            Choices = ChoiceFactory.ToChoices(DinnerMenu.Choices),
        },
        cancellationToken);
}

/// <summary>
/// Defines the second step of the main dialog, which is to process the user's input, and
/// repeat or exit as appropriate.
/// </summary>
/// <param name="stepContext">The current waterfall step context.</param>
/// <param name="cancellationToken">The cancellation token.</param>
/// <returns>The task to perform.</returns>
private async Task<DialogTurnResult> ProcessInputAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the order cart from dialog state.
    Order order = stepContext.Values[OrderCartId] as Order;

    // Get the user's choice from the previous prompt.
    string response = (stepContext.Result as FoundChoice).Value;

    if (response.Equals("process order", StringComparison.InvariantCultureIgnoreCase))
    {
        order.ReadyToProcess = true;

        await stepContext.Context.SendActivityAsync(
            "Your order is on it's way!",
            cancellationToken: cancellationToken);

        // In production, you may want to store something more helpful.
        // "Process" the order and exit.
        order.OrderProcessed = true;
        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
    else if (response.Equals("cancel", StringComparison.InvariantCultureIgnoreCase))
    {
        // Cancel the order.
        await stepContext.Context.SendActivityAsync(
            "Your order has been canceled",
            cancellationToken: cancellationToken);

        // Exit without processing the order.
        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
    else if (response.Equals("more info", StringComparison.InvariantCultureIgnoreCase))
    {
        // Send more information about the options.
        string message = "More info: <br/>" +
            "Potato Salad: contains 330 calories per serving. Cost: 5.99 <br/>"
            + "Tuna Sandwich: contains 700 calories per serving. Cost: 6.89 <br/>"
            + "Clam Chowder: contains 650 calories per serving. Cost: 4.50";
        await stepContext.Context.SendActivityAsync(
            message,
            cancellationToken: cancellationToken);

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }
    else if (response.Equals("help", StringComparison.InvariantCultureIgnoreCase))
    {
        // Provide help information.
        string message = "To make an order, add as many items to your cart as " +
            "you like. Choose the `Process order` to check out. " +
            "Choose `Cancel` to cancel your order and exit.";
        await stepContext.Context.SendActivityAsync(
            message,
            cancellationToken: cancellationToken);

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }

    // We've checked for expected interruptions. Check for a valid item choice.
    if (!DinnerMenu.MenuItems.ContainsKey(response))
    {
        await stepContext.Context.SendActivityAsync("Sorry, that is not a valid item. " +
            "Please pick one from the menu.");

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }
    else
    {
        // Add the item to cart.
        DinnerItem item = DinnerMenu.MenuItems[response];
        order.Items.Add(item);
        order.Total += item.Price;

        // Acknowledge the input.
        await stepContext.Context.SendActivityAsync(
            $"Added `{response}` to your order; your total is ${order.Total:0.00}.",
            cancellationToken: cancellationToken);

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Defina seu diálogo do construtor de bot. Observe que o código verifica e manipula as interrupções _primeiro_ e, depois, prossegue para a próxima etapa lógica.

```javascript
constructor(conversationState) {
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    this.conversationState = conversationState;

    this.dialogs = new DialogSet(this.dialogStateAccessor)
        .add(new ChoicePrompt(CHOICE_PROMPT))
        .add(new WaterfallDialog(ORDER_PROMPT, [
            async (step) => {
                if (step.options && step.options.orders) {
                    // If an order cart was passed in, continue to use it.
                    step.values.orderCart = step.options;
                } else {
                    // Otherwise, start a new cart.
                    step.values.orderCart = {
                        orders: [],
                        total: 0
                    };
                }
                return await step.prompt(CHOICE_PROMPT, "What would you like?", dinnerMenu.choices);
            },
            async (step) => {
                const choice = step.result;
                if (choice.value.match(/process order/ig)) {
                    if (step.values.orderCart.orders.length > 0) {
                        // If the cart is not empty, process the order by returning the order to the parent context.
                        await step.context.sendActivity("Your order has been processed.");
                        return await step.endDialog(step.values.orderCart);
                    } else {
                        // Otherwise, prompt again.
                        await step.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                        return await step.replaceDialog(ORDER_PROMPT);
                    }
                } else if (choice.value.match(/cancel/ig)) {
                    // Cancel the order, and return "cancel" to the parent context.
                    await step.context.sendActivity("Your order has been canceled.");
                    return await step.endDialog("cancelled");
                } else if (choice.value.match(/more info/ig)) {
                    // Provide more information, and then continue the ordering process.
                    var msg = "More info: <br/>Potato Salad: contains 330 calories per serving. <br/>"
                        + "Tuna Sandwich: contains 700 calories per serving. <br/>"
                        + "Clam Chowder: contains 650 calories per serving."
                    await step.context.sendActivity(msg);
                    return await step.replaceDialog(ORDER_PROMPT, step.values.orderCart);
                } else if (choice.value.match(/help/ig)) {
                    // Provide help information, and then continue the ordering process.
                    var msg = `Help: <br/>`
                        + `To make an order, add as many items to your cart as you like then choose `
                        + `the "Process order" option to check out.`
                    await step.context.sendActivity(msg);
                    return await step.replaceDialog(ORDER_PROMPT, step.values.orderCart);
                } else {
                    // The user has chosen a food item from the menu. Add the item to cart.
                    var item = dinnerMenu[choice.value];
                    step.values.orderCart.orders.push(item.description);
                    step.values.orderCart.total += item.price;

                    await step.context.sendActivity(`Added ${item.description} to your cart. <br/>`
                        + `Current total: $${step.values.orderCart.total}`);

                    // Continue the ordering process.
                    return await step.replaceDialog(ORDER_PROMPT, step.values.orderCart);
                }
            }
        ]));
}
```

Atualize o manipulador de turno para chamar o diálogo e exibir os resultados do processo de pedido.

```javascript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        let dc = await this.dialogs.createContext(turnContext);
        let dialogTurnResult = await dc.continueDialog();
        if (dialogTurnResult.status === DialogTurnStatus.complete) {
            // The dialog completed this turn.
            const result = dialogTurnResult.result;
            if (!result || result === "cancelled") {
                await turnContext.sendActivity('You cancelled your order.');
            } else {
                await turnContext.sendActivity(`Your order came to $${result.total}`);
            }
        } else if (!turnContext.responded) {
            // No dialog was active.
            await turnContext.sendActivity("Let's order dinner...");
            await dc.cancelAllDialogs();
            await dc.beginDialog(ORDER_PROMPT);
        } else {
            // The dialog is active.
        }
    } else {
        await turnContext.sendActivity(`[${turnContext.activity.type} event detected]`);
    }
    // Save state changes
    await this.conversationState.saveChanges(turnContext);
}
```

---

## <a name="handle-unexpected-interruptions"></a>Manipular as interrupções inesperadas

Há interrupções que estão fora do escopo do que o bot foi projetado para fazer.
Embora você não possa prever todas as interrupções, existem padrões de interrupções que você pode programar o bot para manipular.

### <a name="switching-topic-of-conversations"></a>Mudando o tópico de conversas

E se o usuário estiver no meio de uma conversa e desejar mudar para outra? Por exemplo, o bot pode reservar uma mesa e anotar o pedido de jantar.
Enquanto o usuário está no fluxo _reservar uma mesa_, em vez de responder à pergunta "Quantas pessoas fazem parte de seu grupo?", ele envia a mensagem "pedir um jantar". Nesse caso, o usuário mudou de ideia e deseja participar de uma conversa de pedido de jantar. Como você deve lidar com essa interrupção?

Você pode mudar de tópico para o fluxo de pedido de jantar ou insistir no assunto informando ao usuário que você espera um número e solicitá-lo novamente. Se permitir que ele mude de tópico, você precisará decidir se salvará o progresso, de modo que o usuário possa recomeçar no ponto em que parou, ou poderá excluir todas as informações coletadas, de modo que ele precise recomeçar todo o processo na próxima vez que desejar reservar uma mesa. Para obter mais informações sobre como gerenciar os dados de estado do usuário, confira [Salvar o estado usando as propriedades da conversa e do usuário](bot-builder-howto-v4-state.md).

### <a name="apply-artificial-intelligence"></a>Aplicar inteligência artificial

Para as interrupções que não estão no escopo, você pode tentar adivinhar qual é a intenção do usuário. Faça isso usando os serviços de IA, como o QnA Maker, o LUIS ou sua lógica personalizada e ofereça sugestões para o que o bot acha que o usuário deseja.

Por exemplo, enquanto está no meio do fluxo de reserva de mesa, o usuário diz: "Quero pedir um sanduíche". Isso não é algo que o bot sabe manipular nesse fluxo da conversa. Como o fluxo atual não tem nada a ver com o pedido e o outro comando de conversa do bot é "pedido de jantar", o bot não sabe o que fazer com essa entrada. Se você aplicar o LUIS, por exemplo, poderá treinar o modelo para reconhecer que ele deseja pedir algo para comer (por ex.: o LUIS pode retornar uma intenção "orderFood"). Portanto, o bot poderia responder com "Parece que você deseja pedir algo para comer. Gostaria de mudar para nosso processo de pedido de jantar?" Para saber mais sobre como treinar o LUIS e detectar as intenções do usuário, confira [Usar o LUIS para reconhecimento vocal](bot-builder-howto-v4-luis.md).

### <a name="default-response"></a>Resposta padrão

Se tudo o mais falhar, você deverá enviar uma resposta padrão em vez de não realizar nenhuma ação e deixar o usuário se perguntando o que está acontecendo. A resposta padrão deve informar o usuário quais comandos são reconhecidos pelo bot, de modo que o usuário possa voltar para um tópico adequado.

Verifique o sinalizador **respondido** no contexto ao final da lógica do bot para ver se o bot enviou algo para o usuário durante seu turno. Se o bot processar a entrada do usuário, mas não respondê-la, é provável que o bot não saiba o que fazer com a entrada. Nesse caso, você pode capturá-la e enviar ao usuário uma mensagem padrão.

Se o padrão para o bot é dar ao usuário uma caixa de diálogo `mainMenu`, você deve decidir qual experiência de bot seu usuário terá nessa situação.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

```cs
// Check whether we replied. If not then clear the dialog stack and present the main menu.
if (!turnContext.Responded)
{
    await dc.CancelAllDialogsAsync(cancellationToken);
    await dc.BeginDialogAsync(OrderDinnerDialogs.MainDialogId, null, cancellationToken);
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Check to see if anyone replied. If not then clear all the stack and present the main menu
if (!context.responded) {
    await dc.cancelAllDialogs();
    await step.beginDialog('mainMenu');
}
```

---

## <a name="handling-global-interruptions"></a>Tratando interrupções globais

No exemplo acima, lidamos com as interrupções que podem ocorrer em um turno específico, em um diálogo específico. E se quisermos lidar com interrupções globais - do tipo que pode ocorrer a qualquer momento?

Isso pode ser feito colocando a nossa lógica de tratamento de interrupção no manipulador principal do bot - a função que processa a entrada de `turnContext` e decide o que fazer com isso.

No exemplo a seguir, a _primeira coisa_ que o bot faz é verificar o texto da mensagem recebida em busca de um sinal de que o usuário precisa de ajuda ou quer cancelar - duas interrupções muito comuns encontradas por bots. Após a conclusão dessa verificação, o bot chama `dc.continueDialog()` para processar qualquer diálogo ativo ainda pendente.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

```cs
// Check for top-level interruptions.
string utterance = turnContext.Activity.Text.Trim().ToLowerInvariant();

if (utterance == "help")
{
    // Start a general help dialog. Dialogs already on the stack remain and will continue
    // normally if the help dialog exits normally.
    await dc.BeginDialogAsync(OrderDinnerDialogs.HelpDialogId, null, cancellationToken);
}
else if (utterance == "cancel")
{
    // Cancel any dialog on the stack.
    await turnContext.SendActivityAsync("Canceled.", cancellationToken: cancellationToken);
    await dc.CancelAllDialogsAsync(cancellationToken);
}
else
{
    await dc.ContinueDialogAsync(cancellationToken);

    // Check whether we replied. If not then clear the dialog stack and present the main menu.
    if (!turnContext.Responded)
    {
        await dc.CancelAllDialogsAsync(cancellationToken);
        await dc.BeginDialogAsync(OrderDinnerDialogs.MainDialogId, null, cancellationToken);
    }
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Podemos lidar com interrupções antes de enviar a resposta de um usuário para um diálogo.

Este snippet pressupõe que temos um `helpDialog` e um `mainMenu` em nosso conjunto de diálogos.

```javascript
const utterance = (context.activity.text || '').trim();

// Let's look for some interruptions first!
if (utterance.match(/help/ig)) {
    // Launch a new help dialog if the user asked for help
    await dc.beginDialog('helpDialog');
} else if (utterance.match(/cancel/ig)) {
    // Cancel any active dialogs if the user says cancel
    await dc.context.sendActivity('Canceled.');
    await dc.cancelAllDialogs();
}

// If the bot hasn't yet responded...
if (!context.responded) {
    // Continue any active dialog, which might send a response...
    await dc.continueDialog();

    // Finally, if the bot still hasn't sent a response, send instructions.
    if (!context.responded) {
        await dc.cancelAllDialogs();
        await context.sendActivity("Starting the main menu...");
        await dc.beginDialog('mainMenu');
    }
}
```

---
