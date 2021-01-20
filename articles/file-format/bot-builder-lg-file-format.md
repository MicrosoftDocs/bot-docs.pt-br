---
title: Formato de arquivo .lg – Serviço de Bot
description: Referência do formato de arquivo .lg
keywords: lg file format, reference, language generation
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: bf209ede3342c6fc080a6ce20c2c57326f54b266
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576838"
---
# <a name="lg-file-format"></a>Formato de arquivo .lg

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O arquivo. LG descreve modelos de geração de linguagem com referências de entidade e sua composição. Este artigo aborda os vários conceitos expressos com o formato de arquivo. LG.

## <a name="special-characters"></a>Caracteres Especiais

### <a name="comments"></a>Comentários

Use **>** para criar um comentário. Todas as linhas que tenham esse prefixo serão ignoradas pelo analisador.

```lg
> This is a comment.
```

### <a name="escape-character"></a>Caractere de escape

Use **\\** como um caractere de escape.

```lg
# TemplateName
- You can say cheese and tomato \[toppings are optional\]
```

## <a name="arrays-and-objects"></a>Matrizes e objetos

### <a name="create-an-array"></a>Criar uma matriz

Para criar uma matriz, use a sintaxe **$ {[object1, object2,...]}** . Por exemplo, esta expressão:

```lg
${['a', 'b', 'c']}
```

Retorna a matriz `['a', 'b', 'c']` .

### <a name="create-an-object"></a>Criar um objeto

Para criar um objeto, use **$ {{key1: value1, Key2: value2,...}}** . Por exemplo, esta expressão:

```lg
${{user: {name: "Wilson", age: 27}}}
```

Retorna o seguinte objeto JSON:

```json
{
  "user": {
    "name": "Wilson",
    "age": 27
  }
}
```

## <a name="templates"></a>Modelos

Os **modelos** são o conceito básico do sistema de geração de linguagem. Cada modelo tem um nome e um dos seguintes:

- uma lista de valores de texto de variação one-of
- uma definição de conteúdo estruturado
- uma coleção de condições, cada uma com:
  - uma [expressão adaptável][3]
  - uma lista de valores de texto de variação one-of por condição

### <a name="template-names"></a>Nomes de modelo

Os nomes de modelo diferenciam maiúsculas de minúsculas e podem conter apenas letras, sublinhados e números. Veja a seguir um exemplo de um modelo chamado `TemplateName` .

```lg
# TemplateName
```

Os modelos não podem começar com um número e qualquer parte de um nome de modelo é dividida por **.** Não é possível iniciar com um número.

### <a name="template-response-variations"></a>Variações de resposta de modelo

As variações são expressas como uma lista Markdown. Você pode prefixar cada variação usando o caractere **-** , **'** ou **+** .

```lg
# Template1
- text variation 1
- text variation 2
- one
- two

# Template2
* text variation 1
* text variation 2

# Template3
+ one
+ two
```

### <a name="simple-response-template"></a>Modelo de resposta simples

Um modelo de resposta simples inclui uma ou mais variações de texto que são usadas para composição e expansão. Uma das variações fornecidas será selecionada aleatoriamente pela biblioteca LG.

Este é um exemplo de um modelo simples que inclui duas variações.

```lg
> Greeting template with two variations.
# GreetingPrefix
- Hi
- Hello
```

### <a name="conditional-response-template"></a>Modelo de resposta condicional

Os modelos de resposta condicional permitem criar um conteúdo selecionado com base em uma condição. Todas as condições são expressas por meio de [expressões adaptáveis][3].

> [!IMPORTANT]
> Os modelos condicionais não podem ser aninhados em um só modelo de resposta condicional. Use a composição em um [modelo de resposta estruturada](../language-generation/language-generation-structured-response-template.md) para aninhar condicionais.

#### <a name="if-else-template"></a>Modelo if-else

O modelo if-else permite criar um modelo que escolhe uma coleção com base em uma ordem de condições em cascata. A avaliação é feita de cima para baixo e para quando uma condição é avaliada como `true` ou o bloco ELSE é atingido.

As expressões condicionais são colocadas entre chaves **${}** . Este é um exemplo que mostra uma definição de modelo de resposta condicional IF ELSE simples.

```lg
> time of day greeting reply template with conditions.
# timeOfDayGreeting
- IF: ${timeOfDay == 'morning'}
    - good morning
- ELSE:
    - good evening
```

Este é outro exemplo que mostra uma definição de modelo de resposta condicional if-else. Observe que você pode incluir referências a outros modelos de resposta simples ou condicional na variação de uma das condições.

```lg
# timeOfDayGreeting
- IF: ${timeOfDay == 'morning'}
    - ${morningTemplate()}
- ELSEIF: ${timeOfDay == 'afternoon'}
    - ${afternoonTemplate()}
- ELSE:
    - I love the evenings! Just saying. ${eveningTemplate()}
```

#### <a name="switch-template"></a>Modelo de opção

O modelo de opção permite projetar um modelo que corresponda ao valor de uma expressão a uma cláusula CASE e produz a saída com base nesse caso. As expressões de condição são colocadas entre chaves **${}** .

