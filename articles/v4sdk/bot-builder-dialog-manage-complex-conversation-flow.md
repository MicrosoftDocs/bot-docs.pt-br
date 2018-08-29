---
title: Gerenciar um fluxo de conversa complexo com diálogos | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa complexo com diálogos no SDK do Construtor de Bot para Node.js.
keywords: fluxo de conversa complexo, repetição, loop, menu, diálogos, prompts, cascatas, conjunto de diálogos
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 7/27/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 304de6783a268140bf74f95d96cd9c24e12c4c05
ms.sourcegitcommit: 1abc32353c20acd103e0383121db21b705e5eec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40236353"
---
# <a name="manage-complex-conversation-flows-with-dialogs"></a>Gerenciar fluxos de conversas complexos com diálogos

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Você pode gerenciar fluxos de conversa simples e complexos usando a biblioteca de diálogos. Em [fluxos de conversa simples](bot-builder-dialog-manage-conversation-flow.md), o usuário começa a primeira etapa de uma *cascata*, percorre até a última etapa, e a conversa termina. Neste artigo, usaremos os diálogos para gerenciar conversas mais complexas, com partes que podem se ramificar e entrar em loop. Para fazer isso, usaremos o método _replace_ do contexto do diálogo, e passaremos argumentos entre partes diferentes da caixa de diálogo.

<!-- TODO: We need a dialogs conceptual topic to link to, so we can reference that here, in place of describing what they are and what their features are in a how-to topic. -->

Para dar a você mais controle sobre a *pilha de diálogos*, a biblioteca **Diálogos** fornece um método _replace_. Esse método permite que você retire o diálogo atual da pilha, envie por push um novo diálogo para o topo da pilha e inicie o novo diálogo. Esse recurso permite que você forneça uma conversa mais complexa. Essas técnicas podem ser usadas para criar conversas aleatoriamente complexas. Caso a complexidade da conversa aumente até o ponto de os diálogos se tornarem difíceis de gerenciar, será possível criar seu próprio fluxo de lógica de controle para acompanhar a conversa do usuário.

<!-- TODO: This is probably a good place to add a link to the modular/composite dialogs topic. -->

Neste artigo, criaremos os diálogos para um bot de hotel usado por um hóspede para reservar uma mesa ou pedir a entrega de uma refeição no quarto. O diálogo principal fornece ao hóspede duas opções. Se ele quiser reservar uma mesa, o diálogo principal iniciará um diálogo de reserva de mesa. Se o hóspede quiser pedir a refeição, o diálogo principal iniciará um diálogo sobre pedido de refeição. Primeiro, o diálogo de pedido de refeição pede ao hóspede que escolha os alimentos em um cardápio e, depois, pede o número do quarto. A seleção de alimentos também é um diálogo, para que o hóspede possa selecionar vários itens antes de processar o pedido de refeição.

Este diagrama ilustra os diálogos que criaremos neste artigo e suas relações uns aos outros.

![Ilustração dos diálogos usados neste artigo](~/media/complex-conversation-flows.png)

## <a name="how-to-branch"></a>Como ramificar

O contexto do diálogo mantém uma _pilha de diálogos_ e, para cada diálogo na pilha, controla a etapa a seguir. O método _begin_ envia um diálogo para o topo da pilha, e o método _end_ retira o primeiro diálogo da pilha.

Um diálogo pode iniciar um novo diálogo (também conhecido como: ramificação) dentro do mesmo diálogo, chamando o método _begin_ do contexto do diálogo e fornecendo a ID do novo diálogo. O diálogo original ainda está na pilha, mas as chamadas para o método _continue_ do contexto do diálogo só são enviadas para o diálogo que está no topo da pilha, o _diálogo ativo_. Quando um diálogo é retirado da pilha, o contexto do diálogo retoma com a próxima etapa da cascata na pilha de onde parou.

Assim, você pode criar uma ramificação dentro do fluxo da sua conversa incluindo uma etapa em um diálogo que possa escolher condicionalmente um diálogo para iniciar a partir de um conjunto de diálogos possíveis.

## <a name="how-to-loop"></a>Como criar um loop

