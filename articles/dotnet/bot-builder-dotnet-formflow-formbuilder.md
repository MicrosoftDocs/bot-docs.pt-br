---
title: Personalizar um formulário usando FormBuilder | Microsoft Docs
description: Aprenda como alterar dinamicamente e personalizar o fluxo de conversação e o conteúdo usando o FormBuilder para o Bot Builder SDK for .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: d24e43a3f48db024ab55c2089acbc42edebab929
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297347"
---
# <a name="customize-a-form-using-formbuilder"></a>Personalizar um formulário usando FormBuilder

[Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md) descrevem uma implementação básica do FormFlow que proporciona uma experiência de usuário bastante genérica, e [recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md) descrevem como você pode personalizar a experiência do usuário usando lógica e atributos de negócios. Este artigo descreve como você pode usar [FormBuilder][formBuilder] para personalizar ainda mais, a experiência do usuário, especificando a sequência na qual o formulário executa as etapas e definindo dinamicamente valores de campo as confirmações e mensagens. 

## <a name="dynamically-define-field-values-confirmations-and-messages"></a>Definir dinamicamente valores de campo, confirmações e mensagens

Usando o FormBuilder, você pode definir dinamicamente valores de campo, confirmações e mensagens.

### <a name="dynamically-define-field-values"></a>Definir dinamicamente os valores de campo 

Usa um bot sandwich projetado para adicionar uma bebida livre ou o cookie a qualquer pedido que especifica um sanduíche de pé longa o `Sandwich.Specials` campo para armazenar dados sobre itens livres. Nesse caso, o valor da `Sandwich.Specials` campo deve ser definido dinamicamente para cada ordem de acordo com se contém ou não a ordem de um sanduíche de pé longa. 

O `Specials` campo for especificado como opcionais e "None" é designado como o texto para a opção que não indica nenhuma preferência.

[!code-csharp[Field definition](../includes/code/dotnet-formflow-formbuilder.cs#fieldDefinition)]

Este exemplo de código mostra como definir dinamicamente o valor da `Specials` campo. 

[!code-csharp[Define value](../includes/code/dotnet-formflow-formbuilder.cs#defineValue)]

Neste exemplo, o [Advanced.Field.SetType] [ setType] método Especifica o tipo de campo (`null` representa um campo de enumeração). O método [Advanced.Field.SetActive][setActive] especifica que o campo só deve ser ativado se o comprimento do sandwich for `Length.FootLong`. Finalmente, o método [Advanced.Field.SetDefine][setDefine] especifica um delegado assíncrono que define o campo. O delegado recebe o objeto de estado atual e o [Advanced.Field][field] que está sendo definido dinamicamente. O representante usa métodos de fluente do campo para definir dinamicamente os valores. Neste exemplo, os valores são strings e os métodos `AddDescription` e `AddTerms` especificam as descrições e os termos de cada valor.

> [!NOTE]
> Para definir dinamicamente um valor de campo, você pode implementar [Advanced.IField][iField] ou agilizar o processo usando a classe [Advanced.FieldReflector][FieldReflector] como mostrado no exemplo acima. 

### <a name="dynamically-define-messages-and-confirmations"></a>Definir dinamicamente a mensagens e confirmações

Usando FormBuilder, você pode definir também dinamicamente as mensagens e confirmações. Cada mensagem e confirmação é executada somente quando as etapas anteriores do formulário estão inativas ou concluídas. 

Este exemplo de código mostra uma confirmação gerada dinamicamente que calcula o custo do sandwich. 

[!code-csharp[Define confirmation](../includes/code/dotnet-formflow-formbuilder.cs#defineConfirmation)]

## <a name="customize-a-form-using-formbuilder"></a>Personalizar um formulário usando FormBuilder

Este exemplo de código usa FormBuilder para definir as etapas do formulário, [validar seleções](bot-builder-dotnet-formflow-advanced.md#add-business-logic), e [definir dinamicamente o valor do campo e a confirmação](#dynamically-define-field-values-confirmations-and-messages). Por padrão, as etapas no formulário serão executadas na seqüência em que estão listadas. No entanto, as etapas podem ser ignoradas para campos que já contêm valores ou se a navegação explícita for especificada. 

[!code-csharp[FormBuilder form](../includes/code/dotnet-formflow-formbuilder.cs#formBuilderForm)]

Neste exemplo, o formulário executa estas etapas:

- Mostra uma mensagem de boas-vinda. 
- Preenche `SandwichOrder.Sandwich`. 
- Preenche `SandwichOrder.Length`. 
- Preenche `SandwichOrder.Bread`. 
- Preenche `SandwichOrder.Cheese`. 
- Preenche `SandwichOrder.Toppings` e adiciona valores ausentes, se o usuário selecionou `ToppingOptions.Everything`. -. Mostra uma mensagem confirmando os ingredientes selecionados. 
- Preenche `SandwichOrder.Sauces`. 
- [Define dinamicamente](#dynamically-define-field-values) o valor do campo para `SandwichOrder.Specials`. 
- [Define dinamicamente](#dynamically-define-messages-and-confirmations) a confirmação de custo do sanduíche. 
- Preenche `SandwichOrder.DeliveryAddress` e [verifica](bot-builder-dotnet-formflow-advanced.md#add-business-logic) a cadeia de caracteres resultante. Se o endereço não começar com um número, o formulário retorna uma mensagem. 
- Preenche `SandwichOrder.DeliveryTime` com um aviso personalizado. 
- Confirmando o pedido. 
- Adiciona todos os campos restantes que foram definidos na classe, mas não explicitamente referenciados pelo `Field`. (Se o exemplo não chamou o `AddRemainingFields` método, o formulário não inclua todos os campos que não foram explicitamente referenciado.) 
- Mostra uma mensagem de agradecimento. 
- Define um `OnCompletionAsync` manipulador para processar o pedido. 

## <a name="sample-code"></a>Exemplo de código

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Recursos adicionais

- [Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md)
- [Recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md)
- [Localizar o conteúdo do formulário](bot-builder-dotnet-formflow-localize.md)
- [Definir um formulário usando o esquema JSON](bot-builder-dotnet-formflow-json-schema.md)
- [Personalizar a experiência do usuário com o idioma padrão](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Construtor de bot do SDK para referência do .NET</a>

[formBuilder]: /dotnet/api/microsoft.bot.builder.formflow.formbuilder-1

[setType]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.settype

[setActive]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setactive

[setDefine]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setdefine

[field]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1

[iField]: /dotnet/api/microsoft.bot.builder.formflow.advanced.ifield-1

[FieldReflector]: /dotnet/api/microsoft.bot.builder.formflow.advanced.fieldreflector-1