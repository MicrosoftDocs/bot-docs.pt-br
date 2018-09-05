---
title: Gerenciar um fluxo de conversa simples com diálogos | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa simples com diálogos no SDK do Construtor de Bot para Node.js.
keywords: fluxo de conversa simples, diálogos, prompts, cascatas, conjunto de diálogos
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 8/2/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 77162601f542e6faa8908bc71abc971eb99fcc93
ms.sourcegitcommit: 1abc32353c20acd103e0383121db21b705e5eec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42756419"
---
# <a name="manage-simple-conversation-flow-with-dialogs"></a>Gerenciar fluxo de conversa simples com diálogos

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Você pode gerenciar fluxos de conversa simples e complexos usando a biblioteca de diálogos. Em um fluxo de conversa simples, o usuário começa na primeira etapa de uma *cascata*, percorre até a última etapa, e a conversa termina. Os diálogos também podem manipular [fluxos de conversa complexos](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md), em que as partes do diálogo podem se ramificar e executar um loop.

<!-- TODO: We need a dialogs conceptual topic to link to, so we can reference that here, in place of describing what they are and what their features are in a how-to topic. -->

<!-- TODO: This paragraph belongs in a conceptual topic. --> Um diálogo é como uma função em um programa. Geralmente, é projetado para executar uma operação específica, em uma ordem específica, e pode ser chamado com a frequência necessária. Usar os diálogos permite que o desenvolvedor do bot oriente o fluxo de conversa. É possível encadear vários diálogos juntos para lidar com praticamente qualquer fluxo de conversação com o qual você deseja que seu bot trabalhe. A biblioteca **Diálogos** no SDK do Construtor de Bot inclui recursos integrados, como _prompts_ e _diálogos em cascata_ para ajudar a gerenciar o fluxo de conversas. Você pode usar os prompts para solicitar aos usuários diferentes tipos de informações. Você pode usar uma cascata para combinar várias etapas em uma sequência.

Neste artigo, usamos _conjuntos de diálogos_ para criar um fluxo de conversa que contém os prompts e as etapas em cascata. Temos dois diálogos de exemplo. A primeira é um diálogo de uma etapa que executa uma operação que não requer nenhuma entrada do usuário. A segunda é um diálogo de várias etapas que solicita ao usuário algumas informações.

## <a name="install-the-dialogs-library"></a>Instalar a biblioteca de diálogos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Começaremos de um modelo básico de EchoBot. Para obter instruções, consulte o [início rápido para .NET](~/dotnet/bot-builder-dotnet-quickstart.md).

Para usar diálogos, instale o pacote do NuGet `Microsoft.Bot.Builder.Dialogs` do projeto ou solução.
Depois, consulte a biblioteca de diálogos usando as instruções em seus arquivos de código, conforme o necessário.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A biblioteca `botbuilder-dialogs` pode ser baixada no NPM. Para instalar a biblioteca `botbuilder-dialogs`, execute o seguinte comando NPM:

```cmd
npm install --save botbuilder-dialogs@preview
```

Para usar **diálogos** em seu bot, inclua isso no seu arquivo **app.js**.

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

---

## <a name="create-a-dialog-stack"></a>Criar uma pilha de diálogos

Neste primeiro exemplo, vamos criar um diálogo de uma etapa que pode somar dois números e exibir o resultado.

Para usar diálogos, primeiro é preciso criar um *conjunto de diálogos*.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A biblioteca `Microsoft.Bot.Builder.Dialogs` fornece uma classe `DialogSet`.
Crie uma classe **AdditionDialog** e adicione as instruções using das quais vamos precisar.
Você pode adicionar diálogos nomeados e conjuntos de diálogos a um conjunto de diálogos e acessá-los por nome mais tarde.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

Derive a classe de **DialogSet** e defina as IDs e chaves que usaremos para identificar os diálogos e as informações de entrada para esse conjunto de diálogos.

