---
title: Gerenciar um fluxo de conversa com diálogos | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa com diálogos no SDK do Construtor de Bot para Node.js.
keywords: fluxo de conversa, diálogos, prompts, cascatas, conjunto de diálogo
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 5/8/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 99184ba71072c159c598c7f68289c42a51926795
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296902"
---
# <a name="manage-conversation-flow-with-dialogs"></a>Gerenciar fluxo de conversa com diálogos
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]


Gerenciar um fluxo de conversa é uma tarefa essencial na criação de bots. Com o SDK do Construtor de Bot para Node.js, é possível gerenciar fluxos de conversa usando **diálogos**.

Um diálogo é como uma função em um programa. Geralmente, é projetado para executar uma operação específica e pode ser chamado com a frequência necessária. É possível encadear vários diálogos juntos para lidar com praticamente qualquer fluxo de conversação com o qual você deseja que seu bot trabalhe. A biblioteca de **diálogos** no SDK do Construtor de Bot para Node.js inclui recursos integrados, como **prompts** e **cascatas** para ajudar a gerenciar o fluxo de conversas por meio de diálogos. A biblioteca de prompts fornece vários prompts, que podem ser usados para solicitar diferentes tipos de informações aos usuários. As cascatas fornecem uma maneira de combinar várias etapas em uma sequência.

Este artigo mostra como criar um objeto diálogo e adicionar etapas de prompts e cascatas em um conjunto de diálogos para gerenciar fluxos de conversa simples e complexos. 

## <a name="install-the-dialogs-library"></a>Instalar a biblioteca de diálogos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Para usar diálogos, instale o pacote do NuGet `Microsoft.Bot.Builder.Dialogs` do projeto ou solução.
Depois referencie a biblioteca de diálogo usando as instruções em seus arquivos de código. Por exemplo: 

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
A biblioteca `botbuilder-dialogs` pode ser baixada no NPM. Para instalar a biblioteca `botbuilder-dialogs`, execute o seguinte comando NPM:

```cmd
npm install --save botbuilder-dialogs
```

Para usar **diálogos** no seu bot, inclua-o no código do bot. Por exemplo: 

**app.js**

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```
---

## <a name="create-a-dialog-stack"></a>Criar uma pilha de diálogos

Para usar diálogos, primeiro é preciso criar um *conjunto de diálogos*.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A biblioteca `Microsoft.Bot.Builder.Dialogs` fornece uma classe `DialogSet`.
Para um conjunto de diálogos, você pode adicionar diálogos nomeados e conjuntos de diálogos e acessá-los por nome mais tarde.

```csharp
IDialog dialog = null;
// Initialize dialog.

DialogSet dialogs = new DialogSet();
dialogs.Add("dialog name", dialog);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

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

O nome do diálogo (por exemplo, `addTwoNumbers`) deve ser exclusivo em cada conjunto de diálogo. É possível definir quantos diálogos forem necessárias em cada conjunto.

A biblioteca de diálogo define os seguintes diálogos:
-   Um diálogo **prompt** no qual o diálogo usa pelo menos duas funções: uma para solicitar entradas do usuário e outra para processar a entrada.
    É possível uni-las em uma cadeia de caracteres usando o modelo **cascata**.
-   Um diálogo em **cascata** define uma sequência de _etapas cascata_ que são executados na ordem.
    Um diálogo em cascata pode ter uma única etapa, na qual ele pode ser pensado como um diálogo simples de uma etapa.

## <a name="create-a-single-step-dialog"></a>Criar um diálogo de uma etapa

Diálogo de uma etapa podem ser úteis para capturar fluxos conversacionais de turno único. Este exemplo cria um bot que pode detectar se o usuário diz algo como "1 + 2" e inicia um diálogo `addTwoNumbers` para responder "1 + 2 = 3". 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Os valores são passados e retornados de diálogos como recipientes de propriedades `IDictionary<string,object>`.

Para criar um diálogo simples dentro de um conjunto de diálogos, use o método `Add`. A etapa a seguir adiciona uma cascata de uma etapa chamada `addTwoNumbers`.

Essa etapa pressupõe que os argumentos de diálogo sendo passados contêm propriedades `first` e `second` que representam os números a serem adicionados.

