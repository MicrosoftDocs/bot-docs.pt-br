---
title: Personalizar a experiência do usuário com linguagem padrão | Microsoft Docs
description: Saiba como personalizar os prompts do FormFlow e substituir os modelos do FormFlow usando linguagem padrão com o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 032d3c9553c5be774bf5d71fb2477d5a5689ca40
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70297910"
---
# <a name="customize-user-experience-with-pattern-language"></a>Personalizar a experiência do usuário com linguagem padrão

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Quando você personaliza um prompt ou substituir um modelo padrão, você pode usar a linguagem padrão para especificar o conteúdo e/ou formato do prompt. 

## <a name="prompts-and-templates"></a>Prompts e modelos

Um [aviso][promptAttribute] define a mensagem que é enviada ao usuário para solicitar uma informação ou pedir uma confirmação. Personalize um aviso usando o [atributo Prompt](bot-builder-dotnet-formflow-advanced.md#customize-prompts-using-the-prompt-attribute) ou implicitamente por meio de [IFormBuilder<T>.Field][field]. 

Formulários usam modelos para construir automaticamente os prompts e outras coisas como obter ajuda. Você pode substituir o modelo padrão de uma classe ou um campo usando o [atributo Template](bot-builder-dotnet-formflow-advanced.md#customize-prompts-using-the-template-attribute). 

> [!TIP]
> A classe [FormConfiguration.Templates][formConfiguration] define um conjunto de modelos internos que fornecem bons exemplos de como usar a linguagem padrão.

## <a name="elements-of-pattern-language"></a>Elementos da linguagem padrão

A linguagem padrão usa chaves (`{}`) para identificar elementos que serão substituídos em tempo de execução por valores reais. Esta tabela lista os elementos da linguagem padrão.

| Elemento | DESCRIÇÃO |
|----|----|
| `{<format>}` | Mostra o valor do campo atual (o campo ao qual o atributo se aplica). |
| `{&}` | Mostra a descrição do campo atual (salvo especificação em contrário, esse é o nome do campo). |
| `{<field><format>}` | Mostra o valor do campo nomeado. | 
| `{&<field>}` | Mostra a descrição do campo nomeado. |
| <code>{&#124;&#124;}</code> | Mostra a escolha ou escolhas atuais, que poderia ser o valor atual de um campo, “Nenhuma preferência” ou os valores de uma enumeração. |
| `{[{<field><format>} ...]}` | Mostra uma lista de valores dos campos nomeados usando [Separator][separator] e [LastSeparator][lastSeparator] para separar os valores individuais na lista. |
| `{*}` | Mostra uma linha para cada campo ativo; cada linha contém a descrição do campo e o valor atual. | 
| `{*filled}` | Mostra uma linha para cada campo ativo que contém um valor real; cada linha contém a descrição do campo e o valor atual. |
| `{<nth><format>}` | Um especificador de formato C# normal que se aplica ao enésimo argumento de um modelo. Para obter a lista de argumentos disponíveis, confira [TemplateUsage][templateUsage]. |
| `{?<textOrPatternElement>...}` | Substituição condicional. Se todos os elementos padrão chamados tiverem valores, os valores são substituídos e toda a expressão é usada. |

Para os elementos listados acima:

- O espaço reservado `<field>` é o nome de um campo em sua classe de formulário. Por exemplo, se sua classe contém um campo com o nome `Size`, você poderia especificar `{Size}` como o elemento padrão. 

- Uma reticências (`"..."`) dentro de um padrão de elemento indica que o elemento pode conter vários valores.

- O espaço reservado `<format>` é um especificador de formato C#. Por exemplo, se a classe contém um campo com o nome `Rating` e do tipo `double`, você poderia especificar `{Rating:F2}` como o elemento padrão para mostrar dois dígitos de precisão.

- O espaço reservado `<nth>` referencia o enésimo argumento de um modelo.

### <a name="pattern-language-within-a-prompt-attribute"></a>Linguagem padrão dentro de um atributo de Prompt

Este exemplo usa o elemento `{&}` para mostrar a descrição do campo `Sandwich` e o elemento `{||}` para mostrar a lista de opções para esse campo.

[!code-csharp[Patterns example](../includes/code/dotnet-formflow-pattern-language.cs#patterns1)]

Essa é a saída resultante:

```console
What kind of sandwich would you like?
1. BLT
2. Black Forest Ham
3. Buffalo Chicken
4. Chicken And Bacon Ranch Melt
5. Cold Cut Combo
6. Meatball Marinara
7. Oven Roasted Chicken
8. Roast Beef
9. Rotisserie Style Chicken
10. Spicy Italian
11. Steak And Cheese
12. Sweet Onion Teriyaki
13. Tuna
14. Turkey Breast
15. Veggie
>
```

## <a name="formatting-parameters"></a>Parâmetros de formatação

Os prompts e modelos oferecem suporte a esses parâmetros de formatação.

| Uso | DESCRIÇÃO |
|----|----|
| `AllowDefault` | Aplica-se aos elementos padrão <code>{&#124;&#124;}</code>. Determina se o formulário deve mostrar o valor atual do campo como uma opção possível. Se `true`, o valor atual é mostrado como um valor possível. O padrão é `true`. |
| `ChoiceCase` | Aplica-se aos elementos padrão <code>{&#124;&#124;}</code>. Determina se o texto de cada opção é normalizado (por exemplo, se a primeira letra de cada palavra é escrita com letra maiúscula). O padrão é `CaseNormalization.None`. Para obter os valores possíveis, confira [CaseNormalization][caseNormalization]. |
| `ChoiceFormat` | Aplica-se aos elementos padrão <code>{&#124;&#124;}</code>. Determina se deve mostrar uma lista de opções como uma lista numerada ou como uma lista com marcadores. Para obter uma lista numerada, defina `ChoiceFormat` para `{0}` (padrão). Para obter uma lista com marcadores, defina `ChoiceFormat` para `{1}`. |
| `ChoiceLastSeparator` | Aplica-se aos elementos padrão <code>{&#124;&#124;}</code>. Determina se uma lista embutida de opções inclui um separador antes da última opção. |
| `ChoiceParens` | Aplica-se aos elementos padrão <code>{&#124;&#124;}</code>. Determina se uma lista embutida de opções é mostrada entre parênteses. Se `true`, a lista de opções é mostrada entre parênteses. O padrão é `true`. |
| `ChoiceSeparator` | Aplica-se aos elementos padrão <code>{&#124;&#124;}</code>. Determina se uma lista embutida de opções inclui um separador antes de cada opção, exceto a última. | 
| `ChoiceStyle` | Aplica-se aos elementos padrão <code>{&#124;&#124;}</code>. Determina se a lista de opções é mostrada embutida ou por linha. O padrão é `ChoiceStyleOptions.Auto` que determina em tempo de execução se a opção é mostrada de forma embutida ou em uma lista. Para obter os valores possíveis, confira [ChoiceStyleOptions][choiceStyleOptions]. |
| `Feedback` | Aplica-se a somente os prompts. Determina se o formulário reflete a escolha do usuário para indicar que o formulário entendeu a seleção. O padrão é `FeedbackOptions.Auto` que reflete a entrada do usuário somente se parte dela não for compreendida. Para obter os valores possíveis, confira [FeedbackOptions][feedbackOptions]. |
| `FieldCase` | Determina se o texto da descrição do campo é normalizado (por exemplo, se a primeira letra de cada palavra é escrita com letra maiúscula). O padrão é `CaseNormalization.Lower` que converte a descrição em minúsculas. Para obter os valores possíveis, confira [CaseNormalization][caseNormalization]. |
| `LastSeparator` | Aplica-se aos elementos padrão `{[]}`. Determina se uma lista matriz de itens inclui um separador antes do último item. |
| `Separator` | Aplica-se aos elementos padrão `{[]}`. Determina se uma lista matriz de itens inclui um separador antes de cada item na matriz, exceto o último item. |
| `ValueCase` | Determina se o texto do valor do campo é normalizado (por exemplo, se a primeira letra de cada palavra é escrita com letra maiúscula). O padrão é `CaseNormalization.InitialUpper` que converte a primeira letra de cada palavra em maiúscula. Para obter os valores possíveis, confira [CaseNormalization][caseNormalization]. |

### <a name="prompt-attribute-with-formatting-parameter"></a>Atributo prompt com parâmetro de formatação 

Este exemplo usa o parâmetro `ChoiceFormat` para especificar que a lista de opções deve ser exibida como uma lista com marcadores.

[!code-csharp[Patterns example](../includes/code/dotnet-formflow-pattern-language.cs#patterns2)]

Essa é a saída resultante:

```console
What kind of sandwich would you like?
* BLT
* Black Forest Ham
* Buffalo Chicken
* Chicken And Bacon Ranch Melt
* Cold Cut Combo
* Meatball Marinara
* Oven Roasted Chicken
* Roast Beef
* Rotisserie Style Chicken
* Spicy Italian
* Steak And Cheese
* Sweet Onion Teriyaki
* Tuna
* Turkey Breast
* Veggie
>
```

## <a name="sample-code"></a>Exemplo de código

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Recursos adicionais

- [Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md)
- [Recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md)
- [Personalizar um formulário usando FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Localizar o conteúdo do formulário](bot-builder-dotnet-formflow-localize.md)
- [Definir um formulário usando o esquema JSON](bot-builder-dotnet-formflow-json-schema.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>

[promptAttribute]: /dotnet/api/microsoft.bot.builder.formflow.promptattribute

[field]: /dotnet/api/microsoft.bot.builder.formflow.iformbuilder-1.field

[formConfiguration]: /dotnet/api/microsoft.bot.builder.formflow.formconfiguration

[separator]: /dotnet/api/microsoft.bot.builder.formflow.advanced.templatebaseattribute.separator

[lastSeparator]: /dotnet/api/microsoft.bot.builder.formflow.advanced.templatebaseattribute.lastseparator

[templateUsage]: /dotnet/api/microsoft.bot.builder.formflow.templateusage

[caseNormalization]: /dotnet/api/microsoft.bot.builder.formflow.casenormalization

[choiceStyleOptions]: /dotnet/api/microsoft.bot.builder.formflow.choicestyleoptions

[feedbackOptions]: /dotnet/api/microsoft.bot.builder.formflow.feedbackoptions
