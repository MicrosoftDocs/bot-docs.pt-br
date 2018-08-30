---
title: Recursos avançados do FormFlow | Microsoft Docs
description: Saiba como personalizar a experiência do usuário usando o FormFlow e o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6637876b016b8680fe722602f530a0c6b0ddfc5a
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905400"
---
# <a name="advanced-features-of-formflow"></a>Recursos avançados do FormFlow

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md) descreve uma implementação básica de FormFlow que proporciona uma experiência do usuário relativamente genérica. Para proporcionar uma experiência de usuário mais personalizada usando o FormFlow, especifique o estado inicial do formulário, adicione a lógica de negócios para gerenciar as interdependências entre os campos e processar a entrada do usuário, e use atributos para personalizar avisos, substituir modelos, indicar campos opcionais, corresponder e validar a entrada do usuário. 

## <a name="specify-initial-form-state-and-entities"></a>Especificar entidades e o estado inicial do formulário

Quando você inicia um [FormDialog][formDialog], pode passar, opcionalmente, uma instância do seu estado. Se você passar uma instância do seu estado, por padrão, o FormFlow ignorará as etapas para todos os campos que já contêm valores; o usuário não receberá uma solicitação para esses campos. Para forçar o formulário a solicitar ao usuário o preenchimento de todos os campos (incluindo os campos que já contêm valores no estado inicial), passe [FormOptions.PromptFieldsWithValues][promptFieldsWithValues] ao iniciar o `FormDialog`. Se um campo contiver um valor inicial, o aviso usará esse valor como o valor padrão.

