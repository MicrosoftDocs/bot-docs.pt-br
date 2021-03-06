---
title: Geradores em diálogos adaptáveis
description: Como fornecer a LG (Geração de Linguagem) para os diálogos adaptáveis usando geradores.
keywords: bot, generators, adaptive dialogs, language generation, lg
author: emgrol
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4e9aa362c3b060327b663c25cda574f248b9f1ce
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717592"
---
# <a name="generators-in-adaptive-dialogs"></a>Geradores em diálogos adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os geradores vinculam um sistema [LG (geração de linguagem)](bot-builder-concept-language-generation.md) específico a um diálogo adaptável.
Neste artigo, você aprenderá sobre os modelos LG que adicionam variedade e personalidade a respostas de bot e como chamar esses modelos em sua caixa de diálogo raiz usando o `TemplateEngineLanguageGenerator` .

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento dos [diálogos adaptáveis](bot-builder-adaptive-dialog-Introduction.md)
- Conhecimento dos [reconhecedores](bot-builder-concept-adaptive-dialog-recognizers.md) em diálogos adaptáveis
- Conhecimento das [expressões adaptáveis](bot-builder-concept-adaptive-expressions.md)

## <a name="language-generation"></a>Geração de linguagem

A geração de linguagem (LG) permite aos desenvolvedores extrair cadeias de caracteres inseridas de seus códigos e arquivos de recursos e gerenciá-las por meio de um tempo de execução e formato de arquivo LG Com o LG, os desenvolvedores podem criar uma experiência de conversação mais natural definindo várias variações em uma frase, executando expressões simples com base no contexto e referindo-se à memória de conversação.

A LG, juntamente com os [reconhecedores](bot-builder-concept-adaptive-dialog-recognizers.md), permite a separação clara e o encapsulamento dos ativos de Reconhecimento vocal e geração de linguagem de um diálogo. Os reconhecedores dão ao bot a capacidade de entender a entrada e a LG permite que o bot responda a essa entrada de uma forma inteligente.

## <a name="lg-templates"></a>Modelos LG

Os modelos são o conceito básico do sistema de geração de linguagem. As variações nos modelos são usadas pelo bot para responder à entrada do usuário. Cada modelo tem um nome e um dos seguintes:

- uma lista de valores de texto de variação one-of
- uma definição de conteúdo estruturado
- uma coleção de condições, cada uma com:
    - uma [expressão adaptável](bot-builder-concept-adaptive-expressions.md)
    - uma lista de valores de texto de variação one-of por condição

Os modelos são definidos em arquivos [.lg](../file-format/bot-builder-lg-file-format.md), que podem conter um ou mais modelos. Há três tipos de modelos:

