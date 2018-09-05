---
title: Adicionar mídia às mensagens | Microsoft Docs
description: Saiba como adicionar mídia às mensagens usando o SDK do Bot Builder.
keywords: mídia, mensagens, imagens, áudio, vídeo, arquivos, MessageFactory, mensagens avançadas
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 08/24/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5883b31df95da26fa0432f4cfe195f12fc3089ad
ms.sourcegitcommit: 86ddf3ebe6cc3385d1c4d30b971ac9c3e1fc5a77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43055990"
---
# <a name="add-media-to-messages"></a>Adicionar mídia às mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma troca de mensagens entre o usuário e o bot pode conter anexos de mídia, tais como imagens, vídeos, áudios e arquivos. O SDK do Bot Builder inclui uma nova classe `Microsoft.Bot.Builder.MessageFactory`, projetada para facilitar a tarefa de enviar mensagens avançadas ao usuário.

## <a name="send-attachments"></a>Enviar anexos

Para enviar o conteúdo do usuário, como uma imagem ou um vídeo, é possível adicionar um anexo ou lista de anexos a uma mensagem.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A propriedade `Attachments` do objeto `Activity` contém uma matriz de objetos `Attachment` que representam os anexos de mídia e os cartões avançados na mensagem. Para adicionar um anexo de mídia a uma mensagem, crie um objeto `Attachment` para a atividade `message` e defina as propriedades `ContentType`, `ContentUrl` e `Name`. 

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and add an attachment.
var activity = MessageFactory.Attachment(
    new Attachment { ContentUrl = "imageUrl.png", ContentType = "image/png" }
);

// Send the activity to the user.
await context.SendActivity(activity);
```

O método `Attachment` da fábrica de mensagens também pode enviar uma lista de anexos, empilhadas um sobre o outro.

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and add an attachment.
var activity = MessageFactory.Attachment(new Attachment[]
{
    new Attachment { ContentUrl = "imageUrl1.png", ContentType = "image/png" },
    new Attachment { ContentUrl = "imageUrl2.png", ContentType = "image/png" },
    new Attachment { ContentUrl = "imageUrl3.png", ContentType = "image/png" }
});

// Send the activity to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para enviar ao usuário uma única parte do conteúdo, como uma imagem ou um vídeo, é possível enviar mídia contida em uma URL:

```javascript
const {MessageFactory} = require('botbuilder');
let imageOrVideoMessage = MessageFactory.contentUrl('imageUrl.png', 'image/jpeg')

// Send the activity to the user.
await context.sendActivity(imageOrVideoMessage);
```

Para enviar uma lista de anexos empilhados um sobre o outro: <!-- TODO: Convert the hero cards to image attachments in this example. -->

```javascript
// require MessageFactory and CardFactory from botbuilder.
const {MessageFactory, CardFactory} = require('botbuilder');

let messageWithCarouselOfCards = MessageFactory.list([
    CardFactory.heroCard('title1', ['imageUrl1'], ['button1']),
    CardFactory.heroCard('title2', ['imageUrl2'], ['button2']),
    CardFactory.heroCard('title3', ['imageUrl3'], ['button3'])
]);

await context.sendActivity(messageWithCarouselOfCards);
```

---

Se um anexo for uma imagem, áudio ou vídeo, o serviço do Connector comunicará os dados do anexo ao canal de modo a permitir que o [canal](~/v4sdk/bot-builder-channeldata.md) renderize esse anexo na conversa. Se o anexo for um arquivo, a URL do arquivo será renderizada como um hiperlink na conversa.

## <a name="send-a-hero-card"></a>Enviar um cartão hero

Além de anexos de vídeo ou imagem simples, é possível anexar um **cartão hero**, o que permite a você combinar imagens e botões em um objeto e depois enviá-los ao usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para redigir uma mensagem com um cartão hero e um botão, você pode anexar um `HeroCard` a uma mensagem:

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach a Hero card.
var activity = MessageFactory.Attachment(
    new HeroCard(
        title: "White T-Shirt",
        images: new CardImage[] { new CardImage(url: "imageUrl.png") },
        buttons: new CardAction[]
        {
            new CardAction(title: "buy", type: ActionTypes.ImBack, value: "buy")
        })
    .ToAttachment());

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para enviar ao usuário um cartão e um botão, você pode anexar um `heroCard` à mensagem:

```javascript
// require MessageFactory and CardFactory from botbuilder.
const {MessageFactory} = require('botbuilder');
const {CardFactory} = require('botbuilder');

