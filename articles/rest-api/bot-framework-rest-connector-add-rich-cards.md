---
title: Adicionar anexos de cartão avançado a mensagens – Serviço de Bot
description: Saiba como adicionar cartões avançados a mensagens usando o serviço Conector de Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: e51d4bcc7059e1130932ca6a8b956dd3b097ef8c
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "76752898"
---
# <a name="add-rich-card-attachments-to-messages"></a>Adicionar anexos de cartão avançados às mensagens

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Bots e canais normalmente trocam cadeias de caracteres de texto, mas alguns canais também oferecem suporte a troca de anexos, o que permite que seu bot envie mensagens mais ricas para os usuários. Por exemplo, seu bot pode enviar anexos de cartões avançados e de mídia (por exemplo, imagens, vídeos, áudio, arquivos). Este artigo descreve como adicionar anexos de cartão avançado às mensagens usando o serviço Conector de Bot.

> [!NOTE]
> Para obter informações sobre como adicionar anexos de mídia às mensagens, consulte [Adicionar anexos de mídia às mensagens](bot-framework-rest-connector-add-media-attachments.md).

## <a name="types-of-rich-cards"></a>Tipos de cartões avançados

Um cartão avançado é formado por um título, descrição, link e imagens.
Uma mensagem pode conter vários cartões avançados, exibidos em formato de carrossel ou de lista.
Atualmente, o Bot Framework dá suporte a oito tipos de cartões avançados:

