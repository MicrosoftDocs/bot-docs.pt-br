---
title: Gerenciar um fluxo de conversa complexo com diálogos | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa complexo com diálogos no SDK do Construtor de Bot para Node.js.
keywords: fluxo de conversa complexo, repetição, loop, menu, diálogos, prompts, cascatas, conjunto de diálogos
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 10/03/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 871bfd9f8d693c5082fe1ccf38349f4d3d46ece2
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326563"
---
# <a name="manage-complex-conversation-flows-with-dialogs"></a>Gerenciar fluxos de conversas complexos com diálogos

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

No último artigo, demonstramos como usar a biblioteca de diálogos para gerenciar conversas simples. Em [fluxos de conversa simples](bot-builder-dialog-manage-conversation-flow.md), o usuário começa a primeira etapa de uma *cascata*, percorre até a última etapa, e a conversa termina. Neste artigo, usaremos os diálogos para gerenciar conversas mais complexas, com partes que podem se ramificar e entrar em loop. Para fazer isso, vamos usar vários métodos definidos no contexto de diálogo e no contexto da etapa de cascata, além de passar argumentos entre diferentes partes do diálogo.

Confira [Biblioteca de diálogos](bot-builder-concept-dialog.md) para obter mais informações básicas sobre diálogos.

Para lhe dar mais controle sobre a *pilha de diálogos*, a biblioteca de **Diálogos** fornece um método _replace dialog_. Esse método permite que você troque o diálogo ativo no momento por outro, mantendo o estado e o fluxo da conversa. Os métodos _begin dialog_ e _replace dialog_ permitem que você execute a ramificação ou o loop, conforme necessário, para criar interações mais complexas. Caso a complexidade da conversa aumente de modo que seus diálogos de cascata fiquem difíceis de gerenciar, investigue usando [diálogos do componente](bot-builder-compositcontrol.md) ou criando uma classe de gerenciamento de diálogo personalizada com base na classe `Dialog`.

Neste artigo, vamos criar diálogos de exemplo para um bot de concierge de hotel que um hóspede poderia usar para acessar serviços comuns, como reservar uma mesa no restaurante do hotel e solicitar uma refeição do serviço de quarto.  Cada um desses recursos, juntamente com um menu que os conecta, será criado como diálogos em um conjunto de diálogos.

O diálogo de nível superior do bot dá ao hóspede duas opções. Caso o hóspede queira reservar uma mesa, o diálogo de nível superior usa o método assíncrono _begin dialog_ para iniciar o diálogo de reserva de mesa. Se o hóspede quiser solicitar o serviço de quarto, o diálogo de nível superior iniciará um diálogo de pedido da refeição.

Primeiro, o diálogo de pedido de refeição solicita que o hóspede escolha os alimentos apresentados em um cardápio, depois solicita o número do quarto. A escolha dos itens de alimento _também_ é um diálogo. Ele é acionado várias vezes à medida que o hóspede escolhe itens do menu antes de enviar o pedido do jantar.

Este diagrama ilustra os diálogos que criaremos neste artigo e suas relações uns aos outros.

![Ilustração dos diálogos usados neste artigo](~/media/complex-conversation-flows.png)

## <a name="how-to-branch"></a>Como ramificar

O contexto do diálogo mantém uma _pilha de diálogos_ e, para cada diálogo na pilha, controla a etapa a seguir. O método _begin dialog_ envia um diálogo para o topo da pilha e o método _end dialog_ retira o primeiro diálogo da pilha.