const message = MessageFactory.attachment(
     CardFactory.heroCard(
        'White T-Shirt',
        ['https://example.com/whiteShirt.jpg'],
        ['buy']
    )
 );

await context.sendActivity(message);
```

---

<!--Lifted from the RESP API documentation-->

Um cartão avançado é formado por um título, descrição, link e imagens. Uma mensagem pode conter vários cartões avançados, exibidos em formato de carrossel ou de lista. O SDK do Bot Builder atualmente dá suporte a uma ampla gama de cartões avançados. Para ver uma listagem dos cartões avançados e canais em que eles têm suporte, consulte [Elementos UX de Design](../bot-service-design-user-experience.md).

> [!TIP]
> Para determinar o tipo dos cartões avançados que um canal dá suporte e ver como o canal renderiza cartões avançados, consulte o [Inspetor de Canal](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-inspector?view=azure-bot-service-4.0). Consulte a documentação do canal para obter informações sobre limitações do conteúdo dos cartões (por exemplo, o número máximo de botões ou o comprimento máximo do título).

## <a name="process-events-within-rich-cards"></a>Processar eventos em cartões avançados

Para processar eventos em cartões avançados, use objetos  _ação do cartão_ para especificar o que deve acontecer quando o usuário clica em um botão ou toca em uma seção do cartão.

Para funcionar corretamente, atribua um tipo de ação para cada item clicável no cartão. Esta tabela lista os valores válidos para a propriedade de um objeto de ação do cartão e descreve o conteúdo esperado da propriedade do valor para cada tipo.

| Tipo | Valor |
| :---- | :---- |
| openUrl | URL a ser aberta no navegador interno. Responde ao toque ou clique abrindo a URL. |
| imBack | Texto da mensagem para enviar ao bot (do usuário que clicou no botão ou tocou no cartão). Essa mensagem (do usuário ao bot) ficará visível a todos os participantes da conversa por meio do aplicativo cliente que hospeda a conversa. |
| postBack | Texto da mensagem para enviar ao bot (do usuário que clicou no botão ou tocou no cartão). Alguns aplicativos cliente podem exibir esse texto no feed de mensagem, onde ficará visível a todos os participantes da conversa. |
| chamada | Destino de uma chamada telefônica neste formato: `tel:123123123123` responde ao toque ou clique iniciando uma chamada.|
| playAudio | URL do áudio a ser reproduzido. Responde ao toque ou clique reproduzindo o áudio. |
| playVideo | URL do vídeo a ser reproduzido. Responde ao toque ou clique reproduzindo o vídeo. |
| showImage | URL da imagem a ser exibida. Responde ao toque ou clique exibindo a imagem. |
| downloadFile | URL do arquivo a ser baixado.  Responde ao toque ou clique baixando o arquivo. |
| signin | URL do fluxo do OAuth a ser iniciado. Responde ao toque ou clique em iniciando a entrada. |

## <a name="hero-card-using-various-event-types"></a>Cartão hero usando vários tipos de evento

O código a seguir mostra exemplos que usam diversos eventos de cartão avançados.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach a Hero card.
var activity = MessageFactory.Attachment(
    new HeroCard(
        title: "Holler Back Buttons",
        images: new CardImage[] { new CardImage(url: "imageUrl.png") },
        buttons: new CardAction[]
        {
            new CardAction(title: "Shout Out Loud", type: ActionTypes.imBack, value: "You can ALL hear me!"),
            new CardAction(title: "Much Quieter", type: ActionTypes.postBack, value: "Shh! My Bot friend hears me."),
            new CardAction(title: "Show me how to Holler", type: ActionTypes.openURL, value: $"https://en.wikipedia.org/wiki/{cardContent.Key}")
        })
    .ToAttachment());

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {ActionTypes} = require("botbuilder");
```

