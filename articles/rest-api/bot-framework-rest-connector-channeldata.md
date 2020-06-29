---
title: Implemente a funcionalidade específica do canal usando a API REST – Serviço de Bot
description: Saiba como implementar a funcionalidade específica do canal usando a API do Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 4716ff913940b09fa24f199492de9e19cf009b71
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790006"
---
# <a name="implement-channel-specific-functionality"></a>Implementar a funcionalidade específica do canal

Alguns canais fornecem recursos que não podem ser implementados apenas com o uso de [texto e anexos da mensagem](bot-framework-rest-connector-create-messages.md). Para implementar funcionalidades específicas do canal, passe metadados nativos para um canal na propriedade `channelData` do objeto [Activity[]]. Por exemplo, o bot pode usar a propriedade `channelData` para instruir o Telegram a enviar um adesivo ou para instruir o Office 365 a enviar um email.

Este artigo descreve como usar a propriedade `channelData` de uma atividade de mensagem para implementar esta funcionalidade específica do canal:

| Canal | Funcionalidade |
|----|----|
| Email | Enviar e receber um email que contém metadados de corpo, assunto e prioridade |
| Margem de atraso | Enviar mensagens do Slack com fidelidade total |
| Facebook | Enviar notificações do Facebook nativamente |
| Telegram | Executar ações específicas do Telegram, como compartilhar um memorando de voz ou um adesivo |
| Kik | Enviar e receber mensagens nativas do Kik | 

> [!NOTE]
> O valor da propriedade de um `Activity` objeto `channelData` é um objeto JSON. A estrutura do objeto JSON varia de acordo com o canal e a funcionalidade que está sendo implementada, conforme descrito abaixo. 

## <a name="create-a-custom-email-message"></a>Criar uma mensagem de Email personalizada

Para criar uma mensagem de email, defina a propriedade do `Activity` objeto `channelData` como um objeto JSON que contém essas propriedades:

[!INCLUDE [Email channelData table](~/includes/snippet-channelData-email.md)]

Este snippet mostra um exemplo da propriedade `channelData` para uma mensagem de email personalizada.

```json
"channelData":
{
    "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
    "subject": "Super awesome message subject",
    "importance": "high",
    "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
}
```

## <a name="create-a-full-fidelity-slack-message"></a>Criar uma mensagem do Slack com fidelidade total

Para criar uma mensagem do Slack com fidelidade total, defina a propriedade `Activity` do objeto `channelData` como um objeto JSON que especifica <a href="https://api.slack.com/docs/messages" target="_blank">mensagens do Slack</a>, <a href="https://api.slack.com/docs/message-attachments" target="_blank">anexos do Slack</a> e/ou <a href="https://api.slack.com/docs/message-buttons" target="_blank">botões do Slack</a>. 

> [!NOTE]
> Para dar suporte a botões em mensagens do Slack, habilite a opção **Mensagens Interativas** ao [conectar o bot](../bot-service-manage-channels.md) ao canal do Slack.

Este snippet mostra um exemplo da propriedade `channelData` para uma mensagem personalizada do Slack.

```json
"channelData": {
   "text": "Now back in stock! :tada:",
   "attachments": [
        {
            "title": "The Further Adventures of Slackbot",
            "author_name": "Stanford S. Strickland",
            "author_icon": "https://api.slack.com/img/api/homepage_custom_integrations-2x.png",
            "image_url": "http://i.imgur.com/OJkaVOI.jpg?1"
        },
        {
            "fields": [
                {
                    "title": "Volume",
                    "value": "1",
                    "short": true
                },
                {
                    "title": "Issue",
                    "value": "3",
                    "short": true
                }
            ]
        },
        {
            "title": "Synopsis",
            "text": "After @episod pushed exciting changes to a devious new branch back in Issue 1, Slackbot notifies @don about an unexpected deploy..."
        },
        {
            "fallback": "Would you recommend it to customers?",
            "title": "Would you recommend it to customers?",
            "callback_id": "comic_1234_xyz",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "recommend",
                    "text": "Recommend",
                    "type": "button",
                    "value": "recommend"
                },
                {
                    "name": "no",
                    "text": "No",
                    "type": "button",
                    "value": "bad"
                }
            ]
        }
    ]
}
```

Quando um usuário clicar em um botão em uma mensagem do Slack, o bot receberá uma mensagem de resposta na qual a propriedade `channelData` é populada com um objeto JSON `payload`. O objeto `payload` especifica o conteúdo da mensagem original, identifica o botão que recebeu o clique e identifica o usuário que clicou no botão. 

Este snippet mostra um exemplo da propriedade `channelData` na mensagem recebida por um bot quando um usuário clica em um botão na mensagem do Slack.

```json
"channelData": {
    "payload": {
        "actions": [
            {
                "name": "recommend",
                "value": "yes"
            }
        ],
        . . .
        "original_message": "{…}",
        "response_url": "https://hooks.slack.com/actions/..."
    }
}
```

