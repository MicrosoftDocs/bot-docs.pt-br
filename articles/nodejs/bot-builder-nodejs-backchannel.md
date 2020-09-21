---
title: Trocar informações usando o controle da Web – Serviço de Bot
description: Saiba como trocar informações entre o bot e uma página da Web usando o SDK do Bot Framework para Node.js.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: d17576205448249a4fd3ebeee53c6449cfb63048
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824876"
---
# <a name="use-the-backchannel-mechanism-in-the-v3-javascript-sdk"></a>Usar o mecanismo de backchannel no SDK do JavaScript v3

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Introduction to backchannel mechanism](../includes/snippet-backchannel.md)]

## <a name="walk-through"></a>Passo a passo

O controle de Webchat de software livre acessa a API de Linha Direta usando uma classe JavaScript chamada <a href="https://github.com/microsoft/botframework-DirectLinejs" target="_blank">DirectLineJS</a>. O controle pode criar sua própria instância de Linha Direta ou compartilhar uma com a página de hospedagem. Se o controle compartilhar uma instância de Linha Direta com a página de hospedagem, o controle e a página poderão enviar e receber atividades. O diagrama a seguir mostra a arquitetura de alto nível de um site que dá suporte à funcionalidade de bot, usando o controle da Web (chat) de software livre e a API de Linha Direta. 

![Backchannel](../media/designing-bots/patterns/back-channel.png)

### <a name="sample-code"></a>Código de exemplo 

Neste exemplo, o bot e a página da Web usarão o mecanismo de backchannel para trocar informações que estão invisíveis para o usuário. O bot solicitará que a página da Web altere a cor da tela de fundo e a página da Web notificará o bot quando o usuário clicar em um botão na página. 

> [!NOTE]
> Os snippets de código deste artigo têm sua origem na <a href="https://github.com/Microsoft/BotFramework-WebChat/blob/master/samples/backchannel/index.html" target="_blank">amostra de backchannel</a> e no <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">bot de backchannel</a>. 

#### <a name="client-side-code"></a>Código do lado do cliente

Primeiro, a página da Web cria um objeto **DirectLine**.

```javascript
var botConnection = new BotChat.DirectLine(...);
```

Em seguida, ela compartilha o objeto **DirectLine** ao criar a instância de WebChat.

```javascript
BotChat.App({
    botConnection: botConnection,
    user: user,
    bot: bot
}, document.getElementById("BotChatGoesHere"));
```

Quando o usuário clica em um botão na página da Web, a página da Web posta uma atividade do tipo "evento" para notificar o bot de que o botão recebeu um clique.

```javascript
const postButtonMessage = () => {
    botConnection
        .postActivity({type: "event", value: "", from: {id: "me" }, name: "buttonClicked"})
        .subscribe(id => console.log("success"));
    }
```

> [!TIP]
> Use os atributos `name` e `value` para comunicar todas as informações de que o bot pode precisar para interpretar e/ou responder corretamente ao evento. 

Por fim, a página da Web também escuta um evento específico do bot.
Neste exemplo, a página da Web escuta uma atividade em que type="event" e name="changeBackground". Quando ela recebe esse tipo de atividade, ela altera sua cor da tela de fundo para o `value` especificado pela atividade. 

```javascript
botConnection.activity$
    .filter(activity => activity.type === "event" && activity.name === "changeBackground")
    .subscribe(activity => changeBackgroundColor(activity.value))
```

#### <a name="server-side-code"></a>Código do servidor

O <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">bot de backchannel</a> cria um evento usando uma função auxiliar.

```javascript
var bot = new builder.UniversalBot(connector, 
    function (session) {
        var reply = createEvent("changeBackground", session.message.text, session.message.address);
        session.endDialog(reply);
    }
);

const createEvent = (eventName, value, address) => {
    var msg = new builder.Message().address(address);
    msg.data.type = "event";
    msg.data.name = eventName;
    msg.data.value = value;
    return msg;
}
```

Da mesma forma, o bot também escuta eventos do cliente. Neste exemplo, se o bot receber um evento com `name="buttonClicked"`, ele enviará uma mensagem ao usuário para informar "Vejo que você clicou em um botão".

```javascript
bot.on("event", function (event) {
    var msg = new builder.Message().address(event.address);
    msg.data.textLocale = "en-us";
    if (event.name === "buttonClicked") {
        msg.data.text = "I see that you clicked a button.";
    }
    bot.send(msg);
})
```

## <a name="additional-resources"></a>Recursos adicionais

- [Direct Line API][directLineAPI]
- <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Controle de WebChat do Microsoft Bot Framework</a>
- <a href="https://aka.ms/v3-js-backchannel-sample" target="_blank">Amostra de backchannel</a>
- <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">Bot de backchannel</a>

[directLineAPI]: https://docs.botframework.com/restapi/directline3/#navtitle