O método _replace_ do contexto do diálogo permite que você substitua o diálogo que está no topo da pilha. O estado do diálogo antigo é descartado, e o novo diálogo começa desde o início. Você pode usar esse método para criar um loop substituindo um diálogo por ele próprio. No entanto, para [persistir o estado](bot-builder-howto-v4-state.md), você precisará passar informações para a nova instância do diálogo na chamada para o método _replace_. No exemplo a seguir, você verá que o carrinho de pedido de refeição está armazenado no estado do diálogo e, quando o diálogo `orderPrompt` é repetido, o estado atual do diálogo é transmitido para que o estado do novo diálogo possa continuar a adicionar itens a ele. Se você preferir armazenar essas informações em outros estados, consulte [Persistir dados do usuário](bot-builder-tutorial-persist-user-inputs.md).

## <a name="create-the-dialogs-for-the-hotel-bot"></a>Criar os diálogos para o bot de hotel

Nesta seção, criaremos os diálogos para gerenciar uma conversa para o bot de hotel que descrevemos.

### <a name="install-the-dialogs-library"></a>Instalar a biblioteca de diálogos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Começaremos de um modelo básico de EchoBot. Para obter instruções, consulte o [início rápido para .NET](~/dotnet/bot-builder-dotnet-quickstart.md).

Para usar diálogos, instale o pacote do NuGet `Microsoft.Bot.Builder.Dialogs` do projeto ou solução.
Depois, consulte a biblioteca de diálogos usando as instruções em seus arquivos de código, conforme o necessário.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A biblioteca `botbuilder-dialogs` pode ser baixada no NPM. Para instalar a biblioteca `botbuilder-dialogs`, execute o seguinte comando NPM:

```cmd
npm install --save botbuilder-dialogs
```

Para usar **diálogos** no seu bot, inclua-o no código do bot. Por exemplo, adicione isso ao seu arquivo **app.js**:

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

---

### <a name="create-a-dialog-set"></a>Criar uma conjunto de diálogos

Crie um _conjunto de diálogos_ ao qual adicionaremos todos os diálogos para este exemplo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Crie uma classe **HotelDialog** e adicione as instruções using das quais vamos precisar.

```csharp
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts.Choices;
using Microsoft.Bot.Schema;
using Microsoft.Recognizers.Text;
using System;
using System.Collections.Generic;
using System.Linq;
```

Derive a classe de **DialogSet** e defina as IDs e chaves que usaremos para identificar os diálogos, solicitações e informações de estado para esse conjunto de diálogos.

```csharp
/// <summary>Contains the set of dialogs and prompts for the hotel bot.</summary>
public class HotelDialog : DialogSet
{
    /// <summary>The ID of the top-level dialog.</summary>
    public const string MainMenu = "mainMenu";

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

Para usar **diálogos** no seu bot, inclua-o no código do bot.

Faça referência à biblioteca de seu aplicativo **app.js**.

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

E, em seguida, crie o conjunto de diálogos.

```javascript
const dialogs = new botbuilder_dialogs.DialogSet();
```

---

### <a name="add-the-prompts-to-the-set"></a>Adicionar os prompts ao conjunto

Usaremos um **ChoicePrompt** para perguntar aos hóspedes se querem fazer um pedido ou reservar uma mesa, e também qual opção escolher do cardápio. E, usaremos um **NumberPrompt** para perguntar o número do quarto do hóspede quando ele pedir a refeição.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No construtor **HotelDialog**, adicione as duas solicitações.

```csharp
// Add the prompts.
this.Add(Inputs.Choice, new ChoicePrompt(Culture.English));
this.Add(Inputs.Number, new NumberPrompt<int>(Culture.English));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione essas duas solicitações ao conjunto de diálogos.

