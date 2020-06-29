---
title: Enviar um indicador de digitação – Serviço de Bot
description: Saiba como adicionar um indicador "aguarde" para informar a um usuário que um bot está processando uma solicitação usando o SDK do Bot Framework para Node.js
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 5f4e67818288c5dc1a5fbb78261dea11c8b3a4c5
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790371"
---
# <a name="send-a-typing-indicator"></a>Enviar um indicador de digitação 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Os usuários esperam uma resposta em tempo hábil às suas mensagens. Se o seu bot executar alguma tarefa de longa duração, como chamar um servidor ou executar uma consulta sem dar ao usuário alguma indicação de que o bot ouviu, o usuário pode ficar impaciente e enviar mensagens adicionais ou simplesmente assumir que o bot está quebrado.
Muitos canais suportam o envio de uma indicação de digitação para mostrar ao usuário que a mensagem foi recebida e está sendo processada.


## <a name="typing-indicator-example"></a>Digitar o exemplo do indicador

O exemplo a seguir demonstra como enviar uma indicação de digitação usando [session.sendTyping ()][SendTyping].  Você pode testar isso com o emulador de Bot Framework.


```javascript

// Create bot and default message handler
var bot = new builder.UniversalBot(connector, function (session) {
    session.sendTyping();
    setTimeout(function () {
        session.send("Hello there...");
    }, 3000);
});
```

Os indicadores de digitação também são úteis ao inserir um atraso de mensagem para impedir que mensagens que contenham imagens sejam enviadas fora de ordem.

Para obter mais informações, consulte [como enviar um cartão avançado](bot-builder-nodejs-send-rich-cards.md).


## <a name="additional-resources"></a>Recursos adicionais

* [sendTyping][SendTyping]


[SendTyping]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping
[IMessage]: http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
