---
title: Expressões adaptáveis
description: Descreve como as expressões adaptáveis funcionam no SDK do Bot Framework.
keywords: adaptive expressions
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 323234c45fd3a2bf7c2ef1556dee7d33ef6863dc
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94599844"
---
# <a name="adaptive-expressions"></a>Expressões adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os bots usam expressões adaptáveis para avaliar o resultado de uma condição com base nas informações de tempo de execução disponíveis na memória para a caixa de diálogo ou o sistema de [geração de idioma](bot-builder-concept-language-generation.md) . Essas avaliações determinam como o bot responde à entrada do usuário e a outros fatores que afetam a funcionalidade dele.

As expressões adaptáveis abordam essa necessidade básica, fornecendo uma linguagem de expressão adaptável que pode ser usada com o SDK do bot Framework e outros componentes de ia de conversação, como o [bot Framework Composer](https://github.com/microsoft/BotFramework-Composer), [geração de linguagem](bot-builder-concept-language-generation.md), [caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-Introduction.md)e [modelagem de cartões adaptáveis](https://docs.microsoft.com/adaptive-cards/templating).

Uma expressão adaptável pode conter um ou mais valores explícitos, [funções predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) ou funções personalizadas. Os consumidores de expressões adaptáveis também podem injetar funções adicionais compatíveis. Por exemplo, todos os modelos de geração de linguagem estão disponíveis como funções, bem como funções adicionais que só estão disponíveis no uso do componente de expressões adaptáveis.

## <a name="operators"></a>Operadores

As expressões adaptáveis dão suporte aos seguintes tipos de operadores e à seguinte sintaxe de expressão:

- aritmética
- comparação
- logical
- sintaxe de outros operadores e expressões

### <a name="arithmetic"></a>[Aritmético](#tab/arithmetic)

| Operador    |                                  Funcionalidade                                            |   Equivalente da função predefinida    |
|-----------|-------------------------------------------------------------------------------------------|-----------------------------------|
|+          | Adição. Exemplo: A + B                                                    |[adicionar][1]                           |
|-            | Subtração. Exemplo: A-B                                                |[sub][2]                           |
|unário +    | Valor positivo. Exemplo: +1, +A                                                    |N/D                                |
|unário -    | Valor negativo. Exemplo:-2,-B                                            |N/D                                |
|\*            | Multiplicação. Exemplo: A \* B                                            |[mul][3]                           |
|/            | Divisão. Exemplo: A / B                                                    |[div][4]                           |
|^            | Exponenciação. Exemplo: A ^ B                                            |[exp][5]                           |
|%            | Módulo. Exemplo: A % B                                                    |[mod][6]                           |

### <a name="comparison"></a>[Comparação](#tab/comparison)

| Operador    |                                  Funcionalidade                                            |   Equivalente da função predefinida    |
|-----------|-------------------------------------------------------------------------------------------|-----------------------------------|
|==            | Igual a. Exemplo: A == B                                                    |[equals][7]                        |
|\!=            | Não é igual a. Exemplo: A != B                                                |[not][8]([equals][7]())            |
|\>            | Maior que. Exemplo: A > B                                                   |[greater][9]                       |
|\<            | Menor que. Exemplo: A < B                                                        |[less][10]                         |
|\>=         | Maior que ou igual a. Exemplo: A >= B                                        |[greaterOrEquals][11]              |
|\<=            | Menor que ou igual a. Exemplo: A <= B                                            |[lessOrEquals][12]                 |

### <a name="logical"></a>[Lógico](#tab/logical)

| Operador    |                                  Funcionalidade                                            |   Equivalente da função predefinida    |
|-----------|-------------------------------------------------------------------------------------------|-----------------------------------|
|&&            |E. Exemplo: exp1 && exp2                                                    |[and][13]                          |
|\|\|        |Ou. Exemplo: exp1 \|\| exp2                                                    |[or][14]                           |
|\!            |Não. Exemplo: !exp1                                                            |[not][8]                           |

### <a name="other"></a>[Outros](#tab/other)

| Operador    |                                  Funcionalidade                                            |   Equivalente da função predefinida    |
|-----------|-------------------------------------------------------------------------------------------|-----------------------------------|
|&, +            |Operadores de concatenação. Os operandos sempre serão convertidos em cadeia de caracteres. Exemplos: A & B, 'foo' + ' bar' => 'foo bar', 'foo' + 3 => 'foo3', 'foo' + (3 + 3) => 'foo6'                |N/D                                |
|'            |Usado para encapsular um literal de cadeia de caracteres. Exemplo: 'myValue'                                                |N/D                                |
|"            |Usado para encapsular um literal de cadeia de caracteres. Exemplo: "myValue"                                                |N/D                                |
|\[\]            |Usado para referenciar um item em uma lista pelo respectivo índice. Exemplo: A[0]                                    |N/D                                |
|${}        |Usado para indicar uma expressão. Exemplo: ${A == B}.                                              |N/D                                |
|${}        |Usado para indicar uma variável na expansão do modelo. Exemplo: ${myVariable}                        |N/D                                |
|\(\)            |Impõe a ordem de precedência e agrupa as subexpressões em expressões maiores. Exemplo: (A + B) \* C    |N/D                                |
|\.            |Seletor de propriedade. Exemplo: myObject.Property1                                                    |N/D                                |
|\\            |Caractere de escape para modelos e expressões.                                               |N/D                                |

---

## <a name="variables"></a>Variáveis

As variáveis são sempre referenciadas pelo nome no formato `${myVariable}`.  Elas podem ser referenciadas pelo operador de seletor de propriedade na forma de `myParent.myVariable`, usando o operador de seleção de índice de item, como em `myParent.myList[0]`, ou pela função [getProperty()](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#getProperty).

Há duas variáveis especiais. **[]** representa uma lista vazia e **{}** representa um objeto vazio.

## <a name="explicit-values"></a>Valores explícitos

Os valores explícitos podem ser colocados entre aspas simples 'myExplicitValue' ou entre aspas duplas "myExplicitValue".

## <a name="functions"></a>Funções

Uma expressão adaptável tem uma ou mais funções. Para obter mais informações sobre as funções com suporte por expressões adaptáveis, consulte o artigo de referência de [funções predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) .

## <a name="bot-framework-composer"></a>Bot Framework Composer

O bot Framework Composer é uma tela de criação visual de software livre para desenvolvedores e equipes multidisciplinares criarem bots. O Composer usa expressões adaptáveis para criar, calcular e modificar valores. As expressões adaptáveis podem ser usadas em definições de modelo de geração de linguagem e como propriedades na tela de criação. Como visto no exemplo abaixo, as propriedades na memória também podem ser usadas em uma expressão adaptável. 

A expressão `(dialog.orderTotal + dialog.orderTax) > 50` adiciona os valores das propriedades `dialog.orderTotal` e `dialog.orderTax` , e avalia `True` se a soma é maior que 50 ou `False` se a soma é menor que 50.

Leia [fluxo de conversa e memória](https://docs.microsoft.com/composer/concept-memory) para obter mais informações sobre como as expressões são usadas na memória.

## <a name="language-generation"></a>Geração de linguagem

As expressões adaptáveis são usadas por sistemas LG (geração de linguagem) para avaliar as condições descritas em modelos de LG. No exemplo a seguir, a função de [junção](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#join) predefinida é usada para listar todos os valores na `recentTasks` coleção.

```lg
# RecentTasks
- IF: ${count(recentTasks) == 1}
    - Your most recent task is ${recentTasks[0]}. You can let me know if you want to add or complete a task.
- ELSEIF: ${count(recentTasks) == 2}
    - Your most recent tasks are ${join(recentTasks, ', ', ' and ')}. You can let me know if you want to add or complete a task.
- ELSEIF: ${count(recentTasks) > 2}
    - Your most recent ${count(recentTasks)} tasks are ${join(recentTasks, ', ', ' and ')}. You can let me know if you want to add or complete a task.
- ELSE:
    - You don't have any tasks.
```

Leia a seção [usando a função predefinida em variações](../file-format/bot-builder-lg-file-format.md#using-prebuilt-functions-in-variations) do artigo [formato de arquivo. LG](../file-format/bot-builder-lg-file-format.md) para obter mais informações.

## <a name="adaptive-cards-templating"></a>Modelagem de cartões adaptáveis

[Modelos de cartões adaptáveis](https://docs.microsoft.com/adaptive-cards/templating/) podem ser usados por desenvolvedores de bots e outras tecnologias para separar dados do layout em um cartão adaptável. Os desenvolvedores podem fornecer [dados embutidos](https://docs.microsoft.com/adaptive-cards/templating/language#option-a-inline-data) com a `AdaptiveCard` carga ou a abordagem mais comum de [separar os dados do modelo](https://docs.microsoft.com/adaptive-cards/templating/language#option-b-separating-the-template-from-the-data).

Por exemplo, digamos que você tenha os seguintes dados:

```json
{
    "id": "1291525457129548",
    "status": 4,
    "author": "Matt Hidinger",
    "message": "{\"type\":\"Deployment\",\"buildId\":\"9542982\",\"releaseId\":\"129\",\"buildNumber\":\"20180504.3\",\"releaseName\":\"Release-104\",\"repoProvider\":\"GitHub\"}",
    "start_time": "2018-05-04T18:05:33.3087147Z",
    "end_time": "2018-05-04T18:05:33.3087147Z"
}
```
A `message` propriedade é uma cadeia de caracteres serializada em JSON. Para acessar os valores dentro da cadeia de caracteres, a função predefinida [JSON](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#json) pode ser chamada:

```json
{
    "type": "TextBlock",
    "text": "${json(message).releaseName}"
}
```

E resultará no seguinte objeto:

```json
{
    "type": "TextBlock",
    "text": "Release-104"
}
```

Para obter mais informações e exemplos, consulte a [documentação de modelagem de cartões adaptáveis](https://docs.microsoft.com/adaptive-cards/templating/).

## <a name="additional-resources"></a>Recursos adicionais

- Pacote [NuGet AdaptiveExpressions](https://www.nuget.org/packages/AdaptiveExpressions) para C#
- pacote de [expressões adaptáveis NPM](https://www.npmjs.com/package/adaptive-expressions) para JavaScript
- [Funções predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) com suporte da biblioteca de expressões adaptáveis
- [Referência da API do C#](https://docs.microsoft.com/dotnet/api/adaptiveexpressions)
- [Referência da API JavaScript](https://docs.microsoft.com/javascript/api/adaptive-expressions)

[1]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#add
[2]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#sub
[3]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#mul
[4]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#div
[5]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#exp
[6]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#mod
[7]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#equals
[8]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#not
[9]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#greater
[10]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#less
[11]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#greaterOrEquals
[12]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#lessOrEquals
[13]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#and
[14]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#or
[15]:https://botbuilder.myget.org/feed/botbuilder-declarative/package/nuget/Microsoft.Bot.Builder.Expressions
[20]:https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/language-generation/README.md
