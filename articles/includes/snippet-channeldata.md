---
ms.openlocfilehash: 392eb8bb197105739222c37f695cfd785515b323
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80117841"
---
Alguns canais fornecem recursos que não podem ser implementados apenas com o uso de texto e anexos da mensagem. Para implementar funcionalidades específicas do canal, passe metadados nativos para um canal na propriedade _dados do canal_ do objeto da atividade. Por exemplo, o bot pode usar a propriedade dados do canal para instruir o Telegram a enviar um adesivo ou para instruir o Office 365 a enviar um email.

Este artigo descreve como usar a propriedade dados do canal de uma atividade de mensagem para implementar esta funcionalidade específica do canal:

| Canal  | Funcionalidade                                                                  |
| -------- | ------------------------------------------------------------------------------ |
| Email    | Enviar e receber um email que contém metadados de corpo, assunto e prioridade |
| Margem de atraso    | Enviar mensagens do Slack com fidelidade total                                              |
| Facebook | Enviar notificações do Facebook nativamente                                           |
| Telegram | Executar ações específicas do Telegram, como compartilhar um memorando de voz ou um adesivo   |
| Kik      | Enviar e receber mensagens nativas do Kik                                           |

> [!NOTE]
> O valor da propriedade dados do canal de um objeto de atividade é um objeto JSON.
> Portanto, os exemplos neste artigo mostram o formato esperado da propriedade JSON `channelData` em vários cenários.
> Para criar um objeto JSON usando o .NET, use a classe `JObject` (.NET).

## <a name="create-a-custom-email-message"></a>Criar uma mensagem de Email personalizada

Para criar uma mensagem de email, defina a propriedade dados do canal do objeto de atividade como um objeto JSON que contém essas propriedades:

| Propriedade      | Descrição                                                                                                                                                  |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| bccRecipients | Uma cadeia de caracteres delimitada por ponto e vírgula (;) de endereços de email a ser adicionada ao campo Cco (cópia oculta) da mensagem.                                                   |
| ccRecipients  | Uma cadeia de caracteres delimitada por ponto e vírgula (;) de endereços de email a ser adicionada ao campo Cc (cópia carbono) da mensagem.                                                          |
| htmlBody      | Um documento HTML que especifica o corpo da mensagem de email. Consulte a documentação do canal para obter informações sobre atributos e elementos HTML com suporte. |
| importance    | O nível de importância do email. Os valores válidos são **alta**, **normal**, e **baixa**. O valor padrão é **normal**.                                           |
| subject       | O assunto do email. Consulte a documentação do canal para obter informações sobre requisitos de campo.                                                               |
| toRecipients  | Uma cadeia de caracteres delimitada por ponto e vírgula (;) de endereços de email a ser adicionada ao campo Para da mensagem.                                                                        |

> [!NOTE]
> As mensagens que seu bot recebe de usuários por meio do canal de email podem conter uma propriedade de dados do canal que é preenchida com um objeto JSON como aquele descrito acima.

Este snippet mostra um exemplo da propriedade `channelData` para uma mensagem de email personalizada.

```json
"channelData": {
    "type": "message",
    "locale": "en-Us",
    "channelID": "email",
    "from": { "id": "mybot@mydomain.com", "name": "My bot"},
    "recipient": { "id": "joe@otherdomain.com", "name": "Joe Doe"},
    "conversation": { "id": "123123123123", "topic": "awesome chat" },
    "channelData":
    {
        "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
        "subject": "Super awesome message subject",
        "importance": "high",
        "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
    }
}
```

## <a name="create-a-full-fidelity-slack-message"></a>Criar uma mensagem do Slack com fidelidade total

Para criar uma mensagem do Slack com fidelidade total, defina a propriedade dados do canal do objeto de atividade como um objeto JSON que especifica <a href="https://api.slack.com/docs/messages" target="_blank">mensagens do Slack</a>, <a href="https://api.slack.com/docs/message-attachments" target="_blank">anexos do Slack</a> e/ou <a href="https://api.slack.com/docs/message-buttons" target="_blank">botões do Slack</a>.

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

Quando um usuário clicar em um botão em uma mensagem do Slack, o bot receberá uma mensagem de resposta na qual a propriedade dados do canal é populada com um objeto JSON `payload`.
O objeto `payload` especifica o conteúdo da mensagem original, identifica o botão que recebeu o clique e identifica o usuário que clicou no botão.

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

O bot pode responder a essa mensagem da maneira normal ou postar sua resposta diretamente no ponto de extremidade especificado pela propriedade `payload` do objeto de `response_url`.
Para obter informações sobre quando e como publicar uma resposta para a `response_url`, confira <a href="https://api.slack.com/docs/message-buttons" target="_blank">Botões do Slack</a>.

