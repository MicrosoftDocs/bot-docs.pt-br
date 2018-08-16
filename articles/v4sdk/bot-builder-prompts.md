---
title: Solicitar que os usuários para a entrada | Microsoft Docs
description: Saiba como solicitar aos usuários informações sobre o SDK do Bot Builder para Node.js.
keywords: prompts, diálogos, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, reprompt, validação
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/10/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b08c087bcce4a3dcee5de20311e2f7b890ea2f6b
ms.sourcegitcommit: b45e16cac2febb7034da4ccd3af3bd7e6f430c31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39469273"
---
# <a name="prompt-users-for-input"></a>Solicitar usuários para entrada

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Frequentemente os bots reúnem suas informações através de perguntas feitas ao usuário. Você pode simplesmente enviar ao usuário uma mensagem padrão usando o método _enviar atividade_ do objeto de contexto de turno para solicitar uma entrada de string; no entanto, o Bot Builder SDK fornece uma biblioteca de **caixas de diálogo** que você pode usar para solicitar diferentes tipos de informações. Este tópico fornece detalhes sobre como usar **solicita** para solicitar que um usuário de entrada.

Este artigo descreve como usar prompts dentro de uma caixa de diálogo. Para obter informações sobre como usar caixas de diálogo em geral, consulte [usando as caixas de diálogo para gerenciar o fluxo da conversa](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prompt-types"></a>Tipos de prompt

A biblioteca de caixas de diálogo oferece uma série de diferentes tipos de prompts, cada solicitação de um tipo diferente de resposta.

| Prompt | DESCRIÇÃO |
|:----|:----|
| **AttachmentPrompt** | Solicitar ao usuário um anexo, como um documento ou uma imagem. |
| **ChoicePrompt** | Solicita que o usuário para escolher um conjunto de opções. |
| **ConfirmPrompt** | Solicita que o usuário confirme sua ação. |
| **DatetimePrompt** | Solicita ao usuário para uma data e hora. Os usuários podem responder usando linguagem natural como "Amanhã às 20h" ou "Sexta-feira às 10h". O SDK do Bot Framework usa a entidade pré-construída `builtin.datetimeV2`LUIS. Para obter mais informações, consulte [builtin.datetimev2](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-prebuilt-entities#builtindatetimev2). |
| **NumberPrompt** | Solicitar ao usuário um número. O usuário pode responder com "10" ou "dez". Se a resposta for "dez", por exemplo, o prompt converterá a resposta em um número e retornará `10` como resultado. |
| **TextPrompt** | Solicitar ao usuário uma sequência de texto. |

## <a name="add-references-to-prompt-library"></a>Adicione referências para solicitar a biblioteca

Você pode obter a biblioteca de **caixas de diálogo** adicionando o pacote de **caixas de diálogo** ao seu bot. Nós cobrimos diálogos em [usando diálogos para gerenciar o fluxo de conversação](bot-builder-dialog-manage-conversation-flow.md), mas usaremos diálogos para nossos prompts.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Instale o pacote **Microsoft.Bot.Builder.Dialogs** do NuGet.

Em seguida, inclua referência à biblioteca do seu código bot.

```cs
using Microsoft.Bot.Builder.Dialogs;
```

Você pode definir um diálogo como uma classe ou in-line como uma propriedade no seu arquivo de código bot.

O código neste artigo é escrito para um diálogo definido como uma classe.
Os exemplos a seguir presumem que você esteja adicionando código ao construtor da caixa de diálogo.

O fluxo principal do seu diálogo é sua coleção de etapas e precisa receber um ID. Seu bot usa esse ID para recuperar o diálogo, então é uma boa prática expor isso como uma constante.

```cs
public class MyDialog : DialogSet
{
    public const string Name = "mainDialog";

    public MyDialog()
    {
        // Define your dialog's prompts and steps here.
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Instale o pacote de caixas de diálogo do NPM:

```cmd
npm install --save botbuilder-dialogs
```

Para usar **caixas de diálogo** no seu bot, incluí-lo no código do bot.

No arquivo App. js, adicione o seguinte.

```javascript
const {DialogSet} = require("botbuilder-dialogs");
const dialogs = new DialogSet();
```

---

## <a name="prompt-the-user"></a>Solicitar que o usuário

Para solicitar uma entrada ao usuário, você pode adicionar um aviso à sua caixa de diálogo. Por exemplo, você pode definir um prompt do tipo **TextPrompt** e fornecer um ID de diálogo de **textPrompt**:

Depois que um diálogo de prompt é adicionado, você pode usá-lo em uma caixa de diálogo simples de duas etapas ou usar vários prompts juntos em uma cascata de várias etapas. Uma caixa de diálogo *waterfall* é simplesmente uma maneira de definir uma sequência de etapas. Para obter mais informações, consulte a seção [usando as caixas de diálogo](bot-builder-dialog-manage-conversation-flow.md#using-dialogs-to-guide-the-user-through-steps) de [para gerenciar o fluxo de conversas com as caixas de diálogo](bot-builder-dialog-manage-conversation-flow.md).

No primeiro turno, o diálogo solicita o nome do usuário e, no segundo turno, o diálogo processa a entrada do usuário como uma resposta ao prompt.

Por exemplo, o seguinte diálogo solicita ao usuário seu nome e, em seguida, cumprimenta-o pelo nome:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Cada solicitação que você usa na sua caixa de diálogo também recebe um nome, usado pela caixa de diálogo ou pelo seu bot para acessar o prompt. Em todos esses exemplos, estamos expondo os IDs de prompt como constantes.

Uma chamada ao método **Prompt** ou **End** do contexto da caixa de diálogo sinaliza o fim da etapa para a caixa de diálogo. Sem essas instruções, a caixa de diálogo não será executada corretamente.

```csharp
/// <summary>Defines a simple greeting dialog that asks for the user's name.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the text prompt.</summary>
        public const string Text = "textPrompt";
    }

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        Add(Inputs.Text, new TextPrompt());
        Add(Name, new WaterfallStep[]
        {
            // Each step takes in a dialog context, arguments, and the next delegate.
            async (dc, args, next) =>
            {
                // Prompt for the user's name.
                await dc.Prompt(Inputs.Text, "What is your name?").ConfigureAwait(false);
            },
            async(dc, args, next) =>
            {
                var user = (string)args["Text"];
                await dc.Context.SendActivity($"Hi {user}!").ConfigureAwait(false);
                await dc.End().ConfigureAwait(false);
            }
        });
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {TextPrompt} = require("botbuilder-dialogs");
```

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
dialogs.add('textPrompt', new TextPrompt());
dialogs.add('greetings', [
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, userName){
        await dc.context.sendActivity(`Hi ${userName}!`);
        await dc.end();
    }
]);
```

---

> [!NOTE]
> Para iniciar um diálogo, obtenha um contexto de diálogo e use seu método _begin_. Para mais informações, consulte [use caixas de diálogo para gerenciar o fluxo de conversas](./bot-builder-dialog-manage-conversation-flow.md).

## <a name="reusable-prompts"></a>Prompts reutilizáveis

Um prompt pode ser reutilizado para solicitar informações diferentes usando o mesmo tipo de prompt. Por exemplo, o código de exemplo acima definiu um prompt de texto e o usou para perguntar ao usuário seu nome. Se você quisesse, por exemplo, você também pode usar o mesmo prompt para pedir ao usuário outra string de texto; como "Onde você trabalha?".

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/// <summary>Defines a simple greeting dialog that asks for the user's name and place of work.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the text prompt.</summary>
        public const string Text = "textPrompt";
    }

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        Add(Inputs.Text, new TextPrompt());
        Add(Name, new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                // Prompt for the user's name.
                await dc.Prompt(Inputs.Text, "What is your name?").ConfigureAwait(false);
            },
            async(dc, args, next) =>
            {
                var user = (string)args["Text"];

                // Ask them where they work.
                await dc.Prompt(Inputs.Text, $"Hi {user}! Where do you work?").ConfigureAwait(false);
            },
            async(dc, args, next) =>
            {
                var workplace = (string)args["Text"];

                await dc.Context.SendActivity($"{workplace} is a cool place!").ConfigureAwait(false);
                await dc.End().ConfigureAwait(false);
            }
        });
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
// Ask them where they work.
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
dialogs.add('greetings',[
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, userName){
        await dc.context.sendActivity(`Hi ${userName}!`);

        // Ask them where they work.
        await dc.prompt('textPrompt', 'Where do you work?');
    },
    async function(dc, workPlace){
        await dc.context.sendActivity(`${workPlace} is a cool place!`);

        await dc.end();
    }
]);
```

---

No entanto, se você deseja emparelhar o prompt com o valor esperado que o prompt está pedindo, você poderia fornecer a cada prompt um *dialogId* exclusivo. Uma caixa de diálogo é adicionada com um ID exclusivo. Usando IDs diferentes, você também pode criar várias caixas de diálogo do **prompt** do mesmo tipo. Por exemplo, você poderia criar dois diálogos **TextPrompt** para o exemplo acima:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/// <summary>The ID of the main dialog in the set.</summary>
public const string Name = "mainDialog";

/// <summary>Defines the IDs of the prompts in the set.</summary>
public struct Inputs
{
    /// <summary>The ID of the name prompt.</summary>
    public const string Name = "namePrompt";

    /// <summary>The ID of the work prompt.</summary>
    public const string Work = "workPrompt";
}

/// <summary>Defines the prompts and steps of the dialog.</summary>
public MyDialog()
{
    Add(Inputs.Name, new TextPrompt());
    Add(Inputs.Work, new TextPrompt());
    Add(Name, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the user's name.
            await dc.Prompt(Inputs.Name, "What is your name?").ConfigureAwait(false);
        },
        async(dc, args, next) =>
        {
            var user = (string)args["Text"];

            // Ask them where they work.
            await dc.Prompt(Inputs.Work, $"Hi {user}! Where do you work?").ConfigureAwait(false);
        },
        async(dc, args, next) =>
        {
            var workplace = (string)args["Text"];

            await dc.Context.SendActivity($"{workplace} is a cool place!").ConfigureAwait(false);
            await dc.End().ConfigureAwait(false);
        }
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
dialogs.add('namePrompt', new TextPrompt());
dialogs.add('workPlacePrompt', new TextPrompt());
```

