---
title: Adicionar anexos de cartão avançado a mensagens (JS v3) – Serviço de Bot
description: Saiba como enviar cartões avançados interativos e envolventes usando o SDK do Bot Framework para Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: cd2997bc4795a922d25f6c4b4f5fef3d7dd4f366
ms.sourcegitcommit: 4e1af50bd46debfdf9dcbab9a5d1b1633b541e27
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2020
ms.locfileid: "76752908"
---
# <a name="add-rich-card-attachments-to-messages"></a>Adicionar anexos de cartão avançados às mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Vários canais, como o Skype e o Facebook, dão suporte ao envio de cartões gráficos avançados para usuários com botões interativos nos quais o usuário clica para iniciar uma ação.
O SDK fornece várias classes de construtor de cartão e mensagem que podem ser usadas para criar e enviar cartões. O Serviço Bot Framework Connector renderizará esses cartões usando o esquema nativo do canal, dando suporte à comunicação multiplataforma. Se o canal não der suporte a cartões, como SMS, o Bot Framework fará o melhor para renderizar uma experiência razoável para os usuários.

## <a name="types-of-rich-cards"></a>Tipos de cartões avançados

Atualmente, o Bot Framework dá suporte a oito tipos de cartões avançados:

| Tipo de cartão | Descrição |
|------|------|
| [Cartão adaptável](/adaptive-cards/get-started/bots) | Um cartão personalizável que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada.  Confira [suporte por canal](/adaptive-cards/get-started/bots#channel-status). |
| [Cartão de animação][animationCard] | Um cartão que pode reproduzir GIFs animados ou vídeos curtos. |
| [Cartão de áudio][audioCard] | Um cartão que pode reproduzir um arquivo de áudio. |
| [Cartão Hero][heroCard] | Um cartão que geralmente contém uma única imagem grande, um ou mais botões e um texto. |
| [Cartão em miniatura][thumbnailCard] | Um cartão que geralmente contém uma única imagem em miniatura, um ou mais botões e um texto.|
| [Cartão de recibo][receiptCard] | Um cartão que permite a um bot fornecer um recibo para o usuário. Normalmente, contém a lista de itens a serem incluídos no recibo, informações fiscais e de totais e outros textos. |
| [Cartão de entrada][signinCard] | Um cartão que permite a um bot solicitar a entrada do usuário. Normalmente, contém um texto e um ou mais botões nos quais o usuário pode clicar para iniciar o processo de entrada. |
| [Cartão de vídeo][videoCard] | Um cartão que pode reproduzir vídeos. |

## <a name="send-a-carousel-of-hero-cards"></a>Enviar um carrossel de cartões Hero

O exemplo a seguir mostra um bot para uma empresa fictícia de camisetas e mostra como enviar um carrossel de cartões em resposta à fala do usuário "mostrar camisas".

```javascript
// Create your bot with a function to receive messages from the user
// Create bot and default message handler
var bot = new builder.UniversalBot(connector, function (session) {
    session.send("Hi... We sell shirts. Say 'show shirts' to see our products.");
});

// Add dialog to return list of shirts available
bot.dialog('showShirts', function (session) {
    var msg = new builder.Message(session);
    msg.attachmentLayout(builder.AttachmentLayout.carousel)
    msg.attachments([
        new builder.HeroCard(session)
            .title("Classic White T-Shirt")
            .subtitle("100% Soft and Luxurious Cotton")
            .text("Price is $25 and carried in sizes (S, M, L, and XL)")
            .images([builder.CardImage.create(session, 'http://petersapparel.parseapp.com/img/whiteshirt.png')])
            .buttons([
                builder.CardAction.imBack(session, "buy classic white t-shirt", "Buy")
            ]),
        new builder.HeroCard(session)
            .title("Classic Gray T-Shirt")
            .subtitle("100% Soft and Luxurious Cotton")
            .text("Price is $25 and carried in sizes (S, M, L, and XL)")
            .images([builder.CardImage.create(session, 'http://petersapparel.parseapp.com/img/grayshirt.png')])
            .buttons([
                builder.CardAction.imBack(session, "buy classic gray t-shirt", "Buy")
            ])
    ]);
    session.send(msg).endDialog();
}).triggerAction({ matches: /^(show|list)/i });
```

Este exemplo usa a classe [Message][Message] para criar um carrossel.  
O carrossel é composto por uma lista de classes [HeroCard][heroCard] que contêm uma imagem, um texto e um único botão que dispara a compra do item.  
O clique no botão **Comprar** dispara o envio de uma mensagem; portanto, precisamos adicionar um segundo diálogo para capturar o clique no botão.

## <a name="handle-button-input"></a>Manipular a entrada do botão

O diálogo `buyButtonClick` será disparado sempre que for recebida uma mensagem que começa com "comprar" ou "adicionar" e ela for seguida por algo que contém a palavra "camiseta".
O diálogo é iniciado usando algumas expressões regulares para procurar a cor e o tamanho de camisa opcional solicitados pelo usuário.
Essa flexibilidade adicional permite o suporte à cliques de botão e mensagens no idioma natural do usuário, como "adicione uma camisa cinza tamanho grande ao meu carrinho".
Se a cor for válida, mas o tamanho for desconhecido, o bot solicitará ao usuário a seleção de um tamanho em uma lista antes de adicionar o item ao carrinho.
Depois que o bot tiver todas as informações necessárias, ele colocará o item em um carrinho persistente usando **session.userData** e, em seguida, enviará ao usuário uma mensagem de confirmação.

```javascript
// Add dialog to handle 'Buy' button click
bot.dialog('buyButtonClick', [
    function (session, args, next) {
        // Get color and optional size from users utterance
        var utterance = args.intent.matched[0];
        var color = /(white|gray)/i.exec(utterance);
        var size = /\b(Extra Large|Large|Medium|Small)\b/i.exec(utterance);
        if (color) {
            // Initialize cart item
            var item = session.dialogData.item = {
                product: "classic " + color[0].toLowerCase() + " t-shirt",
                size: size ? size[0].toLowerCase() : null,
                price: 25.0,
                qty: 1
            };
            if (!item.size) {
                // Prompt for size
                builder.Prompts.choice(session, "What size would you like?", "Small|Medium|Large|Extra Large");
            } else {
                //Skip to next waterfall step
                next();
            }
        } else {
            // Invalid product
            session.send("I'm sorry... That product wasn't found.").endDialog();
        }
    },
    function (session, results) {
        // Save size if prompted
        var item = session.dialogData.item;
        if (results.response) {
            item.size = results.response.entity.toLowerCase();
        }

        // Add to cart
        if (!session.userData.cart) {
            session.userData.cart = [];
        }
        session.userData.cart.push(item);

        // Send confirmation to users
        session.send("A '%(size)s %(product)s' has been added to your cart.", item).endDialog();
    }
]).triggerAction({ matches: /(buy|add)\s.*shirt/i });
```

<!-- 
> [!NOTE]
> When sending a message that contains images, keep in mind that some channels download images before displaying a message to the user. >  
> As a result, a message containing an image followed immediately by a message without images may sometimes be flipped in the user's feed.
> For information on how to avoid messages being sent out of order, see [Message ordering][MessageOrder].  
-->

## <a name="add-a-message-delay-for-image-downloads"></a>Adicionar um atraso de mensagem para downloads de imagem

Alguns canais tendem a baixar imagens antes de exibirem uma mensagem para o usuário. Portanto, se você enviar uma mensagem que contém uma imagem seguida imediatamente por uma mensagem sem imagens, às vezes, você verá as mensagens invertidas no feed do usuário. Para minimizar a probabilidade de isso ocorrer, você pode tentar garantir que as imagens sejam recebidas de CDNs (redes de distribuição de conteúdo) e evitar o uso de imagens excessivamente grandes. Em casos extremos, talvez seja necessário até mesmo inserir um atraso de 1 a 2 segundos entre a mensagem com a imagem e a que vem a seguir. Você pode fazer com que esse atraso pareça um pouco mais natural para o usuário chamando **session.sendTyping()** para enviar um indicador de digitação antes de iniciar o atraso.

<!-- 
To learn more about sending a typing indicator, see [How to send a typing indicator](bot-builder-nodejs-send-typing-indicator.md).
-->

O Bot Framework implementa um envio em lote para tentar impedir que várias mensagens do bot sejam exibidas fora de ordem. <!-- Unfortunately, not all channels can guarantee this. --> Quando o bot enviar várias respostas para o usuário, as mensagens individuais serão automaticamente agrupadas em um lote e entregues ao usuário como um conjunto em um esforço para preservar a ordem original das mensagens. Esse envio em lote automático aguarda um padrão de 250ms após cada chamada a **session.send()** antes de iniciar a próxima chamada a **send()** .

O atraso do envio em lote da mensagem é configurável. Para desabilitar a lógica de envio em lote automático do SDK, defina o atraso padrão com um número grande e, em seguida, chame **sendBatch()** manualmente com um retorno de chamada a ser invocado após a entrega do lote.

## <a name="send-an-adaptive-card"></a>Enviar um Cartão adaptável

O Cartão Adaptável pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada.
Os Cartões Adaptáveis são criados usando o formato JSON especificado em [Cartões Adaptáveis](http://adaptivecards.io), o que fornece a você controle total sobre o conteúdo e o formato do cartão.

Para criar um Cartão Adaptável usando o Node.js, aproveite as informações do site [Cartões Adaptáveis](http://adaptivecards.io) para compreender o esquema de Cartão Adaptável, explore elementos de Cartão Adaptável e veja amostras JSON que podem ser usadas para criar cartões de composição e complexidade variadas. Além disso, você pode usar o Visualizador Interativo para criar o conteúdo de Cartão Adaptável e visualizar a saída do cartão.

Este exemplo de código mostra como criar uma mensagem que contém um Cartão Adaptável para um lembrete de calendário:

[!code-javascript[Add Adaptive Card attachment](../includes/code/node-send-card-buttons.js#addAdaptiveCardAttachment)]

O cartão resultante contém três blocos de texto, um campo de entrada (lista de opções) e três botões:

![Lembrete de calendário do Cartão Adaptável](../media/adaptive-card-reminder.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Referência de canais][inspector]
- [Cartões Adaptáveis](http://adaptivecards.io)
- [AnimationCard][animationCard]
- [AudioCard][audioCard]
- [HeroCard][heroCard]
- [ThumbnailCard][thumbnailCard]
- [ReceiptCard][receiptCard]
- [SigninCard][signinCard]
- [VideoCard][videoCard]
- [Mensagem][Message]
- [Como enviar anexos](bot-builder-nodejs-send-receive-attachments.md)

[MessageOrder]: bot-builder-nodejs-manage-conversation-flow.md#message-ordering
[Message]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message
[IMessage]: http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage

[animationCard]: https://docs.microsoft.com/javascript/api/botbuilder/animationcard?view=botbuilder-ts-3.0
[audioCard]: https://docs.microsoft.com/javascript/api/botbuilder/audiocard?view=botbuilder-ts-3.0
[heroCard]: https://docs.microsoft.com/javascript/api/botbuilder/herocard?view=botbuilder-ts-3.0
[thumbnailCard]: https://docs.microsoft.com/javascript/api/botbuilder/thumbnailcard?view=botbuilder-ts-3.0
[receiptCard]: https://docs.microsoft.com/javascript/api/botbuilder/receiptcard?view=botbuilder-ts-3.0
[signinCard]: https://docs.microsoft.com/javascript/api/botbuilder/signincard?view=botbuilder-ts-3.0
[videoCard]: https://docs.microsoft.com/javascript/api/botbuilder/videocard?view=botbuilder-ts-3.0

[inspector]: ../bot-service-channels-reference.md