```javascript
const hero = MessageFactory.attachment(
    CardFactory.heroCard(
        'Holler Back Buttons',
        ['https://example.com/whiteShirt.jpg'],
        [{
            type: ActionTypes.ImBack,
            title: 'ImBack',
            value: 'You can ALL hear me! Shout Out Loud'
        },
        {
            type: ActionTypes.PostBack,
            title: 'PostBack',
            value: 'Shh! My Bot friend hears me. Much Quieter'
        },
        {
            type: ActionTypes.OpenUrl,
            title: 'OpenUrl',
            value: 'https://en.wikipedia.org/wiki/{cardContent.Key}'
        }]
    )
);

await context.sendActivity(hero);

```

---

## <a name="send-an-adaptive-card"></a>Enviar um cartão adaptável
O Cartão Adaptável e MessageFactory são usados para enviar mensagens avançadas incluindo textos, imagens, vídeo, áudio e arquivos para se comunicar com os usuários. No entanto, há algumas diferenças entre eles. 

Primeiro, somente alguns canais dão suporte aos Cartões Adaptáveis, e os canais que dão suporte podem dar suporte parcial aos Cartões Adaptáveis. Por exemplo, se você enviar um Cartão Adaptável no Facebook, os botões não funcionarão se textos e imagens funcionarem bem. MessageFactory é simplesmente uma classe auxiliar dentro do SDK do Construtor de Bot para automatizar as etapas de criação para você e com suporte da maioria dos canais. 

Em segundo lugar, o Cartão Adaptável entrega as mensagens no formato de cartão e o canal determina o layout do cartão. O formato das mensagens que o MessageFactory entrega depende do canal e não é necessariamente no formato de cartão. a menos que o Cartão Adaptável faça parte do anexo. 

Para localizar as informações mais recentes sobre o suporte de canal do cartão adaptável, consulte o <a href="http://adaptivecards.io/visualizer/">Visualizador de cartões adaptáveis</a>.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Para usar os cartões adaptáveis, certifique-se de adicionar o pacote NuGet `Microsoft.AdaptiveCards`.


> [!NOTE]
> Você deve testar esse recurso com os canais de que bot será usado para determinar se esses canais dão suporte a cartões adaptáveis.

