---
title: Manipular interrupções do usuário | Microsoft Docs
description: Saiba como manipular as interrupções do usuário e orientar o fluxo da conversa.
keywords: interromper, interrupções, mudando o tópico, interrupção
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/17/2018
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 651a7410893f7a66f5941121edc7b34055807ba7
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296679"
---
# <a name="handle-user-interrupt"></a>Manipular interrupções do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A manipulação das interrupções do usuário é um aspecto importante de um bot robusto. Embora talvez você ache que os usuários seguirão o fluxo da conversa definido passo a passo, é muito provável que eles mudem de ideia ou façam uma pergunta no meio do processo, em vez de responderem à pergunta. Nessas situações, como o bot lida com a entrada do usuário? Como é a experiência do usuário? Como você mantém os dados de estado do usuário?

Não há nenhuma resposta correta para essas perguntas, pois cada situação é única para o cenário que o bot foi projetado para manipular. Neste tópico, exploraremos algumas maneiras comuns de manipular as interrupções do usuário e ofereceremos sugestões de como implementá-las no bot.

## <a name="handle-expected-interruptions"></a>Manipular as interrupções esperadas

Um fluxo da conversa de procedimento tem um conjunto principal de etapas pelo qual você deseja orientar o usuário, e as ações do usuário que se desviam dessas etapas são interrupções potenciais. Em um fluxo normal, há interrupções que podem ser previstas.

**Reserva de mesas** Em um bot de reserva de mesas, as principais etapas podem ser a solicitação ao usuário de uma data e uma hora, o tamanho do grupo de pessoas e o nome da reserva. Nesse processo, algumas interrupções esperadas que você pode prever podem incluir: 
 * `cancel`: para sair do processo.
 * `help`: para fornecer diretrizes adicionais sobre esse processo.
 * `more info`: para dar dicas e sugestões ou fornecer maneiras alternativas de reservar uma mesa (por exemplo: um endereço de email ou um número de telefone para contato).
 * `show list of available tables`: se essa é uma opção, mostra uma lista de mesas disponíveis para a data e a hora solicitadas pelo usuário.

**Pedido de jantar** Em um bot de pedido de jantar, as principais etapas são o fornecimento de uma lista de itens de menu e a permissão para que o usuário adicione itens ao carrinho. Nesse processo, algumas interrupções esperadas que você pode prever podem incluir: 
 * `cancel`: para sair do processo de pedido.
 * `more info`: para fornecer detalhes nutricionais sobre cada item de menu.
 * `help`: para fornecer ajuda sobre como usar o sistema.
 * `process order`: para processar o pedido.

Você pode fornecer isso para o usuário como uma lista de **ações sugeridas** ou como uma dica para que o usuário esteja, pelo menos, ciente de quais comandos ele pode enviar que serão reconhecidos pelo bot.

Por exemplo, no fluxo de pedido de jantar, você pode fornecer as interrupções esperadas, juntamente com os itens de menu. Nesse caso, os itens de menu são enviados como uma matriz de `choices`.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

```cs
public class dinnerItem
{
    public string Description;
    public double Price;
}

public class dinnerMenu
{
    static public Dictionary<string, dinnerItem> dinnerChoices = new Dictionary<string, dinnerItem>
    {
        { "potato salad", new dinnerItem { Description="Potato Salad", Price=5.99 } },
        { "tuna sandwich", new dinnerItem { Description="Tuna Sandwich", Price=6.89 } },
        { "clam chowder", new dinnerItem { Description="Clam Chowder", Price=4.50 } }
    };

    static public string[] choices = new string[] {"Potato Salad", "Tuna Sandwich", "Clam Chowder", "more info", "Process order", "help", "Cancel"};
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
var dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50", 
            "more info", "Process order", "Cancel"],
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

Na lógica de ordenação, você pode verificá-los usando uma correspondência de cadeia de caracteres ou expressões regulares.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

Primeiro, precisamos definir um auxiliar para acompanhar nossos pedidos

```cs
// Helper class for storing the order in the dictionary
public class Orders
{
    public double total;
    public string order;
    public bool processOrder;

