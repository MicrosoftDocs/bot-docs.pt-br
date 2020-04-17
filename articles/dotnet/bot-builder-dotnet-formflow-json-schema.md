---
title: Definir um formulário usando o esquema JSON e o FormFlow – Serviço de Bot
description: Saiba como definir um formulário usando o esquema JSON e o FormFlow com o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: cd884c49507b4d7edc84f4bbd79396fe36073a82
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75796483"
---
# <a name="define-a-form-using-json-schema"></a>Definir um formulário usando o esquema JSON

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Se você usar uma [classe C#](bot-builder-dotnet-formflow.md#create-class) para definir o formulário quando você cria um bot com FormFlow, o formulário deriva da definição estática do seu tipo em C#. Como alternativa, você pode definir o formulário usando o <a href="http://json-schema.org/documentation.html" target="_blank">esquema JSON</a>. Um formulário que é definido usando o esquema JSON é simplesmente controlado por dados. Para alterar o formulário (e portanto, o comportamento do bot), basta atualizar o esquema. 

O esquema JSON descreve os campos dentro de seu <a href="http://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JObject.htm" target="_blank">JObject</a> e inclui anotações que controlam os prompts, modelos e termos. Para usar o esquema JSON com FormFlow, você deve adicionar o `Microsoft.Bot.Builder.FormFlow.Json` pacote NuGet ao seu projeto e importar o `Microsoft.Bot.Builder.FormFlow.Json` namespace.

## <a name="standard-keywords"></a>Palavras-chave padrão 

O FormFlow dá suporte a estas palavras-chave do <a href="http://json-schema.org/documentation.html" target="_blank">esquema JSON</a> padrão:

| Palavra-chave | Descrição | 
|----|----|
| type | Define o tipo de dados que o campo contém. |
| enum | Define os valores válidos para o campo. |
| mínimo | Define o valor numérico mínimo permitido para o campo (conforme descrito em [NumericAttribute][numericAttribute]). |
| máximo | Define o valor numérico máximo permitido para o campo (conforme descrito em [NumericAttribute][numericAttribute]). |
| obrigatório | Define quais campos são obrigatórios. |
| pattern | Valida os valores de cadeia de caracteres (conforme descrito em [PatternAttribute][patternAttribute]). |

## <a name="extensions-to-json-schema"></a>Extensões do esquema JSON

O FormFlow estende o <a href="http://json-schema.org/documentation.html" target="_blank">esquema JSON</a> padrão para dar suporte a várias propriedades adicionais.

### <a name="additional-properties-at-the-root-of-the-schema"></a>Propriedades adicionais na raiz do esquema

| Propriedade | Valor |
|----|----|
| OnCompletion | Script do C# com argumentos `(IDialogContext context, JObject state)` para preencher o formulário. |
| Referências | Referências a serem incluídas nos scripts. Por exemplo, `[assemblyReference, ...]`. Os caminhos devem ser absoluto ou relativo para o diretório atual. Por padrão, o script inclui `Microsoft.Bot.Builder.dll`. |
| Importações | Importações a serem incluídas nos scripts. Por exemplo, `[import, ...]`. Por padrão, o script inclui o `Microsoft.Bot.Builder`, `Microsoft.Bot.Builder.Dialogs`, `Microsoft.Bot.Builder.FormFlow`, `Microsoft.Bot.Builder.FormFlow.Advanced`, `System.Collections.Generic`, e `System.Linq` namespaces. |

### <a name="additional-properties-at-the-root-of-the-schema-or-as-peers-of-the-type-property"></a>Propriedades adicionais na raiz do esquema ou como pares de propriedade de tipo

| Propriedade | Valor |
|----|----|
| Modelos | `{ TemplateUsage: { Patterns: [string, ...], <args> }, ...}` |
| Prompt | `{ Patterns:[string, ...] <args>}` |

Para especificar modelos e avisos no esquema JSON, use o mesmo vocabulário, conforme definido por [TemplateAttribute][templateAttribute] e [PromptAttribute][promptAttribute]. Nomes de propriedade e valores no esquema devem corresponder a nomes de propriedade e valores na enumeração C# subjacente. Por exemplo, este snippet de código de esquema define um modelo que substitui o `TemplateUsage.NotUnderstood` modelo e especifica um `TemplateBaseAttribute.ChoiceStyle`: 

```json
"Templates":{ "NotUnderstood": { "Patterns": ["I don't get it"], "ChoiceStyle":"Auto"}}
```

### <a name="additional-properties-as-peers-of-the-type-property"></a>Propriedades adicionais como pares de propriedade de tipo

|   Propriedade   |          Conteúdo           |                                                   Descrição                                                    |
|--------------|-----------------------------|------------------------------------------------------------------------------------------------------------------|
|   Datetime   |            bool             |                                  Indica se o campo é um campo `DateTime`.                                  |
|   Descrever   |      cadeia de caracteres ou objeto       |                  Descrição de um campo, conforme descrito em [DescribeAttribute][describeAttribute].                  |
|    Termos     |       `[string,...]`        |                  Expressões regulares para correspondência de um valor de campo conforme descrito em TermsAttribute.                  |
|  MaxPhrase   |             INT             |                  Executa os termos da `Language.GenerateTerms(string, int)` para expandi-las.                   |
|    Valores    | `{ string: {Describe:string |                                  object, Terms:[string, ...], MaxPhrase}, ...}`                                  |
|    Ativo    |           script            | Script do C# com argumentos `(JObject state)->bool` para testar se o campo, mensagem ou confirmação estão ativos.  |
|   Validar   |           script            |      Script do C# com argumentos `(JObject state, object value)->ValidateResult` para validar um valor de campo.      |
|    Definir    |           script            |        Script do C# com argumentos `(JObject state, Field<JObject> field)` para definir dinamicamente um campo.        |
|     Próximo     |           script            | Script do C# com argumentos `(object value, JObject state)` para determinar a próxima etapa depois de preencher um campo. |
|    Antes de    |          `[confirm          |                                                  message, ...]`                                                  |
|    After (após)     |          `[confirm          |                                                  message, ...]`                                                  |
| Dependências |        [cadeia de caracteres, ...]        |                           Campos em que este campo, mensagem ou confirmação depende.                           |

Use `{Confirm:script|[string, ...], ...templateArgs}` dentro do valor da propriedade **Anterior** ou da propriedade **Posterior** para definir uma confirmação usando qualquer um script do C# com o argumento `(JObject state)` ou um conjunto de padrões que serão selecionados aleatoriamente com argumentos de modelo opcionais.

Use `{Message:script|[string, ...] ...templateArgs}` dentro do valor da propriedade **Anterior** ou da propriedade **Posterior** para definir uma mensagem usando qualquer um script do C# com o argumento `(JObject state)` ou um conjunto de padrões que serão selecionados aleatoriamente com argumentos de modelo opcionais.

## <a name="scripts"></a>Scripts

Diversas propriedades que são descritas acima podem conter um script como o valor da propriedade. Um script pode ser qualquer snippet de código C# que você normalmente encontra no corpo de um método. Você pode adicionar referências usando a propriedade **Referências** e/ou a propriedade **Importações**. As variáveis globais especiais incluem:

| Variável | Descrição |
|----|----|
| opção | Expedição interna para que o script seja executado. |
| state | `JObject` estado de formulário associado para todos os scripts. |
| ifield | `IField<JObject>` para permitir o raciocínio sobre o campo atual para todos os scripts, exceto construtores de prompt de confirmação/mensagem. |
| value | Valor de objeto a ser validado para **Validar**. |
| field | `Field<JObject>` para permitir a atualização dinâmica de um campo em **Definir**. |
| contexto | Contexto `IDialogContext` que permite postar os resultados em **OnCompletion**. |

Os campos que são definidos por meio do esquema JSON têm a mesma capacidade de estender ou substituir as definições de forma programática como qualquer outro campo. Eles também podem ser localizados da mesma maneira.

## <a name="json-schema-example"></a>Exemplo de esquema JSON

A maneira mais simples de definir um formulário é definir tudo, incluindo qualquer código C#, diretamente no esquema JSON. Este exemplo mostra o esquema JSON para o bot sanduíche anotado descrito em [Personalizar um formulário usando o FormBuilder](bot-builder-dotnet-formflow-formbuilder.md).

```json
{
  "References": [ "Microsoft.Bot.Sample.AnnotatedSandwichBot.dll" ],
  "Imports": [ "Microsoft.Bot.Sample.AnnotatedSandwichBot.Resource" ],
  "type": "object",
  "required": [
    "Sandwich",
    "Length",
    "Ingredients",
    "DeliveryAddress"
  ],
  "Templates": {
    "NotUnderstood": {
      "Patterns": [ "I do not understand \"{0}\".", "Try again, I don't get \"{0}\"." ]
    },
    "EnumSelectOne": {
      "Patterns": [ "What kind of {&} would you like on your sandwich? {||}" ],
      "ChoiceStyle": "Auto"
    }
  },
  "properties": {
    "Sandwich": {
      "Prompt": { "Patterns": [ "What kind of {&} would you like? {||}" ] },
      "Before": [ { "Message": [ "Welcome to the sandwich order bot!" ] } ],
      "Describe": { "Image": "https://placeholdit.imgix.net/~text?txtsize=16&txt=Sandwich&w=125&h=40&txttrack=0&txtclr=000&txtfont=bold" },
      "type": [
        "string",
        "null"
      ],
      "enum": [
        "BLT",
        "BlackForestHam",
        "BuffaloChicken",
        "ChickenAndBaconRanchMelt",
        "ColdCutCombo",
        "MeatballMarinara",
        "OvenRoastedChicken",
        "RoastBeef",
        "RotisserieStyleChicken",
        "SpicyItalian",
        "SteakAndCheese",
        "SweetOnionTeriyaki",
        "Tuna",
        "TurkeyBreast",
        "Veggie"
      ],
      "Values": {
        "RotisserieStyleChicken": {
          "Terms": [ "rotis\\w* style chicken" ],
          "MaxPhrase": 3
        }
      }
    },
    "Length": {
      "Prompt": {
        "Patterns": [ "What size of sandwich do you want? {||}" ]
      },
      "type": [
        "string",
        "null"
      ],
      "enum": [
        "SixInch",
        "FootLong"
      ]
    },
    "Ingredients": {
      "type": "object",
      "required": [ "Bread" ],
      "properties": {
        "Bread": {
          "type": [
            "string",
            "null"
          ],
          "Describe": {
            "Title": "Sandwich Bot",
            "SubTitle": "Bread Picker"
          },
          "enum": [
            "NineGrainWheat",
            "NineGrainHoneyOat",
            "Italian",
            "ItalianHerbsAndCheese",
            "Flatbread"
          ]
        },
        "Cheese": {
          "type": [
            "string",
            "null"
          ],
          "enum": [
            "American",
            "MontereyCheddar",
            "Pepperjack"
          ]
        },
        "Toppings": {
          "type": "array",
          "items": {
            "type": "integer",
            "enum": [
              "Everything",
              "Avocado",
              "BananaPeppers",
              "Cucumbers",
              "GreenBellPeppers",
              "Jalapenos",
              "Lettuce",
              "Olives",
              "Pickles",
              "RedOnion",
              "Spinach",
              "Tomatoes"
            ],
            "Values": {
              "Everything": { "Terms": [ "except", "but", "not", "no", "all", "everything" ] }
            }
          },
          "Validate": "var values = ((List<object>) value).OfType<string>(); var result = new ValidateResult {IsValid = true, Value = values} ; if (values != null && values.Contains(\"Everything\")) { result.Value = (from topping in new string[] {  \"Avocado\", \"BananaPeppers\", \"Cucumbers\", \"GreenBellPeppers\", \"Jalapenos\", \"Lettuce\", \"Olives\", \"Pickles\", \"RedOnion\", \"Spinach\", \"Tomatoes\"} where !values.Contains(topping) select topping).ToList();} return result;",
          "After": [ { "Message": [ "For sandwich toppings you have selected {Ingredients.Toppings}." ] } ]
        },
        "Sauces": {
          "type": [
            "array",
            "null"
          ],
          "items": {
            "type": "string",
            "enum": [
              "ChipotleSouthwest",
              "HoneyMustard",
              "LightMayonnaise",
              "RegularMayonnaise",
              "Mustard",
              "Oil",
              "Pepper",
              "Ranch",
              "SweetOnion",
              "Vinegar"
            ]
          }
        }
      }
    },
    "Specials": {
      "Templates": {
        "NoPreference": { "Patterns": [ "None" ] }
      },
      "type": [
        "string",
        "null"
      ],
      "Active": "return (string) state[\"Length\"] == \"FootLong\";",
      "Define": "field.SetType(null).AddDescription(\"cookie\", DynamicSandwich.FreeCookie).AddTerms(\"cookie\", Language.GenerateTerms(DynamicSandwich.FreeCookie, 2)).AddDescription(\"drink\", DynamicSandwich.FreeDrink).AddTerms(\"drink\", Language.GenerateTerms(DynamicSandwich.FreeDrink, 2)); return true;",
      "After": [ { "Confirm": "var cost = 0.0; switch ((string) state[\"Length\"]) { case \"SixInch\": cost = 5.0; break; case \"FootLong\": cost=6.50; break;} return new PromptAttribute($\"Total for your sandwich is {cost:C2} is that ok?\");" } ]
    },
    "DeliveryAddress": {
      "type": [
        "string",
        "null"
      ],
      "Validate": "var result = new ValidateResult{ IsValid = true, Value = value}; var address = (value as string).Trim(); if (address.Length > 0 && (address[0] < '0' || address[0] > '9')) {result.Feedback = DynamicSandwich.BadAddress; result.IsValid = false; } return result;"
    },
    "PhoneNumber": {
      "type": [ "string", "null" ],
      "pattern": "(\\(\\d{3}\\))?\\s*\\d{3}(-|\\s*)\\d{4}"
    },
    "DeliveryTime": {
      "Templates": {
        "StatusFormat": {
          "Patterns": [ "{&}: {:t}" ],
          "FieldCase": "None"
        }
      },
      "DateTime": true,
      "type": [
        "string",
        "null"
      ],
      "After": [ { "Confirm": [ "Do you want to order your {Length} {Sandwich} on {Ingredients.Bread} {&Ingredients.Bread} with {[{Ingredients.Cheese} {Ingredients.Toppings} {Ingredients.Sauces} to be sent to {DeliveryAddress} {?at {DeliveryTime}}?" ] } ]
    },
    "Rating": {
      "Describe": "your experience today",
      "type": [
        "number",
        "null"
      ],
      "minimum": 1,
      "maximum": 5,
      "After": [ { "Message": [ "Thanks for ordering your sandwich!" ] } ]
    }
  },
  "OnCompletion": "await context.PostAsync(\"We are currently processing your sandwich. We will message you the status.\");"
}
```

## <a name="implement-formflow-with-json-schema"></a>Implementar o FormFlow com o esquema JSON

Para implementar o FormFlow com um esquema JSON, use `FormBuilderJson`, que oferece suporte à mesma interface fluente que o `FormBuilder`. Este exemplo de código mostra como implementar o esquema JSON para o bot sanduíche anotado descrito em [Personalizar um formulário usando o FormBuilder](bot-builder-dotnet-formflow-formbuilder.md).

[!code-csharp[Use JSON schema](../includes/code/dotnet-formflow-json-schema.cs#useSchema)]

## <a name="sample-code"></a>Código de exemplo

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Recursos adicionais

- [Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md)
- [Recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md)
- [Personalizar um formulário usando FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Localizar o conteúdo do formulário](bot-builder-dotnet-formflow-localize.md)
- [Personalizar a experiência do usuário com o idioma padrão](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>

[numericAttribute]: /dotnet/api/microsoft.bot.builder.formflow.numericattribute

[patternAttribute]: /dotnet/api/microsoft.bot.builder.formflow.patternattribute

[templateAttribute]: /dotnet/api/microsoft.bot.builder.formflow.templateattribute

[promptAttribute]: /dotnet/api/microsoft.bot.builder.formflow.promptattribute

[describeAttribute]: /dotnet/api/microsoft.bot.builder.formflow.describeattribute