Você pode criar botões dinâmicos usando o seguinte JSON.

```json
{
    "text": "Would you like to play a game ? ",
    "attachments": [
        {
            "text": "Choose a game to play!",
            "fallback": "You are unable to choose a game",
            "callback_id": "wopr_game",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "game",
                    "text": "Chess",
                    "type": "button",
                    "value": "chess"
                },
                {
                    "name": "game",
                    "text": "Falken's Maze",
                    "type": "button",
                    "value": "maze"
                },
                {
                    "name": "game",
                    "text": "Thermonuclear War",
                    "style": "danger",
                    "type": "button",
                    "value": "war",
                    "confirm": {
                        "title": "Are you sure?",
                        "text": "Wouldn't you prefer a good game of chess?",
                        "ok_text": "Yes",
                        "dismiss_text": "No"
                    }
                }
            ]
        }
    ]
}
```

Para criar menus interativos, use o seguinte JSON:

```json
{
    "text": "Would you like to play a game ? ",
    "response_type": "in_channel",
    "attachments": [
        {
            "text": "Choose a game to play",
            "fallback": "If you could read this message, you'd be choosing something fun to do right now.",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "callback_id": "game_selection",
            "actions": [
                {
                    "name": "games_list",
                    "text": "Pick a game...",
                    "type": "select",
                    "options": [
                        {
                            "text": "Hearts",
                            "value": "menu_id_hearts"
                        },
                        {
                            "text": "Bridge",
                            "value": "menu_id_bridge"
                        },
                        {
                            "text": "Checkers",
                            "value": "menu_id_checkers"
                        },
                        {
                            "text": "Chess",
                            "value": "menu_id_chess"
                        },
                        {
                            "text": "Poker",
                            "value": "menu_id_poker"
                        },
                        {
                            "text": "Falken's Maze",
                            "value": "menu_id_maze"
                        },
                        {
                            "text": "Global Thermonuclear War",
                            "value": "menu_id_war"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="create-a-facebook-notification"></a>Criar uma notificação do Facebook

Para criar uma notificação do Facebook, defina a propriedade dados do canal do objeto de atividade em um objeto JSON que especifique estas propriedades:

| Propriedade          | Descrição                                                                                                          |
| ----------------- | -------------------------------------------------------------------------------------------------------------------- |
| notification_type | O tipo de notificação (por exemplo, **REGULAR**, **SILENT_PUSH**, **NO_PUSH**).                                          |
| attachment        | Um anexo que especifica uma imagem, um vídeo ou outro tipo de multimídia ou um anexo modelo como um recibo. |

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

Para criar uma mensagem que implementa ações específicas do Telegram, como compartilhar um memorando de voz ou um adesivo, defina a propriedade dados do canal do objeto de atividade em um objeto JSON que especifique estas propriedades: 

| Propriedade   | Descrição                             |
| ---------- | --------------------------------------- |
| method     | O método de API do Bot do Telegram a ser chamado.    |
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
> <li>Em cada mensagem que o bot recebe do canal do Telegram, a propriedade <code>ChannelData</code> incluirá a mensagem que o bot enviou anteriormente.</li></ul>

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

Quando um método do Telegram for implementado, seu bot receberá uma mensagem de resposta na qual a propriedade de dados do canal é populada com um objeto JSON. Esse objeto de resposta especifica o conteúdo da mensagem original, incluindo um `update_id` e, no máximo, um parâmetro opcional. Para obter informações sobre como receber respostas de entrada, confira <a href="https://core.telegram.org/bots/api#getting-updates" target="_blank">Receber atualizações</a>.

Este snippet mostra um exemplo da propriedade `channelData` na mensagem recebida por um bot quando uma sondagem é criada.

```json
"channelData": {
    "update_id": 43517575,
    "message": {
        "message_id": 618,
        "from": {
            "id": 803613355,
            "is_bot": false,
            "first_name": "Joe",
            "last_name": "Doe",
            "username": "jdoe",
            "language_code": "en"
        },
        "chat": {
            "id": 803613355,
            "first_name": "Joe",
            "last_name": "Doe",
            "username": "jdoe",
            "type": "private"
        },
        "date": 1582577834,
        "poll": {
        "id": "5089525250643722242",
        "question": "How to win?",
        "options": [
            {
                "text": "Be the best",
                "voter_count": 0
            },
            {
                "text": "Help those in need",
                "voter_count": 0
            },
            {
                "text": "All of the above",
                "voter_count": 0
            }
        ],
        "total_voter_count": 0,
        "is_closed": false,
        "is_anonymous": true,
        "type": "regular",
        "allows_multiple_answers": false
        }
    }
}
```

## <a name="create-a-native-kik-message"></a>Criar uma mensagem nativa do Kik

Para criar uma mensagem nativa do Kik, defina a propriedade dados do canal do objeto de atividade como um objeto JSON que especifique esta propriedade:

| Propriedade | Descrição                                                                                                                                                                 |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
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

## <a name="create-a-line-message"></a>Criar uma mensagem do LINE

Para criar uma mensagem que implementa os tipos de mensagem específicos para LINE (por exemplo, adesivos, modelos ou tipos de ação específica para LINE como abrir a câmera do telefone), defina a propriedade de dados de canal do objeto de atividade como um objeto JSON que especifica os tipos de ação e de mensagem do LINE. 

| Propriedade | Descrição                       |
| -------- | --------------------------------- |
| type     | O nome do tipo de ação ou mensagem do LINE |

Há suporte para esses tipos de mensagem do LINE:
* Adesivo
* Imagemap 
* Modelo (botão, confirmar, carrossel) 
* Flex 

Essas ações do LINE podem ser especificadas no campo de ação do objeto de tipo de mensagem JSON: 
* Postback 
* Mensagem 
* URI 
* Datetimerpicker 
* Câmera 
* Rolo da câmera 
* Location 

Para obter detalhes sobre esses métodos do LINE e seus parâmetros, confira a [documentação da API do Bot do LINE](https://developers.line.biz/en/docs/messaging-api/). 

Este snippet mostra um exemplo de uma propriedade `channelData` que especifica um tipo de mensagem de canal `ButtonTemplate` e 3 tipos de ação: câmera, rolo da câmera, Datetimepicker. 

```json
"channelData": { 
    "type": "ButtonsTemplate", 
    "altText": "This is a buttons template", 
    "template": { 
        "type": "buttons", 
        "thumbnailImageUrl": "https://example.com/bot/images/image.jpg", 
        "imageAspectRatio": "rectangle", 
        "imageSize": "cover", 
        "imageBackgroundColor": "#FFFFFF", 
        "title": "Menu", 
        "text": "Please select", 
        "defaultAction": { 
            "type": "uri", 
            "label": "View detail", 
            "uri": "http://example.com/page/123" 
        }, 
        "actions": [{ 
                "type": "cameraRoll", 
                "label": "Camera roll" 
            }, 
            { 
                "type": "camera", 
                "label": "Camera" 
            }, 
            { 
                "type": "datetimepicker", 
                "label": "Select date", 
                "data": "storeId=12345", 
                "mode": "datetime", 
                "initial": "2017-12-25t00:00", 
                "max": "2018-01-24t23:59", 
                "min": "2017-12-25t00:00" 
            } 
        ] 
    } 
}
```

## <a name="adding-a-bot-to-teams"></a>Adicionar um bot ao Teams

Os bots adicionados a uma equipe se tornam outro membro da equipe, que pode ser `@mentioned` como parte da conversa. Na verdade, os bots só recebem mensagens quando são `@mentioned`; portanto, outras conversas no canal não são enviadas para o bot.
Para saber mais, confira [Conversas de chat de canal e grupo com um bot do Microsoft Teams](https://aka.ms/bots-con-channel).

Como os bots em um grupo ou canal respondem apenas quando são mencionados (`@botname`) em uma mensagem, cada mensagem recebida por um bot em um canal de grupo contém seu próprio nome, e você deve verificar se a análise de mensagens manipula isso. Além disso, os bots podem analisar outros usuários mencionados e mencionar os usuários como parte de suas mensagens.

### <a name="check-for-and-strip-bot-mention"></a>Verificar e remover a menção @bot

```csharp