```javascript
dialogs.add('choicePrompt', new botbuilder_dialogs.ChoicePrompt());
dialogs.add('numberPrompt', new botbuilder_dialogs.NumberPrompt());
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
    /// <summary>The text to show the guest for this option.</summary>
    public string Description { get; set; }

    /// <summary>The ID of the associated dialog for this option.</summary>
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

    /// <summary>The name of the meal item or the request.</summary>
    public string Name { get; set; }

    /// <summary>The price of the meal item; or NaN for a request.</summary>
    public double Price { get; set; }

    /// <summary>The text to show the guest for this option.</summary>
    public string Description => (double.IsNaN(Price)) ? Name : $"{Name} - ${Price:0.00}";
}

/// <summary>Contains the lists used to present options to the guest.</summary>
private static class Lists
{
    /// <summary>The options for the top-level dialog.</summary>
    public static List<WelcomeChoice> WelcomeOptions { get; } = new List<WelcomeChoice>
    {
        new WelcomeChoice { Description = "Order dinner", DialogName = Dialogs.OrderDinner },
        new WelcomeChoice { Description = "Reserve a table", DialogName = Dialogs.ReserveTable },
    };

    private static List<string> WelcomeList { get; } = WelcomeOptions.Select(x => x.Description).ToList();

    /// <summary>The choices to present in the choice prompt for the top-level dialog.</summary>
    public static List<Choice> WelcomeChoices { get; } = ChoiceFactory.ToChoices(WelcomeList);

    /// <summary>The reprompt action for the top-level dialog.</summary>
    public static Activity WelcomeReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(WelcomeList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }

    /// <summary>The options for the food-selection dialog.</summary>
    public static List<MenuChoice> MenuOptions { get; } = new List<MenuChoice>
    {
        new MenuChoice { Name = "Potato Salad", Price = 5.99 },
        new MenuChoice { Name = "Tuna Sandwich", Price = 6.89 },
        new MenuChoice { Name = "Clam Chowder", Price = 4.50 },
        new MenuChoice { Name = MenuChoice.Process, Price = double.NaN },
        new MenuChoice { Name = MenuChoice.Cancel, Price = double.NaN },
    };

    private static List<string> MenuList { get; } = MenuOptions.Select(x => x.Description).ToList();

    /// <summary>The choices to present in the choice prompt for the food-selection dialog.</summary>
    public static List<Choice> MenuChoices { get; } = ChoiceFactory.ToChoices(MenuList);

    /// <summary>The reprompt action for the food-selection dialog.</summary>
    public static Activity MenuReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(MenuList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Crie uma constante **dinnerMenu** para conter essas informações.

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

Esse diálogo usa um `ChoicePrompt` para exibir o menu e aguarda até o usuário escolher uma opção. Quando o usuário escolhe entre `Order dinner` ou `Reserve a table`, isso inicia o diálogo da escolha apropriada e, após a conclusão desse diálogo, em vez de apenas encerrar o diálogo na última etapa e deixar o usuários sem saber o que está acontecendo, esse diálogo `mainMenu` se repete, iniciando o diálogo `mainMenu` novamente. Com essa estrutura simples, o bot sempre mostrará o menu, e o usuário sempre saberá quais são suas opções.

O diálogo **MainMenu** contém estas etapas em cascata.

* Na primeira etapa da cascata, inicializamos ou limpamos o estado do diálogo, saudamos o hóspede e pedimos que escolha entre as opções disponíveis: `Order dinner` ou `Reserve a table`.
* Na segunda etapa, recuperamos a seleção e iniciamos o diálogo filho que está associada a essa seleção. Após o término do diálogo filho, esse diálogo continuará com a etapa a seguir.
* Na última etapa, usamos o método **DialogContext.Replace** para substituir esse diálogo por uma nova instância dele mesmo, o que transforma efetivamente o diálogo de boas-vinda em um loop.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Add the main welcome dialog.
this.Add(MainMenu, new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        // Greet the guest and ask them to choose an option.
        await dc.Context.SendActivity("Welcome to Contoso Hotel and Resort.");
        await dc.Prompt(Inputs.Choice, "How may we serve you today?", new ChoicePromptOptions
        {
            Choices = Lists.WelcomeChoices,
            RetryPromptActivity = Lists.WelcomeReprompt,
        });
    },
    async (dc, args, next) =>
    {
        // Begin a child dialog associated with the chosen option.
        var choice = (FoundChoice)args["Value"];
        var dialogId = Lists.WelcomeOptions[choice.Index].DialogName;

        await dc.Begin(dialogId, dc.ActiveDialog.State);
    },
    async (dc, args, next) =>
    {
        // Start this dialog over again.
        await dc.Replace(MainMenu);
    },
});
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Display a menu and ask user to choose a menu item. Direct user to the item selected otherwise, show
// the menu again.
dialogs.add('mainMenu', [
    async function(dc){
        await dc.context.sendActivity("Welcome to Contoso Hotel and Resort.");
        await dc.prompt('choicePrompt', "How may we serve you today?", ['Order Dinner', 'Reserve a table']);
    },
    async function(dc, result){
        if(result.value.match(/order dinner/ig)){
            await dc.begin('orderDinner');
        }
        else if(result.value.match(/reserve a table/ig)){
            await dc.begin('reserveTable');
        }
    },
    async function(dc, result){
        // Start over
        await dc.endAll().begin('mainMenu');
    }
]);
```

