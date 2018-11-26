---
redirect_url: /bot-framework/bot-builder-prompts
ms.openlocfilehash: e7cfbad19290b3ef61d40dc90493db8f530a9a4e
ms.sourcegitcommit: 4661b9bb31d74731dbbb16e625be088b44ba5899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2018
ms.locfileid: "51826933"
---
<a name="--"></a><!--
---
título: faça as perguntas do usuário | Descrição do Microsoft Docs: Saiba como usar o modelo em cascata para solicitar ao usuário várias entradas no SDK do Bot Builder.
keywords: waterfalls, dialogs, asking a question, prompts author: v-ducvo ms.author: v-ducvo manager: kamrani ms.topic: article ms.service: bot-service ms.subservice: sdk ms.date: 5/10/2018 monikerRange: 'azure-bot-service-4.0'
---

# <a name="ask-the-user-questions"></a>Fazer as perguntas do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

No núcleo, um bot é construído em torno da conversa com um usuário. A conversa pode ter [várias formas](bot-builder-conversations.md): podem ser curtas ou mais complexas, podem estar fazendo perguntas ou respondendo perguntas. A forma da conversa depende de vários fatores, mas todos envolvem uma conversa.

Este tutorial irá guiá-lo ao longo da construção de uma conversa, fazendo uma pergunta simples por meio de um bot multiturno. Nosso exemplo será reservar uma mesa, mas você pode imaginar um bot que faz uma variedade de coisas através de uma conversa multiturno como fazer um pedido, responder perguntas frequentes, fazer reservas, e assim por diante.

Um chat bot interativo pode responder à entrada do usuário ou solicitar informações específicas ao usuário. Este tutorial mostrará como fazer uma pergunta ao usuário usando a biblioteca `Prompts`, que é parte de `Dialogs`. Os [diálogos](../bot-service-design-conversation-flow.md) podem ser consideradas como o contêiner que define uma estrutura de conversa, e as solicitações nos diálogos são abordadas mais detalhadamente no próprio [artigo de instruções](bot-builder-prompts.md).

## <a name="prerequisite"></a>Pré-requisito

O código neste tutorial será construído no **bot básico** criado por meio da experiência de [Introdução](~/bot-service-quickstart.md).

## <a name="get-the-package"></a>Obter o pacote

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Instale o pacote **Microsoft.Bot.Builder.Dialogs** do gerenciador de pacotes Nuget.

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)
Navegue até a pasta de projeto do bot e instale o pacote `botbuilder-dialogs` do NPM:

```cmd
npm install --save botbuilder-dialogs@preview
```

---

## <a name="import-package-to-bot"></a>Importar pacote para o bot

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Adicione referência a ambos os diálogos e solicitações no código de bot.

```cs
// ...
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts;
// ...
```


# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Abra o arquivo **app.js** e inclua a biblioteca `botbuilder-dialogs` no código de bot.

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

---

Isso dará acesso às bibliotecas `DialogSet` e `Prompts` que serão usadas para fazer perguntas ao usuário. `DialogSet` é apenas uma coleção de diálogos, que nós estruturamos em um padrão **cascata**. Isso significa simplesmente que um diálogo é seguido de outro.

## <a name="instantiate-a-dialogs-object"></a>Instanciar um objeto de diálogos

Instancie um objeto `dialogs`. Você usará esse objeto de diálogo para gerenciar o processo de perguntas e respostas.

# <a name="ctabcstab"></a>[C#](#tab/cstab)
Declare uma variável de membro na classe de bot e inicialize-a no construtor do bot. 
```cs
public class MyBot : IBot
{
    private readonly DialogSet dialogs;
    public MyBot()
    {
        dialogs = new DialogSet();
    }
    // The rest of the class definition is omitted here
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
const dialogs = new botbuilder_dialogs.DialogSet();
```
---

## <a name="define-a-waterfall-dialog"></a>Definir um diálogo em cascata

