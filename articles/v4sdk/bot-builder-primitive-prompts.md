---
title: Gerenciar um fluxo de conversa com solicitações primitivas | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa com solicitações primitivas no SDK do Construtor de Bot.
keywords: fluxo de conversa, solicitações
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 7/20/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1514e1bcafc87be9e8449382bca7aa156e512ed9
ms.sourcegitcommit: e8c513d3af5f0c514cadcbcd0a737a7393405afa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40228338"
---
# <a name="prompt-users-for-input-using-your-own-prompts"></a>Solicitar informações aos usuários usando seus próprios prompts
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma conversa entre um bot e um usuário muitas vezes envolve solicitar ao usuário algumas informações, analisar as respostas e atuar com base nessas informações. O tópico sobre [solicitar que os usuários utilizem a biblioteca de Diálogos](bot-builder-prompts.md) detalha como solicitar informações aos usuários usando a biblioteca de **Diálogos**. Entre outras coisas, a biblioteca de **Diálogos** se encarrega de acompanhar a conversa atual e a pergunta que está sendo feita. Também fornece métodos para solicitar tipos diferentes de informação, como texto, número, data, hora e assim por diante. 

Em certas situações, talvez os **Diálogos** internos não sejam a solução certa para o seu bot; **Diálogos** podem acrescentar muita sobrecarga a bots simples, ser muito rígidos ou não alcançar o objetivo do seu bot. Nesses casos, ignore a biblioteca e implemente sua própria lógica de solicitação. Este tópico mostrará como configurar seu *bot Echo* básico para que você possa gerenciar a conversa usando suas próprias solicitações.

## <a name="track-prompt-states"></a>Acompanhar os estados da solicitação

Em uma conversa conduzida por solicitações, você precisa acompanhar em qual ponto da conversa você está no momento e qual pergunta está sendo feita. No código, isso se traduz em gerenciar alguns sinalizadores. 

Por exemplo, você pode criar algumas propriedades as quais deseja acompanhar. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aqui, temos uma classe de perfil de usuário aninhada nas informações de nossa solicitação, permitindo que tudo isso seja armazenado junto no [estado](bot-builder-howto-v4-state.md) do nosso bot.