- [resposta simples](../file-format/bot-builder-lg-file-format.md#simple-response-template)
- [resposta condicional](../file-format/bot-builder-lg-file-format.md#conditional-response-template)
- [resposta estruturada](../file-format/bot-builder-lg-file-format.md#structured-response-template)

### <a name="simple-response-template"></a>Modelo de resposta simples

Um modelo de resposta simples inclui uma ou mais variações de texto que são usadas para composição e expansão. Quando esse modelo é avaliado, o runtime da LG escolhe aleatoriamente uma dessas variações.

Este é um exemplo de um modelo de resposta simples com duas variações:

```lg
> Greeting template with 2 variations.
# GreetingPrefix
- Hi
- Hello
```

### <a name="conditional-response-template"></a>Modelo de resposta condicional

Os modelos de resposta condicional permitem criar um conteúdo selecionado com base em uma condição. Todas as condições são expressas usando [expressões adaptáveis](bot-builder-concept-adaptive-expressions.md), [predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) e [personalizadas](../language-generation/bot-builder-howto-use-lg-custom-functions.md).

Há dois tipos de modelos de resposta condicional: [if-else](../file-format/bot-builder-lg-file-format.md#if-else-template) e [opção](../file-format/bot-builder-lg-file-format.md#switch-template).

#### <a name="if-else-template"></a>Modelo if-else

O modelo if-else permite criar um modelo que escolhe uma coleção com base em uma ordem de condições em cascata. Assim como os modelos de resposta simples, ele pode ter variações embutidas em blocos IF ou ELSE. O aninhamento é obtido por meio da [composição](#structured-response-template).

Este é um exemplo de um modelo de resposta condicional if-else:

```lg
> time of day greeting reply template with conditions.
# timeOfDayGreeting
- IF: ${timeOfDay == 'morning'}
    - Good morning!
    - Wake up.
- ELSE:
    - Good evening
    - Sleep well!
    - Goodnight.
```

#### <a name="switch-template"></a>Modelo de opção

O modelo de opção permite projetar um modelo que corresponda ao valor de uma expressão a uma cláusula CASE e produz a saída com base nesse caso.

Este é um exemplo de um modelo de resposta condicional de opção:

```lg
# TestTemplate
- SWITCH: ${condition}
- CASE: ${case-expression-1}
    - output1
- CASE: ${case-expression-2}
    - output2
- DEFAULT:
   - final output
```

### <a name="structured-response-template"></a>Modelo de resposta estruturada

Os modelos de resposta estruturada permitem definir uma estrutura complexa que dá suporte à principal funcionalidade de LG, como criação de modelos, composição e substituição, deixando a interpretação da resposta estruturada para o chamador da biblioteca LG. Atualmente, há suporte para definições de atividade e cartão para aplicativos de bot.

Esta é a definição de um modelo de resposta estruturada:

```lg
# TemplateName
> this is a comment
[Structure-name
    Property1 = <plain text> .or. <plain text with template reference> .or. <expression>
    Property2 = list of values are denoted via '|'. e.g. a | b
> this is a comment about this specific property
    Property3 = Nested structures are achieved through composition
]
```

Leia [Modelo de resposta estruturada](../language-generation/language-generation-structured-response-template.md) para obter mais informações e exemplos de modelos complexos.

## <a name="call-templates-in-your-root-dialog"></a>Chamar modelos no diálogo raiz

Depois de criar modelos para o bot, você pode adicioná-los ao diálogo adaptável. Você pode definir o gerador para um arquivo .lg ou definir o gerador como uma instância `TemplateEngineLanguageGenerator` em que você gerencia explicitamente um ou mais arquivos .lg. O exemplo abaixo mostra a última abordagem.

<!--### [C#](#tab/csharp)-->

Digamos que você queira adicionar modelos de **RootDialog.lg** a um diálogo adaptável. Adicione os seguintes pacotes ao código:

```csharp
using Microsoft.Bot.Builder.LanguageGeneration;
using Microsoft.Bot.Builder.Dialogs.Adaptive.Templates;
using Microsoft.Bot.Builder.Dialogs.Adaptive.Generators;

```

Em seguida, você precisará resolver o caminho do arquivo .lg:

```csharp
string[] paths = { ".", "Dialogs", "RootDialog.lg" };
string fullPath = Path.Combine(paths);
```

Isso garantirá que você esteja chamando os arquivos de modelo corretos para o bot.

Agora você pode criar o `TemplateEngineLanguageGenerator` no diálogo adaptável que gerencia os modelos em **RootDialog.lg**:

```csharp
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog)){
.
.
.
Generator = new TemplateEngineLanguageGenerator(Templates.ParseFile(fullPath))
}
```

Agora você pode chamar modelos no bot por nome, usando o formato `${<template-name>}`.

```csharp
new SendActivity("${FinalUserProfileReadOut()}")
```

No exemplo acima, o bot chama o modelo `FinalUserProfileReadOut` e responde com o conteúdo do modelo.

<!--### [Javascript](#tab/javascript)

Say you want to add templates from **RootDialog.lg** to an adaptive dialog. Add the following lines to your code to require the necessary packages:

```javascript
const { NumberInput, AttachmentInput, ConfirmInput, IfCondition, ActivityTemplate, AdaptiveDialog, TextInput, SendActivity, TemplateEngineLanguageGenerator, OnBeginDialog } = require('botbuilder-dialogs-adaptive');
const { Templates } = require('botbuilder-lg');
```

Resolve the path using `path.join()` and use the `Templates` constant to parse the **rootDialog.lg** file:

```javascript
const lgFile = Templates.parseFile(path.join(__dirname, 'rootDialog.lg'));
```

By joining the path you ensure that you are calling the correct template files for your bot.

Now you can create the `TemplateEngineLanguageGenerator` to manage the templates in **rootDialog.lg**:

```javascript
const rootDialog = new AdaptiveDialog(ROOT_DIALOG).configure(
.
.
.
generator: new TemplateEngineLanguageGenerator(lgFile)
```

You can now call templates in your bot by name, using the format `${<template-name>}`.

```javascript
new SendActivity('${FinalUserProfileReadOut()}')
```

In the example above, the bot calls the `FinalUserProfileReadOut` template and responds with the contents of the template.

---    -->

## <a name="additional-information"></a>Informações adicionais

- [Formato de arquivo .lg](../file-format/bot-builder-lg-file-format.md)
- [Modelo de resposta estruturado](../language-generation/language-generation-structured-response-template.md)
- [Funções predefinidas de expressões adaptáveis](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md)
