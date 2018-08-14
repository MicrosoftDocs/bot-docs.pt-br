---
title: Extrair resultados tipados do LUIS | Microsoft Docs
description: Saiba como usar o LUIS para extrair entidades com o SDK do Construtor de Bot.
keywords: intenções, entidades, LUISGen, extrair
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 5/16/17
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6a88b0a7f44f43d0676ba88314fbba7c486e6be4
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296752"
---
# <a name="extract-intents-and-entities-using-luisgen"></a>Extrair intenções e entidades usando o LUISGen

Além do reconhecimento de intenção, um aplicativo LUIS também pode extrair entidades, que são palavras importantes para atender à solicitação de um usuário. Por exemplo, no exemplo de uma reserva de restaurante, o aplicativo LUIS pode conseguir extrair o tamanho do grupo, a data de reserva ou o local do restaurante na mensagem do usuário. 


Use a [ferramenta LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/LUISGen) para gerar classes que facilitam a extração de entidades do LUIS no código do bot.

Em uma linha de comando do Node.js, instale `luisgen` no caminho global.
```
npm install -g luisgen
```

# <a name="ctabcs"></a>[C#](#tab/cs)

## <a name="generate-a-luis-results-class"></a>Gerar uma classe de resultados do LUIS

Baixe a [amostra CafeBot LUIS](https://aka.ms/contosocafebot-luis) e, na pasta raiz, execute o LUISGen:

```
luisgen Assets\LU\models\LUIS\cafeLUISModel.json -cs ContosoCafeBot.CafeLUISModel
```

## <a name="examine-the-generated-code"></a>Examinar o código gerado
Isso gera **cafeLUISModel.cs**, que pode ser adicionado ao projeto. Ele fornece uma classe `cafeLuisModel` para obter resultados fortemente tipados do LUIS.

Essa classe tem um enum para obter as intenções definidas no aplicativo LUIS.
```cs
public enum Intent {
    Book_Table, 
    Greeting, 
    None, 
    Who_are_you_intent
};
```
Ela também tem uma propriedade `Entities`. Como pode haver várias ocorrências de uma entidade na mensagem do usuário, a classe `_Entities` define uma matriz para cada tipo de entidade. 
```cs
public class _Entities
{
    // Simple entities
    public string[] partySize;

    // Built-in entities
    public Microsoft.Bot.Builder.Ai.LUIS.DateTimeSpec[] datetime;
    public double[] number;

    // Lists
    public string[][] cafeLocation;

    // Instance
    public class _Instance
    {
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] partySize;
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] datetime;
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] number;
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] cafeLocation;
    }
    [JsonProperty("$instance")]
    public _Instance _instance;
}
public _Entities Entities;
```

> [!NOTE]
> Todos os tipos de entidade são matrizes, porque o LUIS pode detectar mais de uma entidade de um tipo especificado no enunciado de um usuário. Por exemplo, se o usuário disser "fazer uma reserva para às 17h amanhã e às 21h no próximo sábado", "17h amanhã" e "21h próximo sábado" serão retornados nos resultados de `datetime`.
>

|Entidade | Tipo | Exemplo | Observações |
|-------|-----|------|---|
|partySize| string[]| Grupo de `four` pessoas| Uma entidade simples reconhece cadeias de caracteres. Neste exemplo, Entities.partySize[0] é `"four"`.
|Datetime| [DateTimeSpec](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.datetimespec?view=botbuilder-4.0.0-alpha)[]| reserva para as `9pm tomorrow`| Cada objeto **DateTimeSpec** tem um campo timex com o valor possível de horários especificados no formato **timex**. Encontre mais informações sobre o timex aqui: http://www.timeml.org/publications/timeMLdocs/timeml_1.2.1.html#timex3 Encontre mais informações sobre a biblioteca que faz o reconhecimento aqui: https://github.com/Microsoft/Recognizers-Text
|número| double[]| Grupo de `four` pessoas, que inclui `2` crianças | `number` identificará todos os números, não apenas o tamanho do grupo. <br/> No enunciado "Grupo de quatro pessoas, que inclui duas crianças", `Entities.number[0]` é 4 e `Entities.number[1]` é 2.
|cafelocation| string[][] | Reserva no local `Seattle`.| cafeLocation é uma entidade de lista, o que significa que ela contém membros reconhecidos de listas. É uma matriz de matrizes, caso uma entidade reconhecida seja membro de mais de uma lista. Por exemplo, "reserva em Washington" pode corresponder a uma lista para o Estado de Washington e para Washington D.C.

A propriedade `_Instance` fornece [InstanceData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.instancedata?view=botbuilder-4.0.0-alpha) para obter mais detalhes sobre cada entidade reconhecida.

## <a name="check-intents-in-your-bot"></a>Verificar as intenções no bot
Em **CafeBot.cs**, dê uma olhada no código em `OnTurn`. Veja em que ponto o bot chama o LUIS e verifica as intenções para decidir qual diálogo ele começará. Os resultados do LUIS da chamada a [`Recognize`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.luisrecognizer?view=botbuilder-4.0.0-alpha#methods) são passados como um argumento para o diálogo `BookTable`.



```cs
if(!context.Responded)
{
    // call LUIS and get results
    LuisRecognizer lRecognizer = createLUISRecognizer();
    // Use the generated class as the type parameter to Recognize()
    cafeLUISModel lResult = await lRecognizer.Recognize<cafeLUISModel>(utterance, ct);
    Dictionary<string,object> lD = new Dictionary<string,object>();
    if(lResult != null) {
        lD.Add("luisResult", lResult);
    }
    
    // top level dispatch
    switch (lResult.TopIntent().intent)
    {
        case cafeLUISModel.Intent.Greeting:
            await context.SendActivity("Hello!");
            if (userState.sendCards) await context.SendActivity(CreateCardResponse(context.Activity, createWelcomeCardAttachment()));
            break;

        case cafeLUISModel.Intent.Book_Table:
            await dc.Begin("BookTable", lD);
            break;

        case cafeLUISModel.Intent.Who_are_you_intent:
            await context.sendActivity("I'm the Contoso Cafe bot.");
            break;

        case cafeLUISModel.Intent.None:
        default:
            await getQnAResult(context);
            break;
    }
}
```

## <a name="extract-entities-in-a-dialog"></a>Extrair entidades em um diálogo

Agora, observe `Dialogs/BookTable.cs`. O diálogo `BookTable` contém uma sequência de etapas de cascata, em que cada uma verifica uma entidade nos resultados do LUIS passados para `args`. Se a entidade não for encontrada, o diálogo ignorará a solicitação dela chamando `next()`. Se ela for encontrada, o diálogo a solicitará, e a resposta do usuário ao prompt será recebida na próxima etapa de cascata.

```cs
    Dialogs.Add("BookTable",
        new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                dc.ActiveDialog.State = new Dictionary<string, object>();
                IDictionary<string,object> state = dc.ActiveDialog.State;

                // add any LUIS entities to active dialog state.
                if(args.ContainsKey("luisResult")) {
                    cafeLUISModel lResult = (cafeLUISModel)args["luisResult"];
                    updateContextWithLUIS(lResult, ref state);
                }
                
                // prompt if we do not already have cafelocation
                if(state.ContainsKey("cafeLocation")) {
                    state["bookingLocation"] = state["cafeLocation"];
                    await next();
                } else {
                    await dc.Prompt("choicePrompt", "Which of our locations would you like?", promptOptions);
                }
            },
            async (dc, args, next) =>
            {
                var state = dc.ActiveDialog.State;
                if(!state.ContainsKey("cafeLocation")) {
                    var choiceResult = (FoundChoice)args["Value"];
                    state["bookingLocation"] = choiceResult.Value;
                }
                bool promptForDateTime = true;
                if(state.ContainsKey("datetime")) {
                    // validate timex
                    var inputdatetime = new string[] {(string)state["datetime"]};
                    var results = evaluateTimeX((string[])inputdatetime);
                    if(results.Count != 0) {
                        var timexResolution = results.First().TimexValue;
                        var timexProperty = new TimexProperty(timexResolution.ToString());
                        var bookingDateTime = $"{timexProperty.ToNaturalLanguage(DateTime.Now)}";
                        state["bookingDateTime"] = bookingDateTime;
                        promptForDateTime = false;
                    }
                }
                // prompt if we do not already have date and time
                if(promptForDateTime) {
                    await dc.Prompt("timexPrompt", "When would you like to arrive? (We open at 4PM.)",
                                    new PromptOptions { RetryPromptString = "We only accept reservations for the next 2 weeks and in the evenings between 4PM - 8PM" });
                } else {
                    await next();
                }                       
                
            },
            async (dc, args, next) =>
            {
                var state = dc.ActiveDialog.State;
                if(!state.ContainsKey("datetime")) { 
                    var timexResult = (TimexResult)args;
                    var timexResolution = timexResult.Resolutions.First();
                    var timexProperty = new TimexProperty(timexResolution.ToString());
                    var bookingDateTime = $"{timexProperty.ToNaturalLanguage(DateTime.Now)}";
                    state["bookingDateTime"] = bookingDateTime;
                }
                // prompt if we already do not have party size
                if(state.ContainsKey("partySize")) {
                    state["bookingGuestCount"] = state["partySize"];
                    await next();
                } else {
                    await dc.Prompt("numberPrompt", "How many in your party?");
                }
            },
            async (dc, args, next) =>
            {
                var state = dc.ActiveDialog.State;
                if(!state.ContainsKey("partySize")) {
                    state["bookingGuestCount"] = args["Value"];
                }

                await dc.Prompt("confirmationPrompt", $"Thanks, Should I go ahead and book a table for {state["bookingGuestCount"].ToString()} guests at our {state["bookingLocation"].ToString()} location for {state["bookingDateTime"].ToString()} ?");
            },
            async (dc, args, next) =>
            {
                var dialogState = dc.ActiveDialog.State;

                // TODO: Verify user said yes to confirmation prompt

                // TODO: book the table! 

                await dc.Context.SendActivity($"Thanks, I have {dialogState["bookingGuestCount"].ToString()} guests booked for our {dialogState["bookingLocation"].ToString()} location for {dialogState["bookingDateTime"].ToString()}.");
            }
        }
    );
}

// This helper method updates dialog state with any LUIS results
private void updateContextWithLUIS(cafeLUISModel lResult, ref IDictionary<string,object> dialogContext) {
    if(lResult.Entities.cafeLocation != null && lResult.Entities.cafeLocation.GetLength(0) > 0) {
        dialogContext.Add("cafeLocation", lResult.Entities.cafeLocation[0][0]);
    }
    if(lResult.Entities.partySize != null && lResult.Entities.partySize.GetLength(0) > 0) {
        dialogContext.Add("partySize", lResult.Entities.partySize[0][0]);
    } else {
        if(lResult.Entities.number != null && lResult.Entities.number.GetLength(0) > 0) {
            dialogContext.Add("partySize", lResult.Entities.number[0]);
        }
    }
    if(lResult.Entities.datetime != null && lResult.Entities.datetime.GetLength(0) > 0) {
        dialogContext.Add("datetime", lResult.Entities.datetime[0].Expressions[0]);
    }
}
```
## <a name="run-the-sample"></a>Execute o exemplo

Abra `ContosoCafeBot.sln` no Visual Studio 2017 e execute o bot. Use o [Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator) para se conectar ao bot de exemplo.

No emulador, diga `reserve a table` para iniciar o diálogo de reserva.

![executar o bot](media/how-to-luisgen/run-bot.png)

# <a name="typescripttabjs"></a>[TypeScript](#tab/js)

Baixe a [amostra CafeBot_LUIS](https://aka.ms/contosocafebot-typescript-luis-dialogs) e, na pasta raiz, execute o LUISGen:

```
luisgen cafeLUISModel.json -ts CafeLUISModel
```

Isso gerará **CafeLUISModel.ts**, que pode ser adicionado ao projeto. Obtenha um resultado tipado do reconhecedor LUIS usando os tipos do arquivo gerado.


```typescript
// call LUIS and get typed results
await luisRec.recognize(context).then(async (res : any) => 
{    
    // get a typed result
    var typedresult = res as CafeLUISModel;   
    
```

## <a name="pass-the-typed-result-to-a-dialog"></a>Passar o resultado tipado para um diálogo

Examine o código em **luisbot.ts**. No manipulador `processActivity`, o bot passa o resultado tipado para um diálogo.

```typescript
// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        const isMessage = context.activity.type === 'message';

        // Create dialog context 
        const state = conversationState.get(context);
        const dc = dialogs.createContext(context, state);
            
        if (!isMessage) {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }

        // Check to see if anyone replied. 
        if (!context.responded) {
            await dc.continue();
            // if the dialog didn't send a response
            if (!context.responded && isMessage) {

                
                await luisRec.recognize(context).then(async (res : any) => 
                {    
                    var typedresult = res as CafeLUISModel;                
                    let topIntent = LuisRecognizer.topIntent(res);    
                    switch (topIntent)
                    {
                        case Intents.Book_Table: {
                            await context.sendActivity("Top intent is Book_Table ");                          
                            await dc.begin('reserveTable', typedresult);
                            break;
                        }
                        
                        case Intents.Greeting: {
                            await context.sendActivity("Top intent is Greeting");
                            break;
                        }
    
                        case Intents.Who_are_you_intent: {
                            await context.sendActivity("Top intent is Who_are_you_intent");
                            break;
                        }
                        default: {
                            await dc.begin('default', topIntent);
                            break;
                        }
                    }
    
                }, (err) => {
                    // there was some error
                    console.log(err);
                }
                );                                
            }
        }
    });
});
```

## <a name="check-for-existing-entities-in-a-dialog"></a>Verificar a existência de entidades em um diálogo

Em **luisbot.ts**, o diálogo `reserveTable` chama uma função auxiliar `SaveEntities` para verificar se alguma entidade foi detectada pelo aplicativo LUIS. Se forem encontradas entidades, elas serão salvas no estado do diálogo. Cada etapa de cascata no diálogo verifica se uma entidade foi salva no estado do diálogo e, caso contrário, solicita que isso seja feito.

```typescript
dialogs.add('reserveTable', [
    async function(dc, args, next){
        var typedresult = args as CafeLUISModel;

        // Call a helper function to save the entities in the LUIS result
        // to dialog state
        await SaveEntities(dc, typedresult);

        await dc.context.sendActivity("Welcome to the reservation service.");
        
        if (dc.activeDialog.state.dateTime) {
            await next();     
        }
        else {
            await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
        }
    },
    async function(dc, result, next){
        if (!dc.activeDialog.state.dateTime) {
            // Save the dateTimePrompt result to dialog state
            dc.activeDialog.state.dateTime = result[0].value;
        }

        // If we don't have party size, ask for it next
        if (!dc.activeDialog.state.partySize) {
            await dc.prompt('textPrompt', "How many people are in your party?");
        } else {
            await next();
        }
    },
    async function(dc, result, next){
        if (!dc.activeDialog.state.partySize) {
            dc.activeDialog.state.partySize = result;
        }
        // Ask for the reservation name next
        await dc.prompt('textPrompt', "Whose name will this be under?");
    },
    async function(dc, result){
        dc.activeDialog.state.Name = result;

        // Save data to conversation state
        var state = conversationState.get(dc.context);

        // Copy the dialog state to the conversation state
        state = dc.activeDialog.state;

        // TODO: Add in <br/>Location: ${state.cafeLocation}
        var msg = `Reservation confirmed. Reservation details:             
            <br/>Date/Time: ${state.dateTime} 
            <br/>Party size: ${state.partySize} 
            <br/>Reservation name: ${state.Name}`;
            
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);
```

A função auxiliar `SaveEntities` verifica se há entidades `datetime` e `partysize`. A entidade `datetime` é uma [entidade predefinida](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-prebuilt-entities#builtindatetimev2).

```typescript
// Helper function that saves any entities found in the LUIS result
// to the dialog state
async function SaveEntities( dc: DialogContext<TurnContext>, typedresult) {
    // Resolve entities returned from LUIS, and save these to state
    if (typedresult.entities)
    {
        console.log(`Entities found.`);
        let datetime = typedresult.entities.datetime;

        if (datetime) {
            // Use the first date or time found in the utterance
            if (datetime[0].timex) {
                timexValues = datetime[0].timex;
                // Datetime results from LUIS are represented in timex format:
                // http://www.timeml.org/publications/timeMLdocs/timeml_1.2.1.html#timex3                                
                // More information on the library which does the recognition can be found here: 
                // https://github.com/Microsoft/Recognizers-Text

                if (datetime[0].type === "datetime") {
                    // To parse timex, here you use the resolve and creator from
                    // @microsoft/recognizers-text-data-types-timex-expression
                    // The second parameter is an array of constraints the results must satisfy
                    var resolution = Resolver.evaluate(
                        // array of timex values to evaluate. There may be more than one: "today at 6" can be 6AM or 6PM.
                        timexValues,
                        // constrain results to times between 4pm and 8pm                        
                        [Creator.evening]);
                    if (resolution[0]) {
                        // toNaturalLanguage takes the current date into account to create a friendly string
                        dc.activeDialog.state.dateTime = resolution[0].toNaturalLanguage(new Date());
                        // You can also use resolution.toString() to format the date/time.
                    } else {
                        // time didn't satisfy constraint.
                        dc.activeDialog.state.dateTime = null;
                    }
                } 
                else  {
                    console.log(`Type ${datetime[0].type} is not yet supported. Provide both the date and the time.`);
                }
            }                                                
        }
        let partysize = typedresult.entities.partySize;
        if (partysize) {
            console.log(`partysize entity detected.${partysize}`);
            // use first partySize entity that was found in utterance
            dc.activeDialog.state.partySize = partysize[0];
        }
        let cafelocation = typedresult.entities.cafeLocation;

        if (cafelocation) {
            console.log(`location entity detected.${cafelocation}`);
            // use first cafeLocation entity that was found in utterance
            dc.activeDialog.state.cafeLocation = cafelocation[0][0];
        }
    } 
}
```

## <a name="run-the-sample"></a>Execute o exemplo

1. Caso não tenha o compilador TypeScript instalado, instale-o usando este comando:

```
npm install --global typescript
```

2. Instale as dependências antes de executar o bot executando `npm install` no diretório raiz da amostra:

```
npm install
```

3. No diretório raiz, compile a amostra usando `tsc`. Isso gerará `luisbot.js`.

4. Execute `luisbot.js` no diretório `lib`.

5. Use o [Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator) para executar a amostra.

6. No emulador, diga `reserve a table` para iniciar o diálogo de reserva.

![executar o bot](media/how-to-luisgen/run-bot.png)

---


## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o LUIS, confira [Reconhecimento vocal](./bot-builder-concept-luis.md).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Combinar o LUIS e o QnA usando a ferramenta de Expedição](./bot-builder-tutorial-dispatch.md)