Você também pode passar entidades de [LUIS](https://luis.ai/) para associar ao estado. Se `EntityRecommendation.Type` for um caminho até um campo em sua classe C#, o `EntityRecommendation.Entity` passará pelo reconhecedor para associar ao seu campo. O FormFlow ignorará as etapas para todos os campos associados a uma entidade; o usuário não receberá uma solicitação para esses campos. 

## <a name="add-business-logic"></a>Adicionar lógica de negócios 

Para lidar com as interdependências entre campos de formulário ou aplicar uma lógica específica durante o processo de obtenção ou definição de um valor de campo, especifique a lógica de negócios dentro de uma função de validação. Uma função de validação permite que você manipule o estado e retorne um objeto [ValidateResult][validateResult], o qual pode conter: 

- uma cadeia de caracteres de comentários que descreve o motivo pelo qual um valor é inválido
- um valor transformado
- um conjunto de opções para ilustrar um valor

Este exemplo de código mostra uma função de validação para o campo `Toppings`. Se a entrada para o campo contiver o valor de enumeração `ToppingOptions.Everything`, a função garantirá que o valor do campo `Toppings` contenha a lista completa de itens.

[!code-csharp[Validation function](../includes/code/dotnet-formflow-advanced.cs#validationFunction)]

Além da função de validação, você pode adicionar o atributo [Term](#match-user-input-using-the-terms-attribute) para corresponder as expressões de usuário, como "tudo" ou "não".

[!code-csharp[Terms for Toppings](../includes/code/dotnet-formflow-advanced.cs#toppingsTerms)]

Com a função de validação mostrada acima, este trecho mostra a interação entre o bot e o usuário quando o usuário solicita "tudo, exceto pimentões". 

```console
Please select one or more toppings (current choice: No Preference)
 1. Everything
 2. Avocado
 3. Banana Peppers
 4. Cucumbers
 5. Green Bell Peppers
 6. Jalapenos
 7. Lettuce
 8. Olives
 9. Pickles
 10. Red Onion
 11. Spinach
 12. Tomatoes
> everything but jalapenos
For sandwich toppings you have selected Avocado, Banana Peppers, Cucumbers, Green Bell Peppers, Lettuce, Olives, Pickles, Red Onion, Spinach, and Tomatoes.
```

## <a name="formflow-attributes"></a>Atributos de FormFlow

Adicione esses atributos de C# à sua classe para personalizar o comportamento de um diálogo FormFlow.

| Atributo | Finalidade |
|----|----| 
| [Describe][describeAttribute] | Alterar como um campo ou valor é mostrado em um modelo ou um cartão |
| [Numeric][numericAttribute] | Restringe os valores aceitos de um campo numérico |
| [Optional][optionalAttribute] | Marca um campo como opcional |
| [Pattern][patternAttribute] | Define uma expressão regular para validar um campo de cadeia de caracteres |
| [Prompt][promptAttribute] | Define o aviso para um campo |
| [Template][templateAttribute] | Define o modelo a ser usado para gerar avisos ou valores nos avisos |
| [Terms][termsAttribute] | Define os termos de entrada que correspondem a um campo ou valor |

## <a name="customize-prompts-using-the-prompt-attribute"></a>Personalizar avisos usando o atributo Prompt

Os avisos padrão são gerados automaticamente para cada campo em seu formulário, mas você pode especificar um aviso personalizado para qualquer campo usando o atributo `Prompt`. Por exemplo, se o aviso padrão para o campo `SandwichOrder.Sandwich` for "Selecionar um sanduíche", adicione o atributo `Prompt` para especificar um aviso personalizado para esse campo.

[!code-csharp[Prompt attribute](../includes/code/dotnet-formflow-advanced.cs#promptAttribute)]

Este exemplo usa [padrão de linguagem](bot-builder-dotnet-formflow-pattern-language.md) para preencher dinamicamente o aviso com dados de formulário em tempo de execução: `{&}` é substituído pela descrição do campo, e `{||}` é substituído pela lista de opções na enumeração. 

> [!NOTE]
> Por padrão, a descrição de um campo é gerada desde o nome do campo. Para especificar uma descrição personalizada para um campo, adicione o atributo `Describe`.

Este trecho mostra o aviso personalizado especificado pelo exemplo anterior.

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

Um atributo `Prompt` também pode especificar parâmetros que afetam como o formulário exibe o aviso. Por exemplo, o parâmetro `ChoiceFormat` determina como o formulário renderiza a lista de opções.

[!code-csharp[Prompt attribute ChoiceFormat parameter](../includes/code/dotnet-formflow-advanced.cs#promptChoice)]

Neste exemplo, o valor do parâmetro `ChoiceFormat` indica que as opções devem ser exibidas como uma lista com marcadores (em vez de uma lista numerada).

```console
What kind of sandwich would you like?
- BLT
- Black Forest Ham
- Buffalo Chicken
- Chicken And Bacon Ranch Melt
- Cold Cut Combo
- Meatball Marinara
- Oven Roasted Chicken
- Roast Beef
- Rotisserie Style Chicken
- Spicy Italian
- Steak And Cheese
- Sweet Onion Teriyaki
- Tuna
- Turkey Breast
- Veggie
>
```

## <a name="customize-prompts-using-the-template-attribute"></a>Personalizar avisos usando o atributo Template

Enquanto o atributo `Prompt` permite que você personalize o aviso de um único campo, o atributo `Template` permite que você substitua os modelos padrão usados pelo FormFlow para gerar automaticamente os avisos. Este exemplo de código usa o atributo `Template` para redefinir como o formulário trata todos os campos de enumeração. O atributo indica que o usuário pode selecionar somente um item, define o texto do aviso usando o [padrão de linguagem](bot-builder-dotnet-formflow-pattern-language.md) e especifica que o formulário deve exibir apenas um item por linha. 

[!code-csharp[Template attribute](../includes/code/dotnet-formflow-advanced.cs#templateAttribute)]

Este trecho mostra os avisos resultantes para o campo `Bread` e o campo `Cheese`.

```console
What kind of bread would you like on your sandwich?
 1. Nine Grain Wheat
 2. Nine Grain Honey Oat
 3. Italian
 4. Italian Herbs And Cheese
 5. Flatbread
> 

What kind of cheese would you like on your sandwich? 
 1. American
 2. Monterey Cheddar
 3. Pepperjack
> 
```

Se você usar o atributo `Template` para substituir os modelos padrão usados pelo FormFlow para gerar avisos, insira algumas variações nos avisos e mensagens gerados pelo formulário. Para fazer isso, defina várias cadeias de caracteres de texto usando o [padrão de linguagem](bot-builder-dotnet-formflow-pattern-language.md), e o formulário escolherá aleatoriamente entre as opções disponíveis sempre que precisar exibir uma mensagem ou um aviso.

Este exemplo de código redefine o modelo [TemplateUsage.NotUnderstood][notUnderstood] para especificar duas variações diferentes da mensagem. Quando o bot precisar comunicar que não entendeu a entrada de um usuário, ele determinará o conteúdo da mensagem selecionando aleatoriamente uma das duas cadeias de texto. 

[!code-csharp[Template variations of message](../includes/code/dotnet-formflow-advanced.cs#templateMessages)]

Este trecho de código mostra um exemplo da interação resultante entre usuário e bot. 

```console
What size of sandwich do you want? (1. Six Inch, 2. Foot Long)
> two feet
I do not understand "two feet".
> two feet
Try again, I don't get "two feet"
> 
```

## <a name="designate-a-field-as-optional-using-the-optional-attribute"></a>Designar um campo como opcionais usando o atributo Opcional

Para designar um campo como opcional, use o atributo `Optional`. Este exemplo de código especifica que o campo `Cheese` é opcional.

[!code-csharp[Optional attribute](../includes/code/dotnet-formflow-advanced.cs#optionalAttribute)]

Se um campo for opcional e nenhum valor tiver sido especificado, a opção atual será exibida como "Sem Preferência".

```console
What kind of cheese would you like on your sandwich? (current choice: No Preference)
  1. American
  2. Monterey Cheddar
  3. Pepperjack
 >
```

Se um campo for opcional, e o usuário tiver especificado um valor, "Sem Preferência" será exibido como a última opção na lista.

```console
What kind of cheese would you like on your sandwich? (current choice: American)
 1. American
 2. Monterey Cheddar
 3. Pepperjack
 4. No Preference
>
```

## <a name="match-user-input-using-the-terms-attribute"></a>Corresponder a entrada do usuário usando o atributo Terms

Quando um usuário envia uma mensagem para um bot criado com o FormFlow, o bot tentará identificar o significado da entrada do usuário comparando a entrada com uma lista de termos. Por padrão, a lista de termos é gerada aplicando essas etapas ao campo ou valor: 

1. Interromper mudanças de capitalização e sublinhados (_).
2. Gere cada <a href="https://en.wikipedia.org/wiki/N-gram" target="_blank">n-gram</a> até um tamanho máximo.
3. Adicione "s?" ao final de cada palavra (para dar suporte a plurais). 

Por exemplo, o valor "BifeAngusEPizzaDeAlho" geraria estes termos: 

- 'angus?'
- 'bifes?'
- 'alhos?'
- 'pizzas?'
- 'bifes? angus?'
- 'pizzas? alhos?'
- 'bife angus e pizza de alho'

Para substituir esse comportamento padrão e definir a lista de termos usados para comparar a entrada do usuário a um campo, ou um valor em um campo, use o atributo `Terms`. Por exemplo, você pode usar o atributo `Terms` (com uma expressão regular) para levar em conta o fato de que os usuários provavelmente errarão a palavra "rotisserie". 

[!code-csharp[Terms attribute](../includes/code/dotnet-formflow-advanced.cs#termsAttribute)]

O atributo `Terms` aumenta a probabilidade de correspondência da entrada do usuário com uma das opções válidas. O parâmetro `Terms.MaxPhrase` neste exemplo faz com que o `Language.GenerateTerms` gere mais variações dos termos. 

Este trecho de código mostra a interação resultante entre usuário e bot quando o usuário erra a palavra "Rotisserie".

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
> rotissary checkin
For sandwich I understood Rotisserie Style Chicken. "checkin" is not an option.
```

## <a name="validate-user-input-using-the-numeric-attribute-or-pattern-attribute"></a>Validar a entrada do usuário usando o atributo Numeric ou o atributo Pattern

Para restringir o intervalo de valores permitidos para um campo numérico, use o atributo `Numeric`. Este exemplo de código usa o atributo `Numeric` para especificar se a entrada para o campo `Rating` deve ser um número entre 1 e 5. 

[!code-csharp[Numeric attribute](../includes/code/dotnet-formflow-advanced.cs#numericAttribute)]

Para especificar o formato exigido para o valor de um campo específico, use o atributo `Pattern`. Este exemplo de código usa o atributo `Pattern` para especificar o formato necessário para o valor do campo `PhoneNumber`.

[!code-csharp[Pattern attribute](../includes/code/dotnet-formflow-advanced.cs#patternAttribute)]

## <a name="summary"></a>Resumo

Este artigo descreveu como proporcionar uma experiência de usuário personalizada usando o FormFlow especificando o estado inicial do formulário, adicionando a lógica de negócios para gerenciar as interdependências entre os campos e processando a entrada do usuário, e usando atributos para personalizar avisos, substituindo modelos, indicando campos opcionais, correspondendo e validando a entrada do usuário. Para conhecer outras maneiras de personalizar a experiência do usuário com o FormFlow, confira [Personalizar um formulário usando o FormBuilder](bot-builder-dotnet-formflow-formbuilder.md).

## <a name="sample-code"></a>Exemplo de código

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Recursos adicionais

- [Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md)
- [Personalizar um formulário usando FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Localizar o conteúdo do formulário](bot-builder-dotnet-formflow-localize.md)
- [Definir um formulário usando o esquema JSON](bot-builder-dotnet-formflow-json-schema.md)
- [Personalizar a experiência do usuário com o idioma padrão](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Construtor de Bot para .NET</a>

[formDialog]: /dotnet/api/microsoft.bot.builder.formflow.formdialog

[promptFieldsWithValues]: /dotnet/api/microsoft.bot.builder.formflow.formoptions.promptfieldswithvalues

[validateResult]: /dotnet/api/microsoft.bot.builder.formflow.validateresult

[describeAttribute]: /dotnet/api/microsoft.bot.builder.formflow.describeattribute

[numericAttribute]: /dotnet/api/microsoft.bot.builder.formflow.numericattribute

[optionalAttribute]: /dotnet/api/microsoft.bot.builder.formflow.optionalattribute

[patternAttribute]: /dotnet/api/microsoft.bot.builder.formflow.patternattribute

[promptAttribute]: /dotnet/api/microsoft.bot.builder.formflow.promptattribute

[templateAttribute]: /dotnet/api/microsoft.bot.builder.formflow.templateattribute

[termsAttribute]: /dotnet/api/microsoft.bot.builder.formflow.termsattribute

[notUnderstood]: /dotnet/api/microsoft.bot.builder.formflow.templateusage.notunderstood