Para ramificar, escolha um diálogo filho presente no conjunto para iniciar. Para obter mais informações sobre esse conceito, confira [ramificar uma conversa](bot-builder-concept-dialog.md#branch-a-conversation).

## <a name="how-to-loop"></a>Como criar um loop

O método _replace dialog_ do contexto do diálogo permite substituir o diálogo que está no topo da pilha. O estado do diálogo antigo é descartado, e o novo diálogo começa desde o início. Você pode usar esse método para criar um loop substituindo um diálogo por ele próprio. No entanto, para [persistir qualquer informação que o bot já tenha coletado](bot-builder-howto-v4-state.md), você precisará passar essa informação para a nova instância do diálogo na chamada para o método _replace dialog_.

No exemplo a seguir, você verá que o pedido de serviço de quarto está armazenado no estado do diálogo e, quando o diálogo `orderPrompt` for repetido, o estado atual do diálogo será passado para que o estado do novo diálogo possa continuar a adicionar itens a ele. Se você preferir armazenar essas informações em um estado de bot fora do diálogo, confira como [persistir dados de usuário](bot-builder-tutorial-persist-user-inputs.md).

## <a name="create-the-dialogs-for-the-hotel-bot"></a>Criar os diálogos para o bot de hotel

Nesta seção, criaremos os diálogos para gerenciar uma conversa para o bot de hotel que descrevemos.

### <a name="install-the-dialogs-library"></a>Instalar a biblioteca de diálogos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Começaremos de um modelo básico de EchoBot. Para obter instruções, consulte o [início rápido para .NET](../dotnet/bot-builder-dotnet-sdk-quickstart.md).

Para usar diálogos, instale o pacote do NuGet `Microsoft.Bot.Builder.Dialogs` do projeto ou solução.
Depois, consulte a biblioteca de diálogos usando as instruções em seus arquivos de código, conforme o necessário.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Começaremos com um modelo de EchoBot. Para obter instruções, confira o [início rápido para JavaScript](../javascript/bot-builder-javascript-quickstart.md).



A biblioteca `botbuilder-dialogs` pode ser baixada do npm. Para instalar a biblioteca `botbuilder-dialogs`, execute o seguinte comando npm:

```cmd
npm install --save botbuilder-dialogs
```

Para usar **diálogos** no seu bot, inclua-o no código do bot. Por exemplo, adicione isso ao arquivo **index.js**:

```javascript
const { DialogSet } = require('botbuilder-dialogs');
```

E esta opção ao arquivo **bot.js**:

```javascript
const { DialogSet, NumberPrompt, ChoicePrompt, WaterfallDialog } = require('botbuilder-dialogs');
```

---

### <a name="create-a-dialog-set"></a>Criar uma conjunto de diálogos

Crie um _conjunto de diálogos_ ao qual adicionaremos todos os diálogos para este exemplo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Crie uma classe **HotelDialogs** e adicione as instruções de uso necessárias.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Choices;
using Microsoft.Bot.Schema;
```

Derive a classe de **DialogSet**. Inclua um construtor que aceite um parâmetro `IStatePropertyAccessor<DialogState>` que será usado para gerenciar o estado interno de uma instância do conjunto de diálogos. Além disso, defina as IDs e chaves que usaremos para identificar os diálogos, os prompts e as informações de estado para esse conjunto de diálogos.

```csharp
/// <summary>Contains the set of dialogs and prompts for the hotel bot.</summary>
public class HotelDialogs : DialogSet
{
    /// <summary>The ID of the top-level dialog.</summary>
    public const string MainMenu = "mainMenu";

    public HotelDialogs(IStatePropertyAccessor<DialogState> dialogStateAccessor)
        : base(dialogStateAccessor)
    {
    }

    /// <summary>Contains the IDs for the other dialogs in the set.</summary>
    private static class Dialogs
    {
        public const string OrderDinner = "orderDinner";
        public const string OrderPrompt = "orderPrompt";
        public const string ReserveTable = "reserveTable";
    }

    /// <summary>Contains the IDs for the prompts used by the dialogs.</summary>
    private static class Inputs
    {
        public const string Choice = "choicePrompt";
        public const string Number = "numberPrompt";
    }

    /// <summary>Contains the keys used to manage dialog state.</summary>
    private static class Outputs
    {
        public const string OrderCart = "orderCart";
        public const string OrderTotal = "orderTotal";
        public const string RoomNumber = "roomNumber";
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No arquivo **index.js**, adicione o código para criar um acessador de propriedade de estado para gerenciar o estado do diálogo e use-o para criar o conjunto de diálogos que usaremos para o bot.

```javascript
// Create conversation state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const dialogStateAccessor = conversationState.createProperty('dialogState');

// Create a dialog set for the bot.
const dialogSet = new DialogSet(dialogStateAccessor);

// Create the bot.
const bot = new MyBot(conversationState, dialogSet)
```

Depois, atualize a chamada de processamento de atividade para usar o objeto do bot.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        // Route to the bot's turn handler.
        await bot.onTurn(context);
    });
});
```

---

### <a name="add-the-prompts-to-the-set"></a>Adicionar os prompts ao conjunto

Usaremos um **ChoicePrompt** para perguntar aos hóspedes se querem fazer um pedido ou reservar uma mesa, e também qual opção escolher do cardápio. E, usaremos um **NumberPrompt** para perguntar o número do quarto do hóspede quando ele pedir a refeição.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No construtor **HotelDialogs**, adicione os dois prompts.

```csharp
// Add the prompts.
Add(new ChoicePrompt(Inputs.Choice));
Add(new NumberPrompt<int>(Inputs.Number));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atualize o construtor do bot e adicione dois prompts ao conjunto de diálogos.