Comece com o modelo EchoBot. Em seguida, adicione o código em sua classe de bot para adicionar o diálogo no construtor.
```csharp
public class EchoBot : IBot
{
    private DialogSet _dialogs;

    public EchoBot()
    {
        _dialogs = new DialogSet();
        _dialogs.Add("addTwoNumbers", new WaterfallStep[]
        {              
            async (dc, args, next) =>
            {
                double sum = (double)args["first"] + (double)args["second"];
                await dc.Context.SendActivity($"{args["first"]} + {args["second"]} = {sum}");
                await dc.End();
            }
        });
    }

    // The rest of the class definition is omitted here but would include OnTurn()
}

```

### <a name="pass-arguments-to-the-dialog"></a>Passar argumentos para o diálogo

Para chamar o diálogo de dentro do método`OnTurn` do seu bot, modifique `OnTurn` para conter o seguinte:
```cs
public async Task OnTurn(ITurnContext context)
{
    // This bot is only handling Messages
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context
        var state = context.GetConversationState<EchoState>();

        // create a dialog context
        var dialogCtx = _dialogs.CreateContext(context, state);

        // Bump the turn count. 
        state.TurnCount++;

        await dialogCtx.Continue();
        if (!context.Responded)
        {
            // Call a helper function that identifies if the user says something 
            // like "2 + 3" or "1.25 + 3.28" and extract the numbers to add            
            if (TryParseAddingTwoNumbers(context.Activity.Text, out double first, out double second))
            { 
                var dialogArgs = new Dictionary<string, object>
                {
                    ["first"] = first,
                    ["second"] = second
                };                        
                await dialogCtx.Begin("addTwoNumbers", dialogArgs);
            }
            else
            {
                // Echo back to the user whatever they typed.
                await context.SendActivity($"Turn: {state.TurnCount}. You said '{context.Activity.Text}'");
            }
        }
    }
}
```

Adicione a função auxiliar para a classe do bot. A função auxiliar usa apenas um regex simples para detectar se a mensagem do usuário é uma solicitação para adicionar dois números.

```cs
// Recognizes if the message is a request to add 2 numbers, in the form: number + number, 
// where number may have optionally have a decimal point.: 1 + 1, 123.99 + 45, 0.4+7. 
// For the sake of simplicity it doesn't handle negative numbers or numbers like 1,000 that contain a comma.
// If you need more robust number recognition, try System.Recognizers.Text
public bool TryParseAddingTwoNumbers(string message, out double first, out double second)
{
    // captures a number with optional -/+ and optional decimal portion
    const string NUMBER_REGEXP = "([-+]?(?:[0-9]+(?:\\.[0-9]+)?|\\.[0-9]+))";
    // matches the plus sign with optional spaces before and after it
    const string PLUSSIGN_REGEXP = "(?:\\s*)\\+(?:\\s*)";
    const string ADD_TWO_NUMBERS_REGEXP = NUMBER_REGEXP + PLUSSIGN_REGEXP + NUMBER_REGEXP;
    var regex = new Regex(ADD_TWO_NUMBERS_REGEXP);
    var matches = regex.Matches(message);
    var succeeded = false;
    first = 0;
    second = 0;
    if (matches.Count == 0)
    {
        succeeded = false;
    }
    else
    {
        var matched = matches[0];
        if ( System.Double.TryParse(matched.Groups[1].Value, out first) 
            && System.Double.TryParse(matched.Groups[2].Value, out second))
        {
            succeeded = true;
        } 
    }
    return succeeded;
}
```

Se você estiver usando o modelo EchoBot, modifique a classe `EchoState` em **EchoState.cs** da seguinte maneira:

```cs
/// <summary>
/// Class for storing conversation state.
/// This bot only stores the turn count in order to echo it to the user
/// </summary>
public class EchoState: Dictionary<string, object>
{
    private const string TurnCountKey = "TurnCount";
    public EchoState()
    {
        this[TurnCountKey] = 0;            
    }

    public int TurnCount
    {
        get { return (int)this[TurnCountKey]; }
        set { this[TurnCountKey] = value; }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

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
        if (isMessage) {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            // create a dialog context
            const dc = dialogs.createContext(context, state);

            // MatchesAdd2Numbers checks if the message matches a regular expression
            // and if it does, returns an array of the numbers to add
            var numbers = await MatchesAdd2Numbers(context.activity.text); 
            if (numbers != null && numbers.length >=2 )
            {    
                await dc.begin('addTwoNumbers', numbers);
            }
            else {
                // Just echo back the user's message if they're not adding numbers
                return context.sendActivity(`Turn ${count}: You said "${context.activity.text}"`); 
            }           
        } else {
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }
        if (!context.responded) {
            await dc.continue();
            // if the dialog didn't send a response
            if (!context.responded && isMessage) {
                await dc.context.sendActivity(`Hi! I'm the add 2 numbers bot. Say something like "what's 1+2?"`);
            }
        }
    });
});
```

Adicione a função auxiliar para **app.js**. A função auxiliar usa apenas uma expressão regular simples para detectar se a mensagem do usuário é uma solicitação para adicionar dois números. Se a expressão regular corresponder, é retornada uma matriz que contém os números a serem adicionados.

```javascript
async function MatchesAdd2Numbers(message) {
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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

### <a name="create-a-composite-dialog"></a>Criar um diálogo composto

Os trechos de código a seguir são tirados do código de exemplo [Microsoft.Bot.Samples.Dialog.Prompts](https://github.com/Microsoft/botbuilder-dotnet/tree/master/samples/MIcrosoft.Bot.Samples.Dialog.Prompts) no repositório botbuilder-dotnet.

Em Startup.cs:
1.  Renomeie seu bot para `DialogContainerBot`.
1.  Use um dicionário simples como um recipiente de propriedade para o estado de conversa do bot.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<DialogContainerBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        options.Middleware.Add(new ConversationState<Dictionary<string, object>>(new MemoryStorage()));
    });
}
```

Renomeie seu `EchoBot` para `DialogContainerBot`.

Em `DialogContainerBot.cs`, defina uma classe para um diálogo de perfil.

```csharp
public class ProfileControl : DialogContainer
{
    public ProfileControl()
        : base("fillProfile")
    {
        Dialogs.Add("fillProfile", 
            new WaterfallStep[]
            {
                async (dc, args, next) =>
                {
                    dc.ActiveDialog.State = new Dictionary<string, object>();
                    await dc.Prompt("textPrompt", "What's your name?");
                },
                async (dc, args, next) =>
                {
                    dc.ActiveDialog.State["name"] = args["Value"];
                    await dc.Prompt("textPrompt", "What's your phone number?");
                },
                async (dc, args, next) =>
                {
                    dc.ActiveDialog.State["phone"] = args["Value"];
                    await dc.End(dc.ActiveDialog.State);
                }
            }
        );
        Dialogs.Add("textPrompt", new Builder.Dialogs.TextPrompt());
    }
}
```

Em seguida, dentro da definição de bot, declare um campo para diálogo principal do bot e inicie-o no construtor do bot.
O diálogo principal do bot inclui o diálogo de perfil.

```csharp
private DialogSet _dialogs;

public DialogContainerBot()
{
    _dialogs = new DialogSet();

    _dialogs.Add("getProfile", new ProfileControl());
    _dialogs.Add("firstRun",
        new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                    await dc.Context.SendActivity("Welcome! We need to ask a few questions to get started.");
                    await dc.Begin("getProfile");
            },
            async (dc, args, next) =>
            {
                await dc.Context.SendActivity($"Thanks {args["name"]} I have your phone number as {args["phone"]}!");
                await dc.End();
            }
        }
    );
}
```

No método `OnTurn` do bot:
-   Cumprimente o usuário quando a conversa iniciar.
-   Inicialize e _continue_ o diálogo principal sempre que receber uma mensagem do usuário.

    Se o diálogo ainda não gerou uma resposta, pressuponha que ele foi concluído anteriormente ou ainda não foi iniciado e o _inicie_, especificando o nome do diálogo do conjunto com o qual começar.

```csharp
public async Task OnTurn(ITurnContext turnContext)
{
    try
    {
        switch (turnContext.Activity.Type)
        {
            case ActivityTypes.ConversationUpdate:
                foreach (var newMember in turnContext.Activity.MembersAdded)
                {
                    if (newMember.Id != turnContext.Activity.Recipient.Id)
                    {
                        await turnContext.SendActivity("Hello and welcome to the Composite Control bot.");
                    }
                }
                break;

            case ActivityTypes.Message:
                var state = ConversationState<Dictionary<string, object>>.Get(turnContext);
                var dc = _dialogs.CreateContext(turnContext, state);

                await dc.Continue();

                if (!turnContext.Responded)
                {
                    await dc.Begin("firstRun");
                }

                break;
        }
    }
    catch (Exception e)
    {
        await turnContext.SendActivity($"Exception: {e.Message}");
    }
}

