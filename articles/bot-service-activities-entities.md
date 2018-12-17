---
title: Entidades e tipos de atividades | Microsoft Docs
description: Entidades e tipos de atividades.
keywords: mencionar entidades, tipos de atividades, consumir entidades
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/01/2018
ms.openlocfilehash: 818017a81b497b13ee181dbb6b87c03a0182736d
ms.sourcegitcommit: 75f32b3325dd0fc4d8128dee6c22ebf91e5785b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2018
ms.locfileid: "53120673"
---
# <a name="entities-and-activity-types"></a>Entidades e tipos de atividades

Entidades são uma parte de uma atividade e fornecem informações adicionais sobre a atividade ou a conversa.

[!include[Entity boilerplate](includes/snippet-entity-boilerplate.md)]

## <a name="entities"></a>Entidades

A propriedade *entidades* de uma mensagem é uma matriz de objetos <a href="http://schema.org/" target="_blank">schema.org</a> abertos que permite a troca de metadados contextuais comuns entre o canal e o bot.

### <a name="mention-entities"></a>Entidades de menção

Vários canais dão suporte à capacidade de um usuário ou bot "mencionar" alguém dentro do contexto de uma conversa.
Para mencionar um usuário em uma mensagem, preencha a propriedade entidades da mensagem com um objeto *mention*.
O objeto mention contém essas propriedades:

| Propriedade | DESCRIÇÃO |
|----|----|
| Tipo | tipo da entidade ("mention") |
| Mencionado | objeto de conta de canal que indica que o usuário foi mencionado | 
| Texto | texto dentro da propriedade *activity.text* que representa a própria menção (pode ser nulo ou vazio) |

Este exemplo de código mostra como adicionar uma entidade mention à coleção de entidades.