```csharp
using AdaptiveCards;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach an Adaptive Card.
var card = new AdaptiveCard();
card.Body.Add(new TextBlock()
{
    Text = "<title>",
    Size = TextSize.Large,
    Wrap = true,
    Weight = TextWeight.Bolder
});
card.Body.Add(new TextBlock() { Text = "<message text>", Wrap = true });
card.Body.Add(new TextInput()
{
    Id = "Title",
    Value = string.Empty,
    Style = TextInputStyle.Text,
    Placeholder = "Title",
    IsRequired = true,
    MaxLength = 50
});
card.Actions.Add(new SubmitAction() { Title = "Submit", DataJson = "{ Action:'Submit' }" });
card.Actions.Add(new SubmitAction() { Title = "Cancel", DataJson = "{ Action:'Cancel'}" });

var activity = MessageFactory.Attachment(new Attachment(AdaptiveCard.ContentType, content: card));

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {CardFactory} = require("botbuilder");

const message = CardFactory.adaptiveCard({
    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
    "version": "1.0",
    "type": "AdaptiveCard",
    "speak": "Your flight is confirmed for you from San Francisco to Amsterdam on Friday, October 10 8:30 AM",
    "body": [
        {
            "type": "TextBlock",
            "text": "Passenger",
            "weight": "bolder",
            "isSubtle": false
        },
        {
            "type": "TextBlock",
            "text": "Sarah Hum",
            "separator": true
        },
        {
            "type": "TextBlock",
            "text": "1 Stop",
            "weight": "bolder",
            "spacing": "medium"
        },
        {
            "type": "TextBlock",
            "text": "Fri, October 10 8:30 AM",
            "weight": "bolder",
            "spacing": "none"
        },
        {
            "type": "ColumnSet",
            "separator": true,
            "columns": [
                {
                    "type": "Column",
                    "width": 1,
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": "San Francisco",
                            "isSubtle": true
                        },
                        {
                            "type": "TextBlock",
                            "size": "extraLarge",
                            "color": "accent",
                            "text": "SFO",
                            "spacing": "none"
                        }
                    ]
                },
                {
                    "type": "Column",
                    "width": "auto",
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": " "
                        },
                        {
                            "type": "Image",
                            "url": "http://messagecardplayground.azurewebsites.net/assets/airplane.png",
                            "size": "small",
                            "spacing": "none"
                        }
                    ]
                },
                {
                    "type": "Column",
                    "width": 1,
                    "items": [
                        {
                            "type": "TextBlock",
                            "horizontalAlignment": "right",
                            "text": "Amsterdam",
                            "isSubtle": true
                        },
                        {
                            "type": "TextBlock",
                            "horizontalAlignment": "right",
                            "size": "extraLarge",
                            "color": "accent",
                            "text": "AMS",
                            "spacing": "none"
                        }
                    ]
                }
            ]
        },
        {
            "type": "ColumnSet",
            "spacing": "medium",
            "columns": [
                {
                    "type": "Column",
                    "width": "1",
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": "Total",
                            "size": "medium",
                            "isSubtle": true
                        }
                    ]
                },
                {
                    "type": "Column",
                    "width": 1,
                    "items": [
                        {
                            "type": "TextBlock",
                            "horizontalAlignment": "right",
                            "text": "$1,032.54",
                            "size": "medium",
                            "weight": "bolder"
                        }
                    ]
                }
            ]
        }
    ]
});

// send adaptive card as attachment 
await context.sendActivity({ attachments: [message] })
```

---

## <a name="send-a-carousel-of-cards"></a>Enviar um carrossel de cartões

As mensagens também podem incluir vários anexos em um layout de carrossel, que coloca os anexos lado a lado e permite ao usuário rolar para deslocar.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach a set of Hero cards.
var activity = MessageFactory.Carousel(
    new Attachment[]
    {
        new HeroCard(
            title: "title1",
            images: new CardImage[] { new CardImage(url: "imageUrl1.png") },
            buttons: new CardAction[]
            {
                new CardAction(title: "button1", type: ActionTypes.ImBack, value: "item1")
            })
        .ToAttachment(),
        new HeroCard(
            title: "title2",
            images: new CardImage[] { new CardImage(url: "imageUrl2.png") },
            buttons: new CardAction[]
            {
                new CardAction(title: "button2", type: ActionTypes.ImBack, value: "item2")
            })
        .ToAttachment(),
        new HeroCard(
            title: "title3",
            images: new CardImage[] { new CardImage(url: "imageUrl3.png") },
            buttons: new CardAction[]
            {
                new CardAction(title: "button3", type: ActionTypes.ImBack, value: "item3")
            })
        .ToAttachment()
    });

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// require MessageFactory and CardFactory from botbuilder.
const {MessageFactory, CardFactory} = require('botbuilder');

//  init message object
let messageWithCarouselOfCards = MessageFactory.carousel([
    CardFactory.heroCard('title1', ['imageUrl1'], ['button1']),
    CardFactory.heroCard('title2', ['imageUrl2'], ['button2']),
    CardFactory.heroCard('title3', ['imageUrl3'], ['button3'])
]);

await context.sendActivity(messageWithCarouselOfCards);
```

---

## <a name="additional-resources"></a>Recursos adicionais

[Visualizar recursos com o Inspetor de Canal](../bot-service-channel-inspector.md)

---