```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="create-a-dialog-with-waterfall-steps"></a>Criar um diálogo com etapas de cascata

Uma conversa consiste em uma série de mensagens trocadas entre o usuário e o bot. Quando o objetivo do bot é conduzir o usuário por uma série de etapas, é possível usar um modelo de **cascata** para definir as etapas da conversa.

Uma **cascata** é uma implementação específica de um diálogo que é geralmente usada para coletar informações do usuário ou orientá-lo por uma série de tarefas. As tarefas são implementadas como uma matriz de funções, em que os resultados da primeira função são passados como argumentos para a função seguinte, e assim por diante. Cada função normalmente representa uma etapa no processo geral. Em cada etapa, um bot [solicita que o usuário insira uma entrada](bot-builder-prompts.md), aguarda uma resposta e depois passa o resultado para a próxima etapa.

Por exemplo, o exemplo de código a seguir define três funções em uma matriz que representa as três etapas de uma **cascata**:

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

A assinatura para um etapa de **cascata** é assim:

| Parâmetro | DESCRIÇÃO |
| ---- | ----- |
| `context` | O contexto do diálogo. |
| `args` | Opcional, contém os argumentos passados para a etapa. |
| `next` | Opcional, um método que permite que você prossiga para a próxima etapa de cascata. É possível fornecer um argumento *args* ao chamar esse método, permitindo que você passe os argumentos para a próxima etapa da cascata. |

Cada etapa deve chamar um dos métodos a seguir antes de retornar: *next()*, *dialogs.prompt()*, *dialogs.end()*, *dialogs.begin()* ou *Promise.resolve()*, caso contrário, o bot ficará preso nessa etapa. Ou seja, se uma função não retornar um desses métodos, qualquer entrada inserida pelo usuário fará com que essa etapa seja executada novamente sempre que o usuário enviar uma mensagem ao bot.

Ao atingir o fim de cascata, uma prática recomendada é retornar com o método `end()` para que o diálogo possa ser removido da pilha. Consulte a seção [Encerrar um diálogo](#end-a-dialog) para obter mais informações. Da mesma forma, para passar para a próxima etapa, a etapa de cascata deve ser encerrada com um prompt ou chamar explicitamente a função `next()` para avançar na cascata. 

### <a name="start-a-dialog"></a>Iniciar um diálogo

Para iniciar um diálogo, passe o *dialogId* que deseja iniciar para os métodos `begin()`, `prompt()` ou `replace()`. O método **começar** efetuará push do diálogo para o topo da pilha e o método **substituir** exibirá o diálogo atual da pilha e efetuará push do diálogo de substituição na pilha.

Para iniciar um diálogo sem argumentos:

```javascript
// Start the 'greetings' dialog.
await dc.begin('greetings');
```

Para iniciar um diálogo com argumentos:

```javascript
// Start the 'greetings' dialog with the 'userName' passed in. 
await dc.begin('greetings', userName);
```

Para iniciar um diálogo **prompt**:

```javascript
// Start a 'choicePrompt' dialog with choices passed in as an array of colors to choose from.
await dc.prompt('choicePrompt', `choice: select a color`, ['red', 'green', 'blue']);
```

Dependendo do tipo de prompt que você estiver iniciando, a assinatura de argumento do prompt pode ser diferente. O método **DialogSet.prompt** é um método auxiliar. Esse método usa argumentos de entrada e constrói as opções apropriadas do prompt; em seguida, ele chama o método **começar** para iniciar o diálogo de prompt.

Para substituir um diálogo na pilha:

```javascript
// End the current dialog and start the 'mainMenu' dialog.
await dc.replace('mainMenu'); // Can optionally passed in an 'args' as the second argument.
```

Para obter mais detalhes sobre como usar o método **replace()** nas seções [Repetir um diálogo](#repeat-a-dialog) e [Loops de diálogo](#dialog-loops) a seguir.

## <a name="end-a-dialog"></a>Encerrar um diálogo

Encerre um diálogo removendo-o da pilha e retorne um resultado opcional no diálogo pai. O diálogo pai terá seu método **Dialog.resume()** chamado com qualquer resultado retornado do método.

É uma prática recomendada chamar explicitamente o método `end()` no final do diálogo; no entanto, ele não é necessário porque o diálogo será automaticamente removido da pilha quando você atingir o fim de cascata.

Para encerrar um diálogo:

```javascript
// End the current dialog by popping it off the stack
await dc.end();
```

Para encerrar um diálogo com os argumentos opcionais passados para o diálogo pai:

```javascript
// End the current dialog and pass a result to the parent dialog
await dc.end(result);
```

Como alternativa, também é possível encerrar o diálogo, retornando uma promessa resolvida:

```javascript
await Promise.resolve();
```

A chamada para `Promise.resolve()` resultará no encerramento do diálogo na remoção da pilha. No entanto, esse método não chama o diálogo pai para retomar a execução. Após a chamada para `Promise.resolve()`, a execução é interrompida e o bot continuará de onde o diálogo pai parou quando o usuário enviar uma mensagem ao bot. Essa pode não ser a experiência de usuário ideal para encerrar um diálogo. Cogite encerrar um diálogo com `end()` ou `replace()` para que seu bot possa continuar interagindo com o usuário.

### <a name="clear-the-dialog-stack"></a>Limpar a pilha de diálogo

Caso queira remover todos os diálogos da pilha, é possível limpar a pilha de diálogo chamando o método `dc.endAll()`.

```javascript
// Pop all dialogs from the current stack.
await dc.endAll();
```

### <a name="repeat-a-dialog"></a>Repetir um diálogo

Para repetir um diálogo, utilize o método `dialogs.replace()`.

```javascript
// End the current dialog and start the 'mainMenu' dialog.
await dc.replace('mainMenu'); 
```

Se você quiser mostrar o menu principal por padrão, é possível criar um diálogo `mainMenu` com as seguintes etapas:

```javascript
// Display a menu and ask user to choose a menu item. Direct user to the item selected.
dialogs.add('mainMenu', [
    async function(dc){
        await dc.context.sendActivity("Welcome to Contoso Hotel and Resort.");
        await dc.prompt('choicePrompt', "How may we serve you today?", ['Order Dinner', 'Reserve a table']);
    },
    async function(dc, result){
        if(result.value.match(/order dinner/ig)){
            await dc.begin('orderDinner');
        }
        else if(result.value.match(/reserve a table/ig)){
            await dc.begin('reserveTable');
        }
        else {
            // Repeat the menu
            await dc.replace('mainMenu');
        }
    },
    async function(dc, result){
        // Start over
        await dc.endAll().begin('mainMenu');
    }
]);

