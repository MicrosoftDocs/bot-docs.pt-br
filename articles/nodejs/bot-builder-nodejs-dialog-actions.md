---
title: Tratar ações do usuário | Microsoft Docs
description: Saiba como tratar ações do usuário, permitindo ao bot escutar e tratar a entrada do usuário contendo determinadas palavras-chave usando o SDK do Bot Framework para Node.js.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 7ca595b1c24769addfbdf7975c48d3a052c4a2de
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54226001"
---
# <a name="handle-user-actions"></a>Tratar ações do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-global-handlers.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-actions.md)

Normalmente, os usuários tentam acessar algumas funcionalidades em um bot, usando palavras-chave como "ajuda", "cancelar" ou "começar novamente." Os usuários fazem isso no meio de uma conversa, quando o bot está esperando uma resposta diferente. Ao implementar **ações**, você pode projetar o bot para manipular essas solicitações normalmente. Os manipuladores examinarão na entrada do usuário as palavras-chave especificadas, como "ajuda", "cancelar" ou "começar novamente" e responderão de maneira adequada. 

![como os usuários falam](../media/designing-bots/capabilities/trigger-actions.png)

## <a name="action-types"></a>Tipos de ação

Os tipos de ação que você pode anexar a um diálogo são listados na tabela abaixo. O link para cada nome de ação irá direcioná-lo para uma seção que fornece mais detalhes sobre essa ação.

