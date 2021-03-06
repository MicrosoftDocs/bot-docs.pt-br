---
title: Adicionar anexos de mídia a mensagens – Serviço de Bot
description: Saiba como adicionar anexos de mídia às mensagens usando o serviço Conector de Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/25/2018
ms.openlocfilehash: 4088960702e6158b66ff084741b5546f8615c1b2
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417101"
---
# <a name="add-media-attachments-to-messages-with-the-bot-connector-api"></a>Adicionar anexos de mídia a mensagens com a API do conector do bot

Bots e canais normalmente trocam cadeias de caracteres de texto, mas alguns canais também oferecem suporte a troca de anexos, o que permite que seu bot envie mensagens mais ricas para os usuários. Por exemplo, seu bot pode enviar anexos de mídia (por exemplo, imagens, vídeos, áudio, arquivos) e [cartões avançados](bot-framework-rest-connector-add-rich-cards.md). Este artigo descreve como adicionar anexos de mídia às mensagens usando o serviço Conector de Bot.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="add-a-media-attachment"></a>Adicionar um anexo de mídia  

Para adicionar um anexo de mídia a uma mensagem, crie um objeto [Anexo][], defina a propriedade `name`, defina a propriedade `contentUrl` como a URL do arquivo de mídia e defina a propriedade `contentType` como o tipo de mídia apropriado (por exemplo, **image/png**, **audio/wav**, **video/mp4**). Em seguida, dentro do objeto [Atividade][] que representa sua mensagem, especifique seu objeto `Attachment` dentro da matriz `attachments`.

O exemplo a seguir mostra uma solicitação que envia uma mensagem contendo o texto e um anexo de imagem única. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "Here's a picture of the duck I was telling you about.",
    "attachments": [
        {
            "contentType": "image/png",
            "contentUrl": "https://aka.ms/DuckOnARock",
            "name": "duck-on-a-rock.jpg"
        }
    ],
    "replyToId": "5d5cdc723"
}
```

Para os canais que dão suporte a binários embutidos de uma imagem, você pode definir a propriedade `contentUrl` do `Attachment` como um binário de base64 da imagem (por exemplo, **data:image/png;base64,iVBORw0KGgo…** ). O canal exibirá a imagem ou a URL da imagem ao lado de cadeia de caracteres de texto da mensagem.

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
    "text": "Here's a picture of the duck I was telling you about.",
    "attachments": [
        {
            "contentType": "image/png",
            "contentUrl": "data:image/png;base64,iVBORw0KGgo…",
            "name": "duck-on-a-rock.jpg"
        }
    ],
    "replyToId": "5d5cdc723"
}
```

Você pode anexar um arquivo de vídeo ou áudio a uma mensagem usando o mesmo processo descrito acima para um arquivo de imagem. Dependendo do canal, áudio e vídeo podem ser reproduzidos em linha ou ele exibidos como um link.

> [!NOTE] 
> O bot também pode receber mensagens que contenham anexos de mídia.
> Por exemplo, uma mensagem recebida por seu bot pode conter um anexo, se o canal permitir que o usuário carregue uma foto para análise ou documento para armazenamento.

## <a name="add-an-audiocard-attachment"></a>Adicionar um anexo de AudioCard

A adição de um anexo de [AudioCard][] ou [VideoCard][] é o mesmo que adicionar um anexo de mídia. Por exemplo, o JSON a seguir mostra como adicionar uma cartão de áudio ao anexo de mídia.

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
      "contentType": "application/vnd.microsoft.card.audio",
      "content": {
        "title": "Allegro in C Major",
        "subtitle": "Allegro Duet",
        "text": "No Image, No Buttons, Autoloop, Autostart, Sharable",
        "duration": "PT2M55S",
        "media": [
          {
            "url": "https://contoso.com/media/AllegrofromDuetinCMajor.mp3"
          }
        ],
        "shareable": true,
        "autoloop": true,
        "autostart": true,
        "value": {
            // Supplementary parameter for this card
        }
      }
    }],
    "replyToId": "5d5cdc723"
}
```

Assim que o canal receber esse anexo, ele iniciará a execução do arquivo de áudio. Se um usuário interage com áudio clicando no botão **Pausar**, por exemplo, o canal enviará um retorno de chamada para o bot com um JSON parecido com este:

```json
{
    ...
    "type": "event",
    "name": "media/pause",
    "value": {
        "url": // URL for media
        "cardValue": {
            // Supplementary parameter for this card
        }
    }
}
```

O nome do evento de mídia **media/pause** aparecerá no campo `activity.name`. Consulte a tabela abaixo para obter uma lista com todos os nomes de evento de mídia.

| Evento | Descrição |
| ---- | ---- |
| **media/next** | O cliente pulou para a próxima mídia |
| **media/pause** | O cliente pausou a reprodução da mídia |
| **media/play** | O cliente começou a reprodução da mídia |
| **media/previous** | O cliente pulou para a mídia anterior |
| **media/resume** | O cliente retomou a reprodução da mídia |
| **media/stop** | O cliente interrompeu a reprodução da mídia |

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)
- [Adicionar cartões ricos mensagens](bot-framework-rest-connector-add-rich-cards.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)
- [Esquema de cartão do bot Framework](https://aka.ms/botSpecs-cardSchema)

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
[Anexo]: bot-framework-rest-connector-api-reference.md#attachment-object
[AudioCard]: bot-framework-rest-connector-api-reference.md#audiocard-object
[VideoCard]: bot-framework-rest-connector-api-reference.md#videocard-object