```javascript
constructor(conversationState, dialogSet) {
    // Creates a new state accessor property.
    // See https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors.
    this.countProperty = conversationState.createProperty(TURN_COUNTER_PROPERTY);
    this.conversationState = conversationState;
    this.dialogSet = dialogSet;

    this.dialogSet.add(new ChoicePrompt('choicePrompt'));
    this.dialogSet.add(new NumberPrompt('numberPrompt'));
}
```

---

### <a name="define-some-of-the-supporting-information"></a>Definir algumas informações de suporte

Já que vamos precisar de informações sobre cada opção do cardápio de refeições, vamos configurar isso agora.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Crie uma classe interna estática **Lists** para conter essas informações. Também criaremos classes internas **WelcomeChoice** e **MenuChoice** para conter informações sobre cada opção.

Enquanto isso, adicionaremos informações para a lista de opções no diálogo de boas-vindas principal, e também criaremos listas de suporte para uso posterior ao solicitar essas informações aos hóspedes. É um pouco mais de trabalho já no início, mas simplificará o código do diálogo.

```csharp
/// <summary>Describes an option for the top-level dialog.</summary>
private class WelcomeChoice
{
    /// <summary>Gets or sets the text to show the guest for this option.</summary>
    public string Description { get; set; }

    /// <summary>Gets or sets the ID of the associated dialog for this option.</summary>
    public string DialogName { get; set; }
}

/// <summary>Describes an option for the food-selection dialog.</summary>
/// <remarks>We have two types of options. One represents meal items that the guest
/// can add to their order. The other represents a request to process or cancel the
/// order.</remarks>
private class MenuChoice
{
    /// <summary>The request text for cancelling the meal order.</summary>
    public const string Cancel = "Cancel order";

    /// <summary>The request text for processing the meal order.</summary>
    public const string Process = "Process order";

    /// <summary>Gets or sets the name of the meal item or the request.</summary>
    public string Name { get; set; }

    /// <summary>Gets or sets the price of the meal item; or NaN for a request.</summary>
    public double Price { get; set; }

    /// <summary>Gets the text to show the guest for this option.</summary>
    public string Description => double.IsNaN(Price) ? Name : $"{Name} - ${Price:0.00}";
}
```

```csharp
/// <summary>Contains the lists used to present options to the guest.</summary>
private static class Lists
{
    /// <summary>Gets the options for the top-level dialog.</summary>
    public static List<WelcomeChoice> WelcomeOptions { get; } = new List<WelcomeChoice>
    {
        new WelcomeChoice { Description = "Order dinner", DialogName = Dialogs.OrderDinner },
        new WelcomeChoice { Description = "Reserve a table", DialogName = Dialogs.ReserveTable },
    };

    private static readonly List<string> _welcomeList = WelcomeOptions.Select(x => x.Description).ToList();

    /// <summary>Gets the choices to present in the choice prompt for the top-level dialog.</summary>
    public static IList<Choice> WelcomeChoices { get; } = ChoiceFactory.ToChoices(_welcomeList);

    /// <summary>Gets the reprompt action for the top-level dialog.</summary>
    public static Activity WelcomeReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(_welcomeList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }

    /// <summary>Gets the options for the food-selection dialog.</summary>
    public static List<MenuChoice> MenuOptions { get; } = new List<MenuChoice>
    {
        new MenuChoice { Name = "Potato Salad", Price = 5.99 },
        new MenuChoice { Name = "Tuna Sandwich", Price = 6.89 },
        new MenuChoice { Name = "Clam Chowder", Price = 4.50 },
        new MenuChoice { Name = MenuChoice.Process, Price = double.NaN },
        new MenuChoice { Name = MenuChoice.Cancel, Price = double.NaN },
    };

    private static readonly List<string> _menuList = MenuOptions.Select(x => x.Description).ToList();

    /// <summary>Gets the choices to present in the choice prompt for the food-selection dialog.</summary>
    public static IList<Choice> MenuChoices { get; } = ChoiceFactory.ToChoices(_menuList);

    /// <summary>Gets the reprompt action for the food-selection dialog.</summary>
    public static Activity MenuReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(_menuList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No arquivo **bot.js**, crie uma constante **dinnerMenu** para conter essas informações.

```javascript
const dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50",
        "Process order", "Cancel"],
    "Potato Salad - $5.99": {
        Description: "Potato Salad",
        Price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        Description: "Tuna Sandwich",
        Price: 6.89
    },
    "Clam Chowder - $4.50": {
        Description: "Clam Chowder",
        Price: 4.50
    }
}
```

---

### <a name="create-the-welcome-dialog"></a>Criar a caixa de diálogo de boas-vindas

Esse diálogo usa um `ChoicePrompt` para exibir o menu, depois aguarda o usuário escolher uma opção. Quando o usuário escolhe `Order dinner` ou `Reserve a table`, ele inicia o diálogo apropriado. Ao terminar o diálogo filho, em vez de apenas encerrar o diálogo na última etapa e deixar o usuário se perguntando o que está acontecendo, o diálogo `mainMenu` entra em loop, reiniciando o diálogo `mainMenu`. Com essa estrutura simples, o bot sempre mostrará o menu, e o usuário sempre saberá quais são suas opções.

O diálogo `mainMenu` contém estas etapas em cascata:

* Na primeira etapa da cascata, inicializamos ou limpamos o estado do diálogo, saudamos o hóspede e pedimos que escolha entre as opções disponíveis: `Order dinner` ou `Reserve a table`.
* Na segunda etapa, recuperamos a seleção e iniciamos o diálogo filho associado a ela. Após o término do diálogo filho, esse diálogo continuará com a etapa a seguir.
* Na última etapa, usamos o método _replace dialog_ para substituir esse diálogo por uma nova instância dele mesmo, o que transforma efetivamente o diálogo de boas-vindas em um menu em loop.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No construtor, adicione o diálogo de cascata. Depois, defina as etapas da cascata. Aqui, definimos as etapas em uma classe aninhada.

No construtor **HotelDialogs**.

```csharp
// Define the steps for and add the main welcome dialog.
WaterfallStep[] welcomeDialogSteps = new WaterfallStep[]
{
    MainDialogSteps.PresentMenuAsync,
    MainDialogSteps.ProcessInputAsync,
    MainDialogSteps.RepeatMenuAsync,
};

