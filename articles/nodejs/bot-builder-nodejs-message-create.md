---
title: Criar mensagens – Serviço de Bot
description: Saiba como criar mensagens com o SDK do Bot Framework para Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/7/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 1837b302b8d0b1d05e16d4d979b5807fa3e2d268
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790841"
---
# <a name="create-messages"></a>Criar mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

A comunicação entre o bot e o usuário é por meio de mensagens. Seu bot enviará atividades de mensagens para comunicar informações aos usuários e, da mesma forma, também receberá atividades de mensagens dos usuários. Algumas mensagens podem simplesmente consistir em texto sem formatação, enquanto outras podem conter conteúdo mais elaborado, como texto a ser falado, ações sugeridas, anexos de mídia, cartões avançados e dados específicos do canal.

Este artigo descreve alguns dos métodos de mensagem usados, que você pode usar para aprimorar sua experiência de usuário.

## <a name="default-message-handler"></a>Manipulador de mensagens padrão

O SDK do Bot Framework para Node.js vem com um manipulador de mensagens padrão incorporado ao objeto [`session`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html). Este manipulador de mensagens permite que você envie e receba mensagens de texto entre o bot e o usuário.

### <a name="send-a-text-message"></a>Enviar uma mensagem de texto

Enviar uma mensagem de texto usando o manipulador de mensagem padrão é simple, basta chamar `session.send` e passar uma **cadeia de caracteres**.

Este exemplo mostra como você pode enviar uma mensagem de texto para saudar o usuário.
```javascript
session.send("Good morning.");
```

Este exemplo mostra como você pode enviar uma mensagem de texto usando o modelo de cadeia de caracteres de JavaScript.
```javascript
var msg = `You ordered: ${order.Description} for a total of $${order.Price}.`;
session.send(msg); //msg: "You ordered: Potato Salad for a total of $5.99."
```

### <a name="receive-a-text-message"></a>Receber uma mensagem de texto

Quando um usuário envia uma mensagem de bot, o bot recebe a mensagem por meio da propriedade `session.message`.

Este exemplo mostra como acessar a mensagem do usuário.
```javascript
var userMessage = session.message.text;
```

## <a name="customizing-a-message"></a>Personalizar uma mensagem

Para ter mais controle sobre a formatação de texto das mensagens, você pode criar um objeto [`message`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html) personalizado e definir as propriedades necessárias antes de enviá-las ao usuário.

Este exemplo mostra como criar um objeto `message` personalizado e definir as propriedades `text`, `textFormat` e `textLocale`.

```javascript
var customMessage = new builder.Message(session)
    .text("Hello!")
    .textFormat("plain")
    .textLocale("en-us");
session.send(customMessage);
```

Em casos em que você não tenha o objeto `session` no escopo, você pode usar o método `bot.send` para enviar uma mensagem formatada para o usuário.

A propriedade `textFormat` de uma mensagem pode ser usada para especificar o formato do texto. A propriedade `textFormat` pode ser definida como **plain**, **markdown** ou **xml**. O valor padrão para `textFormat` é **markdown**. 

## <a name="message-property"></a>Propriedade de mensagem

O objeto [`Message`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html) tem propriedade de **dados** internos que ele usa para gerenciar a mensagem que é enviada. Outras propriedades que você definir são por meio dos métodos diferentes que esse objeto expõe para você. 

## <a name="message-methods"></a>Métodos de mensagem

Propriedades de mensagem são definidas e recuperadas por meio de métodos do objeto. A tabela a seguir fornece uma lista dos métodos que você pode chamar para definir/obter diferentes propriedades de **mensagem**.

| Método | Descrição |
| ---- | ---- | 
| [`addAttachment(attachment:AttachmentType)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#addattachment) | Adicionar um anexo a uma mensagem|
| [`addEntity(obj:Object)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#addentity) | Adiciona uma entidade para a mensagem. |
| [`address(adr:IAddress)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#address) | Informações de roteamento de endereço para a mensagem. Para enviar uma mensagem proativa a um usuário, salve o endereço da mensagem no recipiente de dados do usuário. |
| [`attachmentLayout(style:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#attachmentlayout) | Dica para como os clientes devem organizar vários anexos. O valor padrão é ‘list’. |
| [`attachments(list:AttachmentType)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#attachments) | Uma lista de cartões ou imagens para enviar ao usuário. |
| [`compose(prompts:string[], ...args:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#compose) | Compõe uma resposta aleatória e complexa para o usuário. |
| [`entities(list:Object[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#entities) | Objetos estruturados passados para o usuário ou o bot. |
| [`inputHint(hint:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#inputhint) | Dica enviada para o usuário informando se o bot está esperando a entrada adicional ou não. Os prompts internos popularão automaticamente esse valor para mensagens de saída. |
| [`localTimeStamp((optional)time:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#localtimestamp) | Hora local quando a mensagem foi enviada (definida pelo cliente ou pelo bot, por exemplo: 2016-09-23T13:07:49.4714686-07:00.) |
| [`originalEvent(event:any)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#originalevent) | Mensagem no formato original/nativo do canal para mensagens de entrada. |
| [`sourceEvent(map:ISourceEventMap)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#sourceevent) | Mensagens de saída podem ser usadas para passar dados de evento específico de fonte como anexos personalizados. |
| [`speak(ssml:TextType, ...args:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#speak) | Define o campo de fala da mensagem como *Speech Synthesis Markup Language (SSML)* . Isso será dito ao usuário em dispositivos com suporte. |
| [`suggestedActions(suggestions:ISuggestedActions `&#124;` IIsSuggestedActions)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#suggestedactions) | Ações sugeridas opcionais para enviar ao usuário. Ações sugeridas serão exibidas somente nos canais que dão suporte a ações sugeridas. |
| [`summary(text:TextType, ...argus:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#summary) | Texto a ser exibido como de retorno e breve descrição do conteúdo da mensagem em (por exemplo: lista de conversas recentes.) |
| [`text(text:TextType, ...args:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#text) | Define o texto da mensagem. |
| [`textFormat(style:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#textformat) | Detecta formato de texto. O formato padrão é **markdown**. |
| [`textLocale(locale:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#textlocale) | Define o idioma de destino da mensagem. |
| [`toMessage()`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#tomessage) | Obtém o JSON para a mensagem. |
| [`composePrompt(session:Session, prompts:string[], args?:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#composeprompt-1) | Combina uma matriz de prompts em um único prompt localizado e, opcionalmente, preenche os slots de modelo de prompts com os argumentos passados. |
| [`randomPrompt(prompts:TextType)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#randomprompt) | Obtém um prompt aleatório da matriz de **prompts* que é passada. |

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Enviar e receber anexos](bot-builder-nodejs-send-receive-attachments.md)

