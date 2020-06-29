---
title: Entidades e tipos de atividades – Serviço de Bot
description: Entidades e tipos de atividades.
keywords: mencionar entidades, tipos de atividades, consumir entidades
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/01/2018
ms.openlocfilehash: cd11cc1fbbacb7e555da4e00337d6fd4b79a4df6
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75789073"
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

| Propriedade | Descrição |
|----|----|
| Type | tipo da entidade ("mention") |
| Mencionado | objeto de conta de canal que indica que o usuário foi mencionado | 
| Texto | texto dentro da propriedade *activity.text* que representa a própria menção (pode ser nulo ou vazio) |

Este exemplo de código mostra como adicionar uma entidade mention à coleção de entidades.

# <a name="c"></a>[C#](#tab/cs)
[!code-csharp[set Mention](includes/code/dotnet-create-messages.cs#setMention)]

> [!TIP]
> Ao tentar determinar a intenção do usuário, o bot pode querer ignorar essa parte da mensagem no qual ele é mencionado. Chame o método `GetMentions` e avalie os objetos `Mention` retornados na resposta.

# <a name="javascript"></a>[JavaScript](#tab/js)
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

| Propriedade | Descrição |
|----|----|
| Type | tipo da entidade ("Place") |
| Endereço | Descrição ou objeto de endereço postal (futuro) |
| Localização geográfica | GeoCoordinates |
| HasMap | URL para um mapa ou objeto de mapa (futuro) |
| Nome | nome do local |

O objeto GeoCoordinates contém essas propriedades:

| Propriedade | Descrição |
|----|----|
| Type | tipo da entidade ("GeoCoordinates") |
| Nome | nome do local |
| Longitude | longitude da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Latitude | latitude da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Elevação | elevação da localização (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |

Este exemplo de código mostra como adicionar uma entidade place à coleção de entidades:

# <a name="c"></a>[C#](#tab/cs)
[!code-csharp[set GeoCoordinates](includes/code/dotnet-create-messages.cs#setGeoCoord)]

# <a name="javascript"></a>[JavaScript](#tab/js)
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

# <a name="c"></a>[C#](#tab/cs)

Para consumir entidades, use a palavra-chave `dynamic` ou classes fortemente tipadas.

Este exemplo de código mostra como usar a palavra-chave `dynamic` para processar uma entidade dentro da propriedade `Entities` de uma mensagem:

[!code-csharp[examine entity using dynamic keyword](includes/code/dotnet-create-messages.cs#examineEntity1)]

Este exemplo de código mostra como usar uma classe fortemente tipada para processar uma entidade dentro da propriedade `Entities` de uma mensagem:

[!code-csharp[examine entity using typed class](includes/code/dotnet-create-messages.cs#examineEntity2)]

# <a name="javascript"></a>[JavaScript](#tab/js)

Este exemplo de código mostra como processar uma entidade dentro da propriedade `entity` de uma mensagem:

```javascript
if (entity[0].type === "GeoCoordinates" && entity[0].latitude > 34) {
    // do something
}
```

---

## <a name="activity-types"></a>Tipos de atividade
<!-- 
This code example show how to process an activity of type **message**:

# [C#](#tab/cs)

```cs
if (context.Activity.Type == ActivityTypes.Message){
    // do something
}
```

# [JavaScript](#tab/js)

```js
if(context.activity.type === 'message'){
    // do something
}
```

--- -->

As atividades podem ser de vários tipos diferentes após a **mensagem** mais comum. Encontre explicações e mais detalhes sobre diferentes tipos de atividades no [esquema de Atividade do Bot Framework](https://aka.ms/botSpecs-activitySchema).

::: moniker range="azure-bot-service-3.0"

## <a name="additional-resources"></a>Recursos adicionais

- <a href="https://docs.botframework.com/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe de atividade</a>
::: moniker-end
