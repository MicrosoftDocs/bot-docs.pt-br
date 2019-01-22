---
title: Enviar um indicador de digitação | Microsoft Docs
description: Saiba como adicionar um indicador "aguarde" para informar a um usuário que um bot está processando uma solicitação usando o SDK do Bot Framework para Node.js
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 3852c0b25ea385301be11edd0a46ed5984510820
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224861"
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


[SendTyping]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping
[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