dialogs.add('choicePrompt', new botbuilder_dialogs.ChoicePrompt());
```

Esse diálogo usa um `ChoicePrompt` para exibir o menu e aguarda até o usuário escolher uma opção. Quando o usuário escolhe entre `Order Dinner` ou `Reserve a table`, ele inicia o diálogo da escolha apropriada, e quando essa tarefa for concluída, em vez de apenas encerrar o diálogo na última etapa, o diálogo se repete.

### <a name="dialog-loops"></a>Loops de diálogo

Outra maneira de usar o método `replace()` é emulando loops. Considere este cenário como exemplo. Caso queira permitir que o usuário adicione vários itens de menu a um carrinho, é possível fazer loop das opções de menu até que o usuário termine o pedido.

```javascript
// Order dinner:
// Help user order dinner from a menu

var dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50", 
        "More info", "Process order", "Cancel", "Help"],
    "Potato Salad - $5.99": {
        Description: "Potato Salad",
        Price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        Description: "Tuna Sandwich",
        Price: 6.89
    },
    "Clam Chowder - $4.50": {
        Description: "Clam Chowder",
        Price: 4.50
    }

}

// The order cart
var orderCart = {
    orders: [],
    total: 0,
    clear: function(dc) {
        this.orders = [];
        this.total = 0;
        dc.context.activity.conversation.orderCart = null;
    }
};

