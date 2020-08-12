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
ms.openlocfilehash: 5092a86a57504035caa7085dbe5cf7022c0960b9
ms.sourcegitcommit: 7bf72623d9abf15e1444e8946535724f500643c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143195"
---
# <a name="adaptive-expressions"></a>Expressões adaptáveis

[!INCLUDE[applies-to](../includes/applies-to.md)]

Os bots usam expressões para avaliar o resultado de uma condição com base nas informações de runtime disponíveis na memória para o diálogo ou no sistema de [Geração de Linguagem](bot-builder-concept-language-generation.md). Essas avaliações determinam como o bot responde à entrada do usuário e a outros fatores que afetam a funcionalidade dele.

As expressões adaptáveis abordam essa necessidade básica, fornecendo uma linguagem de expressão adaptável que pode ser usada com o SDK do Bot Framework e outros componentes da IA de conversa, como o [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer), a [Geração de Linguagem](bot-builder-concept-language-generation.md), os [diálogos adaptáveis](bot-builder-adaptive-dialog-Introduction.md) e os [Cartões Adaptáveis](https://docs.microsoft.com/adaptive-cards/).

Uma expressão adaptável pode conter um ou mais valores explícitos, [funções predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) ou funções personalizadas. Os consumidores de expressões adaptáveis também podem injetar funções adicionais compatíveis. Por exemplo, todos os modelos de Geração de Linguagem estão disponíveis como funções, bem como funções adicionais que só estão disponíveis no uso do componente de expressões adaptáveis.

## <a name="operators"></a>Operadores

As expressões adaptáveis dão suporte aos seguintes tipos de operadores e à seguinte sintaxe de expressão:

- [aritmético](#arithmetic-operators)
- [comparação](#comparison-operators)
- [lógico](#logical-operators)
- [aritmético](#other-operators-and-expression-syntax)

### <a name="arithmetic-operators"></a>Operadores aritméticos

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

### <a name="comparison-operators"></a>Operadores de comparação

| Operador    |                                  Funcionalidade                                            |   Equivalente da função predefinida    |
|-----------|-------------------------------------------------------------------------------------------|-----------------------------------|
|==            | Igual a. Exemplo: A == B                                                    |[equals][7]                        |
|\!=            | Não é igual a. Exemplo: A != B                                                |[not][8]([equals][7]())            |
|\>            | Maior que. Exemplo: A > B                                                   |[greater][9]                       |
|\<            | Menor que. Exemplo: A < B                                                        |[less][10]                         |
|\>=         | Maior que ou igual a. Exemplo: A >= B                                        |[greaterOrEquals][11]              |
|\<=            | Menor que ou igual a. Exemplo: A <= B                                            |[lessOrEquals][12]                 |

### <a name="logical-operators"></a>Operadores lógicos

| Operador    |                                  Funcionalidade                                            |   Equivalente da função predefinida    |
|-----------|-------------------------------------------------------------------------------------------|-----------------------------------|
|&&            |E. Exemplo: exp1 && exp2                                                    |[and][13]                          |
|\|\|        |Ou. Exemplo: exp1 \|\| exp2                                                    |[or][14]                           |
|\!            |Não. Exemplo: !exp1                                                            |[not][8]                           |


### <a name="other-operators-and-expression-syntax"></a>Outros operadores e sintaxe de expressão

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

## <a name="variables"></a>Variáveis

As variáveis são sempre referenciadas pelo nome no formato `${myVariable}`.  Elas podem ser referenciadas pelo operador de seletor de propriedade na forma de `myParent.myVariable`, usando o operador de seleção de índice de item, como em `myParent.myList[0]`, ou pela função [getProperty()](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#getProperty).

Há duas variáveis especiais. **[]** representa uma lista vazia e **{}** representa um objeto vazio.

## <a name="explicit-values"></a>Valores explícitos

Os valores explícitos podem ser colocados entre aspas simples 'myExplicitValue' ou entre aspas duplas "myExplicitValue".

## <a name="additional-resources"></a>Recursos adicionais

- Pacote [NuGet AdaptiveExpressions](https://www.nuget.org/packages/AdaptiveExpressions) para C#
- Pacote [npm adaptive-expressions](https://www.npmjs.com/package/adaptive-expressions) para JavaScript
- [Funções predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) compatíveis com a biblioteca de Expressões Adaptáveis
- [Referência do C #API](https://docs.microsoft.com/dotnet/api/adaptiveexpressions)
- [Referência de API de JavaScript](https://docs.microsoft.com/javascript/api/adaptive-expressions)   
<!--- [Extend functions](./extend-functions.md)-->

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