---

Por uma questão de reusabilidade de código, a definição de um único `textPrompt` funcionaria para todos esses prompts, porque eles pedem uma string de texto como uma resposta. No entanto, onde a capacidade de nomear diálogos é útil quando você precisa validar a entrada do prompt. Nesse caso, os prompts podem estar usando **TextPrompt**, mas cada um está procurando por um conjunto diferente de valores. Vamos dar uma olhada em como você pode validar respostas prontas usando um `NumberPrompt`.

## <a name="specify-prompt-options"></a>Especificar opções de prompt

Quando você usa um prompt em uma etapa da caixa de diálogo, também pode fornecer opções de prompt, como uma string de nova solicitação.

Especificar uma string de nova solicitação é útil quando a entrada do usuário pode falhar em satisfazer um prompt, seja porque está em um formato que o prompt não pode analisar, como "amanhã" para um prompt numérico, ou a entrada falha em um critério de validação.

> [!TIP]
> Quando você cria um prompt numérico, é necessário especificar a cultura de entrada que será usada. O prompt de número pode interpretar uma ampla variedade de entradas, como "doze" ou "um quarto", além de "12" e "0,25". A cultura de entrada ajuda o prompt a interpretar mais corretamente a entrada do usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

As culturas de entrada são definidas em uma biblioteca adicional.