    // Initialize order values
    public Orders()
    {
        total = 0;
        order = "";
        processOrder = false;
    }
}
```

Em seguida, adicionar o diálogo ao bot.

```cs
dialogs.Add("orderPrompt", new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        // Prompt the user
        await dc.Prompt("choicePrompt",
            "What would you like for dinner?",
            new ChoicePromptOptions
            {
                Choices = dinnerMenu.choices.Select( s => new Choice { Value = s }).ToList(),
                RetryPromptString = "I'm sorry, I didn't understand that. What would you " +
                    "like for dinner?"
            });
    },
    async(dc, args, next) =>
    {
        var convo = ConversationState<Dictionary<string,object>>.Get(dc.Context);

        // Get the user's choice from the previous prompt
        var response = (args["Value"] as FoundChoice).Value.ToLower();

        if(response == "process order")
        {
            try 
            {
                var order = convo["order"];

                await dc.Context.SendActivity("Order is on it's way!");
                
                // In production, you may want to store something more helpful, 
                // such as send order off to be made
                (order as Orders).processOrder = true;

                // Once it's submitted, clear the current order
                convo.Remove("order");
                await dc.End();
            }
            catch
            {
                await dc.Context.SendActivity("Your order is empty, please add your order choice");
                // Ask again
                await dc.Replace("orderPrompt");
            }
        }
        else if(response == "cancel" )
        {
            // Get rid of current order
            convo.Remove("order");
            await dc.Context.SendActivity("Your order has been canceled");
            await dc.End();
        }
        else if(response == "more info")
        {
            // Send more information about the options
            var msg = "More info: <br/>" +
                "Potato Salad: contains 330 calaries per serving. Cost: 5.99 <br/>"
                + "Tuna Sandwich: contains 700 calaries per serving. Cost: 6.89 <br/>"
                + "Clam Chowder: contains 650 calaries per serving. Cost: 4.50";
            await dc.Context.SendActivity(msg);

            // Ask again
            await dc.Replace("orderPrompt");
        }
        else if(response == "help")
        {
            // Provide help information
            await dc.Context.SendActivity("To make an order, add as many items to your cart as " +
                "you like then choose the \"Process order\" option to check out.");

            // Ask again
            await dc.Replace("orderPrompt");
        }
        else
        {
            // Unlikely to get past the prompt verification, but this will catch 
            // anything that isn't a valid menu choice
            if(!dinnerMenu.dinnerChoices.ContainsKey(response))
            {
                await dc.Context.SendActivity("Sorry, that is not a valid item. " +
                    "Please pick one from the menu.");
    
                // Ask again
                await dc.Replace("orderPrompt");
            }
            else {
                // Add the item to cart
                Orders currentOrder;

                // If there is a current order going, add to it. If not, start a new one
                try
                {
                    currentOrder = convo["order"] as Orders;
                }
                catch
                {
                    convo["order"] = new Orders();
                    currentOrder = convo["order"] as Orders;
                }

                // Add to the current order
                currentOrder.order += (dinnerMenu.dinnerChoices[$"{response}"].Description) + ", ";
                currentOrder.total += (double)dinnerMenu.dinnerChoices[$"{response}"].Price;

                // Save back to the conversation state
                convo["order"] = currentOrder;

                await dc.Context.SendActivity($"Added to cart. Current order: " +
                    $"{currentOrder.order} " +
                    $"<br/>Current total: ${currentOrder.total}");

                // Ask again to allow user to add more items or process order
                await dc.Replace("orderPrompt");
            }
        }
    }
});
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Helper dialog to repeatedly prompt user for orders
dialogs.add('orderPrompt', [
    async function(dc){
        await dc.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function(dc, choice){
        if(choice.value.match(/process order/ig)){
            if(orderCart.orders.length > 0) {
                // Process the order
                dc.context.activity.conversation.dinnerOrder = orderCart;

                await dc.end();
            }
            else {
                await dc.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                await dc.replace('orderPrompt');
            }
        }
        else if(choice.value.match(/cancel/ig)){
            orderCart.clear(context);
            await dc.context.sendActivity("Your order has been canceled.");
            await dc.end(choice.value);
        }
        else if(choice.value.match(/more info/ig)){
            var msg = "More info: <br/>Potato Salad: contains 330 calaries per serving. <br/>"
                + "Tuna Sandwich: contains 700 calaries per serving. <br/>" 
                + "Clam Chowder: contains 650 calaries per serving."
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else if(choice.value.match(/help/ig)){
            var msg = `Help: <br/>To make an order, add as many items to your cart as you like then choose the "Process order" option to check out.`
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else {
            var choice = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if(!choice){
                await dc.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");
                
                // Ask again
                await dc.replace('orderPrompt');
            }
            else {
                // Add the item to cart
                orderCart.orders.push(choice);
                orderCart.total += dinnerMenu[choice.value].Price;

                await dc.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${orderCart.total}`);

                // Ask again
                await dc.replace('orderPrompt');
            }
        }
    }
]);
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
Para as interrupções que não estão no escopo, você pode tentar adivinhar qual é a intenção do usuário. Faça isso usando os serviços de IA, como o QnA Maker, o LUIS ou sua lógica personalizada, e ofereça sugestões para o que o bot acha que o usuário deseja. 

Por exemplo, enquanto está no meio do fluxo de reserva de mesa, o usuário diz: "Quero pedir um sanduíche". Isso não é algo que o bot sabe manipular nesse fluxo da conversa. Como o fluxo atual não tem nada a ver com o pedido e o outro comando de conversa do bot é "pedido de jantar", o bot não sabe o que fazer com essa entrada. Se você aplicar o LUIS, por exemplo, poderá treinar o modelo para reconhecer que ele deseja pedir algo para comer (por ex.: o LUIS pode retornar uma intenção "orderFood"). Portanto, o bot pode responder com "Parece que você deseja pedir algo para comer. Gostaria de mudar para nosso processo de pedido de jantar?" Para obter mais informações sobre como treinar o LUIS e detectar as intenções do usuário, confira [Usar o LUIS para reconhecimento vocal](bot-builder-howto-v4-luis.md).

### <a name="default-response"></a>Resposta padrão
Se tudo o mais falhar, você poderá enviar uma resposta padrão genérica em vez de não realizar nenhuma ação e deixar o usuário se perguntando o que está acontecendo. A resposta padrão deve informar o usuário quais comandos são reconhecidos pelo bot, de modo que o usuário possa voltar para um tópico adequado.

Verifique o sinalizador **respondido** no contexto ao final da lógica do bot para ver se o bot enviou algo para o usuário durante seu turno. Se o bot processar a entrada do usuário, mas não respondê-la, é provável que o bot não saiba o que fazer com a entrada. Nesse caso, você pode capturá-la e enviar ao usuário uma mensagem padrão.

O padrão desse bot é fornecer ao usuário o diálogo `mainMenu`. Cabe a você decidir qual experiência o usuário terá nessa situação para o bot.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

```cs
if(!context.Responded)
{
    await dc.EndAll().Begin("mainMenu");
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Check to see if anyone replied. If not then clear all the stack and present the main menu
if (!context.responded) {
    await dc.endAll().begin('mainMenu');
}
```

---