```csharp
// Where user information will be stored
public class UserProfile
{
    public string userName = null;
    public string workPlace = null;
    public int age = 0;
}

// Flags to keep track of our prompts, and the 
// user profile object for this conversation
public class PromptInformation
{
    public string topicTitle = null;
    public string prompt = null;
    public UserProfile userProfile = null;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Esses estados apenas acompanham em qual tópico e em qual solicitação estamos no momento. Para o funcionamento esperado desses sinalizadores após implantação na nuvem, nós os armazenamos no [estado da conversa](bot-builder-howto-v4-state.md). Coloque esse código na lógica do seu bot principal.

**app.js**
```javascript
// Define a topicStates object if it doesn't exist in the convoState.
if(!convo.topicStates){
    convo.topicStates = {
        "topicTitle": undefined, // Current conversation topic in progress
        "prompt": undefined      // Current prompt state in progress - indicate what question is being asked.
    };
}
```

---

## <a name="manage-a-topic-of-conversation"></a>Gerenciar o tópico da conversa

Em uma conversa sequencial, por exemplo, ao coletar informações do usuário, você precisa saber quando o usuário entrou na conversa, e onde na conversa ele está. Acompanhe isso no manipulador de turnos do bot principal configurando e verificando os sinalizadores de solicitação definidos acima e, depois, agindo adequadamente. O exemplo a seguir mostra como você pode usar esses sinalizadores para coletar as informações de perfil do usuário durante a conversa.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Get our current state, as defined above
var convoState = context.GetConversationState<PromptInformation>();

if (convoState.userProfile == null)
{
    await context.SendActivity("Welcome new user, please fill out your profile information.");
    convoState.topicTitle = "profileTopic"; // Start the userProfile topic
    convoState.userProfile = new UserProfile();
}

// Start or continue a conversation if we are in one
if (convoState.topicTitle == "profileTopic")
{
    if (convoState.userProfile.userName == null && convoState.prompt == null)
    {
        convoState.prompt = "askName";
        await context.SendActivity("What is your name?");
    }
    else if (convoState.prompt == "askName")
    {
        // Save the user's response
        convoState.userProfile.userName = context.Activity.Text;

        // Ask next question
        convoState.prompt = "askAge";
        await context.SendActivity("How old are you?");
    }
    else if (convoState.prompt == "askAge")
    {
        // Save user's response
        if (!Int32.TryParse(context.Activity.Text, out convoState.userProfile.age))
        {
            // Failed to convert to int
            await context.SendActivity("Failed to convert string to int");
        }
        else
        {
            // Ask next question
            convoState.prompt = "workPlace";
            await context.SendActivity("Where do you work?");
        }

    }
    else if (convoState.prompt == "workPlace")
    {
        // Save user's response
        convoState.userProfile.workPlace = context.Activity.Text;

        // Done
        convoState.topicTitle = null; // Reset conversation flag
        convoState.prompt = null;     // Reset the prompt flag
        
        await context.SendActivity("Thank you. Your profile is complete.");
    }
    else // Somehow our flags got inappropriately set
    {
        await context.SendActivity("Looks like something went wrong, let's start over");
        convoState.userProfile = null;
        convoState.prompt = null;
        convoState.topicTitle = null;
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**app.js**
```javascript
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        // State will store all of your information 
        const convo = conversationState.get(context);

        // Defined flags to manage the conversation flow and prompt states
        // convo.topicStates.topicTitle - Current conversation topic in progress
        // convo.topicStates.prompt - Current prompt state in progress - indicate what question is being asked.
        
        if (isMessage) {
            // Define a topicStates object if it doesn't exist in the convoState.
            if(!convo.topicStates){
                convo.topicStates = {
                    "topicTitle": undefined, // Current conversation topic in progress
                    "prompt": undefined      // Current prompt state in progress - indicate what question is being asked.
                };
            }
            
            // If user profile is not defined then define it.
            if(!convo.userProfile){
                
                await context.sendActivity(`Welcome new user, please fill out your profile information.`);
                convo.topicStates.topicTitle = "profileTopic"; // Start the userProfile topic
                convo.userProfile = { // Define the user's profile object
                    "userName": undefined,
                    "age": undefined,
                    "workPlace": undefined
                }; 
            }

            // Start or continue a conversation if we are in one
            if(convo.topicStates.topicTitle == "profileTopic"){
                if(!convo.userProfile.userName && !convo.topicStates.prompt){
                    convo.topicStates.prompt = "askName";
                    await context.sendActivity("What is your name?");
                }
                else if(convo.topicStates.prompt == "askName"){
                    // Save the user's response
                    convo.userProfile.userName = context.activity.text; 

                    // Ask next question
                    convo.topicStates.prompt = "askAge";
                    await context.sendActivity("How old are you?");
                }
                else if(convo.topicStates.prompt == "askAge"){
                    // Save user's response
                    convo.userProfile.age = context.activity.text;

                    // Ask next question
                    convo.topicStates.prompt = "workPlace";
                    await context.sendActivity("Where do you work?");
                }
                else if(convo.topicStates.prompt == "workPlace"){
                    // Save user's response
                    convo.userProfile.workPlace = context.activity.text;

                    // Done
                    convo.topicStates.topicTitle = undefined; // Reset conversation flag
                    convo.topicStates.prompt = undefined;     // Reset the prompt flag
                    await context.sendActivity("Thank you. Your profile is complete.");
                }
            }

            // Check for valid intents
            else if(context.activity.text && context.activity.text.match(/hi/ig)){
                await context.sendActivity(`Hi ${convo.userProfile.userName}.`);
            }
            else {
                // Default message
                await context.sendActivity("Hi. I'm the Contoso bot.");
            }
        }

    });
});