```csharp
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
```

O código a seguir adicionaria um prompt numérico a um conjunto de diálogos existente, **diálogos**.

```csharp
dialogs.Add("numberPrompt", new NumberPrompt<int>(Culture.English));
```

Em uma etapa da caixa de diálogo, o código a seguir solicitaria ao usuário uma entrada e forneceria uma string de aviso para ser usada se a entrada não puder ser interpretada como um número.

```csharp
await dc.Prompt("numberPrompt", "How many people are in your party?", new PromptOptions()
{
    RetryPromptString = "Sorry, please specify the number of people in your party."
}).ConfigureAwait(false);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {NumberPrompt} = require("botbuilder-dialogs");
```

```javascript
await dc.prompt('numberPrompt', 'How many people in your party?', { retryPrompt: `Sorry, please specify the number of people in your party.` })
```

```javascript
dialogs.add('numberPrompt', new NumberPrompt());
```

---

Em particular, o prompt de escolha requer algumas informações adicionais, a lista de opções disponíveis para o usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Este exemplo usa tipos dos namespaces a seguir.

```csharp
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts.Choices;
using Microsoft.Bot.Schema;
using Microsoft.Recognizers.Text;
using System.Collections.Generic;
```


Quando usamos o **ChoicePrompt** para pedir ao usuário que escolha entre um conjunto de opções, temos que fornecer o prompt com esse conjunto de opções, fornecido dentro de um **objeto ChoicePromptOptions**. Aqui, usamos o **ChoiceFactory** para converter uma lista de opções em um formato apropriado.

