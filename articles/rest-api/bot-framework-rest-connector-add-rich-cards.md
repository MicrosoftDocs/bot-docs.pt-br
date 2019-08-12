---
title: Adicionar anexos de cartão avançado às mensagens | Microsoft Docs
description: Saiba como adicionar cartões avançados a mensagens usando o serviço Conector de Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 739af17f39a8537833aafcc8d03fb63ea2c8c914
ms.sourcegitcommit: a1eaa44f182a7210197bd793250907df00e9edab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68757058"
---
# <a name="add-rich-card-attachments-to-messages"></a>Adicionar anexos de cartão avançados às mensagens
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Bots e canais normalmente trocam cadeias de caracteres de texto, mas alguns canais também oferecem suporte a troca de anexos, o que permite que seu bot envie mensagens mais ricas para os usuários. Por exemplo, seu bot pode enviar anexos de cartões avançados e de mídia (por exemplo, imagens, vídeos, áudio, arquivos). Este artigo descreve como adicionar anexos de cartão avançado às mensagens usando o serviço Conector de Bot.

> [!NOTE]
> Para saber mais sobre como adicionar anexos de mídia às mensagens, consulte [Adicionar anexos de mídia às mensagens](bot-framework-rest-connector-add-media-attachments.md).

## <a id="types-of-cards"></a> Tipos de cartões avançados

Um cartão avançado é formado por um título, descrição, link e imagens. Uma mensagem pode conter vários cartões avançados, exibidos em formato de carrossel ou de lista.
Atualmente, o Bot Framework dá suporte a oito tipos de cartões avançados: 