Add(new WaterfallDialog(MainMenu, welcomeDialogSteps));
```

Na classe **HotelDialogs**, adicione as definições das etapas.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the order-dinner dialog.
/// </summary>
private static class MainDialogSteps
{
    public static async Task<DialogTurnResult> PresentMenuAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Greet the guest and ask them to choose an option.
        await stepContext.Context.SendActivityAsync(
            "Welcome to Contoso Hotel and Resort.",
            cancellationToken: cancellationToken);
        return await stepContext.PromptAsync(
            Inputs.Choice,
            new PromptOptions
            {
                Prompt = MessageFactory.Text("How may we serve you today?"),
                RetryPrompt = Lists.WelcomeReprompt,
                Choices = Lists.WelcomeChoices,
            },
            cancellationToken);
    }

    public static async Task<DialogTurnResult> ProcessInputAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Begin a child dialog associated with the chosen option.
        var choice = (FoundChoice)stepContext.Result;
        var dialogId = Lists.WelcomeOptions[choice.Index].DialogName;

        return await stepContext.BeginDialogAsync(dialogId, null, cancellationToken);
    }

    public static async Task<DialogTurnResult> RepeatMenuAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Start this dialog over again.
        return await stepContext.ReplaceDialogAsync(MainMenu, null, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor do bot, adicione o diálogo de cascata `mainMenu`.

```javascript
// Display a menu and ask user to choose a menu item. Direct user to the item selected otherwise, show
// the menu again.
this.dialogSet.add(new WaterfallDialog('mainMenu', [
    async function (step) {
        // Welcome the user and send a prompt.
        await step.context.sendActivity("Welcome to Contoso Hotel and Resort.");
        return await step.prompt('choicePrompt', "How may we serve you today?", ['Order Dinner', 'Reserve a table']);
    },
    async function (step) {
        // Handle the user's response to the previous prompt and branch the dialog.
        if (step.result.value.match(/order dinner/ig)) {
            return await step.beginDialog('orderDinner');
        } else if (step.result.value.match(/reserve a table/ig)) {
            return await step.beginDialog('reserveTable');
        }
    },
    async function (step) {
        // Calling replaceDialog will loop the main menu
        return await step.replaceDialog('mainMenu');
    }
]));
```

---

### <a name="create-the-order-dinner-dialog"></a>Criar o diálogo de pedido de refeição

No diálogo de pedido de refeição, daremos as boas-vindas ao hóspede no "serviço de pedido de refeições" e iniciaremos imediatamente o diálogo de seleção de alimentos, que descreveremos na próxima seção. É importante observar que, se o hóspede pedir que o serviço processe seu pedido, o diálogo de seleção de alimentos retornará a lista de itens do pedido. Para concluir o processo, esse diálogo solicita um número de quarto para entregar a comida, depois envia uma mensagem de confirmação. Se o hóspede cancelar o pedido, esse diálogo não solicitará um número de quarto e terminará imediatamente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para a classe **HotelDialog**, adicione uma estrutura de dados que possamos usar para controlar o pedido de jantar do hóspede. Uma vez que isso será persistido no estado de diálogo, a classe precisa do construtor padrão para serialização.

```csharp
/// <summary>Contains the guest's dinner order.</summary>
private class OrderCart : List<MenuChoice>
{
    public OrderCart() : base() { }

