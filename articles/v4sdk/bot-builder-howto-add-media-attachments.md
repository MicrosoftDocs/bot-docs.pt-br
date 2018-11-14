---
title: Adicionar mídia às mensagens | Microsoft Docs
description: Saiba como adicionar mídia às mensagens usando o SDK do Bot Builder.
keywords: mídia, mensagens, imagens, áudio, vídeo, arquivos, MessageFactory, rich cards, mensagens, cartões adaptáveis, cartão hero e ações sugeridas
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fcbe28110ec71da7263d125e79ca59d15efa9d5f
ms.sourcegitcommit: 15f7fa40b7e0a05507cdc66adf75bcfc9533e781
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50916773"
---
# <a name="add-media-to-messages"></a>Adicionar mídia às mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

A troca de mensagens entre usuário e bot pode conter anexos de mídia como imagens, vídeo, áudio e arquivos. O SDK do Bot Builder dá suporte à tarefa de envio de mensagens avançadas para o usuário. Para determinar o tipo de mensagens avançadas com suporte de um canal (Facebook, Skype, Slack etc), consulte a documentação do canal para obter informações sobre as limitações. Consulte a [experiência de usuário de design](../bot-service-design-user-experience.md) para obter uma lista de cartões disponíveis. 

## <a name="send-attachments"></a>Enviar anexos