---

### <a name="create-the-order-dinner-dialog"></a>Criar o diálogo de pedido de refeição

No diálogo de pedido de refeição, daremos as boas-vindas ao hóspede no "serviço de pedido de refeições" e iniciaremos imediatamente o diálogo de seleção de alimentos, que descreveremos na próxima seção. É importante observar que, se o hóspede pedir que o serviço processe seu pedido, o diálogo de seleção de alimentos retornará a lista de itens do pedido. Para concluir todo o processo, esse diálogo pede um número de quarto para entregar o alimento e, em seguida, envia uma mensagem de confirmação antes do encerramento. Se o hóspede cancelar o pedido, esse diálogo não solicitará um número de quarto e terminará imediatamente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para a classe **HotelDialog**, adicione uma estrutura de dados que possamos usar para controlar o pedido de jantar do hóspede.

```csharp
/// <summary>Contains the guest's dinner order.</summary>
private class OrderCart : List<MenuChoice> { }
```

No construtor **HotelDialog**, adicione o diálogo de pedido de refeição.

* Na primeira etapa, enviamos uma mensagem de boas-vindas e iniciaremos o diálogo de seleção de alimentos.
* Na segunda etapa, verificamos se o diálogo de seleção de alimentos retornou um carrinho de pedido.
  * Em caso positivo, peça o número do quarto e salve as informações do carrinho.
  * Em caso negativo, suponha que o pedido foi cancelado e chame **DialogContext.End** para encerrar esse diálogo.
* Na última etapa, registre o número do quarto e envie uma mensagem de confirmação antes de encerrar. Essa é a etapa na qual seu bot deve encaminhar o pedido para um serviço de processamento.

