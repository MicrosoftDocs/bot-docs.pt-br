---
title: Interceptar mensagens | Microsoft Docs
description: Saiba como criar logs ou outros registros interceptando e processando as trocas de informações usando o SDK do Bot Builder para Node.js.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 9160e81f9086f88f808b41e8eb01745776e0fc9f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296915"
---
# <a name="intercept-messages"></a>Interceptar mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-middleware.md)
> - [Node.js](../nodejs/bot-builder-nodejs-intercept-messages.md)

[!INCLUDE [Introduction to message logging](../includes/snippet-message-logging-intro.md)]

## <a name="example"></a>Exemplo

O exemplo de código a seguir mostra como interceptar mensagens trocadas entre o usuário e o bot usando o conceito de **middleware** no SDK do Bot Builder para Node.js. 

Primeiro, configure o manipulador de mensagens de entrada (`botbuilder`) e de mensagens de saída (`send`).

```javascript
server.post('/api/messages', connector.listen());
var bot = new builder.UniversalBot(connector);
bot.use({
    botbuilder: function (session, next) {
        myMiddleware.logIncomingMessage(session, next);
    },
    send: function (event, next) {
        myMiddleware.logOutgoingMessage(event, next);
    }
})
```

Em seguida, implemente cada um dos manipuladores para definir a ação a ser tomada para cada mensagem interceptada.

```javascript
module.exports = {
    logIncomingMessage: function (session, next) {
        console.log(session.message.text);
        next();
    },
    logOutgoingMessage: function (event, next) {
        console.log(event.text);
        next();
    }
}
```

Agora, cada mensagem de entrada (do usuário para o bot) vai disparar `logIncomingMessage`, e cada mensagem de saída (do bot para o usuário) vai disparar `logOutgoingMessage`.
Neste exemplo, o bot simplesmente imprime algumas informações sobre cada mensagem, mas você pode atualizar `logIncomingMessage` e `logOutgoingMessage` conforme necessário para definir as ações que deseja executar para cada mensagem. 

## <a name="sample-code"></a>Exemplo de código

Para obter um exemplo completo que mostre como interceptar e registrar em log mensagens usando o SDK do Bot Builder para Node.js, veja o <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/capability-middlewareLogging" target="_blank">exemplo de registro em log e middleware</a> no GitHub.
