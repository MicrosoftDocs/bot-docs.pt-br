---
title: Criar mensagens com o Construtor de bot do SDK para .NET | Microsoft Docs
description: Saiba mais sobre as propriedades de mensagens comumente usadas no Construtor de bot do SDK para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: f3f40e41605bfd309d480465a05b91acf5a52d5c
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000383"
---
# <a name="create-messages"></a>Criar mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Seu bot enviará **atividades** de [mensagens](bot-builder-dotnet-activities.md) para comunicar informações aos usuários e, da mesma forma, também receberá atividades de **mensagens** dos usuários. Algumas mensagens podem simplesmente consistir em texto sem formatação, enquanto outras podem conter conteúdo mais elaborado, como [texto a ser falado](bot-builder-dotnet-text-to-speech.md), [ações sugeridas](bot-builder-dotnet-add-suggested-actions.md), [anexos de mídia](bot-builder-dotnet-add-media-attachments.md), [cartões avançados](bot-builder-dotnet-add-rich-card-attachments.md) e [dados específicos do canal](bot-builder-dotnet-channeldata.md). 

Este artigo descreve algumas das propriedades de mensagem comumente usadas.

## <a name="customizing-a-message"></a>Personalizar uma mensagem

Para ter mais controle sobre a formatação de texto das mensagens, você pode criar uma mensagem personalizada usando o objeto [Activity](https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html) e definir as propriedades necessárias antes de enviá-las ao usuário.

Este exemplo mostra como criar um objeto `message` personalizado e definir as propriedades `Text`, `TextFormat` e `Local`.