```csharp
// Add the order-dinner dialog.
this.Add(Dialogs.OrderDinner, new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        await dc.Context.SendActivity("Welcome to our Dinner order service.");

        // Start the food selection dialog.
        await dc.Begin(Dialogs.OrderPrompt);
    },
    async (dc, args, next) =>
    {
        if (args.TryGetValue(Outputs.OrderCart, out object arg) && arg is OrderCart cart)
        {
            // If there are items in the order, record the order and ask for a room number.
            dc.ActiveDialog.State[Outputs.OrderCart] = cart;
            await dc.Prompt(Inputs.Number, "What is your room number?", new PromptOptions
            {
                RetryPromptString = "Please enter your room number."
            });
        }
        else
        {
            // Otherwise, assume the order was cancelled by the guest and exit.
            await dc.End();
        }
    },
    async (dc, args, next) =>
    {
        // Get and save the guest's answer.
        var roomNumber = args["Text"] as string;
        dc.ActiveDialog.State[Outputs.RoomNumber] = roomNumber;

        // Process the dinner order using the collected order cart and room number.

        await dc.Context.SendActivity($"Thank you. Your order will be delivered to room {roomNumber} within 45 minutes.");
        await dc.End();
    },
});
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Order dinner:
// Help user order dinner from a menu
dialogs.add('orderDinner', [
    async function (dc){
        await dc.context.sendActivity("Welcome to our Dinner order service.");
        
        await dc.begin('orderPrompt', dc.activeDialog.state.orderCart); // Prompt for orders
    },
    async function (dc, result) {
        if(result == "Cancel"){
            await dc.end();
        }
        else { 
            await dc.prompt('numberPrompt', "What is your room number?");
        }
    },
    async function(dc, result){
        await dc.context.sendActivity(`Thank you. Your order will be delivered to room ${result} within 45 minutes.`);
        await dc.end();
    }
]);
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

No construtor **HotelDialog**, adicione o diálogo de seleção de alimentos.

* Na primeira etapa, inicializamos o estado do diálogo. Se os argumentos de entrada para o diálogo contiverem informações de carrinho, salvamos isso no estado do diálogo. Caso contrário, criamos um carrinho vazio e adicionamos isso. Em seguida, solicitamos ao hóspede uma escolha no cardápio de refeição.
* Na próxima etapa, examinamos a opção escolhida pelo hóspede:
  * Se for uma solicitação para processar o pedido, verifique se o carrinho contém itens.
    * Em caso positivo, encerre o diálogo, retornando o conteúdo do carrinho.
    * Caso contrário, envie uma mensagem de erro para o hóspede e recomece o diálogo.
  * Se for uma solicitação para cancelar o pedido, termina o diálogo, retornando um dicionário vazio.
  * Se for um item de refeição, adicione-o ao carrinho, envie uma mensagem de status e reinicie o diálogo, passando o estado atual do pedido.

```csharp
// Add the food-selection dialog.
this.Add(Dialogs.OrderPrompt, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            if (args is null || !args.ContainsKey(Outputs.OrderCart))
            {
                // First time through, initialize the order state.
                dc.ActiveDialog.State[Outputs.OrderCart] = new OrderCart();
                dc.ActiveDialog.State[Outputs.OrderTotal] = 0.0;
            }
            else
            {
                // Otherwise, set the order state to that of the arguments.
                dc.ActiveDialog.State = new Dictionary<string, object>(args);
            }

            await dc.Prompt(Inputs.Choice, "What would you like?", new ChoicePromptOptions
            {
                Choices = Lists.MenuChoices,
                RetryPromptActivity = Lists.MenuReprompt,
            });
        },
        async (dc, args, next) =>
        {
            // Get the guest's choice.
            var choice = (FoundChoice)args["Value"];
            var option = Lists.MenuOptions[choice.Index];

            // Get the current order from dialog state.
            var cart = (OrderCart)dc.ActiveDialog.State[Outputs.OrderCart];

            if (option.Name is MenuChoice.Process)
            {
                if (cart.Count > 0)
                {
                    // If there are any items in the order, then exit this dialog,
                    // and return the list of selected food items.
                    await dc.End(new Dictionary<string, object>
                    {
                        [Outputs.OrderCart] = cart
                    });
                }
                else
                {
                    // Otherwise, send an error message and restart from
                    // the beginning of this dialog.
                    await dc.Context.SendActivity(
                        "Your cart is empty. Please add at least one item to the cart.");
                    await dc.Replace(Dialogs.OrderPrompt);
                }
            }
            else if (option.Name is MenuChoice.Cancel)
            {
                await dc.Context.SendActivity("Your order has been cancelled.");

                // Exit this dialog, returning an empty property bag.
                dc.ActiveDialog.State.Clear();
                await dc.End(new Dictionary<string, object>());
            }
            else
            {
                // Add the selected food item to the order and update the order total.
                cart.Add(option);
                var total = (double)dc.ActiveDialog.State[Outputs.OrderTotal] + option.Price;
                dc.ActiveDialog.State[Outputs.OrderTotal] = total;

                await dc.Context.SendActivity($"Added {option.Name} (${option.Price:0.00}) to your order." +
                    Environment.NewLine + Environment.NewLine +
                    $"Your current total is ${total:0.00}.");

                // Present the order options again, passing in the current order state.
                await dc.Replace(Dialogs.OrderPrompt, dc.ActiveDialog.State);
            }
        },
    });
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Helper dialog to repeatedly prompt user for orders
dialogs.add('orderPrompt', [
    async function(dc, orderCart){
        // Define a new cart of one does not exists
        if(!orderCart){
            // Initialize a new cart
            // convoState = conversationState.get(dc.context);
            dc.activeDialog.state.orderCart = {
                orders: [],
                total: 0
            };
        }
        else {
            dc.activeDialog.state.orderCart = orderCart;
        }
        await dc.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function(dc, choice){
        // Get state object
        // convoState = conversationState.get(dc.context);

        if(choice.value.match(/process order/ig)){
            if(dc.activeDialog.state.orderCart.orders.length > 0) {
                // Process the order
                // ...
                dc.activeDialog.state.orderCart = undefined; // Reset cart
                await dc.context.sendActivity("Processing your order.");
                await dc.end();
            }
            else {
                await dc.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                await dc.replace('orderPrompt');
            }
        }
        else if(choice.value.match(/cancel/ig)){
            //dc.activeDialog.state.orderCart = undefined; // Reset cart
            await dc.context.sendActivity("Your order has been canceled.");
            await dc.end(choice.value);
        }
        else {
            var item = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if(!item){
                await dc.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");
                
                // Ask again
                await dc.replace('orderPrompt');
            }
            else {
                // Add the item to cart
                dc.activeDialog.state.orderCart.orders.push(item);
                dc.activeDialog.state.orderCart.total += item.Price;

                await dc.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${dc.activeDialog.state.orderCart.total}`);

                // Ask again
                await dc.replace('orderPrompt', dc.activeDialog.state.orderCart);
            }
        }
    }
]);
```

O código de exemplo acima mostra que o diálogo `orderDinner` principal usa um diálogo auxiliar chamado `orderPrompt` para lidar com as opções do usuário. O diálogo `orderPrompt` exibe o cardápio de itens de alimento, pede ao usuário para escolher um item, adiciona o item ao carrinho e faz a solicitação novamente em um loop. Isso permite que o usuário adicione vários itens ao seu pedido. O diálogo fica em loop até que o usuário escolha `Process order` ou `Cancel`. Nesse ponto, a execução é entregue de volta ao diálogo pai (por exemplo, o diálogo `orderDinner`). O diálogo `orderDinner` faz algumas limpezas no último minuto caso o usuário deseje processar o pedido; caso contrário, ele encerra e retorna a execução volta para seu diálogo pai (por exemplo: `mainMenu`). O diálogo `mainMenu`, por sua vez, continua executando a última etapa, que é a de simplesmente reexibir as opções do menu principal.

---
### <a name="create-the-reserve-table-dialog"></a>Criar o diálogo de reserva de mesa

<!-- TODO: Update the "Manage simple conversation flows" topic to use a reserveTable dialog, and then reference that from here. -->

Para encurtar esse exemplo, mostraremos apenas uma implementação mínima do diálogo `reserveTable`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Add the table-reservation dialog.
this.Add(Dialogs.ReserveTable, new WaterfallStep[]
    {
        // Replace this waterfall with your reservation steps.
        async (dc, args, next) =>
        {
            await dc.Context.SendActivity("Your table has been reserved.");
            await dc.End();
        }
    });
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Reserve a table:
// Help the user to reserve a table

dialogs.add('reserveTable', [
    // Replace this waterfall with your reservation steps.
    async function(dc, args, next){
        await dc.context.sendActivity("Your table has been reserved");
        await dc.end();
    }
]);
```

---

## <a name="next-steps"></a>Próximas etapas

Aprimore este bot oferecendo outras opções como "mais informações" ou "ajuda" às opções de menu. Para saber mais sobre como implementar esses tipos de interrupções, confira [Lidar com interrupções do usuário](bot-builder-howto-handle-user-interrupt.md).

Agora que você aprendeu a usar caixas de diálogo, prompts e cascatas para gerenciar fluxos de conversa complexos, vamos dar uma olhada em como podemos dividir nossas caixas de diálogo (como `orderDinner` e `reserveTable`) em objetos separados, em vez de juntá-las em uma grande caixa de diálogo.

> [!div class="nextstepaction"]
> [Criar lógica de bot modular com controle composto](bot-builder-compositcontrol.md)