| Tipo de cartão | Descrição |
|----|----|
| [AdaptiveCard](/adaptive-cards/get-started/bots) | Um cartão personalizável que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. Confira [suporte por canal](/adaptive-cards/get-started/bots#channel-status). |
| [AnimationCard][] | Um cartão que pode reproduzir GIFs animados ou vídeos curtos. |
| [AudioCard][] | Um cartão que pode reproduzir um arquivo de áudio. |
| [HeroCard][] | Um cartão que geralmente contém uma única imagem grande, um ou mais botões e um texto. |
| [ThumbnailCard][] | Um cartão que geralmente contém uma única imagem em miniatura, um ou mais botões e um texto. |
| [ReceiptCard][] | Um cartão que permite a um bot fornecer um recibo para o usuário. Normalmente, contém a lista de itens a serem incluídos no recibo, informações fiscais e de totais e outros textos. |
| [SignInCard][] | Um cartão que permite a um bot solicitar a entrada do usuário. Normalmente, contém um texto e um ou mais botões nos quais o usuário pode clicar para iniciar o processo de entrada. |
| [VideoCard][] | Um cartão que pode reproduzir vídeos. |

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="process-events-within-rich-cards"></a>Processar eventos em cartões avançados

Para processar eventos em cartões avançados, use objetos [CardAction][] para especificar o que deve acontecer quando o usuário clica em um botão ou toca em uma seção do cartão. Cada objeto `CardAction` contém estas propriedades:

| Propriedade | Type | Descrição |
|----|----|----|
| channelData | string | dados específicos do canal associados a esta ação |
| displayText | string | texto a ser exibido no feed do chat se o botão receber um clique |
| text | string | texto da ação |
| type | string | tipo de ação (um dos valores especificados na tabela a seguir) |
| título | string | título do botão |
| image | string | URL da imagem do botão |
| value | string | valor necessário para executar o tipo de ação especificado |

> [!NOTE]
> Botões dentro de Cartões Adaptáveis não são criados usando objetos `CardAction`, mas usando o esquema definido pelos Cartões Adaptáveis.
> Consulte [Adicionar um Cartão Adaptável a uma mensagem](#add-an-adaptive-card-to-a-message) para obter um exemplo que mostra como adicionar botões a um Cartão Adaptável.

Esta tabela lista os valores válidos para a propriedade `type` de um objeto `CardAction` e descreve o conteúdo esperado da propriedade `value` para cada tipo:

| type | value |
|----|----|
| openUrl | URL a ser aberta no navegador interno |
| imBack | Texto da mensagem para enviar ao bot (do usuário que clicou no botão ou tocou no cartão). Essa mensagem (do usuário ao bot) ficará visível a todos os participantes da conversa por meio do aplicativo cliente que hospeda a conversa. |
| postBack | Texto da mensagem para enviar ao bot (do usuário que clicou no botão ou tocou no cartão). Alguns aplicativos cliente podem exibir esse texto no feed de mensagem, onde ficará visível a todos os participantes da conversa. |
| chamada | Destino de uma chamada telefônica neste formato: **tel:123123123123** |
| playAudio | URL do áudio para reprodução |
| playVideo | URL do vídeo para reprodução |
| showImage | URL da imagem a ser exibida |
| downloadFile | URL do arquivo para download |
| signin | URL do fluxo do OAuth a ser iniciado |

## <a name="add-a-hero-card-to-a-message"></a>Adicionar um cartão Hero a uma mensagem

Para adicionar um anexo de cartão avançado a uma mensagem, primeiro crie um objeto que corresponda ao [tipo do cartão](#types-of-rich-cards) que você deseja adicionar à mensagem. Em seguida, crie um objeto [Anexo][], defina sua propriedade `contentType` como o tipo de mídia do cartão e sua propriedade `content` ao objeto que você criou para representar o cartão. Especifique seu objeto `Attachment` dentro da matriz `attachments` da mensagem.

> [!TIP]
> As mensagens que contêm de cartão avançado normalmente não especificam `text`.

Alguns canais permitem que você adicione vários cartões avançados à matriz `attachments` dentro de uma mensagem. Esse recurso pode ser útil em situações nas quais você deseja apresentar várias opções ao usuário. Por exemplo, se o seu bot permitir que os usuários reservem quartos de hotel, ele poderia apresentar uma lista de cartões avançados com os tipos de quartos disponíveis. Cada cartão pode conter uma imagem e uma lista de comodidades correspondentes ao tipo de quarto, e o usuário pode selecionar um tipo de quarto tocando um cartão ou clicando em um botão.

> [!TIP]
> Para exibir vários cartões avançados em formato de lista, defina a propriedade `attachmentLayout` do objeto [Atividade][] como "lista".
> Para exibir vários cartões avançados em formato de carrossel, defina a propriedade `Activity` do objeto `attachmentLayout` como "carrossel".
> Se o canal não der suporte para formato de carrossel, ele exibirá os cartões avançados no formato de lista, mesmo se a propriedade `attachmentLayout` especificar "carrossel".

O exemplo a seguir mostra uma solicitação que envia uma mensagem contendo um único anexo de cartão de destaque. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "attachments": [
        {
            "contentType": "application/vnd.microsoft.card.hero",
            "content": {
                "title": "title goes here",
                "subtitle": "subtitle goes here",
                "text": "descriptive text goes here",
                "images": [
                    {
                        "url": "https://aka.ms/DuckOnARock",
                        "alt": "picture of a duck",
                        "tap": {
                            "type": "playAudio",
                            "value": "url to an audio track of a duck call goes here"
                        }
                    }
                ],
                "buttons": [
                    {
                        "type": "playAudio",
                        "title": "Duck Call",
                        "value": "url to an audio track of a duck call goes here"
                    },
                    {
                        "type": "openUrl",
                        "title": "Watch Video",
                        "image": "https://aka.ms/DuckOnARock",
                        "value": "url goes here of the duck in flight"
                    }
                ]
            }
        }
    ],
    "replyToId": "5d5cdc723"
}
```

## <a name="add-an-adaptive-card-to-a-message"></a>Adicionar um cartão adaptável a uma mensagem

O Cartão Adaptável pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada.
Os Cartões Adaptáveis são criados usando o formato JSON especificado em [Cartões Adaptáveis](http://adaptivecards.io), o que fornece a você controle total sobre o conteúdo e o formato do cartão.

Aproveite as informações dentro do site [Cartões Adaptáveis](http://adaptivecards.io) para compreender o esquema de Cartão Adaptável, explore os elementos do Cartão Adaptável e veja exemplos de JSON que podem ser usados para criar cartões de composição e complexidade variadas. Além disso, você pode usar o Visualizador Interativo para criar o conteúdo de Cartão Adaptável e visualizar a saída do cartão. O exemplo a seguir é um único Cartão Adaptável para uma atribuição de trabalho.

```json
{
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "type": "AdaptiveCard",
  "version": "1.0",
  "body": [
    {
      "type": "Container",
      "items": [
        {
          "type": "TextBlock",
          "text": "Publish Adaptive Card schema",
          "weight": "bolder",
          "size": "medium"
        },
        {
          "type": "ColumnSet",
          "columns": [
            {
              "type": "Column",
              "width": "auto",
              "items": [
                {
                  "type": "Image",
                  "url": "https://pbs.twimg.com/profile_images/3647943215/d7f12830b3c17a5a9e4afcc370e3a37e_400x400.jpeg",
                  "size": "small",
                  "style": "person"
                }
              ]
            },
            {
              "type": "Column",
              "width": "stretch",
              "items": [
                {
                  "type": "TextBlock",
                  "text": "Matt Hidinger",
                  "weight": "bolder",
                  "wrap": true
                },
                {
                  "type": "TextBlock",
                  "spacing": "none",
                  "text": "Created {{DATE(2017-02-14T06:08:39Z, SHORT)}}",
                  "isSubtle": true,
                  "wrap": true
                }
              ]
            }
          ]
        }
      ]
    },
    {
      "type": "Container",
      "items": [
        {
          "type": "TextBlock",
          "text": "Now that we have defined the main rules and features of the format, we need to produce a schema and publish it to GitHub. The schema will be the starting point of our reference documentation.",
          "wrap": true
        },
        {
          "type": "FactSet",
          "facts": [
            {
              "title": "Board:",
              "value": "Adaptive Card"
            },
            {
              "title": "List:",
              "value": "Backlog"
            },
            {
              "title": "Assigned to:",
              "value": "Matt Hidinger"
            },
            {
              "title": "Due date:",
              "value": "Not set"
            }
          ]
        }
      ]
    }
  ],
  "actions": [
    {
      "type": "Action.ShowCard",
      "title": "Comment",
      "card": {
        "type": "AdaptiveCard",
        "body": [
          {
            "type": "Input.Text",
            "id": "comment",
            "isMultiline": true,
            "placeholder": "Enter your comment"
          }
        ],
        "actions": [
          {
            "type": "Action.Submit",
            "title": "OK"
          }
        ]
      }
    },
    {
      "type": "Action.OpenUrl",
      "title": "View",
      "url": "https://adaptivecards.io"
    }
  ]
}

```

O cartão resultante contém um título, informações sobre quem criou o cartão (seu nome e avatar), quando o cartão foi criado, uma descrição das atribuições de trabalho e informações relacionadas à atribuição. Também há botões que podem ser clicados para comentar a atribuição de trabalho ou exibi-la:

![Lembrete de calendário do Cartão Adaptável](../media/adaptive-card-reminder.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)
- [Adicionar anexos de mídia às mensagens](bot-framework-rest-connector-add-media-attachments.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)
- [Channel Inspector][ChannelInspector]

[ChannelInspector]: ../bot-service-channels-reference.md
[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
[Anexo]: bot-framework-rest-connector-api-reference.md#attachment-object
[CardAction]: bot-framework-rest-connector-api-reference.md#cardaction-object
[AnimationCard]: bot-framework-rest-connector-api-reference.md#animationcard-object
[AudioCard]: bot-framework-rest-connector-api-reference.md#audiocard-object
[HeroCard]: bot-framework-rest-connector-api-reference.md#herocard-object
[ThumbnailCard]: bot-framework-rest-connector-api-reference.md#thumbnailcard-object
[ReceiptCard]: bot-framework-rest-connector-api-reference.md#receiptcard-object
[SigninCard]: bot-framework-rest-connector-api-reference.md#signincard-object
[VideoCard]: bot-framework-rest-connector-api-reference.md#videocard-object