Para fazer uma pergunta serão necessários pelo menos dois diálogos em **cascata**. Para este exemplo, você construirá um diálogo em **cascata** de duas etapas, na qual a primeira etapa solicitará o nome do usuário e a segunda etapa cumprimentará o usuário pelo nome. 

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Modifique o construtor do bot para adicionar o diálogo:
```csharp
public MyBot()
{
    dialogs = new DialogSet();
    dialogs.Add("greetings", new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the guest's name.
            await dc.Prompt("textPrompt","What is your name?");
        },
        async(dc, args, next) =>
        {
            await dc.Context.SendActivity($"Hi {args["Text"]}!");
            await dc.End();
        }
    });
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
dialogs.add('greetings',[
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, results){
        var userName = results;
        await dc.context.sendActivity(`Hello ${userName}!`);
        await dc.end(); // Ends the dialog
    }
]);
```

---

A pergunta é feita usando um método `textPrompt` que veio com a biblioteca `Prompts`. A biblioteca `Prompts` oferece um conjunto de solicitações que permite perguntar aos usuários vários tipos de informações. Para obter mais informações sobre outros tipos de solicitações, consulte [Solicitar usuário para entrada](~/v4sdk/bot-builder-prompts.md).

Para que a solicitação funcione, será necessário adicionar uma solicitação ao objeto `dialogs` no dialogId `textPrompt` e criá-la com o construtor `TextPrompt()`.

# <a name="ctabcstab"></a>[C#](#tab/cstab)

```cs
public MyBot()
{
    dialogs = new DialogSet();
    dialogs.Add("greetings", new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the guest's name.
            await dc.Prompt("textPrompt","What is your name?");
        },
        async(dc, args, next) =>
        {
            await dc.Context.SendActivity($"Hi {args["Text"]}!");
            await dc.End();
        }
    });
    // add the prompt, of type TextPrompt
    dialogs.Add("textPrompt", new Builder.Dialogs.TextPrompt());
}

```
Quando o usuário responder à pergunta, a resposta poderá ser encontrada no parâmetro `args` da etapa 2.

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
```
Quando o usuário responder à pergunta, a resposta poderá ser encontrada no parâmetro `results` da etapa 2. Neste caso, o `results` é atribuído a uma variável local `userName`. Em um tutorial posterior, mostraremos como manter as entradas do usuário em um armazenamento de sua preferência.

---


Agora que você definiu `dialogs` para fazer uma pergunta, será necessário chamar o diálogo para iniciar o processo de solicitação.

## <a name="start-the-dialog"></a>Iniciar a caixa de diálogo

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Modifique a lógica de bot para algo semelhante a:

```cs

public async Task OnTurn(ITurnContext context)
{
    // We'll cover state later, in the next tutorial
    var state = ConversationState<Dictionary<string, object>>.Get(context);
    var dc = dialogs.CreateContext(context, state);
    if (context.Activity.Type == ActivityTypes.Message)
    {
        await dc.Continue();
        
        if(!context.Responded)
        {
            await dc.Begin("greetings");
        }
    }
}
```

A lógica de bot é no método `OnTurn()`. Quando o usuário disser "Oi", o bot iniciará o diálogo `greetings`. A primeira etapa do diálogo `greetings` solicita o nome ao usuário. O usuário enviará uma resposta com o nome como uma atividade de mensagem e o resultado será enviado para a etapa dois da cascata através do método `dc.Continue()`. A segunda etapa da cascata, como você definiu, cumprimentará o usuário pelo nome e finalizará o diálogo. 

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Modifique o método **Básico** do bot `processActivity()` para ser semelhante a este:

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        // State will store all of your information 
        const convo = conversationState.get(context);
        const dc = dialogs.createContext(context, convo);

        if (isMessage) {
            // Check for valid intents
            if(context.activity.text.match(/hi/ig)){
                await dc.begin('greetings');
            }
        }

        if(!context.responded){
            // Continue executing the "current" dialog, if any.
            await dc.continue();

            if(!context.responded && isMessage){
                // Default message
                await context.sendActivity("Hi! I'm a simple bot. Please say 'Hi'.");
            }
        }
    });
});
```

