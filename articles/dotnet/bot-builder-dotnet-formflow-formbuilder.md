---
title: Personalizar um formulário usando FormBuilder | Microsoft Docs
description: Aprenda como alterar dinamicamente e personalizar o fluxo de conversação e o conteúdo usando o FormBuilder para o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b738296d3d796cdccd40e0d3bda503b9d6349bff
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70297990"
---
# <a name="customize-a-form-using-formbuilder"></a>Personalizar um formulário usando FormBuilder

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md) descrevem uma implementação básica do FormFlow que proporciona uma experiência de usuário bastante genérica, e [recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md) descrevem como você pode personalizar a experiência do usuário usando lógica e atributos de negócios. Este artigo descreve como você pode usar o [FormBuilder][formBuilder] para personalizar ainda mais a experiência do usuário, especificando a sequência na qual o formulário executa as etapas e definindo dinamicamente os valores de campo, as confirmações e as mensagens. 

## <a name="dynamically-define-field-values-confirmations-and-messages"></a>Definir dinamicamente valores de campo, confirmações e mensagens

Usando o FormBuilder, você pode definir dinamicamente valores de campo, confirmações e mensagens.

### <a name="dynamically-define-field-values"></a>Definir dinamicamente os valores de campo 

Usa um bot sandwich projetado para adicionar uma bebida livre ou o cookie a qualquer pedido que especifica um sanduíche de pé longa o `Sandwich.Specials` campo para armazenar dados sobre itens livres. Nesse caso, o valor da `Sandwich.Specials` campo deve ser definido dinamicamente para cada ordem de acordo com se contém ou não a ordem de um sanduíche de pé longa. 

O `Specials` campo for especificado como opcionais e "None" é designado como o texto para a opção que não indica nenhuma preferência.

[!code-csharp[Field definition](../includes/code/dotnet-formflow-formbuilder.cs#fieldDefinition)]

Este exemplo de código mostra como definir dinamicamente o valor da `Specials` campo. 

[!code-csharp[Define value](../includes/code/dotnet-formflow-formbuilder.cs#defineValue)]

Neste exemplo, o método [Advanced.Field.SetType][setType] especifica o tipo de campo (`null` representa um campo de enumeração). O método [Advanced.Field.SetActive][setActive] especifica que o campo só deverá ser habilitado se o tamanho do sanduíche for `Length.FootLong`. Por fim, o método [Advanced.Field.SetDefine][setDefine] especifica um representante assíncrono que define o campo. O representante recebe o objeto de estado atual e o [Advanced.Field][field] que está sendo definido dinamicamente. O representante usa métodos de fluente do campo para definir dinamicamente os valores. Neste exemplo, os valores são strings e os métodos `AddDescription` e `AddTerms` especificam as descrições e os termos de cada valor.

> [!NOTE]
> Para definir um valor de campo dinamicamente, implemente [Advanced.IField][iField] ou simplifique o processo usando a classe [Advanced.FieldReflector][FieldReflector], como mostrado no exemplo acima. 

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
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>

[formBuilder]: /dotnet/api/microsoft.bot.builder.formflow.formbuilder-1

[setType]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.settype

[setActive]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setactive

[setDefine]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setdefine

[field]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1

[iField]: /dotnet/api/microsoft.bot.builder.formflow.advanced.ifield-1

[FieldReflector]: /dotnet/api/microsoft.bot.builder.formflow.advanced.fieldreflector-1