Também estamos usando um **SuggestedActions** atividade como solicitar novamente, como uma maneira de fornecer as opções de entrada para o usuário novamente.


```csharp
/// <summary>Defines a dialog that asks for a choice of color.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the color prompt.</summary>
        public const string Color = "colorPrompt";
    }

    /// <summary>The available colors to choose from.</summary>
    public List<string> Colors = new List<string> { "Green", "Blue" };

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        Add(Inputs.Color, new ChoicePrompt(Culture.English));
        Add(Name, new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                // Prompt for a color. A choice prompt requires that you specify choice options.
                await dc.Prompt(Inputs.Color, "Please make a choice.", new ChoicePromptOptions()
                {
                    Choices = ChoiceFactory.ToChoices(Colors),
                    RetryPromptActivity =
                        MessageFactory.SuggestedActions(Colors, "Please choose a color.") as Activity
                }).ConfigureAwait(false);
            },
            async(dc, args, next) =>
            {
                var color = (FoundChoice)args["Value"];

                await dc.Context.SendActivity($"You chose {color.Value}.").ConfigureAwait(false);
                await dc.End().ConfigureAwait(false);
            }
        });
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {ChoicePrompt} = require("botbuilder-dialogs");
```

```javascript
dialogs.add('choicePrompt', new ChoicePrompt());
```

```javascript
// A choice prompt requires that you specify choice options.
const list = ['green', 'blue'];
await dc.prompt('choicePrompt', 'Please make a choice', list, {retryPrompt: 'Please choose a color.'});
```

---

## <a name="validate-a-prompt-response"></a>Validar uma resposta imediata

Você pode validar uma resposta rápida antes de retornar o valor válido para a próxima etapa da **cascata**. Por exemplo, para validar um **NumberPrompt** dentro de um intervalo de números entre **6** e **20**, você pode ter uma lógica de validação semelhante a esta:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
using PromptStatus = Microsoft.Bot.Builder.Prompts.PromptStatus;
```

```cs
/// <summary>Defines a dialog that asks for the number of people in a party.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the party size prompt.</summary>
        public const string Size = "parytySize";
    }

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        // Include a validation function for the party size prompt.
        Add(Inputs.Size, new NumberPrompt<int>(Culture.English, async (context, result) =>
        {
            if (result.Value < 6 || result.Value > 20)
            {
                result.Status = PromptStatus.OutOfRange;
            }
        }));
        Add(Name, new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                // Prompt for the party size.
                await dc.Prompt(Inputs.Size, "How many people are in your party?", new PromptOptions()
                {
                    RetryPromptString = "Please specify party size between 6 and 20."
                }).ConfigureAwait(false);
            },
            async(dc, args, next) =>
            {
                var size = (int)args["Value"];

                await dc.Context.SendActivity($"Okay, {size} people!").ConfigureAwait(false);
                await dc.End().ConfigureAwait(false);
            }
        });
    }
}
```

Validação também pode ser encapsulada em seu próprio método privado e adicionada dessa maneira.

```cs
/// <summary>Validates input for the partySize prompt.</summary>
/// <param name="context">The context object for the current turn of the bot.</param>
/// <param name="result">The recognition result from the prompt.</param>
/// <returns>An updated recognition result.</returns>
private static async Task PartySizeValidator(ITurnContext context, Int32Result result)
{
    if (result.Value < 6 || result.Value > 20)
    {
        result.Status = PromptStatus.OutOfRange;
    }
}
```

Caixa de diálogo, especifique o método a ser usado para validar a entrada.

```cs
// Include a validation function for the party size prompt.
Add(Inputs.Size, new NumberPrompt<int>(Culture.English, PartySizeValidator));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Customized prompts with validations
// A number prompt with validation for valid party size within a range.
dialogs.add('partySizePrompt', new botbuilder_dialogs.NumberPrompt( async (context, value) => {
    try {
        if(value < 6 ){
            throw new Error('Party size too small.');
        }
        else if(value > 20){
            throw new Error('Party size too big.')
        }
        else {
            return value; // Return the valid value
        }
    } catch (err) {
        await context.sendActivity(`${err.message} <br/>Please provide a valid number between 6 and 20.`);
        return undefined;
    }
}));
```

---

Da mesma forma, se você quiser validar uma resposta **DatetimePrompt** para uma data e hora no futuro, poderá ter uma lógica de validação semelhante a esta:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using DateTimeResult = Microsoft.Bot.Builder.Prompts.DateTimeResult;
using PromptStatus = Microsoft.Bot.Builder.Prompts.PromptStatus;
```