```

---

O exemplo de código acima verifica se o perfil de um usuário está definido na memória. Se não estiver, e esse for um usuário novo, defina o sinalizador para iniciar esse tópico automaticamente. Em seguida, ele mostra como você pode avançar na conversa definindo o sinalizador `prompt` como o valor da pergunta atual que está sendo feita. Com esse sinalizador definido como o valor adequado, a cláusula condicional capturará a resposta do usuário em cada turno e a processará apropriadamente. 

Por fim, redefina esses sinalizadores quando terminar de pedir informações, para que o seu bot não fique preso em um loop. Estenda essa configuração básica para gerenciar fluxos de conversa mais complexos, de acordo com a necessidade de seu bot, simplesmente pela definição de outros sinalizadores ou pela ramificação da conversa com base nas entradas do usuário.

## <a name="manage-multiple-topics-of-conversations"></a>Gerenciar vários tópicos de conversas

Quando você conseguir lidar com um tópico de conversa, estenda a lógica do seu bot para lidar com vários tópicos de conversa. Assim como um único tópico de conversa, é possível lidar com vários tópicos simplesmente pela verificação de condições que disparam um tópico específico e, depois, escolhendo o caminho apropriado.

No exemplo acima, é possível refatorá-lo para permitir outras funções e tópicos, como reservar uma mesa ou pedir uma refeição. É possível acrescentar outras informações ao perfil do usuário ou aos sinalizadores de estado do tópico conforme o necessário, a fim de acompanhar a conversa.

Um dos métodos para ajudar a gerenciar melhor vários tópicos da conversa seria fornecer um menu principal. Com as [ações sugeridas](bot-builder-howto-add-suggested-actions.md), você pode permitir que seus usuários escolham quais tópicos eles podem participar e, em seguida, aprofundar-se nesse tópico. Talvez você também considere útil dividir o código em funções independentes, facilitando o acompanhamento do fluxo da conversa.

## <a name="validate-user-input"></a>Valide a entrada do usuário

A biblioteca de **Diálogos** fornece maneiras internas para validar a entrada do usuário, mas também podemos fazer isso com nossas próprias solicitações. Por exemplo, se perguntarmos a idade do usuário, convém assegurar que a resposta seja um número, não algo como "Bob".

Analisar um número ou uma data e hora é uma tarefa complexa que está além do escopo deste tópico. Felizmente, podemos aproveitar uma biblioteca. Para analisar corretamente essas informações, usamos a biblioteca de [Identificadores de Textos da Microsoft](https://github.com/Microsoft/Recognizers-Text). Esse pacote está disponível no NuGet ou pelo download no repositório. Vale dizer que ele também está incluído na biblioteca de **Diálogos**, mas não estamos usando essa biblioteca aqui.

Essa biblioteca é particularmente útil para análise de linguagem comum ou de respostas complexas sobre coisas como data, hora ou números de telefone. Neste exemplo, estamos apenas validando um número do tamanho do grupo para reserva de jantar, mas a mesma ideia pode ser estendida para validações muito mais detalhadas. Se você não estiver familiarizado com essa biblioteca, examine o conteúdo encontrado nesse site para ver o que ele tem a oferecer.

Neste exemplo, mostramos apenas o uso de `RecognizeNumber`. Encontre detalhes sobre como usar outros métodos da Reconhecedora na biblioteca na [documentação desse repositório](https://github.com/Microsoft/Recognizers-Text/blob/master/README.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para usar a biblioteca de identificadores, adicione-a usando instruções.

```csharp
using Microsoft.Recognizers.Text.Number;
using Microsoft.Recognizers.Text;
using System.Linq; // Used to get the first result from the recognizer
```

Em seguida, defina uma função que faça a validação.

```csharp
private async Task<bool> ValidatePartySize(ITurnContext context, string value)
{
    try
    {
        // Recognize the input as a number. This works for responses such as
        // "twelve" as well as "12"
        var result = NumberRecognizer.RecognizeNumber(input, Culture.English);

        // Attempt to convert the Recognizer result to an integer
        Int32.TryParse(result.First().Text, out int partySize);
        
        if (partySize < 6)
        {
            throw new Exception("Party size too small.");
        }
        else if (partySize > 20)
        {
            throw new Exception("Party size too big.");
        }

        // If we got through this, the number is valid
        return true;
    }
    catch (Exception)
    {
        await context.SendActivity("Error with your party size. < br /> Please specify a number between 6 - 20.");
        return false;
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para usar a biblioteca de identificadores, exija-a em **app.js**:

```javascript
// Required packages for this bot
var Recognizers = require('@microsoft/recognizers-text-suite');
```

Em seguida, defina uma função que faça a validação.

```javascript
// Support party size between 6 and 20 only
async function validatePartySize(context, input){
    try {
        // Recognize the input as a number. This works for responses such as
        // "twelve" as well as "12"
        var result = Recognizers.recognizeNumber(input, Recognizers.Culture.English);
        var value = parseInt(results[0].resolution.value);

        if(value < 6) {
            throw new Error(`Party size too small.`);
        }
        else if(value > 20){
            throw new Error(`Party size too big.`);
        }
        return true; // Return the valid value
    }
    catch (err){
        await context.sendActivity(`${err.message} <br/>Please specify a number between 6 - 20.`);
        return false;
    }
}
```

---

Dentro da etapa de solicitação que queremos validar, chame a função de validação antes de passar para a próxima solicitação. Se a validação falhar, faça a pergunta novamente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
if (convoState.prompt == "partySize")
{
    if (await ValidatePartySize(context, context.Activity.Text))
    {
        // Save user's response in our state, ReservationInfo, which 
        // is a new class we've added to our state
        convoState.ReservationInfo.partySize = context.Activity.Text;

        // Ask next question
        convoState.prompt = "reserveName";
        await context.SendActivity("Who's name will this be under?");
    }
    else
    {
        // Ask again
        await context.SendActivity("How many people are in your party?");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**app.js**

```javascript
// ...
if(convo.topicStates.prompt == "partySize"){
    if(await validatePartySize(context, context.activity.text)){
        // Save user's response
        convo.reservationInfo.partySize = context.activity.text;
        
        // Ask next question
        topicStates.prompt = "reserveName";
        await context.sendActivity("Who's name will this be under?");
    }
    else {
        // Ask again
        await context.sendActivity("How many people are in your party?");
    }
}
// ...
```

---

## <a name="next-step"></a>Próxima etapa

Agora que você sabe como gerenciar os estados de solicitação, vamos ver como você pode aproveitar a biblioteca de **Diálogos** para solicitar informações aos usuários.

> [!div class="nextstepaction"]
> [Solicitar informações aos usuários usando Diálogos](bot-builder-prompts.md)