Para enviar o conteúdo do usuário, como uma imagem ou um vídeo, é possível adicionar um anexo ou lista de anexos a uma mensagem.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A propriedade `Attachments` do objeto `Activity` contém uma matriz de objetos `Attachment` que representam os anexos de mídia e os cartões avançados na mensagem. Para adicionar um anexo de mídia a uma mensagem, crie um objeto `Attachment` para a atividade `message` e defina as propriedades `ContentType`, `ContentUrl` e `Name`. A propriedade `Attachments` do objeto `Activity` contém uma matriz de objetos `Attachment` que representam os anexos de mídia e os cartões avançados na mensagem. Para adicionar um anexo de mídia a uma mensagem, use o método `Attachment` para criar um objeto `Attachment` para a atividade `message` e defina as propriedades `ContentType`, `ContentUrl` e `Name`. O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos](https://aka.ms/bot-attachments-sample-code). 

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

var reply = turnContext.Activity.CreateReply();

// Create an attachment.
var attachment = new Attachment
    {
        ContentUrl = "imageUrl.png",
        ContentType = "image/png",
        Name = "imageName",
    };

// Add the attachment to our reply.
reply.Attachments = new List<Attachment>() { attachment };

// Send the activity to the user.
await turnContext.SendActivityAsync(reply, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos JS](https://aka.ms/bot-attachments-sample-code-js).
Para enviar ao usuário uma única parte do conteúdo, como uma imagem ou um vídeo, é possível enviar mídia contida em uma URL:

```javascript
const { ActionTypes, ActivityTypes, CardFactory } = require('botbuilder');
// Call function to get an attachment.
reply.attachments = [this.getInternetAttachment()];
reply.text = 'This is an internet attachment.';
// Send the activity to the user.
await turnContext.sendActivity(reply);

/* function getInternetAttachment - Returns an attachment to be sent to the user from a HTTPS URL */
getInternetAttachment() {
        return {
            name: 'imageName.png',
            contentType: 'image/png',
            contentUrl: 'imageUrl.png'}
}
```

---

Se um anexo for uma imagem, áudio ou vídeo, o serviço do Connector comunicará os dados do anexo ao canal de modo a permitir que o [canal](bot-builder-channeldata.md) renderize esse anexo na conversa. Se o anexo for um arquivo, a URL do arquivo será renderizada como um hiperlink na conversa.

## <a name="send-a-hero-card"></a>Enviar um cartão hero

Além de anexos de vídeo ou imagem simples, é possível anexar um **cartão hero**, o que permite a você combinar imagens e botões em um objeto e depois enviá-los ao usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para redigir uma mensagem com um cartão hero e um botão, anexe um `HeroCard` a uma mensagem. O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos](https://aka.ms/bot-attachments-sample-code). 

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

var reply = turnContext.Activity.CreateReply();

// Create a HeroCard with options for the user to choose to interact with the bot.
var card = new HeroCard
{
    Text = "You can upload an image or select one of the following choices",
    Buttons = new List<CardAction>()
    {
        new CardAction(ActionTypes.ImBack, title: "1. Inline Attachment", value: "1"),
        new CardAction(ActionTypes.ImBack, title: "2. Internet Attachment", value: "2"),
        new CardAction(ActionTypes.ImBack, title: "3. Uploaded Attachment", value: "3"),
    },
};

// Add the card to our reply.
reply.Attachments = new List<Attachment>() { card.ToAttachment() };

await turnContext.SendActivityAsync(reply, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para redigir uma mensagem com um cartão hero e um botão, anexe um `HeroCard` a uma mensagem. O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos JS](https://aka.ms/bot-attachments-sample-code-js):

```javascript
const { ActionTypes, ActivityTypes, CardFactory } = require('botbuilder');
// build buttons to display.
const buttons = [
            { type: ActionTypes.ImBack, title: '1. Inline Attachment', value: '1' },
            { type: ActionTypes.ImBack, title: '2. Internet Attachment', value: '2' },
            { type: ActionTypes.ImBack, title: '3. Uploaded Attachment', value: '3' }
];

// construct hero card.
const card = CardFactory.heroCard('', undefined,
buttons, { text: 'You can upload an image or select one of the following choices.' });

// add card to Activity.
reply.attachments = [card];

// Send hero card to the user.
await turnContext.sendActivity(reply);
```
---

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

var reply = turnContext.Activity.CreateReply();

var card = new HeroCard
{
    Buttons = new List<CardAction>()
    {
        new CardAction(title: "Much Quieter", type: ActionTypes.PostBack, value: "Shh! My Bot friend hears me."),
        new CardAction(ActionTypes.OpenUrl, title: "Azure Bot Service", value: "https://azure.microsoft.com/en-us/services/bot-service/"),
    },
};

```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {ActionTypes} = require("botbuilder");

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

Para usar os cartões adaptáveis, certifique-se de adicionar o pacote NuGet `Microsoft.AdaptiveCards`. 


> [!NOTE]
> Você deve testar esse recurso com os canais de que bot será usado para determinar se esses canais dão suporte a cartões adaptáveis.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O código-fonte mostrado aqui se baseia no exemplo [Usar Cartões Adaptáveis](https://aka.ms/bot-adaptive-cards-sample-code):

```csharp
using AdaptiveCards;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Creates an attachment that contains an adaptive card
// filePath is the path to JSON file
private static Attachment CreateAdaptiveCardAttachment(string filePath)
{
    var adaptiveCardJson = File.ReadAllText(filePath);
    var adaptiveCardAttachment = new Attachment()
    {
        ContentType = "application/vnd.microsoft.card.adaptive",
        Content = JsonConvert.DeserializeObject(adaptiveCardJson),
    };
    return adaptiveCardAttachment;
}

// Create adaptive card and attach it to the message 
var cardAttachment = CreateAdaptiveCardAttachment(adaptiveCardJsonFilePath);
var reply = turnContext.Activity.CreateReply();
reply.Attachments = new List<Attachment>() { cardAttachment };

await turnContext.SendActivityAsync(reply, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código-fonte mostrado aqui se baseia no exemplo [Usar Cartões Adaptáveis JS](https://aka.ms/bot-adaptive-cards-js-sample-code):

```javascript
const { BotFrameworkAdapter } = require('botbuilder');

// Import AdaptiveCard content.
const FlightItineraryCard = require('./resources/FlightItineraryCard.json');
const ImageGalleryCard = require('./resources/ImageGalleryCard.json');
const LargeWeatherCard = require('./resources/LargeWeatherCard.json');
const RestaurantCard = require('./resources/RestaurantCard.json');
const SolitaireCard = require('./resources/SolitaireCard.json');

// Create array of AdaptiveCard content, this will be used to send a random card to the user.
const CARDS = [
    FlightItineraryCard,
    ImageGalleryCard,
    LargeWeatherCard,
    RestaurantCard,
    SolitaireCard
];
// Select a random card to send.
const randomlySelectedCard = CARDS[Math.floor((Math.random() * CARDS.length - 1) + 1)];
// Send adaptive card.
await context.sendActivity({
      text: 'Here is an Adaptive Card:',
       attachments: [CardFactory.adaptiveCard(randomlySelectedCard)]
});
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

<!-- TODO: Add a media card, such as video or audion. Revisit which examples we put here and link to the 06 through 08 samples. -->

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações detalhadas sobre o esquema do cartão, confira o [Esquema de cartão do Bot Framework](https://aka.ms/botSpecs-cardSchema).

Um exemplo de código pode ser encontrado aqui para cartões: [ C#](https://aka.ms/bot-cards-sample-code)/[JS](https://aka.ms/bot-cards-js-sample-code), os cartões adaptáveis: [C#](https://aka.ms/bot-adaptive-cards-sample-code)/[JS](https://aka.ms/bot-adaptive-cards-js-sample-code), anexos: [C#](https://aka.ms/bot-attachments-sample-code)/[JS](https://aka.ms/bot-attachments-sample-code-js)e ações sugeridas: [C#](https://aka.ms/SuggestedActionsCSharp)/[JS](https://aka.ms/SuggestedActionsJS).
Consulte o repositório Exemplos do Bot Builder no [GitHub](https://aka.ms/bot-samples-readme) para obter exemplos adicionais.