Mention[] m = sourceMessage.GetMentions();
var messageText = sourceMessage.Text;

for (int i = 0;i < m.Length;i++)
{
    if (m[i].Mentioned.Id == sourceMessage.Recipient.Id)
    {
        //Bot is in the @mention list.
        //The below example will strip the bot name out of the message, so you can parse it as if it wasn't included. Note that the Text object will contain the full bot name, if applicable.
        if (m[i].Text != null)
            messageText = messageText.Replace(m[i].Text, "");
    }
}
```

```javascript
var text = message.text;
if (message.entities) {
    message.entities
        .filter(entity => ((entity.type === "mention") && (entity.mentioned.id.toLowerCase() === botId)))
        .forEach(entity => {
            text = text.replace(entity.text, "");
        });
    text = text.trim();
}

```

> [!IMPORTANT] 
> A adição de um bot por GUID, para qualquer outra coisa que não seja para fins de teste, não é recomendável. Fazer isso limita severamente a funcionalidade de um bot. Os bots em produção devem ser adicionados ao Teams como parte de um aplicativo. Confira [Criar um bot](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-create) e [Testar e depurar o bot](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-test) do Microsoft Teams.


## <a name="additional-resources"></a>Recursos adicionais

- [Entidades e tipos de atividades](../bot-service-activities-entities.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)
