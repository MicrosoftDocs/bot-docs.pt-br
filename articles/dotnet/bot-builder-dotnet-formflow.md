---
title: Recursos básicos do FormFlow | Microsoft Docs
description: Saiba como orientar o fluxo de conversa usando FormFlow dentro do SDK do construtor de bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: f5b36e1f916539b78f9bdcdd0970317db723f408
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000365"
---
# <a name="basic-features-of-formflow"></a>Recursos básicos do FormFlow

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Os [diálogos](bot-builder-dotnet-dialogs.md) são muito poderosos e flexíveis, mas administrar uma conversa interativa, como pedir um sanduíche, pode exigir muito esforço. Em cada ponto na conversa, há muitas possibilidades sobre o que acontecerá em seguida. Por exemplo, talvez você precise esclarecer alguma ambiguidade, fornecer ajuda, voltar ou mostrar o progresso. Usando o **FormFlow** dentro do SDK do construtor de bot para .NET, você poderá simplificar muito o processo de gerenciar uma conversa interativa como esta. 

O FormFlow gera automaticamente as caixas de diálogo que são necessárias para gerenciar uma conversa interativa, com base em diretrizes que você especificar. Embora usar FormFlow sacrifique parte da flexibilidade que possa ser conseguida de outra forma, criando e gerenciando caixas de diálogo por conta própria, elaborar uma conversa interativa usando FormFlow pode reduzir significativamente o tempo necessário para desenvolver seu bot. Além disso, você pode construir seu bot usando uma combinação de caixas de diálogo geradas pelo FormFlow e outros tipos de caixas de diálogo. Por exemplo, um diálogo do FormFlow pode orientar o usuário durante o processo de preenchimento de um formulário, enquanto um [LuisDialog][LuisDialog] pode avaliar a entrada do usuário para determinar a intenção.

Este artigo descreve como criar um bot que usa os recursos básicos do FormFlow para coletar informações de um usuário.

## <a id="forms-and-fields"></a>Formulários e campos

Para criar um bot usando FormFlow, você deverá especificar as informações que o bot precisa coletar do usuário. Por exemplo, se o objetivo do bot for registrar o pedido de sanduíche de um usuário, você deverá definir um formulário que contém campos para os dados de que o bot precisa para atender ao pedido. Você pode definir o formulário criando uma classe C# que contém uma ou mais propriedades públicas para representar os dados que o bot irá coletar do usuário. Cada propriedade deve ser um destes tipos de dados:

- Integral (sbyte, byte, short, ushort, int, uint, long, ulong)
- Ponto flutuante (float, double)
- Cadeia de caracteres
- Datetime
- Enumeração
- Lista de enumerações

Qualquer um dos tipos de dados pode ser nulo, que você pode usar para modelar que o campo não tem um valor. Se um campo de formulário for baseado em uma propriedade de enumeração que não permite valor nulo, o valor **0** na enumeração representará **nulo** (ou seja, indica que o campo não tem um valor) e você deverá começar seus valores de enumeração no **1**. O FormFlow ignora todos os outros tipos de propriedades e métodos.

Para objetos complexos, você deverá criar um formulário para a classe C# de nível superior e outro formulário para o objeto complexo. Você pode compor os formulários em conjunto usando semântica típica de [diálogo](bot-builder-dotnet-dialogs.md). Também é possível definir um formulário diretamente, ao implementar [Advanced.IField][iField], ou usando [Advanced.Field][field] e preenchendo os dicionários dentro dele. 

> [!NOTE]
> Você pode definir um formulário usando uma classe C# ou o esquema JSON. Este artigo descreve como definir um formulário usando uma classe C#. Para saber mais sobre como usar o esquema JSON, veja [Definir um formulário usando o esquema JSON](bot-builder-dotnet-formflow-json-schema.md).

## <a name="simple-sandwich-bot"></a>Bot de sanduíche simples

Considere este exemplo de um bot de sanduíche simples que foi criado para registrar o pedido de sanduíche de um usuário. 

### <a id="create-class"></a> Criar o formulário

A classe `SandwichOrder` define o formulário e as enumerações definem as opções para a criação de um sanduíche. A classe também inclui o método `BuildForm` estático que usa [FormBuilder][formBuilder] para criar o formulário e definir uma mensagem simples de boas-vindas. 

Para usar o FormFlow, primeiro você deverá importar o namespace `Microsoft.Bot.Builder.FormFlow`.