Veja como você pode especificar um bloco SWITCH CASE DEFAULT na LG.

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

Veja um exemplo mais complicado de SWITCH CASE DEFAULT:

```lg
> Note: Any of the cases can include reference to one or more templates.
# greetInAWeek
- SWITCH: ${dayOfWeek(utcNow())}
- CASE: ${0}
    - Happy Sunday!
-CASE: ${6}
    - Happy Saturday!
-DEFAULT:
    - ${apology-phrase()}, ${defaultResponseTemplate()}
```

> [!NOTE]
> Como os modelos condicionais, os modelos de comutador não podem ser aninhados.

### <a name="structured-response-template"></a>Modelo de resposta estruturada

Os modelos de resposta estruturada permitem definir uma estrutura complexa que dá suporte à principal funcionalidade de LG, como criação de modelos, composição e substituição, deixando a interpretação da resposta estruturada para o chamador da biblioteca LG.

Para aplicativos de bot, damos suporte nativo à:

- definição de atividade
- definição de cartão

Leia mais sobre os [modelos de resposta estruturada](../language-generation/language-generation-structured-response-template.md) para obter mais informações.

## <a name="template-composition-and-expansion"></a>Composição e expansão de modelo

### <a name="references-to-templates"></a>Referências a modelos

O texto de variação pode incluir referências a outro modelo nomeado para auxiliar na composição e na resolução de respostas sofisticadas. Referências a outros modelos nomeados são indicadas por meio de chaves, como **${\<TemplateName>()}** .

```lg
> Example of a template that includes composition reference to another template.
# GreetingReply
- ${GreetingPrefix()}, ${timeOfDayGreeting()}

# GreetingPrefix
- Hi
- Hello

# timeOfDayGreeting
- IF: ${timeOfDay == 'morning'}
    - good morning
- ELSEIF: ${timeOfDay == 'afternoon'}
    - good afternoon
- ELSE:
    - good evening
```

A chamada do modelo `GreetingReply` pode resultar em uma das seguintes resoluções de expansão:

```lg
Hi, good morning
Hi, good afternoon
Hi, good evening
Hello, good morning
Hello, good afternoon
Hello, good evening
```

## <a name="entities"></a>Entidades

Quando usadas diretamente em um texto de variação one-of, as referências de entidade são indicadas colocando-as entre chaves, como ${`entityName`}, ou sem chaves quando usadas como um parâmetro.

As entidades podem ser usadas como um parâmetro:

- dentro de uma [função predefinida][4]
- dentro de uma condição em um [modelo de resposta condicional](#conditional-response-template)
- para a [chamada de resolução de modelo](#parametrization-of-templates)

## <a name="using-prebuilt-functions-in-variations"></a>Como usar funções predefinidas em variações

As [funções predefinidas][4] compatíveis com as [expressões adaptáveis][3] também podem ser usadas embutidas em um texto de variação one-of para obter uma composição de texto ainda mais eficiente. Para usar uma expressão embutida, basta encapsulá-la entre chaves.

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

O exemplo acima usa a função predefinida [join][5] para listar todos os valores na coleção `recentTasks`.

Determinados modelos e funções predefinidas compartilham a assinatura de invocação, mas um modelo não pode ter o mesmo nome de uma função predefinida.

 Um nome de modelo não deve corresponder a um nome de função predefinida. A função predefinida tem precedência. Para evitar esses conflitos, você pode preceder `lg.` ao referenciar o nome do modelo. Por exemplo:

```lg
> Custom length function with one parameter.
# length(a)
- This is use's customized length function

# myfunc1
> will call prebuilt function length, and return 2
- ${length('hi')}

# mufunc2
> this calls the lg template and output 'This is use's customized length function'
- ${lg.length('hi')}
```

## <a name="multiline-text-in-variations"></a>Texto multilinha em variações

Cada variação one-of pode incluir um texto multilinha entre aspas triplas.

```lg
# MultiLineExample
    - ```This is a multiline list
        - one
        - two
        ```
    - ```This is a multiline variation
        - three
        - four
    ```
```

A variação multilinha pode solicitar a expansão do modelo e a substituição da entidade colocando a operação solicitada entre chaves, ${}.

```lg
# MultiLineExample
    - ```
        Here is what I have for the order
        - Title: ${reservation.title}
        - Location: ${reservation.location}
        - Date/ time: ${reservation.dateTimeReadBack}
    ```
```

Com o suporte multilinha, você pode fazer com que o subsistema de Geração de Linguagem resolva totalmente um JSON ou um XML complexo (como um texto encapsulado de SSML para controlar a resposta falada do bot).

## <a name="parametrization-of-templates"></a>Parametrização de modelos

Para auxiliar na capacidade de reutilização contextual, os modelos podem ser parametrizados. Chamadores diferentes do modelo podem passar valores diferentes para uso na resolução de expansão.

```lg
# timeOfDayGreetingTemplate (param1)
- IF: ${param1 == 'morning'}
    - good morning
- ELSEIF: ${param1 == 'afternoon'}
    - good afternoon
- ELSE:
    - good evening

# morningGreeting
- ${timeOfDayGreetingTemplate('morning')}

# timeOfDayGreeting
- ${timeOfDayGreetingTemplate(timeOfDay)}
```

## <a name="importing-external-references"></a>Como importar referências externas

Você pode dividir os modelos de geração de linguagem em arquivos separados e referenciar um modelo de um arquivo em outro. Use links no estilo Markdown para importar os modelos definidos em outro arquivo.

```lg
[Link description](filePathOrUri)
```

Todos os modelos definidos no arquivo de destino serão recebidos. Verifique se os nomes de modelo são exclusivos (ou têm o namespace `# \<namespace>.\<templatename>`) em todos os arquivos que estão sendo recebidos.

```lg
[Shared](../shared/common.lg)
```

## <a name="functions-injected-by-lg"></a>Funções injetadas pela LG

As [expressões adaptáveis][3] fornecem a capacidade de injetar um conjunto personalizado de funções. Leia [Funções injetadas da biblioteca LG][13] para obter mais informações.

## <a name="options"></a>Opções

Os desenvolvedores podem definir opções de analisador para personalizar ainda mais a forma como a entrada é avaliada. Use a notação `> !#` para definir as opções do analisador.

> [!IMPORTANT]
>
> A última configuração encontrada no arquivo prevalece sobre todas as configurações anteriores encontradas no mesmo documento.

### <a name="strict-option"></a>Opção strict

Os desenvolvedores que não desejam permitir um resultado nulo para um resultado avaliado como nulo poderão implementar a opção **strict**. Veja abaixo um exemplo de uma opção strict simples:

```lg
> !# @strict = true
# template
- hi
```

Se a opção strict estiver ativada, os erros nulos vão gerar uma mensagem amigável.

```lg
# welcome
- hi ${name}
```

Se o nome for nulo, o diagnóstico será **'name' avaliado como nulo. [welcome] Erro ao avaliar '- olá, ${name}'.** . Se strict for definido como falso ou não for definido, um resultado compatível será fornecido. A amostra acima produzirá **olá, nulo**.

### <a name="replacenull-option"></a>Opção replaceNull

Os desenvolvedores podem criar delegados para substituir valores nulos em expressões avaliadas usando a opção **replaceNull** :

```lg
> !# @replaceNull = ${path} is undefined
```

No exemplo acima, a entrada nula na variável `path` será substituída por **${path} é indefinido**. A seguinte entrada, em que `user.name` é nulo:

```lg
hi ${user.name}
```

Resultará em **olá, user.name é indefinido**.

### <a name="linebreakstyle-option"></a>Opção lineBreakStyle

Os desenvolvedores podem definir opções de como o sistema LG renderiza as quebras de linha usando a opção **lineBreakStyle**. Atualmente, há suporte para dois modos:

- `default`: as quebras de linha no texto multilinha criam quebras de linha normais.
- `markdown`: as quebras de linha no texto multilinha serão automaticamente convertidas em duas linhas para criar outra linha

O exemplo abaixo mostra como definir a opção lineBreakStyle como `markdown`:

```lg
> !# @lineBreakStyle = markdown
```

### <a name="namespace-option"></a>Opção de namespace

Você pode registrar um namespace para os modelos LG que deseja exportar. Se não houver nenhum namespace especificado, o namespace será definido como o nome de arquivo sem uma extensão.

O seguinte exemplo mostra como definir a opção de namespace como `foo`:

```lg
> !# @Namespace = foo
```

### <a name="exports-option"></a>Opção de exportações

Você pode especificar uma lista de modelos LG para exportar. Os modelos exportados podem ser chamados como funções predefinidas.

O seguinte exemplo mostra como definir a opção de exportações como `template1, template2`:

```lg
> !# @Namespace = foo
> !# @Exports = template1, template2

# template1(a, b)
- ${a + b}

# template2(a, b)
- ${join(a, b)}
```

Use `foo.template1(1,2), foo.template2(['a', 'b', 'c'], ',')` para chamar esses modelos exportados.

## <a name="additional-resources"></a>Recursos adicionais

- [Referência da API do C#](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.languagegeneration)
- [Referência da API JavaScript](https://docs.microsoft.com/javascript/api/botbuilder-lg)
- Leia [depurar com ferramentas adaptáveis](../bot-service-debug-adaptive-tools.md) para saber como analisar e depurar arquivos. LG.

[1]:https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md
[3]:../v4sdk/bot-builder-concept-adaptive-expressions.md
[4]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md
[5]:../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#join
[6]:https://github.com/microsoft/botframework-cli/tree/master/packages/chatdown
[7]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md
[8]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/examples/CardExamples.chat
[9]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md#message-commands
[10]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md#message-cards
[11]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md#message-attachments
[12]:https://github.com/microsoft/botframework-cli/blob/master/packages/chatdown/docs/chatdown-format.md
[13]:../language-generation/functions-injected-from-language-generation.md
