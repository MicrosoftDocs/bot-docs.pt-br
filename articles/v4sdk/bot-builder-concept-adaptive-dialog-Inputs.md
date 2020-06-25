---
title: Solicitar entrada do usuário em caixas de diálogo adaptáveis
description: Coleta de entrada de usuários usando caixas de diálogo adaptáveis
keywords: bot, usuário, ações, gatilhos, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 04/27/2020
ms.openlocfilehash: e10a3c367e0052492b30bf0a25f9b78630e5d0b1
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073964"
---
<!--TODO P2: For each of the inputs in this article, link to relevant samples in BotBuilder-Samples as examples of how that input is used-->
# <a name="asking-for-user-input-in-adaptive-dialogs"></a>Solicitar entrada do usuário em caixas de diálogo adaptáveis

O SDK do Bot Framework define diversas caixas de diálogo de entrada para coletar e validar a entrada do usuário.

> [!IMPORTANT]
> As ações (_ações de gatilho_) são caixas de diálogo e, como tal, têm todo o poder e a flexibilidade de que você precisa para criar um fluxo de conversa totalmente funcional e robusto. Embora as ações incluídas no SDK do Bot Framework sejam amplas, você também pode criar suas próprias ações personalizadas para executar praticamente qualquer tarefa ou processo especializado necessário.

## <a name="prerequisites"></a>Pré-requisitos

* É útil ter uma compreensão geral das caixas de diálogo adaptáveis no SDK do Bot Framework V4. Confira mais informações em [Introdução às caixas de diálogo adaptáveis][1].
* Obtenha uma compreensão geral de [Eventos e gatilhos em caixas de diálogo adaptáveis][2].
* Obtenha uma compreensão geral de [Ações em caixas de diálogo adaptáveis][3].
* Obtenha uma compreensão geral de [Escopos de memória e gerenciamento de estado em caixas de diálogo adaptáveis][7].
* Obtenha familiaridade com [Modelos de geração de linguagem][9].
* Obtenha familiaridade com [Expressões adaptáveis][10].

> [!TIP]
> Essa sintaxe definida nos [modelos de Geração de Linguagem][9], que inclui [Expressões adaptáveis][10], é usada no objeto `ActivityTemplate` que é exigido por vários parâmetros usados na maioria das ações de entrada fornecidas no SDK do Bot Framework.

## <a name="inputs"></a>Entradas

Assim como os [prompts][4], você pode usar _entradas_ em caixas de diálogo adaptáveis para solicitar e coletar entrada de um usuário, validá-la e aceitá-la na memória. Uma entrada:

* Associa o resultado do prompt a uma propriedade em um escopo de [gerenciamento de estado][7].
* Faz a solicitação ao usuário somente se a propriedade de resultado ainda não tem um valor.
* Salva a entrada na propriedade especificada se a entrada do usuário corresponde ao tipo de entidade esperado.
* Aceita restrições de validação, como mínimo, máximo e assim por diante.
* Pode usar como entrada as intenções localmente relevantes dentro de uma caixa de diálogo, bem como usar a interrupção como uma técnica para emergir a resposta do usuário para uma caixa de diálogo pai apropriada que possa tratá-la.

<!-- TODO P0.5: For more information, see [about interruptions in adaptive dialogs](./ all-about-interruptions.md). -->

A biblioteca de diálogos adaptáveis define os seguintes tipos de entrada:

