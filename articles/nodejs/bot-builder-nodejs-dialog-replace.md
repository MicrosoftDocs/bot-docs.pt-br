---
title: Substituir caixas de diálogo – Serviço de Bot
description: Saiba como substituir diálogos para solicitar novamente a entrada e gerenciar o fluxo da conversa usando o SDK do Bot Framework para Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e1f2ad33e84cdb02b598a4e162be1319cd3ea528
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824816"
---
# <a name="replace-dialogs-in-the-v3-javascript-sdk"></a>Substituir caixas de diálogo no SDK do JavaScript v3

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

A capacidade de substituir um diálogo pode ser útil quando você precisa validar a entrada do usuário ou repetir uma ação no decorrer de uma conversa. Com o SDK do Bot Framework para Node.js, você pode substituir um diálogo usando o método [`session.replaceDialog`](http://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#replacedialog). Esse método permite que você encerre o diálogo atual e substitua-o por um novo diálogo sem retornar ao chamador. 

## <a name="create-custom-prompts-to-validate-input"></a>Criar prompts personalizados para validar a entrada

O SDK do Bot Framework para Node.js inclui a validação da entrada para alguns tipos de [prompts](bot-builder-nodejs-dialog-prompt.md), como `Prompts.time` e `Prompts.choice`. Para validar a entrada de texto recebida em resposta a `Prompts.text`, você precisa criar sua própria lógica de validação e prompts personalizados. 

Talvez você deseje validar uma entrada se a entrada precisar estar em conformidade com determinado valor, padrão, intervalo ou critérios que você definir. Se uma entrada falhar na validação, o bot poderá solicitar o usuário a fornecer essas informações novamente usando o método `session.replaceDialog`.

O exemplo de código a seguir mostra como criar um prompt personalizado para validar a entrada do usuário para um número de telefone.

```javascript
// This dialog prompts the user for a phone number. 
// It will re-prompt the user if the input does not match a pattern for phone number.
bot.dialog('phonePrompt', [
    function (session, args) {
        if (args && args.reprompt) {
            builder.Prompts.text(session, "Enter the number using a format of either: '(555) 123-4567' or '555-123-4567' or '5551234567'")
        } else {
            builder.Prompts.text(session, "What's your phone number?");
        }
    },
    function (session, results) {
        var matched = results.response.match(/\d+/g);
        var number = matched ? matched.join('') : '';
        if (number.length == 10 || number.length == 11) {
            session.userData.phoneNumber = number; // Save the number.
            session.endDialogWithResult({ response: number });
        } else {
            // Repeat the dialog
            session.replaceDialog('phonePrompt', { reprompt: true });
        }
    }
]);
```

Neste exemplo, o usuário é inicialmente solicitado a fornecer seu número de telefone. A lógica de validação usa uma expressão regular que corresponde um intervalo de dígitos com base na entrada do usuário. Se a entrada contiver 10 ou 11 dígitos, esse número será retornado na resposta. Caso contrário, o método `session.replaceDialog` será executado para repetir o diálogo `phonePrompt`, que solicita ao usuário uma nova entrada, desta vez, fornecendo diretrizes mais específicas sobre o formato esperado da entrada.

Quando você chama o método `session.replaceDialog`, você especifica o nome do diálogo a ser repetido e uma lista de argumentos. Neste exemplo, a lista de argumentos contém `{ reprompt: true }`, o que permite que o bot forneça diferentes mensagens de prompt, dependendo se ele é um prompt inicial ou um novo prompt. No entanto, você pode especificar todos os argumentos que possam ser exigidos pelo bot. 

## <a name="repeat-an-action"></a>Repetir uma ação

Pode haver ocasiões no decorrer de uma conversa em que você deseja repetir um diálogo para permitir ao usuário concluir uma ação específica várias vezes. Por exemplo, se o bot oferecer uma variedade de serviços, você poderá inicialmente exibir o menu de serviços, explicar ao usuário o processo de solicitação de um serviço e, em seguida, exibir o menu de serviços novamente, permitindo que o usuário solicite outro serviço. Para fazer isso, você pode usar o método [`session.replaceDialog`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#replacedialog) para exibir o menu de serviços novamente, em vez de encerrar a conversa com o método ['session.endConversation'](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#endconversation). 

O exemplo a seguir mostra como usar o método `session.replaceDialog` para implementar esse tipo de cenário. Primeiro, o menu de serviços é definido:

```javascript
// Main menu
var menuItems = { 
    "Order dinner": {
        item: "orderDinner"
    },
    "Dinner reservation": {
        item: "dinnerReservation"
    },
    "Schedule shuttle": {
        item: "scheduleShuttle"
    },
    "Request wake-up call": {
        item: "wakeupCall"
    },
}
```

O diálogo `mainMenu` é invocado pelo diálogo padrão e, portanto, o menu será apresentado ao usuário no início da conversa. Além disso, um [`triggerAction`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction) é associado ao diálogo `mainMenu`, de modo que o menu também seja apresentado sempre que a entrada do usuário for "menu principal". Quando o usuário vê o menu e seleciona uma opção, o diálogo que corresponde à seleção do usuário é invocado usando o método `session.beginDialog`.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This is a reservation bot that has a menu of offerings.
var bot = new builder.UniversalBot(connector, [
    function(session){
        session.send("Welcome to Contoso Hotel and Resort.");
        session.beginDialog("mainMenu");
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 

// Display the main menu and start a new request depending on user input.
bot.dialog("mainMenu", [
    function(session){
        builder.Prompts.choice(session, "Main Menu:", menuItems);
    },
    function(session, results){
        if(results.response){
            session.beginDialog(menuItems[results.response.entity].item);
        }
    }
])
.triggerAction({
    // The user can request this at any time.
    // Once triggered, it clears the stack and prompts the main menu again.
    matches: /^main menu$/i,
    confirmPrompt: "This will cancel your request. Are you sure?"
});
```

Neste exemplo, se o usuário escolher a opção 1 para a entrega do pedido de jantar em seu quarto, o diálogo `orderDinner` será invocado e explicará ao usuário o processo de pedido de jantar. No final do processo, o bot confirmará o pedido e exibirá o menu principal novamente usando o método `session.replaceDialog`.

```javascript
// Menu: "Order dinner"
// This dialog allows user to order dinner to be delivered to their hotel room.
bot.dialog('orderDinner', [
    function(session){
        session.send("Lets order some dinner!");
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    },
    function (session, results) {
        if (results.response) {
            var order = dinnerMenu[results.response.entity];
            var msg = `You ordered: %(Description)s for a total of $${order.Price}.`;
            session.dialogData.order = order;
            session.send(msg);
            builder.Prompts.text(session, "What is your room number?");
        } 
    },
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${results.response}.`;
            session.send(msg);
            session.replaceDialog("mainMenu"); // Display the menu again.
        }
    }
])
.reloadAction(
    "restartOrderDinner", "Ok. Let's start over.",
    {
        matches: /^start over$/i
    }
)
.cancelAction(
    "cancelOrder", "Type 'Main Menu' to continue.", 
    {
        matches: /^cancel$/i,
        confirmPrompt: "This will cancel your order. Are you sure?"
    }
);
```

Dois gatilhos são anexados ao diálogo `orderDinner`, para permitir ao usuário "começar novamente" ou "cancelar" o pedido a qualquer momento durante o processo de pedido. 

O primeiro gatilho é [`reloadAction`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#reloadaction), que permite ao usuário começar o processo de pedido novamente enviando a entrada "começar novamente". Quando o gatilho corresponde ao enunciado "começar novamente", a `reloadAction` reinicia o diálogo do início. 

O segundo gatilho é [`cancelAction`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#cancelaction), que permite ao usuário anular o processo de pedido por completo enviando a entrada "cancelar". Esse gatilho não exibe automaticamente o menu principal novamente, mas envia uma mensagem que informa ao usuário o que fazer em seguida, dizendo "Digite 'Menu Principal' para continuar".

### <a name="dialog-loops"></a>Loops de diálogo

No exemplo acima, o usuário só pode selecionar um item por pedido. Ou seja, se o usuário desejar pedir dois itens do menu, precisará concluir o processo inteiro de pedido para o primeiro item e, em seguida, repetir todo o processo de pedido para o segundo item. 

O exemplo a seguir mostra como aprimorar o bot anterior refatorando o menu de jantar em um diálogo separado. Fazer isso permite que o bot repita o menu de jantar em um loop e, portanto, possibilita ao usuário selecionar vários itens em um único pedido.

Primeiro, adicione uma opção "Finalizar compra" ao menu. Essa opção permitirá ao usuário sair do processo de seleção de item e continuar com o processo de finalizar a compra.

```javascript
// The dinner menu
var dinnerMenu = { 
    //...other menu items...,
    "Check out": {
        Description: "Check out",
        Price: 0 // Order total. Updated as items are added to order.
    }
};
```

Em seguida, refatore o prompt de pedido em seu próprio diálogo, de modo que o bot possa repetir o menu e permitir ao usuário adicionar vários itens ao pedido.

```javascript
// Add dinner items to the list by repeating this dialog until the user says `check out`. 
bot.dialog("addDinnerItem", [
    function(session, args){
        if(args && args.reprompt){
            session.send("What else would you like to have for dinner tonight?");
        }
        else{
            // New order
            // Using the conversationData to store the orders
            session.conversationData.orders = new Array();
            session.conversationData.orders.push({ 
                Description: "Check out",
                Price: 0
            })
        }
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    },
    function(session, results){
        if(results.response){
            if(results.response.entity.match(/^check out$/i)){
                session.endDialog("Checking out...");
            }
            else {
                var order = dinnerMenu[results.response.entity];
                session.conversationData.orders[0].Price += order.Price; // Add to total.
                var msg = `You ordered: ${order.Description} for a total of $${order.Price}.`;
                session.send(msg);
                session.conversationData.orders.push(order);
                session.replaceDialog("addDinnerItem", { reprompt: true }); // Repeat dinner menu
            }
        }
    }
])
.reloadAction(
    "restartOrderDinner", "Ok. Let's start over.",
    {
        matches: /^start over$/i
    }
);
```

Neste exemplo, os pedidos são armazenados em um armazenamento de dados de bot com escopo para a conversa atual, `session.conversationData.orders`. Para cada novo pedido, a variável é reinicializada com uma nova matriz e para cada item escolhido pelo usuário, o bot adiciona esse item à matriz `orders` e adiciona o preço ao total, que é armazenado na variável `Price` do processo de finalizar a compra. Quando o usuário conclui a seleção de itens para seu pedido, ele pode dizer "finalizar compra" e, em seguida, continuar com o restante do processo de pedido.

> [!NOTE]
> Para obter mais informações sobre o armazenamento de dados do bot, confira [Gerenciar dados de estado](bot-builder-nodejs-state.md). 

Por fim, atualize a segunda etapa da cascata no diálogo `orderDinner` para processar o pedido com a confirmação. 

```javascript
// Menu: "Order dinner"
// This dialog allows user to order dinner and have it delivered to their room.
bot.dialog('orderDinner', [
    function(session){
        session.send("Lets order some dinner!");
        session.beginDialog("addDinnerItem");
    },
    function (session, results) {
        if (results.response) {
            // Display itemize order with price total.
            for(var i = 1; i < session.conversationData.orders.length; i++){
                session.send(`You ordered: ${session.conversationData.orders[i].Description} for a total of $${session.conversationData.orders[i].Price}.`);
            }
            session.send(`Your total is: $${session.conversationData.orders[0].Price}`);

            // Continue with the check out process.
            builder.Prompts.text(session, "What is your room number?");
        } 
    },
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${results.response}`;
            session.send(msg);
            session.replaceDialog("mainMenu");
        }
    }
])
//...attached triggers...
;
```

## <a name="cancel-a-dialog"></a>Cancelar um diálogo

Embora o método `session.replaceDialog` possa ser usado para substituir o diálogo *atual* por um novo, ele não pode ser usado para substituir um diálogo que está localizado mais abaixo da pilha de diálogos. Para substituir um diálogo na pilha de diálogos que não seja o diálogo *atual*, use o método [`session.cancelDialog`](http://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#canceldialog). 

O método `session.cancelDialog` pode ser usado para encerrar um diálogo, independentemente de sua localização na pilha de diálogos e, opcionalmente, para invocar um novo diálogo em seu lugar. Para chamar o método `session.cancelDialog`, especifique a ID do diálogo a ser cancelada e, opcionalmente, especifique a ID do diálogo a ser invocada em seu lugar. Por exemplo, este snippet de código cancela o diálogo `orderDinner` e o substitui pelo diálogo `mainMenu`:

```javascript
session.cancelDialog('orderDinner', 'mainMenu'); 
```

Quando o método `session.cancelDialog` for chamado, a pilha de diálogos será pesquisada de forma retroativa e a primeira ocorrência desse diálogo será cancelada, fazendo com que esse diálogo e seus diálogos filho (se houver) sejam removidos da pilha de diálogos. Em seguida, o controle será retornado para o diálogo de chamada, que pode verificar se há um código [`results.resumed`](http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html#resumed) igual a [`ResumeReason.notCompleted`](http://docs.botframework.com/node/builder/chat-reference/enums/_botbuilder_d_.resumereason.html#notcompleted) para detectar o cancelamento.

Como uma alternativa para especificar a ID do diálogo a ser cancelada quando você chama o método `session.cancelDialog`, você pode especificar o índice baseado em zero do diálogo a ser cancelado, em que o índice representa a posição do diálogo na pilha de diálogos. Por exemplo, o snippet de código a seguir encerra o diálogo ativo no momento (índice = 0) e inicia o diálogo `mainMenu` em seu lugar. O diálogo `mainMenu` é invocado na posição 0 da pilha de diálogos, tornando-se o novo diálogo padrão.

```javascript
session.cancelDialog(0, 'mainMenu');
```

Considere a amostra que é abordada nos [loops de diálogo](#dialog-loops) acima. Quando o usuário acessa o menu de seleção de item, esse diálogo (`addDinnerItem`) é o quarto diálogo da pilha de diálogos: `[default dialog, mainMenu, orderDinner, addDinnerItem]`. Como você pode permitir que o usuário cancele seu pedido no diálogo `addDinnerItem`? Se você anexar um gatilho `cancelAction` ao diálogo `addDinnerItem`, ele apenas retornará o usuário para o diálogo anterior (`orderDinner`), que enviará o usuário de volta para o diálogo `addDinnerItem`.

É nesse momento em que o método `session.cancelDialog` é útil. Começando com o [exemplo de loops de diálogo](#dialog-loops), adicione "Cancelar pedido" como uma opção explícita no menu do jantar.

```javascript
// The dinner menu
var dinnerMenu = { 
    //...other menu items...,
    "Check out": {
        Description: "Check out",
        Price: 0      // Order total. Updated as items are added to order.
    },
    "Cancel order": { // Cancel the order and back to Main Menu
        Description: "Cancel order",
        Price: 0
    }
};
```

Em seguida, atualize o diálogo `addDinnerItem` para verificar se há uma solicitação "cancelar pedido". Se "cancelar" for detectado, use o método `session.cancelDialog` para cancelar o diálogo padrão (ou seja, o diálogo no índice 0 da pilha) e invoque o diálogo `mainMenu` em seu lugar. 

```javascript
// Add dinner items to the list by repeating this dialog until the user says `check out`. 
bot.dialog("addDinnerItem", [
    //...waterfall steps...,
    // Last step
    function(session, results){
        if(results.response){
            if(results.response.entity.match(/^check out$/i)){
                session.endDialog("Checking out...");
            }
            else if(results.response.entity.match(/^cancel/i)){
                // Cancel the order and start "mainMenu" dialog.
                session.cancelDialog(0, "mainMenu");
            }
            else {
                //...add item to list and prompt again...
                session.replaceDialog("addDinnerItem", { reprompt: true }); // Repeat dinner menu.
            }
        }
    }
])
//...attached triggers...
;
```

Usando o método `session.cancelDialog` dessa forma, você pode implementar qualquer fluxo da conversa necessário para o bot.

## <a name="next-steps"></a>Próximas etapas

Como você pode ver, para substituir os diálogos na pilha, você usa vários tipos de **ações** para realizar essa tarefa. As ações oferecem uma excelente flexibilidade no gerenciamento do fluxo da conversa. Vamos dar uma olhada nas **ações** e em como manipular melhor as ações do usuário.

> [!div class="nextstepaction"]
> [Manipular ações de usuário](bot-builder-nodejs-dialog-actions.md)