[!code-csharp[Set message properties](../includes/code/dotnet-create-messages.cs#setBasicProperties)]

A propriedade `TextFormat` de uma mensagem pode ser usada para especificar o formato do texto. A propriedade `TextFormat` pode ser definida como **plain**, **markdown** ou **xml**. O valor padrão para `TextFormat` é **markdown**. 

## <a name="attachments"></a>Anexos

A propriedade `Attachments` de uma atividade de mensagens pode ser usada para enviar e receber anexos de mídia simples (imagem, áudio, vídeo, arquivo) e cartões avançados. Para obter detalhes, confira [Adicionar anexos de mídia às mensagens](bot-builder-dotnet-add-media-attachments.md) e [Adicionar cartões avançados às mensagens](bot-builder-dotnet-add-rich-card-attachments.md).

## <a name="entities"></a>Entidades

A propriedade `Entities` de uma mensagem é uma matriz de objetos <a href="http://schema.org/" target="_blank">schema.org</a> abertos que permite a troca de metadados contextuais comuns entre o canal e o bot.

### <a name="mention-entities"></a>Entidades de Menção

Vários canais dão suporte à capacidade de um usuário ou bot "mencionar" alguém dentro do contexto de uma conversa. Para mencionar um usuário em uma mensagem, preencha a propriedade `Entities` da mensagem com um objeto `Mention`. O objeto `Mention` contém essas propriedades: 

| Propriedade | DESCRIÇÃO | 
|----|----|
| Tipo | tipo da entidade ("mention") | 
| Mencionado | O objeto `ChannelAccount` que indica que o usuário foi mencionado | 
| Texto | texto dentro da propriedade `Activity.Text` que representa a própria menção (pode ser nulo ou vazio) |

Este exemplo de código mostra como adicionar uma entidade `Mention` à coleção `Entities`.

[!code-csharp[set Mention](../includes/code/dotnet-create-messages.cs#setMention)]

> [!TIP]
> Ao tentar determinar a intenção do usuário, o bot pode querer ignorar essa parte da mensagem no qual ele é mencionado. Chame o método `GetMentions` e avalie os objetos `Mention` retornados na resposta.

### <a name="place-objects"></a>Posicionar objetos

As <a href="https://schema.org/Place" target="_blank">informações relacionadas a locais</a> pode ser transmitida dentro de uma mensagem preenchendo a propriedade `Entities` da mensagem com um objeto `Place` ou um objeto `GeoCoordinates`. 

O objeto `Place` contém essas propriedades:

| Propriedade | DESCRIÇÃO | 
|----|----|
| Tipo | tipo da entidade ("Place") |
| Endereço | descrição ou objeto `PostalAddress` (futuro) | 
| Localização geográfica | GeoCoordinates | 
| HasMap | URL para um mapa ou objeto `Map` (futuro) |
| NOME | nome do local |

O objeto `GeoCoordinates` contém essas propriedades:

| Propriedade | DESCRIÇÃO | 
|----|----|
| Tipo | tipo da entidade ("GeoCoordinates") |
| NOME | nome do local |
| Longitude | longitude da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) | 
| Latitude | latitude da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) | 
| Elevação | elevação da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) | 

Este exemplo de código mostra como adicionar uma entidade `Place` à coleção `Entities`:

[!code-csharp[set GeoCoordinates](../includes/code/dotnet-create-messages.cs#setGeoCoord)]

### <a name="consume-entities"></a>Consumir entidades

Para consumir entidades, use a palavra-chave `dynamic` ou classes fortemente tipadas.

Este exemplo de código mostra como usar a palavra-chave `dynamic` para processar uma entidade dentro da propriedade `Entities` de uma mensagem:

[!code-csharp[examine entity using dynamic keyword](../includes/code/dotnet-create-messages.cs#examineEntity1)]

Este exemplo de código mostra como usar uma classe fortemente tipada para processar uma entidade dentro da propriedade `Entities` de uma mensagem:

[!code-csharp[examine entity using typed class](../includes/code/dotnet-create-messages.cs#examineEntity2)]

## <a name="channel-data"></a>Dados de canal

A propriedade `ChannelData` de uma atividade de mensagem pode ser usada para implementar a funcionalidade específica do canal. Para ver detalhes, confira [Implementar a funcionalidade específica do canal](bot-builder-dotnet-channeldata.md).

## <a name="text-to-speech"></a>Texto em fala

A propriedade `Speak` de uma atividade de mensagem pode ser usada para especificar o texto a ser falado pelo seu bot em um canal habilitado para fala. A propriedade `InputHint` de uma atividade de mensagem pode ser usada para controlar o estado do microfone e a caixa de entrada do cliente (se houver uma). Para ver detalhes, confira [Adicionar fala a mensagens](bot-builder-dotnet-text-to-speech.md).

## <a name="suggested-actions"></a>Ações sugeridas

A propriedade `SuggestedActions` de uma atividade de mensagem pode ser usada para apresentar os botões que o usuário pode tocar para fornecer entradas. Ao contrário de botões exibidos em cartões avançados (que permanecem visíveis e acessíveis ao usuário, mesmo após serem tocados), os botões que aparecem no painel de ações sugeridas desaparecerão após a seleção do usuário. Para ver detalhes, confira [Adicionar ações sugeridas a mensagens](bot-builder-dotnet-add-suggested-actions.md).

## <a name="next-steps"></a>Próximas etapas

Um bot e um usuário podem enviar mensagens uns aos outros. Quando a mensagem for mais complexa, seu bot pode enviar um cartão avançado em uma mensagem para o usuário. Os cartões avançados abrangem vários cenários de interação e apresentação comumente necessários na maioria dos bots.

> [!div class="nextstepaction"]
> [Enviar um cartão avançado em uma mensagem](bot-builder-dotnet-add-rich-card-attachments.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral das atividades](bot-builder-dotnet-activities.md)
- [Enviar e receber atividades](bot-builder-dotnet-connector.md)
- [Adicionar anexos de mídia a mensagens](bot-builder-dotnet-add-media-attachments.md)
- [Adicionar cartões avançados a mensagens](bot-builder-dotnet-add-rich-card-attachments.md)
- [Adicionar fala a mensagens](bot-builder-dotnet-text-to-speech.md)
- [Adicionar ações sugeridas a mensagens](bot-builder-dotnet-add-suggested-actions.md)
- [Implementar a funcionalidade específica do canal](bot-builder-dotnet-channeldata.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">Interface IMessageActivity</a>