| Ação | Escopo | DESCRIÇÃO |
|------|------| ---- |
| [triggerAction](#bind-a-triggeraction) | Global | Associa uma ação ao diálogo que limpará a pilha de diálogo e efetuará push para a parte inferior da pilha. Use a opção `onSelectAction` para substituir esse comportamento padrão. |
| [customAction](#bind-a-customaction) | Global | Associa uma ação personalizada ao bot que pode processar informações ou executar ações sem afetar a pilha de diálogo. Use a opção `onSelectAction` para personalizar a funcionalidade dessa ação. |
[beginDialogAction](#bind-a-begindialogaction) | Contextual | Associa uma ação ao diálogo que inicia outro diálogo quando é disparada. Será efetuado push do diálogo inicial na pilha e removido assim que for encerrado. |
[reloadAction](#bind-a-reloadaction) | Contextual | Associa uma ação ao diálogo, fazendo com que o diálogo seja recarregado quando for disparada. É possível usar `reloadAction` para manipular enunciados de usuário como "começar novamente." |
[cancelAction](#bind-a-cancelaction) | Contextual | Associa uma ação ao diálogo que cancela o diálogo quando é disparada. É possível usar `cancelAction` para manipular enunciados de usuário como "cancelar" ou "esquecer." |
[endConversationAction](#bind-an-endconversationaction) | Contextual | Associa uma ação ao diálogo que encerra a conversa com o usuário quando é disparada. É possível usar `endConversationAction` para manipular enunciados de usuário como "até logo." |

## <a name="action-precedence"></a>Precedência de ação 

Quando um bot recebe um enunciado de um usuário, ele verifica o enunciado em relação a todas as ações registradas que estão na pilha de diálogo. A correspondência começa do topo da pilha até a parte inferior da pilha. Se nada corresponder, então, verifique o enunciado em relação à opção `matches` de todas as ações globais.

A precedência de ação é importante nos casos em que usar o mesmo comando em contextos diferentes. Por exemplo, é possível usar o comando "Ajuda" para o bot como uma ajuda geral. Também é possível usar "Ajuda" para cada uma das tarefas, mas esses comandos de ajuda são contextuais para cada tarefa. Para um exemplo de trabalho que elabora esse ponto, consulte [Responder à entrada do usuário](bot-builder-nodejs-dialog-manage-conversation-flow.md#respond-to-user-input).

## <a name="bind-actions-to-dialog"></a>Associar ações ao diálogo

Os enunciados de usuário ou cliques no botão podem *disparar* uma ação, que é associada a um [diálogo](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html).
Se as *correspondências* forem especificadas, a ação escutará o usuário dizer uma palavra ou frase que disparará a ação.  A opção `matches` pode ter uma expressão regular ou o nome de um [reconhecedor][RecognizeIntent].
Para associar a ação a um clique no botão, use [CardAction.dialogAction()][CardAction] para disparar a ação.

As ações são *encadeáveis*, o que permite associar o máximo de ações a um diálogo que você desejar.

### <a name="bind-a-triggeraction"></a>Associar um triggerAction

Para associar um [triggerAction][triggerAction] a um diálogo, faça conforme a seguir:

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will clear the dialog stack and pushes
// the 'orderDinner' dialog onto the bottom of stack.
.triggerAction({
    matches: /^order dinner$/i
});
```

Associar `triggerAction` a um diálogo registra-o no bot. Quando disparado, `triggerAction` limpará a pilha de diálogo e efetuará push do diálogo disparado para a pilha. Essa ação é melhor usada para alternar entre o [tópico da conversa](bot-builder-nodejs-dialog-manage-conversation-flow.md#change-the-topic-of-conversation) ou para permitir que os usuários solicitem tarefas autônomas arbitrárias. Se quiser substituir o comportamento onde essa ação limpa a pilha de diálogo, adicione uma opção `onSelectAction` ao `triggerAction`.

O snippet de código abaixo mostra como fornecer ajuda geral de um contexto global sem limpar a pilha de diálogo.

```javascript
bot.dialog('help', function (session, args, next) {
    //Send a help message
    session.endDialog("Global help menu.");
})
// Once triggered, will start a new dialog as specified by
// the 'onSelectAction' option.
.triggerAction({
    matches: /^help$/i,
    onSelectAction: (session, args, next) => {
        // Add the help dialog to the top of the dialog stack 
        // (override the default behavior of replacing the stack)
        session.beginDialog(args.action, args);
    }
});
```

Nesse caso, o `triggerAction` está anexado ao próprio diálogo `help` (em oposição ao diálogo `orderDinner`). A opção `onSelectAction` permite iniciar esse diálogo sem limpar a pilha de diálogo. Isso permite manipular solicitações globais como "ajuda", "sobre", "suporte" etc. Observe que a opção `onSelectAction` chama explicitamente o método `session.beginDialog` para iniciar o diálogo disparado. A ID do diálogo disparado é fornecida por meio de `args.action`. Não digite manualmente a ID de diálogo (por exemplo, 'ajuda') nesse método, caso contrário, você poderá receber erros de tempo de execução. Se você quiser disparar uma mensagem de ajuda contextual para a tarefa `orderDinner` propriamente dita, considere anexar um `beginDialogAction` ao diálogo `orderDinner`.

### <a name="bind-a-customaction"></a>Associar um customAction

Ao contrário de outros tipos de ação, `customAction` não possui nenhuma ação padrão definida. Cabe a você definir o que essa ação executará. O benefício de usar `customAction` é ter a opção de processar solicitações de usuários sem manipular a pilha de diálogo. Quando um `customAction` é disparado, a opção `onSelectAction` pode processar a solicitação sem efetuar push de novos diálogos na pilha. Depois que a ação for concluída, o controle será passado de volta para o diálogo que está no topo da pilha e o bot poderá continuar.

É possível usar um `customAction` para fornecer solicitações de ações gerais e rápidas como "Qual é a temperatura externa neste momento?", "Que horas são em Paris agora?", "Lembre-me de comprar leite às 17h hoje" etc. Essas são ações gerais que o bot pode executar sem manipular a pilha.

Outra diferença importante com `customAction` é que, é associado ao bot em oposição a um diálogo.

O exemplo de código a seguir mostra como associar um `customAction` ao `bot` escutando solicitações para definir um lembrete.

```javascript
bot.customAction({
    matches: /remind|reminder/gi,
    onSelectAction: (session, args, next) => {
        // Set reminder...
        session.send("Reminder is set.");
    }
})
```

### <a name="bind-a-begindialogaction"></a>Associar um beginDialogAction

Associar um `beginDialogAction` a um diálogo registrará a ação no diálogo. Esse método iniciará outro diálogo quando for disparado. O comportamento dessa ação é semelhante a chamar o método [beginDialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#begindialog). É efetuado push do novo diálogo na parte superior da pilha de diálogo para que não finalize automaticamente a tarefa atual. A tarefa atual será continuada quando o novo diálogo encerrar. 

O snippet de código a seguir mostra como associar um [beginDialogAction][beginDialogAction] a um diálogo.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will start the 'showDinnerCart' dialog.
// Then, the waterfall will resumed from the step that was interrupted.
.beginDialogAction('showCartAction', 'showDinnerCart', {
    matches: /^show cart$/i
});

// Show dinner items in cart
bot.dialog('showDinnerCart', function(session){
    for(var i = 1; i < session.conversationData.orders.length; i++){
        session.send(`You ordered: ${session.conversationData.orders[i].Description} for a total of $${session.conversationData.orders[i].Price}.`);
    }

    // End this dialog
    session.endDialog(`Your total is: $${session.conversationData.orders[0].Price}`);
});
```

Nos casos em que for necessário passar argumentos adicionais para o novo diálogo, será possível adicionar uma opção [`dialogArgs`](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.idialogactionoptions#dialogargs) à ação.

Usando o exemplo acima, você poderá modificá-lo para aceitar argumentos transmitidos através de `dialogArgs`.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will start the 'showDinnerCart' dialog.
// Then, the waterfall will resumed from the step that was interrupted.
.beginDialogAction('showCartAction', 'showDinnerCart', {
    matches: /^show cart$/i,
    dialogArgs: {
        showTotal: true;
    }
});

// Show dinner items in cart with the option to show total or not.
bot.dialog('showDinnerCart', function(session, args){
    for(var i = 1; i < session.conversationData.orders.length; i++){
        session.send(`You ordered: ${session.conversationData.orders[i].Description} for a total of $${session.conversationData.orders[i].Price}.`);
    }

    if(args && args.showTotal){
        // End this dialog with total.
        session.endDialog(`Your total is: $${session.conversationData.orders[0].Price}`);
    }
    else{
        session.endDialog(); // Ends without a message.
    }
});
```

### <a name="bind-a-reloadaction"></a>Associar um reloadAction

Associar um `reloadAction` a um diálogo irá registrá-lo no diálogo. Associar essa ação a um diálogo faz com que o diálogo seja reiniciado quando a ação for disparada. Disparar essa ação é semelhante a chamar o método [replaceDialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#replacedialog). Isso é útil para implementar a lógica para lidar com enunciados de usuário como "começar novamente" ou criar [loops](bot-builder-nodejs-dialog-replace.md#repeat-an-action).

O snippet de código a seguir mostra como associar um [reloadAction][reloadAction] a um diálogo.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will restart the dialog.
.reloadAction('startOver', 'Ok, starting over.', {
    matches: /^start over$/i
});
```

Nos casos em que for necessário passar argumentos adicionais para o diálogo recarregado, será possível adicionar uma opção [`dialogArgs`](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.idialogactionoptions#dialogargs) à ação. Essa opção é passada para o parâmetro `args`. Regravar o código de exemplo acima para receber um argumento em uma ação de recarga será algo parecido com isto:

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    function(session, args, next){
        if(args && args.isReloaded){
            // Reload action was triggered.
        }

        session.send("Lets order some dinner!");
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    }
    //...other waterfall steps...
])
// Once triggered, will restart the dialog.
.reloadAction('startOver', 'Ok, starting over.', {
    matches: /^start over$/i,
    dialogArgs: {
        isReloaded: true;
    }
});
```

### <a name="bind-a-cancelaction"></a>Associar um cancelAction

Associar um `cancelAction` irá registrá-lo na caixa de diálogo. Uma vez disparada, essa ação encerrará abruptamente o diálogo. Quando o diálogo encerrar, o diálogo pai será retomado com um código retomado indicando que estava `canceled`. Essa ação permite manipular enunciados como "esquecer" ou "cancelar." Se for necessário cancelar programaticamente um diálogo, consulte [Cancelar um diálogo](bot-builder-nodejs-dialog-replace.md#cancel-a-dialog). Para obter mais informações sobre *código retomado*, consulte [Solicitar resultados](bot-builder-nodejs-dialog-prompt.md#prompt-results). 

O snippet de código a seguir mostra como associar um [cancelAction][cancelAction] a um diálogo.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
//Once triggered, will end the dialog.
.cancelAction('cancelAction', 'Ok, cancel order.', {
    matches: /^nevermind$|^cancel$|^cancel.*order/i
});
```

### <a name="bind-an-endconversationaction"></a>Associar um endConversationAction

Associar um `endConversationAction` irá registrá-lo no diálogo. Quando disparada, essa ação encerrará a conversa com o usuário. Disparar essa ação é semelhante a chamar o método [endConversation](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#endconversation). Quando uma conversa encerrar, o SDK do Bot Framework para Node.js limpará a pilha de diálogo e os dados de estado persistentes. Para obter mais informações sobre dados de estado persistentes, consulte [Gerenciar dados de estado](bot-builder-nodejs-state.md).

O snippet de código a seguir mostra como associar um [endConversationAction][endConversationAction] a um diálogo.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will end the conversation.
.endConversationAction('endConversationAction', 'Ok, goodbye!', {
    matches: /^goodbye$/i
});
```

## <a name="confirm-interruptions"></a>Confirmar interrupções

A maioria dessas ações, se não todas, interrompe o fluxo normal de uma conversa. Muitos são interrompidas e devem ser manipuladas com cuidado. Por exemplo, `triggerAction`, `cancelAction` ou `endConversationAction` limpará a pilha de diálogo. Se o usuário disparou uma dessas ações acidentalmente, deverá iniciar a tarefa novamente. Para garantir que o usuário realmente quer disparar essas ações, é possível adicionar uma opção `confirmPrompt` a essas ações. O `confirmPrompt` perguntará se o usuário tem certeza sobre o cancelamento ou encerramento da tarefa atual. Isso permite que o usuário mude de ideia e continue o processo.

O snippet de código abaixo mostra [cancelAction][cancelAction] com um [confirmPrompt](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#confirmprompt) para certificar-se de que o usuário realmente quer cancelar o processo do pedido.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Confirm before triggering the action.
// Once triggered, will end the dialog. 
.cancelAction('cancelAction', 'Ok, cancel order.', {
    matches: /^nevermind$|^cancel$|^cancel.*order/i,
    confirmPrompt: "Are you sure?"
});
```

Quando essa ação for disparada, perguntará ao usuário "Você tem certeza?" O usuário terá que responder "Sim" para realizar a ação ou "Não" para cancelar a ação e continuar de onde estava.

## <a name="next-steps"></a>Próximas etapas

As **ações** permitem que você antecipe as solicitações dos usuários e que o bot manipule essas solicitações normalmente. Muitas dessas ações são prejudiciais ao fluxo de conversa atual. Se quiser permitir que os usuários possam alternar e retomar o fluxo de conversa, será necessário salvar o estado de usuário antes de alternar. Veremos como salvar o estado de usuário e gerenciar dados de estado.

> [!div class="nextstepaction"]
> [Gerenciar dados de estado](bot-builder-nodejs-state.md)


[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[cancelAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#cancelaction

[reloadAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#reloadaction

[beginDialogAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#begindialogaction

[endConversationAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#endconversationaction

[RecognizeIntent]: bot-builder-nodejs-recognize-intent-messages.md

[CardAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.cardaction#dialogaction