dialogs.add('orderDinner', [
    async function (dc){
        await dc.context.sendActivity("Welcome to our Dinner order service.");
        orderCart.clear(dc); // Clears the cart.

        await dc.begin('orderPrompt'); // Prompt for orders
    },
    async function (dc, result) {
        if(result == "Cancel"){
            await dc.end();
        }
        else { 
            await dc.prompt('numberPrompt', "What is your room number?");
        }
    },
    async function(dc, result){
        await dc.context.sendActivity(`Thank you. Your order will be delivered to room ${result} within 45 minutes.`);
        await dc.end();
    }
]);

// Helper dialog to repeatedly prompt user for orders
dialogs.add('orderPrompt', [
    async function(dc){
        await dc.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function(dc, choice){
        if(choice.value.match(/process order/ig)){
            if(orderCart.orders.length > 0) {
                // Process the order
                // ...
                await dc.end();
            }
            else {
                await dc.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                await dc.replace('orderPrompt');
            }
        }
        else if(choice.value.match(/cancel/ig)){
            orderCart.clear(context);
            await dc.context.sendActivity("Your order has been canceled.");
            await dc.end(choice.value);
        }
        else if(choice.value.match(/more info/ig)){
            var msg = "More info: <br/>Potato Salad: contains 330 calaries per serving. <br/>"
                + "Tuna Sandwich: contains 700 calaries per serving. <br/>" 
                + "Clam Chowder: contains 650 calaries per serving."
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else if(choice.value.match(/help/ig)){
            var msg = `Help: <br/>To make an order, add as many items to your cart as you like then choose the "Process order" option to check out.`
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else {
            var choice = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if(!choice){
                await dc.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");
                
                // Ask again
                await dc.replace('orderPrompt');
            }
            else {
                // Add the item to cart
                orderCart.orders.push(choice);
                orderCart.total += dinnerMenu[choice.value].Price;

                await dc.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${orderCart.total}`);

                // Ask again
                await dc.replace('orderPrompt');
            }
        }
    }
]);

// Define prompts
// Generic prompts
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
dialogs.add('numberPrompt', new botbuilder_dialogs.NumberPrompt());
dialogs.add('dateTimePrompt', new botbuilder_dialogs.DatetimePrompt());
dialogs.add('choicePrompt', new botbuilder_dialogs.ChoicePrompt());

```

O código de exemplo acima mostra que o diálogo `orderDinner` principal usa um diálogo auxiliar chamado `orderPrompt` para lidar com as opções do usuário. O diálogo `orderPrompt` exibe o menu, pede ao usuário para escolher um item, adicione o item ao carrinho e faz a solicitação novamente. Isso permite que o usuário adicione vários itens ao seu pedido. O diálogo fica em loop até que o usuário escolha `Process order` ou `Cancel`. Nesse ponto, a execução é entregue de volta ao diálogo pai (por exemplo: `orderDinner`). O diálogo `orderDinner` faz algumas limpezas no último minuto caso o usuário deseje processar o pedido; caso contrário, ele encerra e retorna a execução volta para seu diálogo pai (por exemplo: `mainMenu`). O diálogo `mainMenu`, por sua vez, continua executando a última etapa, que é a de simplesmente reexibir as opções do menu principal.

---

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como usar **diálogos**, **prompts** e **cascatas** para gerenciar o fluxo da conversa, vamos dar uma olhada em como é possível dividir os diálogos em tarefas modulares em vez de aglomerá-los todos juntos no objeto `dialogs` da lógica do bot.

> [!div class="nextstepaction"]
> [Criar lógica de bot modular com controle composto](bot-builder-compositcontrol.md)