```csharp
/// <summary>Defines a simple dialog for adding two numbers together.</summary>
public class AdditionDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Main = "additionDialog";

    /// <summary>Defines the IDs of the input arguments.</summary>
    public struct Inputs
    {
        public const string First = "first";
        public const string Second = "second";
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A biblioteca `botbuilder-dialogs` fornece uma classe `DialogSet`.
A classe **DialogSet** define uma **pilha de diálogos** e oferece uma interface simples para gerenciar a pilha.
O SDK do Construtor de Bot implementa a pilha como uma matriz.

Para criar um **DialogSet**, faça o seguinte:

```javascript
const dialogs = new botbuilder_dialogs.DialogSet();
```

A chamada acima criará um **DialogSet** com um padrão de **pilha de diálogo** nomeado como `dialogStack`.
Caso deseje nomear sua pilha, é possível passá-la no como um parâmetro para **DialogSet()**. Por exemplo: 

```javascript
const dialogs = new botbuilder_dialogs.DialogSet("myStack");
```

---

A criação de um diálogo apenas adiciona a definição de diálogo ao conjunto. O diálogo não é executado até que seja efetuado seu push para a pilha de diálogos por meio da chamada de um método _começar_ ou _substituir_.

O nome do diálogo (por exemplo, `addTwoNumbers`) deve ser exclusivo em cada conjunto de diálogo. É possível definir quantos diálogos forem necessárias em cada conjunto. Se você quiser criar vários conjuntos de diálogos e fazer com que eles funcionem juntos, veja [Criar lógica de bot modular](bot-builder-compositcontrol.md).

A biblioteca de diálogo define os seguintes diálogos:

* Um diálogo **prompt** no qual o diálogo usa pelo menos duas funções: uma para solicitar entradas do usuário e outra para processar a entrada. É possível uni-las em uma cadeia de caracteres usando o modelo **cascata**.
* Um diálogo em **cascata** define uma sequência de _etapas cascata_ que são executados na ordem. Um diálogo em cascata pode ter uma única etapa, na qual ele pode ser pensado como um diálogo simples de uma etapa.

## <a name="create-a-single-step-dialog"></a>Criar um diálogo de uma etapa

Diálogo de uma etapa podem ser úteis para capturar fluxos conversacionais de turno único. Este exemplo cria um bot que pode detectar se o usuário diz algo como "1 + 2" e inicia um diálogo `addTwoNumbers` para responder "1 + 2 = 3".

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Os valores são passados e retornados de diálogos como recipientes de propriedades `IDictionary<string,object>`.

Para criar um diálogo simples dentro de um conjunto de diálogos, use o método `Add`. A etapa a seguir adiciona uma cascata de uma etapa chamada `addTwoNumbers`.

Essa etapa pressupõe que os argumentos de diálogo sendo passados contêm propriedades `first` e `second` que representam os números a serem adicionados.

Adicione o construtor a seguir à classe **AdditionDialog**.

```csharp
/// <summary>Defines the steps of the dialog.</summary>
public AdditionDialog()
{
    Add(Main, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Get the input from the arguments to the dialog and add them.
            var x =(double)args[Inputs.First];
            var y =(double)args[Inputs.Second];
            var sum = x + y;

            // Display the result to the user.
            await dc.Context.SendActivity($"{x} + {y} = {sum}");

            // End the dialog.
            await dc.End();
        }
    });
}
```

### <a name="pass-arguments-to-the-dialog"></a>Passar argumentos para o diálogo

No código do bot, atualize suas instruções using.

```cs
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;
using System.Collections.Generic;
using System.Text.RegularExpressions;
using System.Threading.Tasks;
```

Adicione uma propriedade estática à classe para o diálogo de adição.

```cs
private static AdditionDialog AddTwoNumbers { get; } = new AdditionDialog();
```

Para chamar o diálogo de dentro do método`OnTurn` do seu bot, modifique `OnTurn` para conter o seguinte:

```cs
public async Task OnTurn(ITurnContext context)
{
    // Handle any message activity from the user.
    if (context.Activity.Type is ActivityTypes.Message)
    {
        // Get the conversation state from the turn context.
        var conversationState = context.GetConversationState<ConversationData>();

        // Generate a dialog context for the addition dialog.
        var dc = AddTwoNumbers.CreateContext(context, conversationState.DialogState);

        // Call a helper function that identifies if the user says something
        // like "2 + 3" or "1.25 + 3.28" and extract the numbers to add.
        if (TryParseAddingTwoNumbers(context.Activity.Text, out double first, out double second))
        {
            // Start the dialog, passing in the numbers to add.
            var args = new Dictionary<string, object>
            {
                [AdditionDialog.Inputs.First] = first,
                [AdditionDialog.Inputs.Second] = second
            };
            await dc.Begin(AdditionDialog.Main, args);
        }
        else
        {
            // Echo back to the user whatever they typed.
            await context.SendActivity($"You said '{context.Activity.Text}'");
        }
    }
}
```

Adicione uma função auxiliar **TryParseAddingTwoNumbers** à classe de bot. A função auxiliar usa apenas um regex simples para detectar se a mensagem do usuário é uma solicitação para adicionar dois números.

```cs
// Recognizes if the message is a request to add 2 numbers, in the form: number + number,
// where number may have optionally have a decimal point.: 1 + 1, 123.99 + 45, 0.4+7.
// For the sake of simplicity it doesn't handle negative numbers or numbers like 1,000 that contain a comma.
// If you need more robust number recognition, try System.Recognizers.Text
public static bool TryParseAddingTwoNumbers(string message, out double first, out double second)
{
    // captures a number with optional -/+ and optional decimal portion
    const string NUMBER_REGEXP = "([-+]?(?:[0-9]+(?:\\.[0-9]+)?|\\.[0-9]+))";

    // matches the plus sign with optional spaces before and after it
    const string PLUSSIGN_REGEXP = "(?:\\s*)\\+(?:\\s*)";

    const string ADD_TWO_NUMBERS_REGEXP = NUMBER_REGEXP + PLUSSIGN_REGEXP + NUMBER_REGEXP;

    var regex = new Regex(ADD_TWO_NUMBERS_REGEXP);
    var matches = regex.Matches(message);

    first = 0;
    second = 0;
    if (matches.Count > 0)
    {
        var matched = matches[0];
        if (double.TryParse(matched.Groups[1].Value, out first)
            && double.TryParse(matched.Groups[2].Value, out second))
        {
            return true;
        }
    }
    return false;
}
```

Se você estiver usando o modelo EchoBot, altere o nome da classe **EchoState** para **ConversationData** e modifique-a para conter o seguinte.

```cs
using System.Collections.Generic;