| Tipo de entrada       | Classe de entrada                       | Descrição                                          | Retornos                                      |
| ---------------- | --------------------------------- | ---------------------------------------------------- | -------------------------------------------- |
| Classe base       | [InputDialog](#inputdialog)       | Essa é a classe base da qual derivam todas as classes de entrada. Ela define todas as propriedades compartilhadas. |
| Texto             | [TextInput](#textinput)           | Usado para solicitar aos usuários uma **palavra ou frase**.   | Uma cadeia de caracteres.                                    |
| Número           | [NumberInput](#numberinput)       | Usado para solicitar um **número** aos usuários.             | Um valor numérico.                             |
| Confirmação     | [ConfirmInput](#confirminput)     | Usado para solicitar uma **confirmação** ao usuário.    | Um valor booliano.                             |
| Múltipla escolha  | [ChoiceInput](#choiceinput)       | Usado para solicitar uma escolha entre um **conjunto de opções**. | O valor ou o índice da seleção.         |
| Arquivo ou anexo |[AttachmentInput](#attachmentinput)| Usado para solicitar/habilitar um usuário para **carregar um arquivo**.| Uma coleção de objetos de anexos.          |
| Data ou hora     | [DateTimeInput](#datetimeinput)   | Usado para solicitar aos usuários uma **data ou hora**.   | Uma coleção de objetos de data e hora.           |
| Logon do OAuth      | [OAuthInput](#oauthinput)              | Usado para permitir que os usuários **entrem em um site seguro**.| Uma resposta do token.                        |

<!--TODO P1: Add a general section on locales to show all Supported locales that can be used in the  `DefaultLocale` property.
`DefaultLocale`: Sets the default locale for input processing that will be used unless one is passed by the caller. Supported locales are Spanish, Dutch, English, French, German, Japanese, Portuguese, Chinese.
--->

### <a name="inputdialog"></a>InputDialog

As classes de entrada fornecidas pelo SDK do Bot Framework derivam do _diálogo de entrada_ de base, que deriva da classe de _diálogo_. Todas as caixas de diálogo de entrada têm estas propriedades em comum:

#### <a name="allowinterruptions"></a>AllowInterruptions

Uma expressão booliana. `true` para permitir que a caixa de diálogo pai interrompa a caixa de diálogo de entrada; caso contrário, `false`.

<!--TODO P0.5: Link to information on interruptions-->

> [!NOTE]
> A caixa de diálogo pai de entrada também pode causar interrupções. Ou seja, quando `AllowInterruptions` for `true`, o reconhecedor na caixa de diálogo adaptável pai das entradas será executado, e seus gatilhos serão avaliados.

#### <a name="alwaysprompt"></a>AlwaysPrompt

Uma expressão booliana. Se `true`, sempre solicitará a entrada; se `false`, somente avisará quando a [propriedade](#property) associada for nula ou vazia.

<!--TODO P1:
This is true enough. I lost some sanity following the code down a rabbit hole. The full story is that the input dialog will run the property value (or the evaluation of the value value if _property is null) through all its recognition and validation logic. Thus, if it is null or empty or isn't recognized or doesn't validate, then the input dialog will prompt the user.
This clarification is probably a low priority, but should happen at some point. Since this logic involves about 4 properties and is a little complex, it would be worth describing outside the list of properties.
--->

#### <a name="defaultvalue"></a>DefaultValue

Uma expressão adaptável que representa o resultado padrão para a caixa de diálogo de entrada. Se a entrada do usuário falhar por [contagem máxima de turnos](#maxturncount), a caixa de diálogo de entrada terminará e definirá o valor padrão dessa propriedade.



```csharp
DefaultValue = "9"
```

#### <a name="defaultvalueresponse"></a>DefaultValueResponse

A resposta a ser enviada quando as [validações](#validations) dos turnos de [MaxTurnCount](#maxturncount) falharem na entrada dos usuários e um [DefaultValue](#defaultvalue) estiver especificado.

```csharp
DefaultValueResponse = new ActivityTemplate("Sorry, we have reach the maximum number of attempts of '${%MaxTurnCount}' to get your input, so for now, we will go with a default value of: '${%DefaultValue}'")
```

#### <a name="invalidprompt"></a>InvalidPrompt

O modelo de atividade com o qual se solicita novamente a entrada caso a entrada do usuário seja reconhecida, mas falhe na validação. (Se a [contagem máxima de ativações](#maxturncount) da entrada for ultrapassada e falhar, então o [valor padrão](#defaultvalue) da [resposta de valor padrão](#defaultvalueresponse) será enviado.)

>[!NOTE]
> A propriedade `InvalidPrompt` só funciona em conjunto com a propriedade [Validações](#validations).

```csharp
InvalidPrompt = new ActivityTemplate("Sorry, {this.value} does not work. Please enter a number between one and ten (1-10).")
```

#### <a name="maxturncount"></a>MaxTurnCount

A expressão de um número inteiro. O número máximo de tentativas para solicitar uma entrada. Se esse limite for excedido, o [valor padrão](#defaultvalue) será usado, e a [resposta de valor padrão](#defaultvalueresponse) será enviada.

```csharp
MaxTurnCount = 2
```

#### <a name="prompt"></a>Prompt

O modelo da atividade que solicita inicialmente a entrada do usuário.

```csharp
Prompt = new ActivityTemplate("Hi, What is your name?")
```

#### <a name="property"></a>Propriedade

O caminho da memória, ou uma expressão avaliada como o caminho da memória, da propriedade à qual a caixa de diálogo de entrada está associada. O caminho da memória será usado para obter o valor inicial da caixa de diálogo de entrada. Também é usado para armazenar o resultado dessa caixa de diálogo. As propriedades `Prompt` e `Value` passam pelas etapas de reconhecimento e validação; portanto, um valor inicial inválido resultará em um prompt.

Use-o para definir a qual propriedade a caixa de diálogo de entrada está vinculada. Por exemplo:

```csharp
Property = "user.name"
```

#### <a name="unrecognizedprompt"></a>UnrecognizedPrompt

O modelo de atividade com o qual se solicita novamente a entrada caso a entrada do usuário não seja reconhecida. (Se a [contagem máxima de ativações](#maxturncount) da entrada for ultrapassada e falhar, então o [valor padrão](#defaultvalue) da [resposta de valor padrão](#defaultvalueresponse) será enviado.)

```csharp
UnrecognizedPrompt = new ActivityTemplate("Sorry, '{turn.activity.text}' did not include a valid number")
```

#### <a name="validations"></a>Validações

Uma lista de expressões boolianas. A entrada reconhecida será inválida se qualquer uma dessas expressões for avaliada como `false`. Você pode usar  `this.value` para examinar a entrada do usuário nas expressões de validação. As validações são informadas usando [Expressões adaptáveis][10]

#### <a name="value"></a>Valor

Uma expressão de cadeia de caracteres. O caminho de memória da propriedade para obter a entrada em cada turno. Essa propriedade será usada como o valor inicial para a caixa de diálogo de entrada se a [propriedade](#property) da caixa de diálogo for avaliada como nula ou vazia. Se as propriedades de _valor_ e _propriedade_ da caixa de diálogo forem avaliadas como nulas ou vazias, a caixa de diálogo solicitará a entrada.

Do que é necessário se lembrar em relação à propriedade `Value`:

* A propriedade `Value` é uma [expressão adaptável][10].
* Se a expressão retornar nula, a caixa de diálogo de entrada poderá tentar efetuar o pull de dados diretamente da entrada.
* Se a expressão tiver um valor, será usada como entrada.
* A propriedade `Value` permite definir de que forma dados como o [Reconhecedor][8] estão associados à caixa de diálogo de entrada.

 Exemplos:

* Para associar a entrada a qualquer entidade etária reconhecida na entrada: "=@age"
* Para usar @age ou @number como entrada: "=coalesce(@age, @number)"

> [!TIP]
> Confira abaixo um exemplo que usa essas propriedades `InputDialog` no exemplo de código da seção [NumberInput](#numberinput).

### <a name="textinput"></a>TextInput

Use a _entrada de texto_ quando quiser aceitar textualmente a entrada do usuário como o valor de uma informação específica que seu bot está tentando coletar. Os exemplos incluem _nome do usuário_ e o _assunto de um email_.

A ação `TextInput` herda todas as propriedades definidas em [InputDialog](#inputdialog) e define uma propriedade adicional:

* `OutputFormat`: usando [expressões adaptáveis][10], você pode modificar a cadeia de caracteres, por exemplo, no código abaixo, a expressão `OutputFormat` colocará em maiúscula a primeira letra de cada palavra do nome dos usuários.

#### <a name="textinput-example"></a>Exemplo de TextInput

``` C#
// Create root dialog as an adaptive dialog.
var getUserNameDialog = new AdaptiveDialog("GetUserNameDialog");

// Add an intent trigger.
getUserNameDialog.Triggers.Add(new OnIntent()
{
    Intent = "GetName",
    Actions = new List<Dialog>()
    {
        // Add TextInput step. This step will capture user's input and use it to populate the 'user.name' property.
        new TextInput()
        {
            Property = "user.fullName",
            Prompt = new ActivityTemplate("Please enter your full name.")
            OutputFormat = "join(foreach(split(this.value, ' '), item, concat(toUpper(substring(item, 0, 1)), substring(item, 1))), ' ')"
        }
    }
});
```
<!--TODO P2: List Samples that use this action, for example: CoreBot->RootDialog.cs--->

### <a name="numberinput"></a>NumberInput

Solicita um número ao usuário.

A ação `NumberInput` herda todas as propriedades definidas em [InputDialog](#inputdialog) e define estas duas propriedades adicionais:

<!--https://blog.botframework.com/2018/02/01/contributing-luis-microsoft-recognizers-text-part-2/-->

1. `DefaultLocale`: define a localidade padrão para o processamento de entrada a ser usado, a menos que um seja passado pelo chamador. As localidades com suporte estão em espanhol, holandês, inglês, francês, alemão, japonês, português, chinês.
2. `OutputFormat`: usando [expressões adaptáveis][10], você pode realizar ações para manipular o número de alguma forma. Por exemplo, você poderia escrever uma expressão para converter um número inserido como uma temperatura em Fahrenheit para seu valor equivalente em Celsius, executar um cálculo matemático, como adicionar impostos e frete ao valor inserido, ou simplesmente executar uma conversão de tipo para especificar que o valor é um número inteiro ou flutuante, como demonstrado no código de exemplo abaixo.

#### <a name="numberinput-example"></a>Exemplo de NumberInput

``` C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(),
    Triggers = new List<OnCondition> ()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new NumberInput() {
                    Property = "user.favoriteNumber",
                    Prompt = new ActivityTemplate("Give me your favorite number (1-10)"),
                    // You can refer to incoming user message via turn.activity.text
                    UnrecognizedPrompt = new ActivityTemplate("Sorry, '{turn.activity.text}' did not include a valid number"),
                    // You can provide a list of validation expressions. Use turn.value to refer to any value extracted by the recognizer.
                    Validations = new List<String> () {
                        "int(this.value) >= 1",
                        "int(this.value) <= 10"
                    },
                    InvalidPrompt = new ActivityTemplate("Sorry, {this.value} does not work. Can you give me a different number that is between 1-10?"),
                    MaxTurnCount = 2,
                    DefaultValue = "9",
                    DefaultValueResponse = new ActivityTemplate("Sorry, we have tried for '${%MaxTurnCount}' number of times and I'm still not getting it. For now, I'm setting '${%property}' to '${%DefaultValue}'"),
                    AllowInterruptions = "false",
                    AlwaysPrompt = true,
                    OutputFormat = "float(this.value)"
                },
                new SendActivity("Your favorite number is {user.favoriteNumber}")
            }
        }
    }
};
```

### <a name="confirminput"></a>ConfirmInput

As **entradas de confirmação** são úteis para usar depois que você já fez uma pergunta ao usuário e quer confirmar a resposta. Ao contrário da ação com **Múltiplas escolhas**, que permite ao bot apresentar ao usuário uma lista de opções para escolha, os prompts de confirmação solicitam que o usuário tome uma decisão binária (sim/não).

A ação `ConfirmInput` herda todas as propriedades definidas em [InputDialog](#inputdialog) e define estas propriedades adicionais:

1. `ChoiceOptions`: usada para formatar a apresentação das opções de confirmação mostradas ao usuário, esta é uma [expressão adaptável][10] avaliada como um objeto `ChoiceSet`. Esse objeto `ChoiceSet` só será usado como um backup se a tentativa inicial de reconhecimento do `ConfirmInput` falhar. Quando a ação `ConfirmInput` é executada, primeiro ela tenta avaliar a entrada como um valor booliano. Se isso falhar, ela fará uma segunda tentativa, desta vez usando um reconhecedor de opção que faz a avaliação em relação ao ChoiceSet.
2. `ConfirmChoices`: as opções ou uma [expressão adaptável][10] que avalia as opções apresentadas ao usuário.
3. `DefaultLocale`: define a localidade padrão para o processamento de entrada a ser usado, a menos que um seja passado pelo chamador. As localidades com suporte estão em espanhol, holandês, inglês, francês, alemão, japonês, português, chinês
4. `OutputFormat`: o formato de saída padrão para a ação `ConfirmInput` é booliano. Você pode substituir isso usando a propriedade `OutputFormat`, uma das [expressões adaptáveis][10] que se pode usar para modificar os resultados de retorno, se necessário. Por exemplo, é possível usá-la para fazer com que a ação `ConfirmInput` retorne um número: `OutputFormat = "if(this.value == true, 1, 0)"`.
Se essa propriedade estiver definida, a saída da expressão será o valor retornado pela caixa de diálogo.
5. `Style`: define o tipo de lista a apresentar ao usuário ao confirmar a entrada. Usa a enumeração `ListStyle`, que consiste em:
    1. `None`: não inclui nenhuma opção para o prompt.
    2. `Auto`: seleciona automaticamente o estilo apropriado para o canal atual.
    3. `Inline`: adiciona opções ao prompt, como uma lista embutida.
    4. `List`: adiciona opções ao prompt, como uma lista numerada.
    5. `SuggestedAction`: adiciona opções ao prompt, como ações sugeridas.
    6. `HeroCard`: adiciona opções ao prompt, como um HeroCard com botões.

#### <a name="confirminput-example"></a>Exemplo de ConfirmInput

``` C#
// Create adaptive dialog.
var ConfirmationDialog = new AdaptiveDialog("ConfirmationDialog") {
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                // Add confirmation input.
                new ConfirmInput()
                {
                    Property = "turn.contoso.travelBot.confirmOutcome",
                    // Since this prompt is built as a generic confirmation wrapper, the actual prompt
                    // text is read from a specific memory location. The caller of this dialog needs to
                    // set the prompt string to that location before calling the "ConfirmationDialog".
                    // All prompts support rich language generation based resolution for output generation.
                    // See https://aka.ms/language-generation-file-format to learn more about the LG
                    // template format used in the ActivityTemplate object.
                    Prompt = new ActivityTemplate("${turn.contoso.travelBot.confirmPromptMessage}")
                }
            }
        }
    }
};
```

### <a name="choiceinput"></a>ChoiceInput

As **entradas de opções** são um conjunto de opções apresentado ao usuário como uma seleção de **Múltipla escolha** que permite apresentar aos usuários uma lista de opções a escolher.

A ação `ChoiceInput` herda todas as propriedades definidas em [InputDialog](#inputdialog) e define estas propriedades adicionais:

1. `ChoiceOptions`: essa propriedade é usada para formatar a apresentação das opções de confirmação exibidas ao usuário.
2. `Choices`: uma expressão adaptável que é avaliada como uma ChoiceSet que contém a lista [ordenada] de opções para o usuário escolher.
3. `DefaultLocale`: define a localidade padrão para o processamento de entrada a ser usado, a menos que um seja passado pelo chamador. As localidades com suporte estão em espanhol, holandês, inglês, francês, alemão, japonês, português, chinês
4. `OutputFormat`: uma expressão adaptável avaliada como um dos valores de enumeração `ChoiceOutputFormat`:

    ~~~csharp
    switch (this.OutputFormat.GetValue(dc.State))
    {
        case ChoiceOutputFormat.Value:
        default:
            dc.State.SetValue(VALUE_PROPERTY, foundChoice.Value);
            break;
        case ChoiceOutputFormat.Index:
            dc.State.SetValue(VALUE_PROPERTY, foundChoice.Index);
            break;
    }
    ~~~

5. `Style`: define o tipo de lista a apresentar ao usuário ao confirmar a entrada. Usa a enumeração `ListStyle`, que consiste em:
    1. `None`: não inclui nenhuma opção para o prompt.
    2. `Auto`: seleciona automaticamente o estilo apropriado para o canal atual.
    3. `Inline`: adiciona opções ao prompt, como uma lista embutida.
    4. `List`: adiciona opções ao prompt, como uma lista numerada.
    5. `SuggestedAction`: adiciona opções ao prompt, como ações sugeridas.
    6. `HeroCard`: adiciona opções ao prompt, como um HeroCard com botões.
6. `RecognizerOptions`: `FindChoicesOptions` ou a expressão que é avaliada como `FindChoicesOptions`. O `FindChoicesOptions` tem estas propriedades:
    1. `NoValue`: Um valor booliano. `true` para pesquisar na propriedade de _valor_ de cada opção; caso contrário, `false`. O padrão é `false`.
    2. `NoAction`: Um valor booliano. `true` para pesquisar o título na propriedade de _ação_ de cada opção; caso contrário, `false`. O padrão é `false`.
    3. `RecognizeNumbers`: Um valor booliano. `true` para permitir que a entrada volte a usar um reconhecedor de número para corresponder às opções de entrada; caso contrário, `false`. O padrão é `true`.
    4. `RecognizeOrdinals`: Um valor booliano. `true` para permitir que a entrada volte a usar um número ordinal para corresponder às opções de entrada; caso contrário, `false`. O padrão é `true`.

<!--TODO Nice to have: Need more complete description of RecognizerOptions --->

#### <a name="choiceinput-example"></a>Exemplo de ChoiceInput

``` C#
// Create an adaptive dialog.
var getUserFavoriteColor = new AdaptiveDialog("GetUserColorDialog");
getUserFavoriteColor.Triggers.Add(new OnIntent()
{
    Intent = "GetColor",
    Actions = new List<Dialog>()
    {
        // Add choice input.
        new ChoiceInput()
        {
            // Output from the user is automatically set to this property
            Property = "user.favColor",

            // List of possible styles supported by choice prompt.
            Style = Bot.Builder.Dialogs.Choices.ListStyle.Auto,
            Prompt = new ActivityTemplate("What is your favorite color?"),
            Choices = new ChoiceSet(new List<Choice>()
            {
                new Choice("Red"),
                new Choice("Blue"),
                new Choice("Green")
            })
        }
    }
});
```

### <a name="datetimeinput"></a>DateTimeInput

<!-- TODO Post R9, maybe mention that this often generates multiple results, depending on how ambiguous the input was. -->

Solicita uma data/hora.

A ação `DateTimeInput` herda todas as propriedades definidas em [InputDialog](#inputdialog) e define essas propriedades adicionais:

1. `DefaultLocale`: define a localidade padrão para o processamento de entrada a ser usado, a menos que um seja passado pelo chamador. As localidades com suporte estão em espanhol, holandês, inglês, francês, alemão, japonês, português, chinês.
2. `OutputFormat`: A saída padrão para `DateTimeInput` é uma matriz de `DateTimeResolutions`; essa propriedade permite definir uma expressão adaptável. Qualquer que seja o valor retornado, ele se torna o valor final da propriedade `property` da caixa de diálogo, seja ou não avaliado como uma data/hora.

#### <a name="datetimeinput-example"></a>Exemplo de DateTimeInput

``` C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(_templateEngine),
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new DateTimeInput()
                {
                    Property = "$userDate",
                    Prompt = new ActivityTemplate("Give me a date"),
                },
                new SendActivity("You gave me ${$userDate}")
            }
        }
    }
};
```

### <a name="attachmentinput"></a>AttachmentInput

Use para solicitar um anexo do usuário como entrada.

A ação `AttachmentInput` herda todas as propriedades definidas em [InputDialog](#inputdialog) e define esta propriedade adicional:

- `OutputFormat`: O `AttachmentOutputFormat` ou uma expressão que é avaliada como `AttachmentOutputFormat`. Os valores de `AttachmentOutputFormat` válidos são:
    1. `All`: retorna todos os anexos como uma lista.
    2. `First`: retorna somente o primeiro anexo.

#### <a name="attachmentinput-example"></a>Exemplo de AttachmentInput

``` C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(_templateEngine),
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new AttachmentInput()
                {
                    Property = "$userAttachmentCarImage",
                    Prompt = new ActivityTemplate("Please give me an image of your car. Drag drop the image to the chat canvas."),
                    OutputFormat = AttachmentOutputFormat.All
                },
                new SendActivity("You gave me ${$userAttachmentCarImage}")
            }
        }
    }
};
```

### <a name="oauthinput"></a>OAuthInput

Use para solicitar a entrada do usuário.

A ação `OAuthInput` herda todas as propriedades definidas em [InputDialog](#inputdialog) e define estas propriedades adicionais:

1. `ConnectionName`: nome da conexão OAuth configurada na página de configurações do Serviço de Bot do Azure para o bot.
2. `Text`: texto adicional a ser exibido no cartão de entrada.
3. `Title`: título adicional a ser exibido no cartão de entrada.
4. `Timeout`: o número de milissegundos que `OAuthInput` aguarda para a conclusão da autenticação do usuário.  O padrão é de 900.000 segundos, que são 15 minutos.

A ação `OAuthInput` também define dois novos métodos:

1. `GetUserTokenAsync`: esse método tenta recuperar o token do usuário.
2. `SignOutUserAsync`: esse método desconecta o usuário.


#### <a name="oauth-example"></a>Exemplo de OAuth

```C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(_templateEngine),
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new OAuthInput()
                {
                    // Name of the connection configured on Azure Bot Service for the OAuth connection.
                    ConnectionName = "GitHub",

                    // Title of the sign in card.
                    Title = "Sign in",

                    // Text displayed in sign in card.
                    Text = "Please sign in to your GitHub account.",

                    // Property path to store the authorization token.
                    TokenProperty = "user.authToken"
                },
                new SendActivity("You are signed in with token = ${user.authToken}")
            }
        }
    }
};
```

#### <a name="additional-information-related-to-oauth"></a>Informações adicionais relacionadas ao OAuth

Os links a seguir fornecem informações generalizadas sobre o tópico de autenticação no SKD do Microsoft Bot Framework. Essas informações não são personalizadas ou específicas de caixas de diálogo adaptáveis.

* [Autenticação de bot][5]
* [Adicionar autenticação a um bot][6]

## <a name="additional-information"></a>Informações adicionais

* Confira mais sobre expressões no artigo [Expressões adaptáveis][10].

[1]:bot-builder-adaptive-dialog-introduction.md
[2]:bot-builder-concept-adaptive-dialog-triggers.md
[3]:bot-builder-concept-adaptive-dialog-actions.md
[4]:https://aka.ms/bot-builder-concept-dialog#prompts
[5]:https://aka.ms/azure-bot-authentication
[6]:https://aka.ms/azure-bot-add-authentication
[7]:bot-builder-concept-adaptive-dialog-memory-states.md
[8]:bot-builder-concept-adaptive-dialog-recognizers.md
[9]:bot-builder-concept-adaptive-dialog-generators.md
[10]:bot-builder-concept-adaptive-expressions.md