O bot pode responder a essa mensagem da [maneira normal](bot-framework-rest-connector-send-and-receive-messages.md#create-reply) ou postar sua resposta diretamente no ponto de extremidade especificado pela propriedade `payload` do objeto `response_url`. Para obter informações sobre quando e como publicar uma resposta para a `response_url`, confira <a href="https://api.slack.com/docs/message-buttons" target="_blank">Botões do Slack</a>. 

## <a name="create-a-facebook-notification"></a>Criar uma notificação do Facebook

Para criar uma notificação do Facebook, defina a propriedade `Activity` do objeto `channelData` em um objeto JSON que especifique estas propriedades: 

| Propriedade | Descrição |
|----|----|
| notification_type | O tipo de notificação (por exemplo, **REGULAR**, **SILENT_PUSH**, **NO_PUSH**).
| attachment | Um anexo que especifica uma imagem, um vídeo ou outro tipo de multimídia ou um anexo modelo como um recibo. |

> [!NOTE]
> Para obter detalhes sobre o formato e o conteúdo das propriedades `notification_type` e `attachment`, confira a <a href="https://developers.facebook.com/docs/messenger-platform/send-api-reference#guidelines" target="_blank">documentação da API do Facebook</a>. 

Este snippet mostra um exemplo da propriedade `channelData` para um anexo de recibo do Facebook.

```json
"channelData": {
    "notification_type": "NO_PUSH",
    "attachment": {
        "type": "template"
        "payload": {
            "template_type": "receipt",
            . . .
        }
    }
}
```

## <a name="create-a-telegram-message"></a>Criar uma mensagem do Telegram

Para criar uma mensagem que implementa ações específicas do Telegram, como compartilhar um memorando de voz ou um adesivo, defina a propriedade `Activity` do objeto `channelData` em um objeto JSON que especifique estas propriedades: 

| Propriedade | Descrição |
|----|----|
| method | O método de API do Bot do Telegram a ser chamado. |
| parâmetros | Os parâmetros do método especificado. |

Há suporte para estes métodos do Telegram: 

- answerInlineQuery
- editMessageCaption
- editMessageReplyMarkup
- editMessageText
- forwardMessage
- kickChatMember
- sendAudio
- sendChatAction
- sendContact
- sendDocument
- sendLocation
- sendMessage
- sendPhoto
- sendSticker
- sendVenue
- sendVideo
- sendVoice
- unbanChateMember

Para obter detalhes sobre esses métodos do Telegram e seus parâmetros, confira a <a href="https://core.telegram.org/bots/api#available-methods" target="_blank">documentação da API do Bot do Telegram</a>.

> [!NOTE]
> <ul><li>O parâmetro <code>chat_id</code> é comum a todos os métodos do Telegram. Se você não especificar <code>chat_id</code> como parâmetro, a estrutura fornecerá a ID para você.</li>
> <li>Em vez de passar o conteúdo do arquivo embutido, especifique o arquivo usando uma URL e um tipo de mídia, conforme mostrado no exemplo abaixo.</li>
> <li>Em cada mensagem que o bot recebe do canal do Telegram, a propriedade <code>channelData</code> incluirá a mensagem que o bot enviou anteriormente.</li></ul>

Este snippet mostra um exemplo de uma propriedade `channelData` que especifica um único método do Telegram.

```json
"channelData": {
    "method": "sendSticker",
    "parameters": {
        "sticker": {
            "url": "https://domain.com/path/gif",
            "mediaType": "image/gif",
        }
    }
}
```

Este snippet mostra um exemplo de uma propriedade `channelData` que especifica uma matriz de métodos do Telegram.

```json
"channelData": [
    {
        "method": "sendSticker",
        "parameters": {
            "sticker": {
                "url": "https://domain.com/path/gif",
                "mediaType": "image/gif",
            }
        }
    },
    {
        "method": "sendMessage",
        "parameters": {
            "text": "<b>This message is HTML formatted.</b>",
            "parse_mode": "HTML"
        }
    }
]
```

## <a name="create-a-native-kik-message"></a>Criar uma mensagem nativa do Kik

Para criar uma mensagem nativa do Kik, defina a propriedade `Activity` do objeto `channelData` como um objeto JSON que especifique esta propriedade: 

| Propriedade | Descrição |
|----|----|
| da nuvem para o dispositivo | Uma matriz de mensagens do Kik. Para obter detalhes sobre o formato de mensagem do Kik, confira <a href="https://dev.kik.com/#/docs/messaging#message-formats" target="_blank">Formatos de mensagem do Kik</a>. |

Este snippet mostra um exemplo da propriedade `channelData` para uma mensagem nativa do Kik.

```json
"channelData": {
    "messages": [
        {
            "chatId": "c6dd8165…",
            "type": "link",
            "to": "kikhandle",
            "title": "My Webpage",
            "text": "Some text to display",
            "url": "http://botframework.com",
            "picUrl": "http://lorempixel.com/400/200/",
            "attribution": {
                "name": "My App",
                "iconUrl": "http://lorempixel.com/50/50/"
            },
            "noForward": true,
            "kikJsData": {
                    "key": "value"
                }
        }
    ]
}
```

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)
- [Referência de canais](../bot-service-channels-reference.md)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