# <a name="ctabcs"></a>[C#](#tab/cs)
[!code-csharp[set Mention](includes/code/dotnet-create-messages.cs#setMention)]

> [!TIP]
> Ao tentar determinar a intenção do usuário, o bot pode querer ignorar essa parte da mensagem no qual ele é mencionado. Chame o método `GetMentions` e avalie os objetos `Mention` retornados na resposta.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
var entity = context.activity.entities;

const mention = {
    type: "Mention",
    text: "@johndoe",
    mentioned: {
        name: "John Doe",
        id: "UV341235"
    }
}

entity = [mention];
```

---

### <a name="place-objects"></a>Posicionar objetos

As <a href="https://schema.org/Place" target="_blank">informações relacionadas a locais</a> podem ser transmitidas dentro de uma mensagem preenchendo a propriedade da mensagem com um objeto *Place* ou um objeto *GeoCoordinates*.

O objeto Place contém essas propriedades:

| Propriedade | DESCRIÇÃO |
|----|----|
| Tipo | tipo da entidade ("Place") |
| Endereço | Descrição ou objeto de endereço postal (futuro) |
| Localização geográfica | GeoCoordinates |
| HasMap | URL para um mapa ou objeto de mapa (futuro) |
| NOME | nome do local |

O objeto GeoCoordinates contém essas propriedades:

| Propriedade | DESCRIÇÃO |
|----|----|
| Tipo | tipo da entidade ("GeoCoordinates") |
| NOME | nome do local |
| Longitude | longitude da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Longitude | latitude da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Elevação | elevação da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |

Este exemplo de código mostra como adicionar uma entidade place à coleção de entidades:

# <a name="ctabcs"></a>[C#](#tab/cs)
[!code-csharp[set GeoCoordinates](includes/code/dotnet-create-messages.cs#setGeoCoord)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
var entity = context.activity.entities;

const place = {
    elavation: 100,
    type: "GeoCoordinates",
    name : "myPlace",
    latitude: 123,
    longitude: 234
};

entity = [place];

```

---

### <a name="consume-entities"></a>Consumir entidades

# <a name="ctabcs"></a>[C#](#tab/cs)

Para consumir entidades, use a palavra-chave `dynamic` ou classes fortemente tipadas.

Este exemplo de código mostra como usar a palavra-chave `dynamic` para processar uma entidade dentro da propriedade `Entities` de uma mensagem:

[!code-csharp[examine entity using dynamic keyword](includes/code/dotnet-create-messages.cs#examineEntity1)]

Este exemplo de código mostra como usar uma classe fortemente tipada para processar uma entidade dentro da propriedade `Entities` de uma mensagem:

[!code-csharp[examine entity using typed class](includes/code/dotnet-create-messages.cs#examineEntity2)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Este exemplo de código mostra como processar uma entidade dentro da propriedade `entity` de uma mensagem:

```javascript
if (entity[0].type === "GeoCoordinates" && entity[0].latitude > 34) {
    // do something
}
```

---

## <a name="activity-types"></a>Tipos de atividade

Este exemplo de código mostra como processar uma atividade de tipo **mensagem**:

# <a name="ctabcs"></a>[C#](#tab/cs)

```cs
if (context.Activity.Type == ActivityTypes.Message){
    // do something
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```js
if(context.activity.type === 'message'){
    // do something
}
```

---

As atividades podem ser de vários tipos diferentes após a **mensagem** mais comum. Há vários tipos de atividade:

| Activity.Type | Interface | DESCRIÇÃO |
|-----|-----|-----|
| [message](#message) | IMessageActivity (C#) <br> Atividade (JS) | Representa uma comunicação entre o bot e o usuário. |
| [contactRelationUpdate](#contactrelationupdate) | IContactRelationUpdateActivity (C#) <br> Atividade (JS) | Indica que o bot foi adicionado ou removido da lista de contatos do usuário. |
| [conversationUpdate](#conversationupdate) | IConversationUpdateActivity (C#) <br> Atividade (JS) | Indica que o bot foi adicionado a uma conversa, outros membros foram adicionados ou removidos da conversa ou houve alteração de metadados da conversa. |
| [deleteUserData](#deleteuserdata) | n/d | Indica para um bot que um usuário solicitou ao bot a exclusão de quaisquer dados de usuário que ele possa ter armazenado. |
| [endOfConversation](#endofconversation) | IEndOfConversationActivity (C#) <br> Atividade (JS) | Indica o final de uma conversa. |
| [event](#event) | IEventActivity (C#) <br> Atividade (JS) | Representa uma comunicação enviada a um bot que não é visível para o usuário. |
| [installationUpdate](#installationupdate) | IInstallationUpdateActivity (C#) <br> Atividade (JS) | Representa uma instalação ou desinstalação de um bot dentro de uma unidade organizacional (por exemplo, um locatário do cliente ou uma "equipe") de um canal. |
| [invoke](#invoke) | IInvokeActivity (C#) <br> Atividade (JS) | Representa uma comunicação enviada a um bot para solicitar a execução de uma operação específica. Esse tipo de atividade é reservado para uso interno do Microsoft Bot Framework. |
| [messageReaction](#messagereaction) | IMessageReactionActivity (C#) <br> Atividade (JS) | Indica que um usuário reagiu a uma atividade existente. Por exemplo, um usuário clica no botão "Curtir" em uma mensagem. |
| [typing](#typing) | ITypingActivity (C#) <br> Atividade (JS) | Indica que o usuário ou o bot na outra extremidade da conversa está compilando uma resposta. |
| messageUpdate | IMessageUpdateActivity (C#) <br> Atividade (JS) | Indica uma solicitação para atualizar uma atividade de mensagem anterior em uma conversa. |
| messageDelete | IMessageDeleteActivity (C#) <br> Atividade (JS) | Indica uma solicitação para excluir uma atividade de mensagem anterior em uma conversa. |
| suggestion | ISuggestionActivity (C#) <br> Atividade (JS) | Indica uma sugestão privada para o destinatário sobre outra atividade específica. |
| trace | ITraceActivity (C#) <br> Atividade (JS) | Uma atividade pela qual um bot pode registrar informações internas em uma transcrição de conversa registrada. |
| handoff | IHandoffActivity (C#) <br> Atividade (JS) | Controla se a conversa foi transferida, ou se foi feita uma solicitação para transferir o controle da conversa. |

## <a name="message"></a>Message

<!-- Only the last link is different. -->

::: moniker range="azure-bot-service-3.0"

Seu bot enviará atividades de mensagem para comunicar informações aos usuários e receber atividades de mensagem dos usuários.
Algumas mensagens podem simplesmente consistir em texto sem formatação, enquanto outras podem ter um conteúdo mais elaborado, como texto falado, [ações sugeridas](v4sdk/bot-builder-howto-add-suggested-actions.md), [anexos de mídia](v4sdk/bot-builder-howto-add-media-attachments.md), [cartões avançados](v4sdk/bot-builder-howto-add-media-attachments.md#send-a-hero-card) e [dados específicos do canal](~/dotnet/bot-builder-dotnet-channeldata.md).

::: moniker-end

::: moniker range="azure-bot-service-4.0"

Seu bot enviará atividades de mensagem para comunicar informações aos usuários e receber atividades de mensagem dos usuários.
Algumas mensagens podem simplesmente consistir em texto sem formatação, enquanto outras podem ter um conteúdo mais elaborado, como texto falado, [ações sugeridas](v4sdk/bot-builder-howto-add-suggested-actions.md), [anexos de mídia](v4sdk/bot-builder-howto-add-media-attachments.md), [cartões avançados](v4sdk/bot-builder-howto-add-media-attachments.md#send-a-hero-card) e [dados específicos do canal](~/v4sdk/bot-builder-channeldata.md).

::: moniker-end

## <a name="contactrelationupdate"></a>contactRelationUpdate

Um bot receberá uma atividade de atualização de contato sempre que for adicionado ou removido da lista de contatos de um usuário. O valor da propriedade ação da atividade (adicionar | remover) indica se o bot foi adicionado ou removido da lista de contatos do usuário.

## <a name="conversationupdate"></a>conversationUpdate

Um bot receberá uma atividade de atualização de conversa sempre que uma conversa for adicionada, outros membros forem adicionados ou removidos de uma conversa ou metadados de conversa foram alterados.

Se os membros foram adicionados à conversa, a propriedade adicionada de membros da atividade conterá uma matriz de objetos de conta de canal para identificar os novos membros.

Para determinar se o seu bot foi adicionado à conversa (ou seja, é um dos novos membros), avalie se o valor ID do destinatário da atividade (ou seja, a ID do seu bot) corresponde à propriedade ID de qualquer uma das contas na matriz adicionada de membros adicionados.

Se os membros tiverem sido removidos da conversa, a propriedade de membros removidos conterá uma matriz de objetos de conta de canal para identificar os membros removidos.

> [!TIP]
> Se o seu bot receber uma atividade de atualização de conversa indicando que um usuário entrou para a conversa, você poderá optar por fazer com que ele responda, enviando uma mensagem de boas-vinda para esse usuário.

## <a name="deleteuserdata"></a>deleteUserData

Um bot recebe uma atividade de excluir dados de usuário quando um usuário solicita a exclusão de quaisquer dados que o bot tenha mantido anteriormente para o usuário. Se o bot receber esse tipo de atividade, ele deverá excluir quaisquer PII (informações de identificação pessoal) que armazenou anteriormente para o usuário que fez a solicitação.

## <a name="endofconversation"></a>endOfConversation

Um bot recebe uma atividade de fim de conversa para indicar que o usuário terminou a conversa. Um bot pode enviar uma atividade de fim de conversa para indicar ao usuário que a conversa está terminando.

## <a name="event"></a>evento

Seu bot pode receber uma atividade de evento de um processo ou serviço externo que deseja comunicar informações ao seu bot, sem que essas informações fiquem visíveis aos usuários. O remetente de uma atividade de evento normalmente não espera que o bot confirme o recebimento de qualquer forma.

## <a name="installationupdate"></a>installationUpdate

Atividades de atualização de instalação representam uma instalação ou desinstalação de um bot dentro de uma unidade organizacional (por exemplo, um locatário do cliente ou uma "equipe") de um canal. Atividades de atualização de instalação geralmente não representam a adição ou remoção de um canal. Os canais podem enviar as atividades de instalação quando um bot é adicionado ou removido de um locatário, equipe ou a outra unidade de organização dentro do canal. Os canais não deveriam enviar as atividades de instalação quando o bot for instalado ou removido de um canal.

## <a name="invoke"></a>invoke

Seu bot pode receber uma atividade invoke que representa uma solicitação para executar uma operação específica.
O remetente de uma atividade invoke normalmente espera que o bot confirme o recebimento por meio de resposta HTTP.
Esse tipo de atividade é reservado para uso interno do Microsoft Bot Framework.

## <a name="messagereaction"></a>messageReaction

Alguns canais enviarão atividades de reação de mensagem ao seu bot quando um usuário reagir a uma atividade existente. Por exemplo, um usuário clica no botão "Curtir" em uma mensagem. A propriedade replyToId indicará a qual atividade o usuário reagiu.

A atividade de reação de mensagem pode corresponder a qualquer quantidade de tipos de reação de mensagem definida pelo canal. Por exemplo, "Like" ou "PlusOne" como tipos de reação enviados por um canal.

## <a name="typing"></a>digitação

A bot recebe uma atividade digitação para indicar que o usuário está digitando uma resposta.
Um bot pode enviar uma atividade digitação para indicar ao usuário que está trabalhando para atender uma solicitação ou compilar uma resposta.

::: moniker range="azure-bot-service-3.0"

## <a name="additional-resources"></a>Recursos adicionais

- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe de atividade</a>
::: moniker-end
