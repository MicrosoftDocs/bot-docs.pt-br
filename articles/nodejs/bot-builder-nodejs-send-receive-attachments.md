---
title: Enviar e receber anexos | Microsoft Docs
description: Saiba como enviar e receber mensagens contendo anexos usando o SDK do Bot Framework para Node.js.
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: c830ed61a70b3b92451988ea73f223da3e55bbc7
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299727"
---
# <a name="send-and-receive-attachments"></a>Enviar e receber anexos

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-media-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-receive-attachments.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-media-attachments.md)

Uma troca de mensagens entre usuário e bot pode conter anexos de mídia como imagens, vídeo, áudio e arquivos. Os tipos de anexos que podem ser enviados variam por canal, mas esses são os tipos básicos:

* **Mídia e arquivos**: é possível enviar arquivos como imagens, áudio e vídeo configurando **contentType** para o tipo MIME do [objeto IAttachment][IAttachment] e, em seguida, passando um link para o arquivo em **contentUrl**.
* **Cartões**: Você pode enviar um conjunto avançado de cartões visuais <!-- and custom keyboards --> configurando o **contentType** para o tipo de cartão desejado e, em seguida, passar o JSON para o cartão. Se você utilizar uma das classes do construtor de cartão avançado como **HeroCard**, o anexo será preenchido automaticamente. Consulte [enviar um cartão avançado](bot-builder-nodejs-send-rich-cards.md) para obter um exemplo.

## <a name="add-a-media-attachment"></a>Adicionar um anexo de mídia
O objeto de mensagem deve ser uma instância de [IMessage][IMessage] e será mais útil enviar ao usuário uma mensagem como um objeto quando você quiser incluir um anexo como uma imagem. Use o método [session.send()][SessionSend] para enviar mensagens na forma de um objeto JSON. 

## <a name="example"></a>Exemplo

O exemplo a seguir verifica se o usuário enviou um anexo e, em caso afirmativo, ecoará de volta qualquer imagem contida no anexo. É possível testar isso com o Bot Framework Emulator, enviando uma imagem ao bot.

```javascript
// Create your bot with a function to receive messages from the user
var bot = new builder.UniversalBot(connector, function (session) {
    var msg = session.message;
    if (msg.attachments && msg.attachments.length > 0) {
     // Echo back attachment
     var attachment = msg.attachments[0];
        session.send({
            text: "You sent:",
            attachments: [
                {
                    contentType: attachment.contentType,
                    contentUrl: attachment.contentUrl,
                    name: attachment.name
                }
            ]
        });
    } else {
        // Echo back users text
        session.send("You said: %s", session.message.text);
    }
});
```
## <a name="additional-resources"></a>Recursos adicionais

* [Visualizar recursos com o Inspetor de Canal][inspector]
* [IMessage][IMessage]
* [Enviar um cartão avançado][SendRichCard]
* [session.send][SessionSend]

[IMessage]: http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
[SendRichCard]: bot-builder-nodejs-send-rich-cards.md
[SessionSend]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#send
[IAttachment]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iattachment.html
[inspector]: ../bot-service-channel-inspector.md