    public OrderCart(OrderCart other) : base(other) { }
}
```

No construtor, adicione o diálogo de cascata. Depois, defina as etapas da cascata. Aqui, definimos as etapas em uma classe aninhada.

No construtor **HotelDialogs**.

```csharp
// Define the steps for and add the order-dinner dialog.
WaterfallStep[] orderDinnerDialogSteps = new WaterfallStep[]
{
    OrderDinnerSteps.StartFoodSelectionAsync,
    OrderDinnerSteps.GetRoomNumberAsync,
    OrderDinnerSteps.ProcessOrderAsync,
};

Add(new WaterfallDialog(Dialogs.OrderDinner, orderDinnerDialogSteps));
```

Na classe **HotelDialogs**, adicione as definições das etapas.

* Na primeira etapa, enviamos uma mensagem de boas-vindas e iniciaremos o diálogo de seleção de alimentos.
* Na segunda etapa, verificamos se o diálogo de seleção de alimentos retornou um carrinho de pedido.
  * Em caso positivo, peça o número do quarto e salve as informações do carrinho.
  * Em caso negativo, suponha que o pedido foi cancelado e chame o método _end dialog_ para encerrar esse diálogo.
* Na última etapa, registre o número do quarto e envie uma mensagem de confirmação antes de encerrar. Essa é a etapa na qual seu bot deve encaminhar o pedido para um serviço de processamento.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the order-dinner dialog.
/// </summary>
private static class OrderDinnerSteps
{
    public static async Task<DialogTurnResult> StartFoodSelectionAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync(
            "Welcome to our Dinner order service.",
            cancellationToken: cancellationToken);

        // Start the food selection dialog.
        return await stepContext.BeginDialogAsync(Dialogs.OrderPrompt, null, cancellationToken);
    }

    public static async Task<DialogTurnResult> GetRoomNumberAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        if (stepContext.Result != null && stepContext.Result is OrderCart cart)
        {
            // If there are items in the order, record the order and ask for a room number.
            stepContext.Values[Outputs.OrderCart] = cart;
            return await stepContext.PromptAsync(
                Inputs.Number,
                new PromptOptions
                {
                    Prompt = MessageFactory.Text("What is your room number?"),
                    RetryPrompt = MessageFactory.Text("Please enter your room number."),
                },
                cancellationToken);
        }
        else
        {
            // Otherwise, assume the order was cancelled by the guest and exit.
            return await stepContext.EndDialogAsync(null, cancellationToken);
        }
    }

    public static async Task<DialogTurnResult> ProcessOrderAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Get and save the guest's answer.
        var roomNumber = (int)stepContext.Result;
        stepContext.Values[Outputs.RoomNumber] = roomNumber;

        // Process the dinner order using the collected order cart and room number.

        await stepContext.Context.SendActivityAsync(
            $"Thank you. Your order will be delivered to room {roomNumber} within 45 minutes.",
            cancellationToken: cancellationToken);
        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor do bot, adicione o diálogo de cascata `orderDinner`.

```javascript
// Order dinner:
// Help user order dinner from a menu
this.dialogSet.add(new WaterfallDialog('orderDinner', [
    async function (step) {
        await step.context.sendActivity("Welcome to our dinner order service.");

        return await step.beginDialog('orderPrompt', step.values.orderCart = {
            orders: [],
            total: 0
        }); // Prompt for orders
    },
    async function (step) {
        if (step.result == "Cancel") {
            return await step.endDialog();
        } else {
            return await step.prompt('numberPrompt', "What is your room number?");
        }
    },
    async function (step) {
        await step.context.sendActivity(`Thank you. Your order will be delivered to room ${step.result} within 45 minutes.`);
        return await step.endDialog();
    }
]));
```

---

### <a name="create-the-order-prompt-dialog"></a>Criar o diálogo de solicitação de pedido

No diálogo de seleção de alimentos, apresentaremos ao hóspede uma lista de opções que inclui os itens possíveis de escolha para a refeição e as suas solicitações de processamento de pedidos. Esse diálogo entra em loop até que o hóspede opta pelo processamento ou cancelamento do pedido pelo bot.

* Quando o hóspede seleciona um item de refeição, nós o adicionamos ao seu _carrinho_.
* Se o hóspede optar por processar o pedido, primeiro verificamos se o carrinho está vazio.
  * Se estiver, enviamos uma mensagem de erro e continuamos o loop.
  * Caso contrário, terminamos esse diálogo, retornando as informações do carrinho para o diálogo principal.
* Se o hóspede optar por cancelar o pedido, terminamos o diálogo e não retornamos nenhuma informação do carrinho.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No construtor, adicione o diálogo de cascata. Depois, defina as etapas da cascata. Aqui, definimos as etapas em uma classe aninhada.

No construtor **HotelDialogs**.

```csharp
// Define the steps for and add the order-prompt dialog.
WaterfallStep[] orderPromptDialogSteps = new WaterfallStep[]
{
    OrderPromptSteps.PromptForItemAsync,
    OrderPromptSteps.ProcessInputAsync,
};

