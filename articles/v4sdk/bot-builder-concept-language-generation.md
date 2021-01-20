---
title: Geração de Linguagem
description: Descreve o funcionamento da Geração de Linguagem no SDK do Bot Framework.
keywords: geração de linguagem
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 39f0ac7767cd981312d8536f6c6ea36204591f97
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577418"
---
# <a name="language-generation"></a>Geração de Linguagem

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

<!-- See [what's new](#Change-Log) for what's new in **4.8.0 RC1** release.-->

A LG (Geração de Linguagem) permite aos desenvolvedores extrair cadeias de caracteres inseridas de seus códigos e arquivos de recurso e gerenciá-las por meio de um formato de arquivo e um runtime de LG. Com a LG, os desenvolvedores podem criar uma experiência de conversa mais natural definindo diversas variações em uma frase, executando expressões simples com base no contexto e referindo-se à memória de conversação.

A LG pode ser usada por desenvolvedores para:

- obter um tom de voz com personalidade coerente para o bot
- separar a lógica de negócios da apresentação
- incluir variações e resolução baseada em composição sofisticada para qualquer uma das respostas do bot
- construir fala versus adaptações de tela
- construir cartões, ações sugeridas e anexos

No núcleo da LG está a expansão de modelo e a substituição de entidade. Você pode fornecer uma variação única para expansão, bem como expandir um modelo condicionalmente. A saída da LG pode ser uma cadeia de caracteres de texto simples, uma resposta de várias linhas ou uma carga de objeto complexo que uma camada acima da LG usará para construir uma [atividade][1].

Confira a seguir um modelo de LG de saudação simples. Observe que todas as saudações fazem referência ao nome do usuário na memória com a variável `${user.name}`.

```lg
# greetingTemplate
- Hello ${user.name}, how are you?
- Good morning ${user.name}.It's nice to see you again.
- Good day ${user.name}. What can I do for you today?
```

## <a name="lg-in-action"></a>LG em ação

Você pode usar a LG de várias maneiras ao desenvolver bots. Para começar, crie um ou mais [arquivos .lg][3] para abranger todos os cenários possíveis em que você usaria o subsistema de geração de linguagem com as respostas de seu bot para um usuário.

# <a name="c"></a>[C#](#tab/csharp)

Inclua a biblioteca de geração de linguagem [`Microsoft.Bot.Builder.LanguageGeneration`](https://www.nuget.org/packages/Microsoft.Bot.Builder.LanguageGeneration/). Em seguida, analise e carregue modelos em seu arquivo .lg adicionando o seguinte:

```csharp
    _templates = Templates.ParseFile(fullPath);
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Inclua a biblioteca de geração de linguagem [`botbuilder-lg`][15]. Em seguida, analise e carregue modelos em seu arquivo .lg adicionando o seguinte:

```javascript
     let lgTemplates = Templates.parseFile(fullPath);
```

---

Quando precisar de expansão de modelo, use `Evaluate` e passe o nome do modelo relevante.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
    var lgOutput = _templates.Evaluate(<TemplateName>);
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    let lgOutput = lgTemplates.evaluate(<TemplateName>);
```

---

Se o modelo precisar de propriedades específicas a serem passadas para resolução/expansão, você poderá passá-las ao chamar `Evaluate`.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
    var lgOutput = _templates.Evaluate("WordGameReply", new { GameName = "MarcoPolo" } );
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    let lgOutput = lgTemplates.evaluate("WordGameReply", { GameName = "MarcoPolo" } );
```

---

## <a name="multilingual-generation-and-language-fallback-policy"></a>Política de fallback de linguagem e geração multilíngue

O bot pode ser direcionado para mais de um idioma falado ou de exibição. Você pode gerenciar instâncias separadas do *TemplateEngine*, uma por idioma de destino. Para obter um exemplo de como adicionar vários idiomas, também conhecido como fallback de idioma, ao seu bot, confira o prompt de vários turnos com uma amostra de fallback de idioma no [C#](https://aka.ms/csharp-lg-multi-turn-prompt-language-fallback-sample) ou no [JavaScript](https://aka.ms/js-lg-multi-turn-prompt-language-fallback-sample).

<!--
## Grammar check and correction
The current library does not include any capabilities for grammar check or correction-->

## <a name="expand-api"></a>API de expansão

Para obter todas as expansões possíveis de um modelo, você pode usar `ExpandTemplate`.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
    var results = lgTemplates.ExpandTemplate("WordGameReply", { GameName = "MarcoPolo" } )
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    const results = lgTemplates.expandTemplate("WordGameReply", { GameName = "MarcoPolo" } )
```

---

Por exemplo, dado este conteúdo de LG:

```lg
# Greeting
- Hi
- Hello

#TimeOfDay
- Morning
- Evening

# FinalGreeting
- ${Greeting()} ${TimeOfDay()}

# TimeOfDayWithCondition
- IF: ${time == 'morning'}
    - ${Greeting()} Morning
- ELSEIF: ${time == 'evening'}
    - ${Greeting()} Evening
- ELSE:
    - ${Greeting()} Afternoon
```

A chamada `ExpandTemplate("FinalGreeting")` resulta em quatro avaliações:

- **Hi Morning**
- **Hi Evening**
- **Hello Morning**
- **Hello Evening**

A chamada `ExpandTemplate("TimeOfDayWithCondition", new { time = "evening" })` com escopo resulta em duas expansões:

- **Hi Evening**
- **Hello Evening**

## <a name="additional-resources"></a>Recursos adicionais

- Confira o [formato de arquivo .lg][3] para saber mais sobre arquivos .lg.
- Leia [modelos de resposta estruturados](../language-generation/language-generation-structured-response-template.md) para saber mais sobre modelos complexos.
- [Referência da API do C#](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.languagegeneration)
- [Referência da API JavaScript](https://docs.microsoft.com/javascript/api/botbuilder-lg)

<!---
## Change Log
### 4.8 PREVIEW
- \[**BREAKING CHANGES**\]:
    - `ActivityFactory`
        - has been moved to `Microsoft.Bot.Builder`
        - `CreateActivity` renamed to `FromObject`
    - `TemplateEngine`
        - has been renamed to `Templates`
        - `TemplateEngine.EvaluateTemplate` renamed to `Templates.Evaluate`
        - `TemplateEngine.Evaluate` renamed to `Templates.EvaluateText`
        - `TemplateEngine().AddFile` has been replaced by `Templates.ParseFile`
        - `AddFiles` has been deprecated. You no longer can load multiple .lg files. Instead, you should use [import][50] support in your .lg files.
    - Bounding character for expressions has been changed from **@**{expression} to **$**{expression}

    |  Old  | New |
    |-------|-----|
    |  # myTemplate <br/> - I have @{user.name} as your name |  # myTemplate <br/> - I have ${user.name} as your name |
    | # myTemplate <br/> - @{ackPhrase()} <br/><br/> # ackPhrase <br/> - hi <br/>- hello | # myTemplate <br/> - ${ackPhrase()} <br/><br/> # ackPhrase <br/> - hi <br/>- hello |
    | # myTemplate <br/> - IF : @{user.name == null} <br/>&nbsp;&nbsp;&nbsp;&nbsp;- hello<br/>- ELSE : <br/>&nbsp;&nbsp;&nbsp;&nbsp;- null | # myTemplate <br/> - IF : ${user.name == null} <br/>&nbsp;&nbsp;&nbsp;&nbsp;- hello<br/>- ELSE : <br/>&nbsp;&nbsp;&nbsp;&nbsp;- null |
- New sample [C#][100], [JS][101] that demonstrates how to extend the set of prebuilt expression functions and using custom functions in LG.
- You can now use back-quote for string interpolation. e.g. \`=json({'user': ${user.name}})`
### 4.7 PREVIEW
- \[**BREAKING CHANGES**\]:
    - Old way to refer to a template via `[TemplateName]` notation is deprecated in favor of `${TemplateName()}` notation. There are no changes to how structured response templates are defined.
    - All expressions must now be enclosed within `${<expression>}`. The old notation `{<expression>}` is no longer supported.
    - `ActivityBuilder` has been deprecated and removed in favor of `ActivityFactory`. Note that by stable release, functionality offered by `ActivityFactory` is likely to move into `MessageFactory`.

    |  Old  | New |
    |-------|-----|
    | # myTemplate <br/> - I have {user.name} as your name |  # myTemplate <br/> - I have @{user.name} as your name |
    | # myTemplate <br/> - [ackPhrase] <br/><br/> # ackPhrase <br/> - hi <br/>- hello | # myTemplate <br/> - @{ackPhrase()} <br/><br/> # ackPhrase <br/> - hi <br/>- hello |

- \[**NEW**\]:
    - Language generation preview is now available for JavaScript as well. Checkout [packages][15]. See [samples][26]
    - New `ActivityFactory` class that helps transform structured response template output from LG into a Bot framework activity.
    - Bug fixes and stability improvements.

### 4.6 PREVIEW 2
- \[**BREAKING CHANGES**\]:
    - Old `display || speak` notation is deprecated in favor of structured template support. See below for more details on structured template.
    - Old `Chatdown` style cards are deprecated in favor of structured template support. See below for more details on structured template.
- \[**NEW**\]:
    - Structured Template support in .lg file format. See [response template](../language-generation/language-generation-structured-response-template.md) to learn more about Structured Template definition.
    - ActivityGenerator.GenerateFromLG static method to transform output from LG sub-system into a full blown [Bot Framework Activity][1]

### 4.6 PREVIEW
- \[**NEW**\] [VS code extension][22] for LG (syntax highlighting, auto-suggest (including expressions, pre-built functions, template names etc), validation)
- LG file format:
    - Support for [Switch..Case..Default][20]
    - Support for [import reference][21] to another .lg file.
- [API changes][2]:
    - Dropped FromFile and FromText methods in favor of AddFile and AddFiles.
    - Added ability to provide a delegate to externally resolve import references found in content.
- \[**NEW**\] Translate functionality in [MSLG CLI][23]

### 4.5 PREVIEW
- Initial preview release
-->
[1]:https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md
[3]:../file-format/bot-builder-lg-file-format.md
[6]:https://github.com/microsoft/botframework-cli/tree/master/packages/chatdown
[7]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md
[8]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/examples/CardExamples.chat
[9]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md#message-commands
[10]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md#message-cards
[11]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md#message-attachments
[12]:https://botbuilder.myget.org/F/botbuilder-v4-dotnet-daily/api/v3/index.json
[13]:https://botbuilder.myget.org/gallery/botbuilder-v4-js-daily
[14]:https://www.nuget.org/packages/Microsoft.Bot.Builder.LanguageGeneration/4.7.0-preview
[15]:https://www.npmjs.com/package/botbuilder-lg
[20]:../file-format/bot-builder-lg-file-format.md#switch-case
[21]:../file-format/bot-builder-lg-file-format.md#importing-external-references
[22]:https://aka.ms/lg-vscode-extension
[23]:https://github.com/microsoft/botbuilder-tools/tree/V.Future/packages/MSLG
[26]:https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/javascript_nodejs
[50]:../file-format/bot-builder-lg-file-format.md#importing-external-references
[100]:https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/language-generation/csharp_dotnetcore/20.extending-with-custom-functions/README.md
[101]:https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/language-generation/javascript_nodejs/20.custom-functions/README.md
