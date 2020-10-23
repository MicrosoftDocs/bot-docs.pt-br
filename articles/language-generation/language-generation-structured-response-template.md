---
title: Modelo de resposta estruturada – Serviço de Bot
description: Descreve os modelos de resposta estruturada disponíveis na geração de linguagem.
keywords: structure response template, reference, language generation, lg
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 70ddbfa9e4809d820e001e1828f95617b944f29b
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416803"
---
# <a name="structured-response-template"></a>Modelo de resposta estruturada

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os modelos de resposta estruturada permitem que os desenvolvedores definam uma estrutura complexa que dá suporte à ampla funcionalidade de [LG (geração de linguagem)](../v4sdk/bot-builder-concept-language-generation.md), como criação de modelos, composição, deixando a interpretação da resposta estruturada para o chamador da biblioteca LG.

Para aplicativos de bot, o seguinte suporte é fornecido:

- Definição da [atividade](https://aka.ms/botSpecs-activitySchema)
- definição do [cartão](https://aka.ms/botSpecs-cardSchema)

O modelo de [atividade do bot Framework](https://aka.ms/botSpecs-activitySchema) inclui vários campos personalizáveis. As propriedades a seguir são as mais comumente usadas e podem ser configuradas por meio de uma definição de modelo de atividade:

| Propriedade          | Caso de uso                                                                                                                          |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| Texto              | Exibir o texto usado pelo canal a ser renderizado visualmente                                                                               |
| Speak             | Texto falado usado pelo canal a ser renderizado de maneira audível                                                                                 |
| Anexos       | Lista de anexos com o respectivo tipo. Usado por canais para serem renderizados como cartões de interface do usuário ou outros tipos de anexo de arquivo genéricos.                |
| SuggestedActions  | Lista de ações renderizadas como sugestões para o usuário.                                                                                  |
| InputHint         | Controla o estado do fluxo de captura de áudio em dispositivos que dão suporte à entrada falada. Os valores possíveis incluem `accepting`, `expecting` ou `ignoring`.   |

Não há nenhum comportamento de fallback padrão implementado pelo resolvedor de modelo. Se uma propriedade não for especificada, ela permanecerá não especificada. Por exemplo, a propriedade `Speak` não é atribuída automaticamente para ser a propriedade `Text` se apenas a propriedade `Text` é especificada.

## <a name="definition"></a>Definição

Esta é a definição de um modelo estruturado:

```.lg
# TemplateName
> this is a comment
[Structure-name
    Property1 = <plain text> .or. <plain text with template reference> .or. <expression>
    Property2 = list of values are denoted via '|'. e.g. a | b
> this is a comment about this specific property
    Property3 = Nested structures are achieved through composition
]
```

Veja um exemplo de um modelo de texto básico:

```.lg
# AskForAge.prompt
[Activity
    Text = ${GetAge()}
    Speak = ${GetAge()}
]

# GetAge
- how old are you?
- what is your age?
```

Este é um exemplo de texto com uma ação sugerida. Use **|** para indicar uma lista.

```.lg
> With '|' you are making attachments a list.
# AskForAge.prompt
[Activity
    Text = ${GetAge()}
    SuggestedActions = 10 | 20 | 30
]
```

Este é um exemplo de uma definição de [cartão Hero](https://docs.microsoft.com/microsoftteams/platform/task-modules-and-cards/cards/cards-reference#hero-card):

```.lg
# HeroCard
[Herocard
    title = Hero Card Example
    subtitle = Microsoft Bot Framework
    text = Build and connect intelligent bots to interact with your users naturally wherever they are, from text/sms to Skype, Slack, Office 365 mail and other popular services.
    images = https://sec.ch9.ms/ch9/7ff5/e07cfef0-aa3b-40bb-9baa-7c9ef8ff7ff5/buildreactionbotframework_960.jpg
    buttons = Option 1| Option 2| Option 3
]
```

> [!NOTE]
>
> O LG fornece alguma variabilidade na definição do cartão, que é convertida para alinhar com a [definição da placa do SDK](https://aka.ms/botSpecs-cardSchema). Por exemplo, os `image` `images` campos e têm suporte em todas as definições de cartão no LG, embora apenas `images` tenham suporte na definição da placa do SDK.
>
>Os valores definidos em todos os `image` campos e `images` em um cartão de HeroCard ou miniatura são combinados e convertidos em uma lista de imagens no cartão gerado. Para os outros tipos de cartões, o último valor definido no modelo será atribuído ao `image` campo. Os valores que você atribui ao `image/images` campo podem ser uma cadeia de caracteres, uma [expressão adaptável](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-adaptive-expressions)ou uma matriz no formato usando **|** .

Veja abaixo a combinação dos modelos anteriores:

```.lg
# AskForAge.prompt
[Activity
    Text = ${GetAge()}
    Speak = ${GetAge()}
    Attachments = ${HeroCard()}
    SuggestedActions = 10 | 20 | 30
    InputHint = expecting
]

# GetAge
- how old are you?
- what is your age?

# HeroCard
[Herocard
    title = Hero Card Example
    subtitle = Microsoft Bot Framework
    text = Build and connect intelligent bots to interact with your users naturally wherever they are, from text/sms to Skype, Slack, Office 365 mail and other popular services.
    images = https://sec.ch9.ms/ch9/7ff5/e07cfef0-aa3b-40bb-9baa-7c9ef8ff7ff5/buildreactionbotframework_960.jpg
    buttons = Option 1| Option 2| Option 3
]
```

Por padrão, qualquer referência de modelo é avaliada uma vez durante a avaliação de um modelo estruturado.

Por exemplo, `# AskForAge.prompt` retorna o mesmo texto de resolução para as propriedades `Speak` e `Text`.

```.lg
# AskForAge.prompt
[Activity
    Text = ${GetAge()}
    Speak = ${GetAge()}
]

# GetAge
- how old are you?
- what is your age?
```

Use `<TemplateName>!()` para solicitar uma nova avaliação em cada referência dentro de um modelo estruturado.

No exemplo abaixo, `Speak` e `Text` podem ter um texto de resolução diferente, porque `GetAge` é reavaliado em cada instância.

```.lg
[Activity
    Text = ${GetAge()}
    Speak = ${GetAge!()}
]

# GetAge
- how old are you?
- what is your age?
```

Veja como exibir um carrossel de cartões:

```.lg
# AskForAge.prompt
[Activity
> Defaults to carousel layout in case of list of cards
    Attachments = ${foreach($cardValues, item, HeroCard(item)}
]

# AskForAge.prompt_2
[Activity
> Explicitly specify an attachment layout
    Attachments = ${foreach($cardValues, item, HeroCard(item)}
    AttachmentLayout = list
]

# HeroCard (title, subtitle, text)
[Herocard
    title = ${title}
    subtitle = ${subtitle}
    text = ${text}
    images = https://sec.ch9.ms/ch9/7ff5/e07cfef0-aa3b-40bb-9baa-7c9ef8ff7ff5/buildreactionbotframework_960.jpg
    buttons = Option 1| Option 2| Option 3
]
```

Use **\\**  como um caractere de escape.

```.lg
> You can use '\' as an escape character
> \${GetAge()} would not be evaluated as expression, would be parsed as '${getAge()}' string
# AskForAge.prompt
[Activity
        Text = \${GetAge()}
        SuggestedActions = 10 \| cards | 20 \| cards
]
```

## <a name="structured-template-composition"></a>Composição de modelo estruturado

Há suporte para o seguinte comportamento de composição nos modelos estruturados:

- A composição tem reconhecimento de contexto da estrutura. Se o modelo de destino referenciado também for um modelo estruturado, o tipo de estrutura deverá ser correspondente. Por exemplo, um ActivityTemplate pode ser referenciado em outro ActivityTemplate.
- As referências a um modelo de resposta simples ou condicional podem existir em qualquer lugar dentro de um modelo estruturado.

Suponha que você tenha o seguinte modelo:

```.lg
# T1
[Activity
    Text = ${T2()}
    Speak = foo bar ${T3().speak}
]

# T2
- This is awesome

# T3
[Activity
    Speak = I can also speak!
]
```

Uma chamada a `evaluateTemplate('T1')` resultará na seguinte estrutura interna:

```.lg
[Activity
    Text = This is awesome
    Speak = I can also speak!
]
```

## <a name="full-reference-to-another-structured-template"></a>Referência completa a outro modelo estruturado

Inclua uma referência a outro modelo estruturado como uma propriedade em outro modelo estruturado ou como uma referência em outro modelo de resposta simples ou condicional

Este é um exemplo de referência completa a outro modelo estruturado:

```.lg
# ST1
[MyStruct
    Text = foo
    ${ST2()}
]
# ST2
[MyStruct
    Speak = bar
]
```

Com esse conteúdo, uma chamada a `evaluateTemplate('ST1')` resultará na seguinte estrutura interna:

```.lg
[MyStruct
    Text = foo
    Speak = bar
]
```

Quando a mesma propriedade existir no modelo de chamada, bem como no modelo chamado, o conteúdo no chamador substituirá qualquer conteúdo no modelo chamado.

Aqui está um exemplo:

```.lg
# ST1
[MyStruct
    Text = foo
    ${ST2()}
]
# ST2
[MyStruct
    Speak = bar
    Text = zoo
]
```

Com esse contexto, uma chamada a `evaluateTemplate('ST1')` resultará na seguinte estrutura interna:

```.lg
[MyStruct
    Text = foo
    Speak = bar
]
```

Observe que esse estilo de composição só pode existir no nível raiz. Se houver uma referência a outro modelo estruturado dentro de uma propriedade, a resolução será contextual para essa propriedade.

## <a name="external-file-reference-in-attachment-structured"></a>Referência de arquivo externo no anexo estruturado

Há duas funções predefinidas usadas para referenciar arquivos externamente

1. `fromFile(fileAbsoluteOrRelativePath)` carrega um arquivo especificado. O conteúdo retornado por essa função dará suporte à avaliação do conteúdo. As referências de modelo e as propriedades/as expressões são avaliadas.
2. `ActivityAttachment(content, contentType)` define o `contentType` se ele ainda não está especificado no conteúdo.

Com essas duas funções predefinidas, você pode extrair qualquer conteúdo definido externamente, incluindo todos os tipos de cartões. Use a seguinte LG estruturada para compor uma atividade:

```
# AdaptiveCard
[Activity
                Attachments = ${ActivityAttachment(json(fromFile('../../card.json')), 'adaptiveCard')}
]

# HeroCard
[Activity
                Attachments = ${ActivityAttachment(json(fromFile('../../card.json')), 'heroCard')}
]
```

Use também anexos, vistos abaixo:

```
# AdaptiveCard
[Attachment
    contenttype = adaptivecard
    content = ${json(fromFile('../../card.json'))}
]

# HeroCard
[Attachment
    contenttype = herocard
    content = ${json(fromFile('../../card.json'))}
]
```

<!--
## Chatdown style content as structured activity template
It is a natural extension to also define full [chatdown][1] style templates using the structured template definition capability. This helps eliminate the need to always define chatdown style cards in a multi-line definition

### Existing chatdown style constructs supported
1. Typing
2. Suggestions
3. HeroCard
4. SigninCard
5. ThumbnailCard
6. AudioCard
7. VideoCard
8. AnimationCard
9. MediaCard
8. SigninCard
9. OAuthCard
10. Attachment
11. AttachmentLayout
12. [New] CardAction
13. [New] AdaptiveCard
14. Activity

### Improvements to chatown style constructs

#### CardAction
```.lg
# CardAction (title, type, value)
[CardAction
> type can be 'openUrl', 'imBack', 'postBack', 'messageBack'
    Type = ${if(type == null, 'imBack', type)}
> description that appears on button
    Title = ${title}
> payload to return as object.
    Value = ${value}
]
```

#### Suggestions
Suggestions can now support a full blown CardAction structure.

```.lg
# AskForColor
[Activity
    SuggestedActions = ${CardAction('red')} | ${CardAction('blue') | ${CardAction('See all choices', 'openUrl', 'http://contoso.com/color/choices')}}
]
```

#### Adaptive card
Adaptive cards today are rendered via `[Attachment=cardpath.json adaptive]` notation. You can define adaptive cards inline and consume them via the `json()` function.


```.lg
    # GetColor.prompt
    [Activity
        Attachments = ${json(GetColor.adaptive.card())
    ]

    # GetColor.adaptive.card
    - ```json
    {
        // adaptive card definition
    }
    ```
```

### All card types
Buttons in any of the card types will also support full blown CardAction definition.

Here's an example:
```.lg
# HeroCardTemplate
[HeroCard
    title = BotFramework Hero Card
    subtitle = Microsoft Bot Framework
    text = Build and connect intelligent bots to interact with your users naturally wherever they are, from text/sms to Skype, Slack, Office 365 mail and other popular services.
    image = https://sec.ch9.ms/ch9/7ff5/e07cfef0-aa3b-40bb-9baa-7c9ef8ff7ff5/buildreactionbotframework_960.jpg
    buttons = {CardAction('Option 1| Option 2| Option 3')} | {CardAction('See our library', 'postBack', 'http://contoso.com/cards/all')}
]
```

#### other type of activity
[Bot framework activity protocol][2] supports ability for bot to send a custom activity to the client. We will add support for it via structured LG using the following definition. This should set the outgoing activities `type` property to `event` or the type Activity owns.

```.lg
[Activity
    type = event
    name = some name
    value = some value
]
```

[more test samples][4]
-->
## <a name="additional-information"></a>Informações adicionais
- [Referência da API do C#](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.languagegeneration)
- [Referência da API JavaScript](https://docs.microsoft.com/javascript/api/botbuilder-lg)

[1]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/
[2]:https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md
[4]:https://github.com/microsoft/botbuilder-dotnet/blob/master/tests/Microsoft.Bot.Builder.Dialogs.Adaptive.Templates.Tests/lg/NormalStructuredLG.lg