/// <summary>
/// Class for storing conversation state.
/// </summary>
public class ConversationData
{
    /// <summary>Property for storing dialog state.</summary>
    public Dictionary<string, object> DialogState { get; set; } = new Dictionary<string, object>();
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Comece com o modelo JS descrito em [Criar um bot com o SDK do Construtor de Bot v4](../javascript/bot-builder-javascript-quickstart.md). Em **app.js**, adicione uma instrução para exigir `botbuilder-dialogs`.

```js
const {DialogSet} = require('botbuilder-dialogs');
```

Em **app.js**, adicione o código a seguir, que define um diálogo simples nomeado como `addTwoNumbers` que pertence ao conjunto `dialogs`:

```javascript
const dialogs = new DialogSet("myDialogStack");

// Show the sum of two numbers.
dialogs.add('addTwoNumbers', [async function (dc, numbers){
        var sum = Number.parseFloat(numbers[0]) + Number.parseFloat(numbers[1]);
        await dc.context.sendActivity(`${numbers[0]} + ${numbers[1]} = ${sum}`);
        await dc.end();
    }]
);
```

Substitua o código em **app.js** para processar a atividade de entrada com o seguinte. O bot chama uma função auxiliar para verificar se a mensagem de entrada se parece com uma solicitação para adicionar dois números. Em caso afirmativo, ele passa os números no argumento para `DialogContext.begin`.

```js
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        const isMessage = context.activity.type === 'message';
        // State will store all of your information
        const convoState = conversationState.get(context);
        const dc = dialogs.createContext(context, convoState);

        if (isMessage) {
            // TryParseAddingTwoNumbers checks if the message matches a regular expression
            // and if it does, returns an array of the numbers to add
            var numbers = await TryParseAddingTwoNumbers(context.activity.text); 
            if (numbers != null && numbers.length >=2 )
            {
                await dc.begin('addTwoNumbers', numbers);
            }
            else {
                // Just echo back the user's message if they're not adding numbers
                const count = (convoState.count === undefined ? convoState.count = 0 : ++convoState.count);
                return context.sendActivity(`Turn ${count}: You said "${context.activity.text}"`);
            }
        }
        else {
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }

        if (!context.responded) {
            await dc.continue();
            // if the dialog didn't send a response
            if (!context.responded && isMessage) {
                await dc.context.sendActivity(`Hi! I'm the add 2 numbers bot. Say something like "What's 2+3?"`);
            }
        }
    });
});