```cs
/// <summary>Validates input for the reservationTime prompt.</summary>
/// <param name="context">The context object for the current turn of the bot.</param>
/// <param name="result">The recognition result from the prompt.</param>
/// <returns>An updated recognition result.</returns>
private static async Task TimeValidator(ITurnContext context, DateTimeResult result)
{
    if (result.Resolution.Count == 0)
    {
        await context.SendActivity("Sorry, I did not recognize the time that you entered.").ConfigureAwait(false);
        result.Status = PromptStatus.NotRecognized;
    }

    // Find any recognized time that is not in the past.
    var now = DateTime.Now;
    DateTime time = default(DateTime);
    var resolution = result.Resolution.FirstOrDefault(
        res => DateTime.TryParse(res.Value, out time) && time > now);

    if (resolution != null)
    {
        // If found, keep only that result.
        result.Resolution.Clear();
        result.Resolution.Add(resolution);
    }
    else
    {
        // Otherwise, flag the input as out of range.
        await context.SendActivity("Please enter a time in the future, such as \"tomorrow at 9am\"").ConfigureAwait(false);
        result.Status = PromptStatus.OutOfRange;
    }
}
```

```csharp
Add(Inputs.Time, new DateTimePrompt(Culture.English, TimeValidator));
```

Outros exemplos podem ser encontrados em nosso [repositório de amostras](https://github.com/Microsoft/botbuilder-dotnet).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```JavaScript
// A date and time prompt with validation for date/time in the future.
dialogs.add('dateTimePrompt', new botbuilder_dialogs.DatetimePrompt( async (context, values) => {
    try {
        if (values.length < 0) { throw new Error('missing time') }
        if (values[0].type !== 'datetime') { throw new Error('unsupported type') }
        const value = new Date(values[0].value);
        if (value.getTime() < new Date().getTime()) { throw new Error('in the past') }
        return value;
    } catch (err) {
        await context.sendActivity(`Please enter a valid time in the future like "tomorrow at 9am".`);
        return undefined;
    }
}));
```

Outros exemplos podem ser encontrados em nosso [repositório de amostras](https://github.com/Microsoft/botbuilder-js).

---

> [!TIP]
> Os prompts de data e hora podem ser resolvidos em algumas datas diferentes se o usuário fornecer uma resposta ambígua. Dependendo do que você está usando, você pode querer verificar todas as resoluções fornecidas pelo resultado do prompt, em vez de apenas o primeiro.

You can use the similar techniques to validate prompt responses for any of the prompt types.

## <a name="save-user-data"></a>Salvar dados do usuário

Quando você solicita a entrada do usuário, você tem várias opções sobre como lidar com essa entrada. Por exemplo, você pode consumir e descartar a entrada, pode salvá-la em uma variável global, pode salvá-la em um recipiente de armazenamento volátil ou em memória, pode salvá-la em um arquivo ou pode salvá-la em um arquivo externo. base de dados. Para obter mais informações sobre como salvar dados do usuário, consulte [ Gerenciar dados do usuário ](bot-builder-howto-v4-state.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como solicitar uma entrada ao usuário, ele aprimora o código bot e a experiência do usuário, gerenciando vários fluxos de conversas por meio de caixas de diálogo.

> [!div class="nextstepaction"]
> [Gerencie o fluxo de conversas com caixas de diálogo](bot-builder-dialog-manage-conversation-flow.md)