Add(new WaterfallDialog(Dialogs.OrderPrompt, orderPromptDialogSteps));
```

* Na primeira etapa, inicializamos o estado do diálogo. Se os argumentos de entrada para o diálogo contiverem informações de carrinho, salvamos isso no estado do diálogo. Caso contrário, criamos um carrinho vazio e adicionamos isso. Em seguida, solicitamos ao hóspede uma escolha no cardápio de refeição.
* Na próxima etapa, examinamos a opção escolhida pelo hóspede:
  * Se for uma solicitação para processar o pedido, verifique se o carrinho contém itens.
    * Em caso positivo, encerre o diálogo, retornando o conteúdo do carrinho.
    * Caso contrário, envie uma mensagem de erro para o hóspede e recomece o diálogo.
  * Se for uma solicitação para cancelar o pedido, termina o diálogo, retornando um dicionário vazio.
  * Se for um item de refeição, adicione-o ao carrinho, envie uma mensagem de status e reinicie o diálogo, passando o estado atual do pedido.

Na classe **HotelDialogs**, adicione as definições das etapas.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the order-prompt dialog.
/// </summary>
private static class OrderPromptSteps
{
    public static async Task<DialogTurnResult> PromptForItemAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // First time through, options will be null.
        var cart = (stepContext.Options is OrderCart oldCart && oldCart != null)
            ? new OrderCart(oldCart) : new OrderCart();

        stepContext.Values[Outputs.OrderCart] = cart;
        stepContext.Values[Outputs.OrderTotal] = cart.Sum(item => item.Price);

        return await stepContext.PromptAsync(
            Inputs.Choice,
            new PromptOptions
            {
                Prompt = MessageFactory.Text("What would you like?"),
                RetryPrompt = Lists.MenuReprompt,
                Choices = Lists.MenuChoices,
            },
            cancellationToken);
    }

    public static async Task<DialogTurnResult> ProcessInputAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Get the guest's choice.
        var choice = (FoundChoice)stepContext.Result;
        var menuOption = Lists.MenuOptions[choice.Index];

        // Get the current order from dialog state.
        var cart = (OrderCart)stepContext.Values[Outputs.OrderCart];

        if (menuOption.Name is MenuChoice.Process)
        {
            if (cart.Count > 0)
            {
                // If there are any items in the order, then exit this dialog,
                // and return the list of selected food items.
                return await stepContext.EndDialogAsync(cart, cancellationToken);
            }
            else
            {
                // Otherwise, send an error message and restart from
                // the beginning of this dialog.
                await stepContext.Context.SendActivityAsync(
                    "Your cart is empty. Please add at least one item to the cart.",
                    cancellationToken: cancellationToken);
                return await stepContext.ReplaceDialogAsync(Dialogs.OrderPrompt, null, cancellationToken);
            }
        }
        else if (menuOption.Name is MenuChoice.Cancel)
        {
            await stepContext.Context.SendActivityAsync(
                "Your order has been cancelled.",
                cancellationToken: cancellationToken);

            // Exit this dialog, returning null.
            return await stepContext.EndDialogAsync(null, cancellationToken);
        }
        else
        {
            // Add the selected food item to the order and update the order total.
            cart.Add(menuOption);
            var total = (double)stepContext.Values[Outputs.OrderTotal] + menuOption.Price;
            stepContext.Values[Outputs.OrderTotal] = total;

            await stepContext.Context.SendActivityAsync(
                $"Added {menuOption.Name} (${menuOption.Price:0.00}) to your order." +
                    Environment.NewLine + Environment.NewLine +
                    $"Your current total is ${total:0.00}.",
                cancellationToken: cancellationToken);

            // Present the order options again, passing in the current order state.
            return await stepContext.ReplaceDialogAsync(Dialogs.OrderPrompt, cart);
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor do bot, adicione o diálogo de cascata `orderPrompt`.

```javascript
// Helper dialog to repeatedly prompt user for orders
this.dialogSet.add(new WaterfallDialog('orderPrompt', [
    async function (step) {
        // Define a new cart of one does not exists
        step.values.orderCart = step.options;

        return await step.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function (step) {
        const choice = step.result;
        if (choice.value.match(/process order/ig)) {
            if (step.values.orderCart.orders.length > 0) {
                // Process the order
                await step.context.sendActivity("Processing your order.");
                // ...
                step.values.orderCart = undefined; // Reset cart
                return await step.endDialog();
            } else {
                await step.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                return await step.replaceDialog('orderPrompt', step.values.orderCart);
            }
        } else if (choice.value.match(/cancel/ig)) {
            await step.context.sendActivity("Your order has been canceled.");
            return await step.endDialog(choice.value);
        } else {
            var item = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if (!item) {
                await step.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");

                // Ask again
                return await step.replaceDialog('orderPrompt', step.values.orderCart);
            } else {
                // Add the item to cart
                step.values.orderCart.orders.push(item);
                step.values.orderCart.total += item.Price;

                await step.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${step.values.orderCart.total}`);

                // Ask again
                return await step.replaceDialog('orderPrompt', step.values.orderCart);
            }
        }
    }
]));
```

O código de exemplo acima mostra que o diálogo `orderDinner` principal usa um diálogo auxiliar chamado `orderPrompt` para lidar com as opções do usuário. O diálogo `orderPrompt` exibe o cardápio de itens de alimento, pede ao usuário para escolher um item, adiciona o item ao carrinho e faz a solicitação novamente em um loop. Isso permite que o usuário adicione vários itens ao seu pedido. O diálogo fica em loop até que o usuário escolha `Process order` ou `Cancel`. Nesse ponto, a execução é entregue de volta ao diálogo pai (por exemplo, o diálogo `orderDinner`). O diálogo `orderDinner` faz algumas limpezas no último minuto caso o usuário deseje processar o pedido; caso contrário, ele encerra e retorna a execução volta para seu diálogo pai (por exemplo: `mainMenu`). O diálogo `mainMenu`, por sua vez, continua executando a última etapa, que é a de simplesmente reexibir as opções do menu principal.

---

### <a name="create-the-reserve-table-dialog"></a>Criar o diálogo de reserva de mesa

<!-- TODO: Update the "Manage simple conversation flows" topic to use a reserveTable dialog, and then reference that from here. -->

Para encurtar esse exemplo, mostraremos apenas uma implementação mínima do diálogo `reserveTable`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No construtor, adicione o diálogo de cascata. Depois, defina as etapas da cascata. Aqui, definimos as etapas em uma classe aninhada.

No construtor **HotelDialogs**.

```csharp
// Define the steps for and add the reserve-table dialog.
WaterfallStep[] reserveTableDialogSteps = new WaterfallStep[]
{
    ReserveTableSteps.StubAsync,
};

Add(new WaterfallDialog(Dialogs.ReserveTable, reserveTableDialogSteps));
```

Na classe **HotelDialogs**, adicione as definições das etapas.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the reserve-table dialog.
/// </summary>
private static class ReserveTableSteps
{
    public static async Task<DialogTurnResult> StubAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync(
            "Your table has been reserved.",
            cancellationToken: cancellationToken);

        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor do bot, adicione o diálogo de cascata do espaço reservado `reserveTable`.

```javascript
// Reserve a table:
// Help the user to reserve a table
this.dialogSet.add(new WaterfallDialog('reserveTable', [
    // Replace this waterfall with your reservation steps.
    async function(step){
        await step.context.sendActivity("Your table has been reserved");
        await step.endDialog();
    }
]));
```

---

### <a name="update-the-bot-code-to-call-the-dialogs"></a>Atualizar o código do bot para chamar os diálogos

Atualize o código do manipulador de turno do bot para chamar o diálogo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Renomeie **EchoBotAccessors.cs** como **BotAccessors.cs** e a classe de `EchoBotAccessors` para `BotAccessors`. Atualize as instruções de uso e a definição de classe para fornecer o acessador de propriedade de estado necessário para este bot.

```csharp
using System;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
```

```csharp
/// <summary>
/// This class is created as a Singleton and passed into the IBot-derived constructor.
///  - See <see cref="EchoWithCounterBot"/> constructor for how that is injected.
///  - See the Startup.cs file for more details on creating the Singleton that gets
///    injected into the constructor.
/// </summary>
public class BotAccessors
{
    /// <summary>
    /// Initializes a new instance of the <see cref="BotAccessors"/> class.
    /// Contains the <see cref="ConversationState"/> and associated <see cref="IStatePropertyAccessor{T}"/>.
    /// </summary>
    /// <param name="conversationState">The state object that stores the counter.</param>
    public BotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    /// <summary>
    /// Gets the <see cref="IStatePropertyAccessor{T}"/> name used for the <see cref="DialogState"/> accessor.
    /// </summary>
    /// <remarks>Accessors require a unique name.</remarks>
    /// <value>The accessor name for the dialog state accessor.</value>
    public static string DialogStateAccessorName { get; } = $"{nameof(BotAccessors)}.DialogState";

    /// <summary>
    /// Gets or sets the DialogState property accessor.
    /// </summary>
    /// <value>
    /// The DialogState property accessor.
    /// </value>
    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }

    /// <summary>
    /// Gets the <see cref="ConversationState"/> object for the conversation.
    /// </summary>
    /// <value>The <see cref="ConversationState"/> object.</value>
    public ConversationState ConversationState { get; }
}
```

Atualize o arquivo **Startup.cs** para configurar o singleton `BotAccessors`.

1. Atualize as instruções de uso.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Integration;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    using Microsoft.Bot.Configuration;
    using Microsoft.Bot.Connector.Authentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    ```

1. Atualize a parte do método `ConfigureServices` que registra os acessadores de propriedade de estado do bot.

    ```csharp
    // Create and register state accesssors.
    // Acessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState)
        {
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(BotAccessors.DialogStateAccessorName),
        };

        return accessors;
    });
    ```

Renomeie o arquivo EchoWithCounterBot.cs para HotelBot.cs e renomeie a classe EchoWithCounterBot para HotelBot.

1. Atualize o código de inicialização do bot.

    ```csharp
    private readonly BotAccessors _accessors;
    private readonly HotelDialogs _dialogs;
    private readonly ILogger _logger;

    /// <summary>
    /// Initializes a new instance of the <see cref="HotelBot"/> class.
    /// </summary>
    /// <param name="accessors">A class containing <see cref="IStatePropertyAccessor{T}"/> used to manage state.</param>
    /// <param name="loggerFactory">A <see cref="ILoggerFactory"/> that is hooked to the Azure App Service provider.</param>
    public HotelBot(BotAccessors accessors, ILoggerFactory loggerFactory)
    {
        _logger = loggerFactory.CreateLogger<HotelBot>();
        _logger.LogTrace("EchoBot turn start.");
        _accessors = accessors;
        _dialogs = new HotelDialogs(_accessors.DialogStateAccessor);
    }
    ```

1. Atualize o manipulador de turno do bot de modo que ele execute o diálogo.

    ```csharp
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        var dc = await _dialogs.CreateContextAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            await dc.ContinueDialogAsync(cancellationToken);
            if (!turnContext.Responded)
            {
                await dc.BeginDialogAsync(HotelDialogs.MainMenu, null, cancellationToken);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            var activity = turnContext.Activity.AsConversationUpdateActivity();
            if (activity.MembersAdded.Any(member => member.Id != activity.Recipient.Id))
            {
                await dc.BeginDialogAsync(HotelDialogs.MainMenu, null, cancellationToken);
            }
        }

        await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
    }
    ```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async onTurn(turnContext) {
    let dc = await this.dialogSet.createContext(turnContext);

    // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
    if (turnContext.activity.type === ActivityTypes.Message) {

        await dc.continueDialog();

        if (!turnContext.responded) {
            await dc.beginDialog('mainMenu');
        }
    } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate) {
        // Do we have any new members added to the conversation?
        if (turnContext.activity.membersAdded.length !== 0) {
            // Iterate over all new members added to the conversation
            for (var idx in turnContext.activity.membersAdded) {
                // Greet anyone that was not the target (recipient) of this message.
                // Since the bot is the recipient for events from the channel,
                // context.activity.membersAdded === context.activity.recipient.Id indicates the
                // bot was added to the conversation, and the opposite indicates this is a user.
                if (turnContext.activity.membersAdded[idx].id !== turnContext.activity.recipient.id) {
                    // Start the dialog.
                    await dc.beginDialog('mainMenu');
                }
            }
        }
    }

    // Save state changes
    await this.conversationState.saveChanges(turnContext);
}
```

---

## <a name="next-steps"></a>Próximas etapas

Aprimore este bot oferecendo outras opções como "mais informações" ou "ajuda" às opções de menu. Para saber mais sobre como implementar esses tipos de interrupções, confira [Lidar com interrupções do usuário](bot-builder-howto-handle-user-interrupt.md).

Agora que você aprendeu a usar caixas de diálogo, prompts e cascatas para gerenciar fluxos de conversa complexos, vamos dar uma olhada em como podemos dividir nossas caixas de diálogo (como `orderDinner` e `reserveTable`) em objetos separados, em vez de juntá-las em uma grande caixa de diálogo.

> [!div class="nextstepaction"]
> [Criar lógica de bot modular com controle composto](bot-builder-compositcontrol.md)
