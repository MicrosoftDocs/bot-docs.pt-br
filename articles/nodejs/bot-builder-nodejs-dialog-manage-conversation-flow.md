---
title: Gerenciar um fluxo de conversa com diálogos | Microsoft Docs
description: Aprenda a gerenciar uma conversa entre um bot e um usuário com diálogos no Bot Builder SDK for Node.js.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 656b6304a576c553db948a348b1c6d8c3fc5ae71
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905660"
---
# <a name="manage-conversation-flow-with-dialogs"></a>Gerenciar fluxo de conversa com diálogos

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-manage-conversation-flow.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-manage-conversation-flow.md)

Gerenciar um fluxo de conversa é uma tarefa essencial na criação de bots. Um bot precisa ser capaz de executar tarefas básicas de forma elegante e lidar com interrupções de forma elegante. Com o Bot Builder SDK for Node.js, você pode gerenciar o fluxo de conversas usando diálogos.

Uma caixa de diálogo é como uma função em um programa. Geralmente, é projetado para executar uma operação específica e pode ser chamado com a frequência necessária. Você pode encadear vários diálogos juntos para lidar com praticamente qualquer fluxo de conversação que você deseja que seu bot manipule. O Bot Builder SDK para Node.js inclui recursos integrados, como [prompts](bot-builder-nodejs-dialog-prompt.md) e [quedas d'água](bot-builder-nodejs-dialog-waterfall.md) para ajudá-lo a gerenciar o fluxo de conversas.

Este artigo fornece uma série de exemplos para explicar como gerenciar os fluxos de conversação simples e fluxos de conversa complexas em que seu bot pode lidar com interrupções e retomar o fluxo, normalmente usando as caixas de diálogo. Os exemplos são baseados nos seguintes cenários: 

1. Seu bot fará uma reserva para o jantar.
2. Seu bot pode processar a solicitação de "Ajuda" a qualquer momento durante a reserva.
3. Seu bot pode processar "Ajuda" sensível ao contexto para a etapa atual da reserva.
4. Seu bot pode lidar com vários tópicos de conversação.

## <a name="manage-conversation-flow-with-a-waterfall"></a>Gerenciar o fluxo de conversas com uma cachoeira

Uma [cascata](bot-builder-nodejs-dialog-waterfall.md) é uma caixa de diálogo que permite ao bot percorrer facilmente um usuário por meio de uma série de tarefas. Neste exemplo, o bot de reserva faz ao usuário uma série de perguntas para que o bot possa processar o pedido de reserva. O bot irá solicitar ao usuário as seguintes informações:

1. Data e hora de reserva
2. Número de pessoas do grupo
3. Nome da pessoa que está fazendo a reserva

O exemplo de código a seguir mostra como usar uma cascata para guiar o usuário por meio de uma série de prompts.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This is a dinner reservation bot that uses a waterfall technique to prompt users for input.
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.send("Welcome to the dinner reservation.");
        builder.Prompts.time(session, "Please provide a reservation date and time (e.g.: June 6th at 5pm)");
    },
    function (session, results) {
        session.dialogData.reservationDate = builder.EntityRecognizer.resolveTime([results.response]);
        builder.Prompts.number(session, "How many people are in your party?");
    },
    function (session, results) {
        session.dialogData.partySize = results.response;
        builder.Prompts.text(session, "Whose name will this reservation be under?");
    },
    function (session, results) {
        session.dialogData.reservationName = results.response;
        
        // Process request and display reservation details
        session.send(`Reservation confirmed. Reservation details: <br/>Date/Time: ${session.dialogData.reservationDate} <br/>Party size: ${session.dialogData.partySize} <br/>Reservation name: ${session.dialogData.reservationName}`);
        session.endDialog();
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 
```

A principal funcionalidade deste bot ocorre na caixa de diálogo padrão. A caixa de diálogo padrão é definida quando o bot é criado: 

```javascript
var bot = new builder.UniversalBot(connector, [..waterfall steps..]); 
```

Além disso, durante esse processo de criação, você pode definir quais [armazenamento de dados](bot-builder-nodejs-state.md) você deseja usar. Por exemplo, para usar o armazenamento na memória, você pode defini-lo da seguinte maneira:

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();
var bot = new builder.UniversalBot(connector, [..waterfall steps..]).set('storage', inMemoryStorage); // Register in-memory storage 
```

O diálogo padrão é criado como uma matriz de funções que define as etapas da cascata. No exemplo, existem quatro funções, portanto, a cascata tem quatro etapas. Cada etapa realiza uma única tarefa e os resultados são processados na próxima etapa. O processo continua até a última etapa, onde a reserva é confirmada e o diálogo é finalizado.

A captura de tela a seguir mostra os resultados deste bot em execução no [emulador do Bot Framework](../bot-service-debug-emulator.md):

![Gerenciar o fluxo de conversas com a cascata](../media/bot-builder-nodejs-dialog-manage-conversation/waterfall-results.png)

### <a name="prompt-user-for-input"></a>Solicitar entrada do usuário

Cada etapa deste exemplo usa um prompt para solicitar informações ao usuário. Um prompt é um tipo especial de diálogo que solicita a entrada do usuário, aguarda uma resposta e retorna a resposta para a próxima etapa na cascata. Ver [solicitar que os usuários para a entrada](bot-builder-nodejs-dialog-prompt.md) para obter informações sobre os vários tipos diferentes de prompts, você pode usar em seu bot.

Neste exemplo, o bot usa `Prompts.text()` para solicitar uma resposta do usuário no formato de texto de forma livre. O usuário pode responder com qualquer texto e o bot deve decidir como lidar com a resposta. `Prompts.time()` usa a biblioteca [Chrono](https://github.com/wanasit/chrono) para analisar as informações de data e hora de uma string. Isso permite que seu bot entenda uma linguagem mais natural para especificar data e hora. Por exemplo: "6 de junho de 2017 às 21h", "Hoje às 19h30", "segunda-feira seguinte às 18h" e assim por diante.

> [!TIP] 
> O horário em que o usuário entra é convertido em hora UTC com base no fuso horário do servidor que hospeda o bot. Como o servidor pode estar localizado em um fuso horário diferente do usuário, lembre-se de considerar os fusos horários. Para converter data e hora para a hora local do usuário, considere perguntar ao usuário em que fuso horário está.

## <a name="manage-a-conversation-flow-with-multiple-dialogs"></a>Gerenciar um fluxo de conversa com várias caixas de diálogo

Outra técnica para gerenciar o fluxo de conversação é usar uma combinação de cascata e várias caixas de diálogo. A cascata permite agrupar as funções em uma caixa de diálogo, enquanto as caixas de diálogo permitem que você divida uma conversa em partes menores da funcionalidade que podem ser reutilizadas a qualquer momento.

Por exemplo, considere o bot de reserva do jantar. A amostra de código a seguir mostra o exemplo anterior reescrito para usar cascata e várias caixas de diálogo.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This is a dinner reservation bot that uses multiple dialogs to prompt users for input.
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.send("Welcome to the dinner reservation.");
        session.beginDialog('askForDateTime');
    },
    function (session, results) {
        session.dialogData.reservationDate = builder.EntityRecognizer.resolveTime([results.response]);
        session.beginDialog('askForPartySize');
    },
    function (session, results) {
        session.dialogData.partySize = results.response;
        session.beginDialog('askForReserverName');
    },
    function (session, results) {
        session.dialogData.reservationName = results.response;

        // Process request and display reservation details
        session.send(`Reservation confirmed. Reservation details: <br/>Date/Time: ${session.dialogData.reservationDate} <br/>Party size: ${session.dialogData.partySize} <br/>Reservation name: ${session.dialogData.reservationName}`);
        session.endDialog();
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 

// Dialog to ask for a date and time
bot.dialog('askForDateTime', [
    function (session) {
        builder.Prompts.time(session, "Please provide a reservation date and time (e.g.: June 6th at 5pm)");
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
]);

// Dialog to ask for number of people in the party
bot.dialog('askForPartySize', [
    function (session) {
        builder.Prompts.text(session, "How many people are in your party?");
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
])

// Dialog to ask for the reservation name.
bot.dialog('askForReserverName', [
    function (session) {
        builder.Prompts.text(session, "Who's name will this reservation be under?");
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
]);
```

Os resultados da execução desse bot são exatamente os mesmos do bot anterior, onde apenas a cascata é usada. No entanto, programaticamente, existem duas diferenças principais:

1. O diálogo padrão é dedicado a gerenciar o fluxo da conversa.
2. A tarefa para cada etapa da conversa está sendo gerenciada por um diálogo separado. Nesse caso, o bot precisou de três informações para avisar o usuário três vezes. Cada prompt agora está contido em sua própria caixa de diálogo.

Com essa técnica, você pode separar o fluxo de conversação da lógica da tarefa. Isso permite que os diálogos sejam reutilizados por diferentes fluxos de conversação, se necessário. 

## <a name="respond-to-user-input"></a>Responda à entrada do usuário

No processo de orientar o usuário por meio de uma série de tarefas, se o usuário tiver dúvidas ou quiser solicitar informações adicionais antes de responder, como você lidaria com essas solicitações? Por exemplo, independentemente de onde o usuário está na conversa, como o bot responderá se o usuário digitar "Ajuda", "Suporte" ou "Cancelar"? E se o usuário quiser informações adicionais sobre uma etapa? O que acontece se o usuário mudar de ideia e quiser abandonar a tarefa atual para iniciar uma tarefa completamente diferente?

O Bot Builder SDK para Node.js permite que um bot escute certas entradas em um contexto global ou dentro de um contexto local no escopo do diálogo atual. Essas entradas são chamadas de [ações](bot-builder-nodejs-dialog-actions.md), que permitem ao bot escutar a entrada do usuário com base em uma cláusula `matches`. Cabe ao bot para decidir como reagir às entradas de usuário específico.

### <a name="handle-global-action"></a>Identificador de ação global

Se você quiser que seu bot possa lidar com ações em qualquer ponto de uma conversa, use `triggerAction`. Os gatilhos permitem o bot invocar uma caixa de diálogo específica quando a entrada coincide com o termo especificado. Por exemplo, se você quiser oferecer suporte a uma opção global "Ajuda", poderá criar uma caixa de diálogo de ajuda e anexar um `triggerAction` que atenda a entrada correspondente à "Ajuda".

A amostra de código a seguir mostra como anexar um `triggerAction` a um diálogo para especificar que o diálogo deve ser chamado quando o usuário inserir "help".

```javascript
// The dialog stack is cleared and this dialog is invoked when the user enters 'help'.
bot.dialog('help', function (session, args, next) {
    session.endDialog("This is a bot that can help you make a dinner reservation. <br/>Please say 'next' to continue");
})
.triggerAction({
    matches: /^help$/i,
});
```

Por padrão, quando um `triggerAction` é executado, a pilha de diálogos é limpa e o diálogo acionado se torna o novo diálogo padrão. Neste exemplo, quando o `triggerAction` é executado, a caixa de diálogo pilha é limpa e o `help` caixa de diálogo, em seguida, é adicionada à pilha como a nova caixa de diálogo padrão. Se isso não é o comportamento desejado, você pode adicionar o `onSelectAction` opção para o `triggerAction`. A opção `onSelectAction` permite que o bot inicie uma nova caixa de diálogo sem limpar a pilha de diálogos, o que permite que a conversação seja temporariamente redirecionada e, mais tarde, continue de onde parou.

A amostra de código a seguir mostra como usar a `onSelectAction` opção com `triggerAction` para que a caixa de diálogo `help` seja adicionada à pilha de caixas de diálogo existente (e a pilha de caixas de diálogo não esteja desmarcada).

```javascript
bot.dialog('help', function (session, args, next) {
    session.endDialog("This is a bot that can help you make a dinner reservation. <br/>Please say 'next' to continue");
})
.triggerAction({
    matches: /^help$/i,
    onSelectAction: (session, args, next) => {
        // Add the help dialog to the dialog stack 
        // (override the default behavior of replacing the stack)
        session.beginDialog(args.action, args);
    }
});
```

Neste exemplo, o `help` diálogo assume o controle da conversa quando o usuário digitar "help". Como o `triggerAction` contém a opção `onSelectAction`, o diálogo `help` é colocado na pilha de diálogos existente e a pilha não é removida. Quando a caixa de diálogo `help` termina, ela é removida da pilha de caixas de diálogo e a conversa é retomada a partir do ponto em que foi interrompida com o comando `help`.

### <a name="handle-contextual-action"></a>Identificador de ação contextual

No exemplo anterior, o `help` caixa de diálogo é invocada se o usuário digitar "help" em qualquer ponto na conversa. Dessa forma, a caixa de diálogo só pode oferecer orientação geral de ajuda, pois não há contexto específico para a solicitação de ajuda do usuário. Mas, e se o usuário quiser solicitar ajuda sobre um ponto específico da conversa? Nesse caso, o diálogo `help` deve ser acionado dentro do contexto do diálogo atual.

Por exemplo, considere o bot de reserva do jantar. E se um usuário quiser saber o tamanho máximo da parte quando for perguntado pelo número de membros em sua festa? Para lidar com este cenário, você pode anexar um `beginDialogAction` à caixa de `askForPartySize` diálogo, ouvindo a entrada do usuário "help".

O exemplo de código a seguir mostra como anexar a Ajuda contextual em uma caixa de diálogo usando `beginDialogAction`.

```javascript
// Dialog to ask for number of people in the party
bot.dialog('askForPartySize', [
    function (session) {
        builder.Prompts.text(session, "How many people are in your party?");
    },
    function (session, results) {
       session.endDialogWithResult(results);
    }
])
.beginDialogAction('partySizeHelpAction', 'partySizeHelp', { matches: /^help$/i });

// Context Help dialog for party size
bot.dialog('partySizeHelp', function(session, args, next) {
    var msg = "Party size help: Our restaurant can support party sizes up to 150 members.";
    session.endDialog(msg);
})
```

Neste exemplo, sempre que o usuário digitar "help", o bot pressionará o diálogo `partySizeHelp` na pilha. Essa caixa de diálogo envia uma mensagem de ajuda ao usuário e, em seguida, finaliza a caixa de diálogo, retornando o controle de volta para a caixa de diálogo `askForPartySize`, que solicita ao usuário um tamanho de parte.

É importante observar que essa ajuda sensível ao contexto é executada apenas quando o usuário está na caixa de diálogo `askForPartySize`. Caso contrário, a mensagem de ajuda geral do `triggerAction` será executada em seu lugar. Em outras palavras, a cláusula `matches` local sempre tem precedência sobre a cláusula global `matches`. Por exemplo, se um `beginDialogAction` correspondências para **ajudar**, em seguida, as correspondências para **ajudar** no `triggerAction` não será executado. Para obter mais informações, consulte [precedência da ação](bot-builder-nodejs-dialog-actions.md#action-precedence).

### <a name="change-the-topic-of-conversation"></a>Alterar o tópico de conversa

Por padrão, a execução de um `triggerAction` limpa a pilha de caixas de diálogo e redefine a conversa, começando com a caixa de diálogo especificada. Esse comportamento costuma ser preferível quando o bot precisa alternar de um tópico de conversa para outro, como se um usuário no meio da reserva de um jantar em vez disso decidisse solicitar que o jantar fosse entregue em seu quarto de hotel. 

O exemplo a seguir se baseia no anterior, de modo que o bot permite que o usuário faça uma reserva para o jantar ou ordene que o jantar seja entregue. Neste bot, a caixa de diálogo padrão é uma caixa de diálogo de saudação que apresenta duas opções para o usuário: `Dinner Reservation` e `Order Dinner`.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This bot enables users to either make a dinner reservation or order dinner.
var bot = new builder.UniversalBot(connector, function(session){
    var msg = "Welcome to the reservation bot. Please say `Dinner Reservation` or `Order Dinner`";
    session.send(msg);
}).set('storage', inMemoryStorage); // Register in-memory storage 
```

A lógica de reserva de jantar que estava no diálogo padrão do exemplo anterior agora está em sua própria caixa de diálogo chamada `dinnerReservation`. O fluxo de `dinnerReservation` permanece o mesmo que a versão de vários diálogos discutida anteriormente. A única diferença é que a caixa de diálogo tem um `triggerAction` anexado a ele. Observe que nesta versão, o `confirmPrompt` pede ao usuário para confirmar que deseja alterar o tópico da conversa, antes de invocar o novo diálogo. É uma boa prática incluir um `confirmPrompt` em cenários como este, porque uma vez que a pilha de diálogos esteja limpa, o usuário será direcionado para o novo tópico de conversação, abandonando assim o tópico de conversação que estava em andamento.

```javascript
// This dialog helps the user make a dinner reservation.
bot.dialog('dinnerReservation', [
    function (session) {
        session.send("Welcome to the dinner reservation.");
        session.beginDialog('askForDateTime');
    },
    function (session, results) {
        session.dialogData.reservationDate = builder.EntityRecognizer.resolveTime([results.response]);
        session.beginDialog('askForPartySize');
    },
    function (session, results) {
        session.dialogData.partySize = results.response;
        session.beginDialog('askForReserverName');
    },
    function (session, results) {
        session.dialogData.reservationName = results.response;

        // Process request and display reservation details
        session.send(`Reservation confirmed. Reservation details: <br/>Date/Time: ${session.dialogData.reservationDate} <br/>Party size: ${session.dialogData.partySize} <br/>Reservation name: ${session.dialogData.reservationName}`);
        session.endDialog();
    }
])
.triggerAction({
    matches: /^dinner reservation$/i,
    confirmPrompt: "This will cancel your current request. Are you sure?"
});
```

O segundo tópico de conversação é definido no diálogo `orderDinner` usando uma cascata. Essa caixa de diálogo simplesmente exibe o menu de jantar e solicita ao usuário um número de quarto após o pedido ser especificado. Um `triggerAction` é anexado à caixa de diálogo para especificar que ele deve ser chamado quando o usuário inserir "jantar de pedido" e garantir que o usuário seja solicitado a confirmar sua seleção, caso ele indique que deseja alterar o tópico da conversa.

```javascript
// This dialog help the user order dinner to be delivered to their hotel room.
var dinnerMenu = {
    "Potato Salad - $5.99": {
        Description: "Potato Salad",
        Price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        Description: "Tuna Sandwich",
        Price: 6.89
    },
    "Clam Chowder - $4.50":{
        Description: "Clam Chowder",
        Price: 4.50
    }
};

bot.dialog('orderDinner', [
    function(session){
        session.send("Lets order some dinner!");
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    },
    function (session, results) {
        if (results.response) {
            var order = dinnerMenu[results.response.entity];
            var msg = `You ordered: ${order.Description} for a total of $${order.Price}.`;
            session.dialogData.order = order;
            session.send(msg);
            builder.Prompts.text(session, "What is your room number?");
        } 
    },
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${session.dialogData.room}`;
            session.endDialog(msg);
        }
    }
])
.triggerAction({
    matches: /^order dinner$/i,
    confirmPrompt: "This will cancel your order. Are you sure?"
});
```

Depois que o usuário iniciar uma conversa e selecionar `Dinner Reservation` ou `Order Dinner`, poderá mudar de ideia a qualquer momento. Por exemplo, se o usuário estiver no meio de fazer uma reserva para o jantar e entrar "encomendar o jantar", o bot confirmará dizendo: "Isso cancelará sua solicitação atual. Tem certeza? ". Se o usuário digitar "não", a solicitação será cancelada e o usuário poderá continuar com o processo de reserva do jantar. Se o usuário digitar "sim", o bot irá limpar a pilha de diálogos e transferir o controle da conversa para o diálogo `orderDinner`.

## <a name="end-conversation"></a>Terminar conversa

Nos exemplos acima, as caixas de diálogo são fechadas por meio `session.endDialog` ou `session.endDialogWithResult`, sendo que ambos encerrar a caixa de diálogo, removê-lo da pilha e retornar o controle para a caixa de diálogo de chamada. Em situações em que o usuário chegou ao final da conversa, você deve usar `session.endConversation` para indicar que a conversa foi concluída.

O método [`session.endConversation`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#endconversation) termina uma conversa e, opcionalmente, envia uma mensagem para o usuário. Por exemplo, o `orderDinner` caixa de diálogo no exemplo anterior poderia terminar a conversa usando `session.endConversation`, conforme mostrado no exemplo de código a seguir.

```javascript
bot.dialog('orderDinner', [
    //...waterfall steps...
    // Last step
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${session.dialogData.room}`;
            session.endConversation(msg);
        }
    }
]);
```

Chamar `session.endConversation` encerrará a conversa limpando a pilha de caixas de diálogo e redefinindo o armazenamento [`session.conversationData`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#conversationdata). Para obter mais informações sobre armazenamento de dados, consulte [Gerenciar dados do estado](bot-builder-nodejs-state.md).

Chamar `session.endConversation` é uma coisa lógica a ser feita quando o usuário conclui o fluxo de conversas para o qual o bot foi projetado. Você também pode usar `session.endConversation` para terminar a conversa em situações em que o usuário entra em "Cancelar" ou "adeus" no meio de uma conversa. Para fazer isso, basta anexar um `endConversationAction` ao diálogo e fazer com que esse gatilho ouça a entrada correspondente a "cancelar" ou "até logo".

A amostra de código a seguir mostra como anexar um `endConversationAction` a uma caixa de diálogo para encerrar a conversa se o usuário inserir "cancelar" ou "até logo".

```javascript
bot.dialog('dinnerOrder', [
    //...waterfall steps...
])
.endConversationAction(
    "endOrderDinner", "Ok. Goodbye.",
    {
        matches: /^cancel$|^goodbye$/i,
        confirmPrompt: "This will cancel your order. Are you sure?"
    }
);
```

Uma vez que terminar uma conversa com `session.endConversation` ou `endConversationAction` irá limpar a pilha de diálogos e forçar o usuário a começar de novo, você deve incluir um `confirmPrompt` para garantir que o usuário realmente deseja fazê-lo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você deve explorar maneiras de gerenciar conversas são sequenciais por natureza. E se você quiser repetir uma caixa de diálogo ou usar o padrão de loop em sua conversa? Vamos ver como você pode fazer isso, substituindo as caixas de diálogo na pilha.

> [!div class="nextstepaction"]
> [Substituir diálogos](bot-builder-nodejs-dialog-replace.md)