A lógica de bot é dentro do método `processActivity()`. Quando o usuário disser "Oi", o bot iniciará o diálogo `greetings`. A primeira etapa do diálogo `greetings` solicita o nome ao usuário. O usuário enviará uma resposta com o nome como a mensagem `text`. Como a mensagem não corresponde a nenhuma intenção esperada e o bot não enviou nenhuma resposta ao usuário, o resultado é enviado para a etapa dois da cascata através do método `dc.continue()`. A segunda etapa da cascata, como você definiu, cumprimentará o usuário pelo nome e finalizará o diálogo. Se, por exemplo, a segunda etapa não enviou ao usuário a mensagem de saudação, o método `processActivity` encerrará com a *mensagem padrão* enviada ao usuário.

---



## <a name="define-a-more-complex-waterfall-dialog"></a>Definir um diálogo em cascata mais complexo

Agora que abordamos como funciona um diálogo em cascata e como construir um, experimentaremos um diálogo mais complexo com o objetivo de reservar uma mesa.

Para gerenciar a solicitação de reserva de mesa, será necessário definir um diálogo em **cascata** com quatro etapas. Nesta conversa, você também usará um `DatetimePrompt` e `NumberPrompt` adicional ao `TextPrompt`.



# <a name="ctabcstab"></a>[C#](#tab/cstab)

Inicie com o modelo de Bot de Eco e renomeie o bot para CafeBot. Adicione um `DialogSet` e algumas variáveis de membro estático.

```cs

namespace CafeBot
{
    public class CafeBot : IBot
    {
        private readonly DialogSet dialogs;

        // Usually, we would save the dialog answers to our state object, which will be covered in a later tutorial.
        // For purpose of this example, let's use the three static variables to store our reservation information.
        static DateTime reservationDate;
        static int partySize;
        static string reservationName;

        // the rest of the class definition is omitted here
        // but is discussed in the rest of this article
    }
}
```

Em seguida, defina o diálogo `reserveTable`. É possível adicionar o diálogo dentro do construtor da classe de bot.
```cs
public CafeBot()
{
    dialogs = new DialogSet();

    // Define our dialog
    dialogs.Add("reserveTable", new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the guest's name.
            await dc.Context.SendActivity("Welcome to the reservation service.");

            await dc.Prompt("dateTimePrompt", "Please provide a reservation date and time.");
        },
        async(dc, args, next) =>
        {
            var dateTimeResult = ((DateTimeResult)args).Resolution.First();

            reservationDate = Convert.ToDateTime(dateTimeResult.Value);
            
            // Ask for next info
            await dc.Prompt("partySizePrompt", "How many people are in your party?");

        },
        async(dc, args, next) =>
        {
            partySize = (int)args["Value"];

            // Ask for next info
            await dc.Prompt("textPrompt", "Whose name will this be under?");
        },
        async(dc, args, next) =>
        {
            reservationName = args["Text"];
            string msg = "Reservation confirmed. Reservation details - " +
            $"\nDate/Time: {reservationDate.ToString()} " +
            $"\nParty size: {partySize.ToString()} " +
            $"\nReservation name: {reservationName}";
            await dc.Context.SendActivity(msg);
            await dc.End();
        }
    });

     // Add a prompt for the reservation date
     dialogs.Add("dateTimePrompt", new Microsoft.Bot.Builder.Dialogs.DateTimePrompt(Culture.English));
     // Add a prompt for the party size
     dialogs.Add("partySizePrompt", new Microsoft.Bot.Builder.Dialogs.NumberPrompt<int>(Culture.English));
     // Add a prompt for the user's name
     dialogs.Add("textPrompt", new Microsoft.Bot.Builder.Dialogs.TextPrompt());
}
```


# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

O `reserveTable` caixa de diálogo terá esta aparência:

```javascript
// Reserve a table:
// Help the user to reserve a table
var reservationInfo = {};

dialogs.add('reserveTable', [
    async function(dc, args, next){
        await dc.context.sendActivity("Welcome to the reservation service.");

        reservationInfo = {}; // Clears any previous data
        await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
    },
    async function(dc, result){
        reservationInfo.dateTime = result[0].value;

        // Ask for next info
        await dc.prompt('partySizePrompt', "How many people are in your party?");
    },
    async function(dc, result){
        reservationInfo.partySize = result;

        // Ask for next info
        await dc.prompt('textPrompt', "Whose name will this be under?");
    },
    async function(dc, result){
        reservationInfo.reserveName = result;
        
        // Reservation confirmation
        var msg = `Reservation confirmed. Reservation details: 
            <br/>Date/Time: ${reservationInfo.dateTime} 
            <br/>Party size: ${reservationInfo.partySize} 
            <br/>Reservation name: ${reservationInfo.reserveName}`;
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);
```