[!code-csharp[Define form](../includes/code/dotnet-formflow.cs#defineForm)]

### <a name="connect-the-form-to-the-framework"></a>Conecte o formulário à estrutura 

Para conectar o formulário à estrutura, você deverá adicioná-lo ao controlador. Neste exemplo, o método `Conversation.SendAsync` chama o método `MakeRootDialog` estático, que, por sua vez, chama o método `FormDialog.FromForm` para criar o formulário `SandwichOrder`. 

[!code-csharp[Connect form to framework](../includes/code/dotnet-formflow.cs#connectToFramework)]

### <a name="see-it-in-action"></a>Veja isso em ação

Apenas definindo o formulário com uma classe C# e conectá-lo à estrutura, você terá habilitado o FormFlow para gerenciar automaticamente a conversa entre o usuário e o bot. As interações de exemplo mostradas a seguir demonstram os recursos de um bot que é criado usando os recursos básicos do FormFlow. Em cada interação, um símbolo **>** indica o ponto em que o usuário insere uma resposta. 

#### <a name="display-the-first-prompt"></a>Exibir o primeiro aviso 

Este formulário preenche a propriedade `SandwichOrder.Sandwich`. O formulário gera automaticamente o aviso, "Selecionar um sanduíche", em que a palavra "sanduíche" no aviso deriva do nome da propriedade `Sandwich`. A enumeração `SandwichOptions` define as opções que são apresentadas para o usuário, com cada valor de enumeração automaticamente sendo dividido em palavras com base em alterações de maiúsculas e minúsculas e sublinhados.

```console
Please select a sandwich
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

#### <a name="provide-guidance"></a>Fornecer orientação

O usuário pode digitar "ajuda" a qualquer momento da conversa para obter orientação com o preenchimento do formulário. Por exemplo, se o usuário digitar "ajuda" no aviso do sanduíche, o bot responderá com essa orientação. 

```console
> help
* You are filling in the sandwich field. Possible responses:
* You can enter a number 1-15 or words from the descriptions. (BLT, Black Forest Ham, Buffalo Chicken, Chicken And Bacon Ranch Melt, Cold Cut Combo, Meatball Marinara, Oven Roasted Chicken, Roast Beef, Rotisserie Style Chicken, Spicy Italian, Steak And Cheese, Sweet Onion Teriyaki, Tuna, Turkey Breast, and Veggie)
* Back: Go back to the previous question.
* Help: Show the kinds of responses you can enter.
* Quit: Quit the form without completing it.
* Reset: Start over filling in the form. (With defaults from your previous entries.)
* Status: Show your progress in filling in the form so far.
* You can switch to another field by entering its name. (Sandwich, Length, Bread, Cheese, Toppings, and Sauce).
```

#### <a name="advance-to-the-next-prompt"></a>Prosseguir para o próximo aviso

Se o usuário inserir "2" em resposta à ao aviso inicial do sanduíche, o bot exibirá um aviso para a próxima propriedade que é definida pelo formulário: `SandwichOrder.Length`.

```console
Please select a sandwich
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
> 2
Please select a length (1. Six Inch, 2. Foot Long)
> 
```

#### <a name="return-to-the-previous-prompt"></a>Retornar ao aviso anterior 

Se o usuário inserir "voltar" nesse momento da conversa, o bot retornará ao aviso anterior. O prompt mostra a opção atual do usuário ("Black Forest Ham"); o usuário poderá alterar essa seleção digitando um número diferente ou confirmar essa seleção digitando "c".

```console
> back
Please select a sandwich(current choice: Black Forest Ham)
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
> c
Please select a length (1. Six Inch, 2. Foot Long)
> 
```

#### <a name="clarify-user-input"></a>Esclarecer a entrada do usuário

Se o usuário responder com texto (em vez de um número) para indicar uma opção, o bot perguntará automaticamente para fins de esclarecimento se a entrada do usuário corresponde a mais de uma opção. 

```console
Please select a bread
 1. Nine Grain Wheat
 2. Nine Grain Honey Oat
 3. Italian
 4. Italian Herbs And Cheese
 5. Flatbread
> nine grain
By "nine grain" bread did you mean (1. Nine Grain Honey Oat, 2. Nine Grain Wheat)
> 1
```

Se qualquer uma das opções válidas não corresponder diretamente à entrada do usuário, o bot automaticamente pedirá esclarecimento para o usuário.

```console
Please select a cheese (1. American, 2. Monterey Cheddar, 3. Pepperjack)
> amercan
"amercan" is not a cheese option.
> american smoked
For cheese I understood American. "smoked" is not an option.
```

Se a entrada do usuário especificar várias opções para uma propriedade e o bot não entender as opções especificadas, ele automaticamente pedirá esclarecimento ao usuário.

```console
Please select one or more toppings
 1. Banana Peppers
 2. Cucumbers
 3. Green Bell Peppers
 4. Jalapenos
 5. Lettuce
 6. Olives
 7. Pickles
 8. Red Onion
 9. Spinach
 10. Tomatoes
> peppers, lettuce and tomato
By "peppers" toppings did you mean (1. Green Bell Peppers, 2. Banana Peppers)
> 1
```

#### <a name="show-current-status"></a>Mostrar status atual

Se o usuário inserir "status" a qualquer momento no pedido, a resposta do bot indicará quais valores já foram especificados e quais ainda devem ser especificados. 

```console
Please select one or more sauce
 1. Honey Mustard
 2. Light Mayonnaise
 3. Regular Mayonnaise
 4. Mustard
 5. Oil
 6. Pepper
 7. Ranch
 8. Sweet Onion
 9. Vinegar
> status
* Sandwich: Black Forest Ham
* Length: Six Inch
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Unspecified  
```

#### <a name="confirm-selections"></a>Confirmar seleções

Quando o usuário preenche o formulário, o bot pedirá que o usuário confirme suas seleções.

```console
Please select one or more sauce
 1. Honey Mustard
 2. Light Mayonnaise
 3. Regular Mayonnaise
 4. Mustard
 5. Oil
 6. Pepper
 7. Ranch
 8. Sweet Onion
 9. Vinegar
> 1
Is this your selection?
* Sandwich: Black Forest Ham
* Length: Six Inch
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Honey Mustard
>
```

Se o usuário responder digitando "não", o bot permitirá que o usuário atualize as seleções anteriores. Se o usuário responder digitando "sim", o formulário terá sido concluído e o controle será retornado para o diálogo de chamada. 

```console
Is this your selection?
* Sandwich: Black Forest Ham
* Length: Six Inch
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Honey Mustard
> no
What do you want to change?
 1. Sandwich(Black Forest Ham)
 2. Length(Six Inch)
 3. Bread(Nine Grain Honey Oat)
 4. Cheese(American)
 5. Toppings(Lettuce, Tomatoes, and Green Bell Peppers)
 6. Sauce(Honey Mustard)
> 2
Please select a length (current choice: Six Inch) (1. Six Inch, 2. Foot Long)
> 2
Is this your selection?
* Sandwich: Black Forest Ham
* Length: Foot Long
* Bread: Nine Grain Honey Oat
* Cheese: American
* Toppings: Lettuce, Tomatoes, and Green Bell Peppers
* Sauce: Honey Mustard
> y
```

## <a name="handling-quit-and-exceptions"></a>Gerenciando encerramentos e exceções

Se o usuário digitar "encerrar" no formulário ou uma exceção ocorrer em algum momento na conversa, seu bot precisará saber a etapa na qual o evento ocorreu, o estado do formulário quando o evento ocorreu e quais etapas do formulário foram concluídas com êxito antes do evento. O formulário retorna essas informações por meio da classe `FormCanceledException<T>`. 

Este exemplo de código mostra como capturar a exceção e exibir uma mensagem de acordo com o evento que ocorreu. 

[!code-csharp[Handle exception or quit](../includes/code/dotnet-formflow.cs#handleExceptionOrQuit)]

## <a name="summary"></a>Resumo

Este artigo descreveu como usar os recursos básicos do FormFlow para criar um bot que pode:

- Gerar e gerenciar a conversa automaticamente
- Fornecer orientação e ajuda claras
- Entender as entradas de texto e números
- Fornecer comentários ao usuário sobre o que é entendido e o que não é 
- Fazer perguntas esclarecedoras quando for necessário 
- Permitir que o usuário navegue entre as etapas

Embora a funcionalidade básica do FormFlow seja suficiente em alguns casos, você deverá considerar os possíveis benefícios de incorporar alguns dos recursos mais avançados do FormFlow ao seu bot. Para saber mais, veja [Recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md) e [Personalizar um formulário usando o FormBuilder](bot-builder-dotnet-formflow-formbuilder.md).

## <a name="sample-code"></a>Exemplo de código

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="next-steps"></a>Próximas etapas

O FormFlow simplifica o desenvolvimento de diálogos. Os recursos avançados do FormFlow permitem que você personalize o comportamento de um objeto do FormFlow.

> [!div class="nextstepaction"]
> [Recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Personalizar um formulário usando FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Localizar o conteúdo do formulário](bot-builder-dotnet-formflow-localize.md)
- [Definir um formulário usando o esquema JSON](bot-builder-dotnet-formflow-json-schema.md)
- [Personalizar a experiência do usuário com o idioma padrão](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Construtor de Bot para .NET</a>

[LuisDialog]: /dotnet/api/microsoft.bot.builder.dialogs.luisdialog-1

[iField]: /dotnet/api/microsoft.bot.builder.formflow.advanced.ifield-1

[field]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1

[formBuilder]: /dotnet/api/microsoft.bot.builder.formflow.formbuilder-1