| Tipo de cartão | DESCRIÇÃO |
|----|----|
| <a href="/adaptive-cards/get-started/bots">AdaptiveCard</a> | Um cartão personalizável que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. Confira [suporte por canal](/adaptive-cards/get-started/bots#channel-status).  |
| `AnimationCard` | Um cartão que pode reproduzir GIFs animados ou vídeos curtos. |
| `AudioCard` | Um cartão que pode reproduzir um arquivo de áudio. |
| `HeroCard` | Um cartão que geralmente contém uma única imagem grande, um ou mais botões e um texto. |
| `ThumbnailCard` | Um cartão que geralmente contém uma única imagem em miniatura, um ou mais botões e um texto. |
| `ReceiptCard` | Um cartão que permite a um bot fornecer um recibo para o usuário. Normalmente, contém a lista de itens a serem incluídos no recibo, informações fiscais e de totais e outros textos. |
| `SignInCard` | Um cartão que permite a um bot solicitar a entrada do usuário. Normalmente, contém um texto e um ou mais botões nos quais o usuário pode clicar para iniciar o processo de entrada. |
| `VideoCard` | Um cartão que pode reproduzir vídeos. |

> [!TIP]
> Para determinar o tipo dos cartões avançados que um canal dá suporte e ver como o canal renderiza cartões avançados, consulte o [Inspetor de Canal][ChannelInspector]. Consulte a documentação do canal para obter informações sobre limitações do conteúdo dos cartões (por exemplo, o número máximo de botões) ou o comprimento máximo do título.

## <a name="process-events-within-rich-cards"></a>Processar eventos em cartões avançados

Para processar eventos em cartões avançados, use objetos `CardAction` para especificar o que deverá acontecer quando o usuário clicar em um botão ou tocar em uma seção do cartão. Cada objeto `CardAction` contém estas propriedades:

| Propriedade | Type | DESCRIÇÃO | 
|----|----|----|
| Tipo | string | tipo de ação (um dos valores especificados na tabela a seguir) |
| título | string | título do botão |
| image | string | URL da imagem do botão |
| value | string | valor necessário para executar o tipo de ação especificado |

> [!NOTE]
> Botões dentro de Cartões Adaptáveis não são criados usando objetos `CardAction`, mas usando o esquema definido pelos <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a>. Consulte [Adicionar um Cartão Adaptável a uma mensagem](#adaptive-card) para obter um exemplo que mostra como adicionar botões a um Cartão Adaptável.

Esta tabela lista os valores válidos para a propriedade `type` de um objeto `CardAction` e descreve o conteúdo esperado da propriedade `value` para cada tipo:

| Tipo | value | 
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

## <a name="add-a-hero-card-to-a-message"></a>Adicionar um cartão de destaque a uma mensagem

Para adicionar um anexo de cartão avançado a uma mensagem, primeiro crie um objeto que corresponda ao [tipo do cartão](#types-of-cards) que você deseja adicionar à mensagem. Em seguida, crie um objeto `Attachment`, defina sua propriedade `contentType` como o tipo de mídia do cartão e sua propriedade `content` ao objeto que você criou para representar o cartão. Especifique seu objeto `Attachment` dentro da matriz `attachments` da mensagem.

> [!TIP]
> As mensagens que contêm de cartão avançado normalmente não especificam `text`.

Alguns canais permitem que você adicione vários cartões avançados à matriz `attachments` dentro de uma mensagem. Esse recurso pode ser útil em situações nas quais você deseja apresentar várias opções ao usuário. Por exemplo, se o seu bot permitir que os usuários reservem quartos de hotel, ele poderia apresentar uma lista de cartões avançados com os tipos de quartos disponíveis. Cada cartão pode conter uma imagem e uma lista de comodidades correspondentes ao tipo de quarto, e o usuário pode selecionar um tipo de quarto tocando um cartão ou clicando em um botão.

> [!TIP]
> Para exibir vários cartões avançados em formato de lista, defina a propriedade `Activity` do objeto `attachmentLayout` como "lista". Para exibir vários cartões avançados em formato de carrossel, defina a propriedade `Activity` do objeto `attachmentLayout` como "carrossel". Se o canal não der suporte para formato de carrossel, ele exibirá os cartões avançados no formato de lista, mesmo se a propriedade `attachmentLayout` especificar "carrossel".

O exemplo a seguir mostra uma solicitação que envia uma mensagem contendo um único anexo de cartão de destaque. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI de base; o URI de base para solicitações em que os seus problemas de bot podem ser diferentes. Para obter detalhes sobre como definir o URI de base, veja [Referência da API](bot-framework-rest-connector-api-reference.md#base-uri).

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

## <a id="adaptive-card"></a> Adicionar um cartão adaptável a uma mensagem

O Cartão Adaptável que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. Os Cartões Adaptáveis são criados usando o formato JSON especificado em <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a>, o que lhe dá controle total sobre o conteúdo e o formato do cartão. 

Aproveite as informações dentro do site <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a> para compreender o esquema de Cartão Adaptável, explore os elementos do Cartão Adaptável e veja exemplos de JSON que podem ser usados para criar cartões de composição e complexidade variadas. Além disso, você pode usar o Visualizador Interativo para criar o conteúdo de Cartão Adaptável e visualizar a saída do cartão.

O exemplo a seguir mostra uma solicitação que envia uma mensagem contendo um único Cartão Adaptável para um lembre de calendário. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI de base; o URI de base para solicitações em que os seus problemas de bot podem ser diferentes. Para obter detalhes sobre como definir o URI de base, veja [Referência da API](bot-framework-rest-connector-api-reference.md#base-uri).

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
            "contentType": "application/vnd.microsoft.card.adaptive",
            "content": {
                "type": "AdaptiveCard",
                "body": [
                    {
                        "type": "TextBlock",
                        "text": "Adaptive Card design session",
                        "size": "large",
                        "weight": "bolder"
                    },
                    {
                        "type": "TextBlock",
                        "text": "Conf Room 112/3377 (10)"
                    },
                    {
                        "type": "TextBlock",
                        "text": "12:30 PM - 1:30 PM"
                    },
                    {
                        "type": "TextBlock",
                        "text": "Snooze for"
                    },
                    {
                        "type": "Input.ChoiceSet",
                        "id": "snooze",
                        "style": "compact",
                        "choices": [
                            {
                                "title": "5 minutes",
                                "value": "5",
                                "isSelected": true
                            },
                            {
                                "title": "15 minutes",
                                "value": "15"
                            },
                            {
                                "title": "30 minutes",
                                "value": "30"
                            }
                        ]
                    }
                ],
                "actions": [
                    {
                        "type": "Action.Http",
                        "method": "POST",
                        "url": "http://foo.com",
                        "title": "Snooze"
                    },
                    {
                        "type": "Action.Http",
                        "method": "POST",
                        "url": "http://foo.com",
                        "title": "I'll be late"
                    },
                    {
                        "type": "Action.Http",
                        "method": "POST",
                        "url": "http://foo.com",
                        "title": "Dismiss"
                    }
                ]
            }
        }
    ],
    "replyToId": "5d5cdc723"
}
```

O cartão resultante contém três blocos de texto, um campo de entrada (lista de opções) e três botões:

![Lembrete de calendário do Cartão Adaptável](../media/adaptive-card-reminder.png)


## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)
- [Adicionar anexos de mídia às mensagens](bot-framework-rest-connector-add-media-attachments.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)
- [Channel Inspector][ChannelInspector]
- <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a>

[ChannelInspector]: ../bot-service-channel-inspector.md