```

Adicione a função auxiliar para **app.js**. A função auxiliar usa apenas uma expressão regular simples para detectar se a mensagem do usuário é uma solicitação para adicionar dois números. Se a expressão regular corresponder, é retornada uma matriz que contém os números a serem adicionados.

```javascript
async function TryParseAddingTwoNumbers(message) {
    const ADD_NUMBERS_REGEXP = /([-+]?(?:[0-9]+(?:\.[0-9]+)?|\.[0-9]+))(?:\s*)\+(?:\s*)([-+]?(?:[0-9]+(?:\.[0-9]+)?|\.[0-9]+))/i;
    let matched = ADD_NUMBERS_REGEXP.exec(message);
    if (!matched) {
        // message wasn't a request to add 2 numbers
        return null;
    }
    else {
        var numbers = [matched[1], matched[2]];
        return numbers;
    }
}
```

---

### <a name="run-the-bot"></a>Executar o bot

Tente executar o bot no Emulador do Bot Framework e diga coisas como “quanto é 1+1?” a ele.

![executar o bot](./media/how-to-dialogs/bot-output-add-numbers.png)

## <a name="using-dialogs-to-guide-the-user-through-steps"></a>Usando diálogos para orientar o usuário por etapas

Neste exemplo, podemos criar um diálogo com várias etapas para solicitar informações ao usuário.

### <a name="create-a-dialog-with-waterfall-steps"></a>Criar um diálogo com etapas de cascata

Uma **cascata** é uma implementação específica de um diálogo que é geralmente usada para coletar informações do usuário ou orientá-lo por uma série de tarefas. As tarefas são implementadas como uma matriz de funções, em que os resultados da primeira função são passados como argumentos para a função seguinte, e assim por diante. Cada função normalmente representa uma etapa no processo geral. Em cada etapa, um bot [solicita que o usuário insira uma entrada](bot-builder-prompts.md), aguarda uma resposta e depois passa o resultado para a próxima etapa.

Por exemplo, o exemplo de código a seguir define três funções em uma matriz que representa as três etapas de uma **cascata**. Após cada prompt, o bot reconhece a entrada do usuário, mas não salva a entrada. Se você quiser manter as entradas do usuário, consulte [Persistir dados de usuário](bot-builder-tutorial-persist-user-inputs.md) para obter mais detalhes.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Isso mostra um construtor para um diálogo de saudação, onde **GreetingDialog** deriva de **DialogSet**, **Inputs.Text** contém a ID que estamos usando para o objeto  **TextPrompt** e **Main** contém a ID para o diálogo de mensagem em si.

```csharp
public GreetingDialog()
{
    // Include a text prompt.
    Add(Inputs.Text, new TextPrompt());

    // Define the dialog logic for greeting the user.
    Add(Main, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Ask for their name.
            await dc.Prompt(Inputs.Text, "What is your name?");
        },
        async (dc, args, next) =>
        {
            // Get the prompt result.
            var name = args["Text"] as string;

            // Acknowledge their input.
            await dc.Context.SendActivity($"Hi, {name}!");

            // Ask where they work.
            await dc.Prompt(Inputs.Text, "Where do you work?");
        },
        async (dc, args, next) =>
        {
            // Get the prompt result.
            var work = args["Text"] as string;

            // Acknowledge their input.
            await dc.Context.SendActivity($"{work} is a fun place.");

            // End the dialog.
            await dc.End();
        }
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
// Ask them where they work.
dialogs.add('greetings',[
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, results){
        var userName = results;
        await dc.context.sendActivity(`Hi ${userName}!`);
        await dc.prompt('textPrompt', 'Where do you work?');
    },
    async function(dc, results){
        var workPlace = results;
        await dc.context.sendActivity(`${workPlace} is a fun place.`);
        await dc.end(); // Ends the dialog
    }
]);

dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
```

---

A assinatura para um etapa de **cascata** é assim:

| Parâmetro | DESCRIÇÃO |
| :---- | :----- |
| `dc` | O contexto do diálogo. |
| `args` | Opcional, contém os argumentos passados para a etapa. |
| `next` | Opcional, um método que permite que você prossiga para a próxima etapa de cascata sem ser avisado. É possível fornecer um argumento *args* ao chamar esse método, permitindo que você passe os argumentos para a próxima etapa da cascata. |

Cada etapa deve chamar um dos métodos a seguir antes de retornar: o delegado *next()* ou um dos métodos de contexto de diálogo *begin*, *end*, *prompt*  ou *replace*; caso contrário, o bot ficará preso nesta etapa. Ou seja, se uma função não terminar com um desses métodos, qualquer entrada inserida pelo usuário fará com que essa etapa seja executada novamente sempre que o usuário enviar uma mensagem ao bot.

Ao atingir o fim de cascata, uma prática recomendada é retornar com o método _end_ para que o diálogo possa ser removido da pilha. Veja a seção [Encerrar um diálogo](#end-a-dialog) abaixo para obter mais informações. Da mesma forma, para passar para a próxima etapa, a etapa de cascata deve ser encerrada com um prompt ou chamar explicitamente o delegado _next_ para avançar na cascata.

## <a name="start-a-dialog"></a>Iniciar um diálogo

Para iniciar um diálogo, passe a *dialogId* que você deseja iniciar para o método _begin_, _prompt_ ou _replace_ do contexto do diálogo. O método _begin_ efetuará push do diálogo para o topo da pilha e o método _replace_ exibirá o diálogo atual da pilha e efetuará push do diálogo de substituição na pilha.

Para iniciar um diálogo sem argumentos:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Start the greetings dialog.
await dc.Begin("greetings");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Start the 'greetings' dialog.
await dc.begin('greetings');
```

---

Para iniciar um diálogo com argumentos:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Start the greetings dialog, passing in a property bag.
await dc.Begin("greetings", args);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Start the 'greetings' dialog with the 'userName' passed in.
await dc.begin('greetings', userName);
```

---

Para iniciar um diálogo **prompt**:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aqui, **Inputs.Text** contém a ID de um **TextPrompt** que está no mesmo conjunto de diálogos.

```csharp
// Ask a user for their name.
await dc.Prompt(Inputs.Text, "What is your name?");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Ask a user for their name.
await dc.prompt('textPrompt', "What is your name?");
```

---

Dependendo do tipo de prompt que você estiver iniciando, a assinatura de argumento do prompt pode ser diferente. O método **DialogSet.prompt** é um método auxiliar. Esse método usa argumentos de entrada e constrói as opções apropriadas do prompt; em seguida, ele chama o método **começar** para iniciar o diálogo de prompt. Para saber mais sobre avisos, veja [Solicitar informações ao usuário](bot-builder-prompts.md).

## <a name="end-a-dialog"></a>Encerrar um diálogo

O método _end_ encerra um diálogo removendo-o da pilha e retorna um resultado opcional no diálogo pai.

É uma prática recomendada chamar explicitamente o método _end_ no final do diálogo; no entanto, ele não é necessário porque o diálogo será automaticamente removido da pilha quando você atingir o fim de cascata.

Para encerrar um diálogo:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// End the current dialog by popping it off the stack.
await dc.End();
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// End the current dialog by popping it off the stack
await dc.end();
```

---

Para encerrar um diálogo e retornar informações para o diálogo pai, inclua um argumento de recipiente de propriedade.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// End the current dialog and return information to the parent dialog.
await dc.end(new Dictionary<string, object>
    {
        ["property1"] = value1,
        ["property2"] = value2
    });
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// End the current dialog and pass a result to the parent dialog
await dc.end({
    "property1": value1,
    "property2": value2
});
```

---

## <a name="clear-the-dialog-stack"></a>Limpar a pilha de diálogo

Caso queira remover todos os diálogos da pilha, é possível limpar a pilha de diálogos chamando o método _end all_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Pop all dialogs from the current stack.
await dc.EndAll();
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Pop all dialogs from the current stack.
await dc.endAll();
```

---

## <a name="repeat-a-dialog"></a>Repetir um diálogo

Para repetir um diálogo, utilize o método _replace_. O método *replace* do contexto do diálogo destacará o diálogo atual na fila e enviará por push o diálogo de substituição para o topo da fila e o iniciará. Essa é uma ótima maneira de tratar [fluxos de conversa complexos](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) e uma boa técnica para gerenciar os menus.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// End the current dialog and start the main menu dialog.
await dc.Replace("mainMenu");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// End the current dialog and start the 'mainMenu' dialog.
await dc.replace('mainMenu');
```

---

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar os fluxos de conversa simples, vamos dar uma olhada e como você pode aproveitar o método _replace_ para lidar com fluxos de conversa complexas.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo de conversas complexo](bot-builder-dialog-manage-complex-conversation-flow.md)
