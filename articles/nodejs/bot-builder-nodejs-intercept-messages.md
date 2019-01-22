---
title: Interceptar mensagens | Microsoft Docs
description: Saiba como criar logs ou outros registros interceptando e processando as trocas de informações usando o SDK do Bot Framework para Node.js.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/02/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b262e936cd48bb73d7b5aa3fa4f7b6318ea7c2a0
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225621"
---
# <a name="intercept-messages"></a>Interceptar mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-middleware.md)
> - [Node.js](../nodejs/bot-builder-nodejs-intercept-messages.md)

[!INCLUDE [Introduction to message logging](../includes/snippet-message-logging-intro.md)]

## <a name="example"></a>Exemplo

O exemplo de código a seguir mostra como interceptar mensagens trocadas entre o usuário e o bot usando o conceito de **middleware** no SDK do Bot Framework para Node.js. 

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

Veja um exemplo completo que mostra como interceptar e registrar em log mensagens usando o SDK do Bot Framework para Node.js no <a href="https://aka.ms/v3-js-capability-middlewareLogging" target="_blank">exemplo de registro em log e middleware</a> no GitHub.