---

O fluxo de conversa do diálogo `reserveTable` fará ao usuário 3 perguntas através das três primeiras etapas da cascata. A quarta etapa processa a resposta à última pergunta e envia ao usuário a confirmação da reserva.



# <a name="ctabcstab"></a>[C#](#tab/cstab)
Cada etapa da cascata do diálogo `reserveTable` usa uma solicitação para perguntar informações ao usuário. O código a seguir foi usado para adicionar as solicitações ao conjunto de diálogo.

```cs
dialogs.Add("dateTimePrompt", new Microsoft.Bot.Builder.Dialogs.DateTimePrompt(Culture.English));
dialogs.Add("partySizePrompt", new Microsoft.Bot.Builder.Dialogs.NumberPrompt<int>(Culture.English));
dialogs.Add("textPrompt", new Microsoft.Bot.Builder.Dialogs.TextPrompt());
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Para que essa cascata funcione, também será necessário adicionar essas solicitações ao objeto `dialogs`:

```javascript
// Define prompts
// Generic prompts
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
dialogs.add('dateTimePrompt', new botbuilder_dialogs.DatetimePrompt());
dialogs.add('partySizePrompt', new botbuilder_dialogs.NumberPrompt());
```

---

Agora, você está pronto para conectar isso à lógica do bot.

## <a name="start-the-dialog"></a>Iniciar a caixa de diálogo

# <a name="ctabcstab"></a>[C#](#tab/cstab)
Modifique `OnTurn` do bot para conter o código a seguir:
```cs
public async Task OnTurn(ITurnContext context)
{
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // The type parameter PropertyBag inherits from 
        // Dictionary<string,object>
        var state = ConversationState<Dictionary<string, object>>.Get(context);
        var dc = dialogs.CreateContext(context, state);
        await dc.Continue();

        // Additional logic can be added to enter each dialog depending on the message received
        
        if(!context.Responded)
        {
            if (context.Activity.Text.ToLowerInvariant().Contains("reserve table"))
            {
                await dc.Begin("reserveTable");
            }
            else
            {
                await context.SendActivity($"You said '{context.Activity.Text}'");
            }
        }
    }
}
```


Em **Startup.cs**, altere a inicialização do middleware ConversationState para usar uma classe derivada de `Dictionary<string,object>` em vez de `EchoState`.

Por exemplo, em `Configure()`:
```cs
options.Middleware.Add(new ConversationState<Dictionary<string, object>>(dataStore));
```


# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Para capturar a intenção do usuário para essa solicitação, adicione algumas linhas de código ao método `processActivity()`. Modifique o método `processActivity()` do bot para ficar semelhante a:

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        // State will store all of your information 
        const convo = conversationState.get(context);
        const dc = dialogs.createContext(context, convo);

        if (isMessage) {
            // Check for valid intents
            if(context.activity.text.match(/hi/ig)){
                await dc.begin('greetings');
            }
            else if(context.activity.text.match(/reserve table/ig)){
                await dc.begin('reserveTable');
            }
        }

        if(!context.responded){
            // Continue executing the "current" dialog, if any.
            await dc.continue();

            if(!context.responded && isMessage){
                // Default message
                await context.sendActivity("Hi! I'm a simple bot. Please say 'Hi' or 'Reserve table'.");
            }
        }
    });
});
```

No momento da execução, sempre que o usuário enviar a mensagem que contém a string `reserve table`, o bot iniciará a conversa `reserveTable`.

---



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o bot está salvando a entrada do usuário para variáveis dentro do bot. Se você quer armazenar ou persistir essas informações, é necessário incluir o estado na camada de middleware. Vamos examinar mais detalhadamente como persistir dados de estado de usuário no próximo tutorial. 

> [!div class="nextstepaction"]
> [Persistir dados de usuário](bot-builder-tutorial-persist-user-inputs.md)

-->
