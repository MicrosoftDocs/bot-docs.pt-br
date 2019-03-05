---
title: Crie seus próprio prompts para coletar entradas do usuário | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa com solicitações primitivas no SDK do Bot Framework.
keywords: fluxo da conversa, prompts, estado da conversa, estado do usuário, prompts personalizados
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 02/19/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4b18cc5d32d04b69fa349d22058b51fcec0e12d7
ms.sourcegitcommit: 05ddade244874b7d6e2fc91745131b99cc58b0d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591067"
---
# <a name="create-your-own-prompts-to-gather-user-input"></a>Crie seus próprio prompts para coletar entradas do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma conversa entre um bot e um usuário muitas vezes envolve solicitar ao usuário algumas informações, analisar as respostas e atuar com base nessas informações.

Seu bot deve controlar o contexto de uma conversa, para que ele possa gerenciar seu comportamento e lembrar-se das respostas às perguntas anteriores. Um *estado* do bot é uma informação que o bot controla para responder apropriadamente às mensagens recebidas.

## <a name="prerequisites"></a>Pré-requisitos

- O código neste artigo se baseia no exemplo de **Solicitar Entrada aos Usuários**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/cs-primitive-prompt-sample) ou [JS](https://aka.ms/js-primitive-prompt-sample).
- Conhecimento sobre [gerenciar estado](bot-builder-concept-state.md) e como [salvar dados do usuário e da conversa](bot-builder-howto-v4-state.md).
- [Emulador do Bot Framework](https://aka.ms/Emulator-wiki-getting-started) para testar o bot localmente.

## <a name="about-the-sample-code"></a>Sobre o código de exemplo

Neste artigo, faremos uma série de perguntas ao usuário, validaremos algumas respostas e salvaremos sua entrada.
Usamos o manipulador de turnos do bot e as propriedades do estado do usuário e da conversa para gerenciar o fluxo da conversa e a coleta de entrada.

1. Definir e configurar o estado
1. Usar as propriedades do estado para direcionar a conversa
   1. Atualize o manipulador de turnos do bot.
   1. Implemente um método auxiliar para gerenciar a coleta de dados do usuário.
   1. Implemente os métodos de validação para a entrada do usuário.

## <a name="define-and-configure-state"></a>Definir e configurar o estado

Precisamos configurar nosso bot para controlar as informações a seguir:

- O nome do usuário, idade e data escolhida, que definiremos no estado do usuário.
- O que acabamos de perguntar ao usuário, que definiremos no estado da conversa.

Como não planejamos implantar esse bot, configuraremos os estados do usuário e da conversa para usar o _armazenamento de memória_. Abaixo, descrevemos alguns aspectos fundamentais do código da configuração.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Definimos os seguintes tipos.

- Uma classe `UserProfile` para as informações do usuário que o bot coletará.
- Uma classe `ConversationFlow` para controlar as informações sobre onde estamos na conversa.
- Uma enumeração `ConversationFlow.Question` interna para controlar onde estamos na conversa.
- Uma classe `CustomPromptBotAccessors` na qual agrupar as informações de gerenciamento do estado.

A classe de acessadores do bot contém nosso gerenciamento do estado e os objetos do acessador de propriedades do estado, sendo transmitidos para o bot por meio da injeção de dependência no ASP.NET Core. Em nosso bot, gravamos as informações do acessador de propriedades do estado que você recebe quando o bot é criado em cada turno.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Criamos os objetos de gerenciamento do estado e os transmitimos quando criamos nosso bot.
Em nosso bot, definimos os identificadores para as propriedades do estado e o controle sobre onde estamos na conversa, em seguida, registramos os objetos de gerenciamento do estado e criamos os acessadores de propriedades do estado no construtor do bot.

---

## <a name="use-state-properties-to-direct-the-conversation"></a>Usar as propriedades do estado para direcionar a conversa

Assim que as propriedades do estado são configuradas, podemos usá-las em nosso bot.

- Defina nosso [manipulador de turnos](#the-bots-turn-handler) para acessar o estado e chamar o método auxiliar.
- Implemente um [método auxiliar](#filling-out-the-user-profile) para gerenciar a coleta do perfil do usuário.
- Implemente os [métodos de validação](#parse-and-validate-input) para analisar e validar a entrada do usuário.

### <a name="the-bots-turn-handler"></a>Manipulador de turnos do bot

Usamos os acessadores de propriedades do estado para obter as propriedades do estado no contexto dos turnos.
Se precisarmos preencher o perfil do usuário, poderemos chamar o método auxiliar e salvar as alterações do estado.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No **CustomPromptBot.cs**, obtenha a propriedade de estado e chame o método auxiliar. (Observe que a propriedade de instância `_accessors` é definida no construtor do bot.)

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
   if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Get the state properties from the turn context.
        ConversationFlow flow = await _accessors.ConversationFlowAccessor.GetAsync(turnContext, () => new ConversationFlow());
        UserProfile profile = await _accessors.UserProfileAccessor.GetAsync(turnContext, () => new UserProfile());

        await FillOutUserProfileAsync(flow, profile, turnContext);

        // Update state and save changes.
        await _accessors.ConversationFlowAccessor.SetAsync(turnContext, flow);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);

        await _accessors.UserProfileAccessor.SetAsync(turnContext, profile);
        await _accessors.UserState.SaveChangesAsync(turnContext);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No **bot.js**, obtenha a propriedade de estado e chame o método auxiliar. (Observe que a propriedade de instância `conversationFlow` é definida no construtor do bot.)

```javascript
// The bot's turn handler.
async onTurn(turnContext) {
    // This bot listens for message activities.
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Get the state properties from the turn context.
        const flow = await this.conversationFlow.get(turnContext, { lastQuestionAsked: question.none });
        const profile = await this.userProfile.get(turnContext, {});

        await MyBot.fillOutUserProfile(flow, profile, turnContext);

        // Update state and save changes.
        await this.conversationFlow.set(turnContext, flow);
        await this.conversationState.saveChanges(turnContext);

        await this.userProfile.set(turnContext, profile);
        await this.userState.saveChanges(turnContext);
    }
}
```

---

### <a name="filling-out-the-user-profile"></a>Preenchendo o perfil do usuário

Começaremos com a coleta de informações. Cada uma fornecerá uma interface semelhante.

- O valor retornado indica se a entrada é uma resposta válida para a pergunta.
- Se a validação é bem-sucedida, produz um valor analisado e normalizado para salvar.
- Se a validação falha, produz uma mensagem com a qual o bot pode solicitar as informações novamente.

 Na [próxima seção](#parse-and-validate-input), definiremos os métodos auxiliares para analisar e validar a entrada do usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private static async Task FillOutUserProfileAsync(ConversationFlow flow, UserProfile profile, ITurnContext turnContext)
{
    string input = turnContext.Activity.Text?.Trim();
    string message;
    switch (flow.LastQuestionAsked)
    {
        case ConversationFlow.Question.None:
            await turnContext.SendActivityAsync("Let's get started. What is your name?");
            flow.LastQuestionAsked = ConversationFlow.Question.Name;
            break;
        case ConversationFlow.Question.Name:
            if (ValidateName(input, out string name, out message))
            {
                profile.Name = name;
                await turnContext.SendActivityAsync($"Hi {profile.Name}.");
                await turnContext.SendActivityAsync("How old are you?");
                flow.LastQuestionAsked = ConversationFlow.Question.Age;
                break;
            }
            else
            {
                await turnContext.SendActivityAsync(message ?? "I'm sorry, I didn't understand that.");
                break;
            }
        case ConversationFlow.Question.Age:
            if (ValidateAge(input, out int age, out message))
            {
                profile.Age = age;
                await turnContext.SendActivityAsync($"I have your age as {profile.Age}.");
                await turnContext.SendActivityAsync("When is your flight?");
                flow.LastQuestionAsked = ConversationFlow.Question.Date;
                break;
            }
            else
            {
                await turnContext.SendActivityAsync(message ?? "I'm sorry, I didn't understand that.");
                break;
            }
        case ConversationFlow.Question.Date:
            if (ValidateDate(input, out string date, out message))
            {
                profile.Date = date;
                await turnContext.SendActivityAsync($"Your cab ride to the airport is scheduled for {profile.Date}.");
                await turnContext.SendActivityAsync($"Thanks for completing the booking {profile.Name}.");
                await turnContext.SendActivityAsync($"Type anything to run the bot again.");
                flow.LastQuestionAsked = ConversationFlow.Question.None;
                profile = new UserProfile();
                break;
            }
            else
            {
                await turnContext.SendActivityAsync(message ?? "I'm sorry, I didn't understand that.");
                break;
            }
    }
}


```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Manages the conversation flow for filling out the user's profile.
static async fillOutUserProfile(flow, profile, turnContext) {
    const input = turnContext.activity.text;
    let result;
    switch (flow.lastQuestionAsked) {
        // If we're just starting off, we haven't asked the user for any information yet.
        // Ask the user for their name and update the conversation flag.
        case question.none:
            await turnContext.sendActivity("Let's get started. What is your name?");
            flow.lastQuestionAsked = question.name;
            break;

        // If we last asked for their name, record their response, confirm that we got it.
        // Ask them for their age and update the conversation flag.
        case question.name:
            result = this.validateName(input);
            if (result.success) {
                profile.name = result.name;
                await turnContext.sendActivity(`I have your name as ${profile.name}.`);
                await turnContext.sendActivity('How old are you?');
                flow.lastQuestionAsked = question.age;
                break;
            } else {
                // If we couldn't interpret their input, ask them for it again.
                // Don't update the conversation flag, so that we repeat this step.
                await turnContext.sendActivity(
                    result.message || "I'm sorry, I didn't understand that.");
                break;
            }

        // If we last asked for their age, record their response, confirm that we got it.
        // Ask them for their date preference and update the conversation flag.
        case question.age:
            result = this.validateAge(input);
            if (result.success) {
                profile.age = result.age;
                await turnContext.sendActivity(`I have your age as ${profile.age}.`);
                await turnContext.sendActivity('When is your flight?');
                flow.lastQuestionAsked = question.date;
                break;
            } else {
                // If we couldn't interpret their input, ask them for it again.
                // Don't update the conversation flag, so that we repeat this step.
                await turnContext.sendActivity(
                    result.message || "I'm sorry, I didn't understand that.");
                break;
            }

        // If we last asked for a date, record their response, confirm that we got it,
        // let them know the process is complete, and update the conversation flag.
        case question.date:
            result = this.validateDate(input);
            if (result.success) {
                profile.date = result.date;
                await turnContext.sendActivity(`Your cab ride to the airport is scheduled for ${profile.date}.`);
                await turnContext.sendActivity(`Thanks for completing the booking ${profile.name}.`);
                await turnContext.sendActivity('Type anything to run the bot again.');
                flow.lastQuestionAsked = question.none;
                profile = {};
                break;
            } else {
                // If we couldn't interpret their input, ask them for it again.
                // Don't update the conversation flag, so that we repeat this step.
                await turnContext.sendActivity(
                    result.message || "I'm sorry, I didn't understand that.");
                break;
            }
    }
}
```

---

### <a name="parse-and-validate-input"></a>Analisar e validar a entrada

Usaremos os critérios a seguir para validar a entrada.

- O **nome** deve ser uma cadeia de caracteres preenchida. Iremos normalizar eliminando o espaço em branco.
- A **idade** deve ser entre 18 e 120. Iremos normalizar retornando um número inteiro.
- A **data** deve ser qualquer data ou hora pelo menos uma hora no futuro.
  Iremos normalizar retornando apenas a parte da data da entrada analisada.

> [!NOTE]
> Para as entradas da idade e da data, podemos usar as bibliotecas [Reconhecedores de Texto/Microsoft](https://github.com/Microsoft/Recognizers-Text/) para fazer a análise inicial.
> Embora forneçamos o código de exemplo, não explicamos como funcionam as bibliotecas de reconhecedores de texto; é apenas uma maneira de analisar a entrada.
> Para obter mais informações sobre essas bibliotecas, confira o arquivo **LEIAME** do repositório.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Adicione os seguintes métodos de validação ao bot.

```csharp
private static bool ValidateName(string input, out string name, out string message)
{
    name = null;
    message = null;

    if (string.IsNullOrWhiteSpace(input))
    {
        message = "Please enter a name that contains at least one character.";
    }
    else
    {
        name = input.Trim();
    }

    return message is null;
}

private static bool ValidateAge(string input, out int age, out string message)
{
    age = 0;
    message = null;

    // Try to recognize the input as a number. This works for responses such as "twelve" as well as "12".
    try
    {
        // Attempt to convert the Recognizer result to an integer. This works for "a dozen", "twelve", "12", and so on.
        // The recognizer returns a list of potential recognition results, if any.
        List<ModelResult> results = NumberRecognizer.RecognizeNumber(input, Culture.English);
        foreach (var result in results)
        {
            // result.Resolution is a dictionary, where the "value" entry contains the processed string.
            if (result.Resolution.TryGetValue("value", out object value))
            {
                age = Convert.ToInt32(value);
                if (age >= 18 && age <= 120)
                {
                    return true;
                }
            }
        }

        message = "Please enter an age between 18 and 120.";
    }
    catch
    {
        message = "I'm sorry, I could not interpret that as an age. Please enter an age between 18 and 120.";
    }

    return message is null;
}

private static bool ValidateDate(string input, out string date, out string message)
{
    date = null;
    message = null;

    // Try to recognize the input as a date-time. This works for responses such as "11/14/2018", "9pm", "tomorrow", "Sunday at 5pm", and so on.
    // The recognizer returns a list of potential recognition results, if any.
    try
    {
        List<ModelResult> results = DateTimeRecognizer.RecognizeDateTime(input, Culture.English);

        // Check whether any of the recognized date-times are appropriate,
        // and if so, return the first appropriate date-time. We're checking for a value at least an hour in the future.
        DateTime earliest = DateTime.Now.AddHours(1.0);
        foreach (ModelResult result in results)
        {
            // result.Resolution is a dictionary, where the "values" entry contains the processed input.
            var resolutions = result.Resolution["values"] as List<Dictionary<string, string>>;
            foreach (var resolution in resolutions)
            {
                // The processed input contains a "value" entry if it is a date-time value, or "start" and
                // "end" entries if it is a date-time range.
                if (resolution.TryGetValue("value", out string dateString)
                    || resolution.TryGetValue("start", out dateString))
                {
                    if (DateTime.TryParse(dateString, out var candidate)
                        && earliest < candidate)
                    {
                        date = candidate.ToShortDateString();
                        return true;
                    }
                }
            }
        }

        message = "I'm sorry, please enter a date at least an hour out.";
    }
    catch
    {
        message = "I'm sorry, I could not interpret that as an appropriate date. Please enter a date at least an hour out.";
    }

    return false;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione os seguintes métodos de validação ao bot.

```javascript
// Validates name input. Returns whether validation succeeded and either the parsed and normalized
// value or a message the bot can use to ask the user again.
static validateName(input) {
    const name = input && input.trim();
    return name != undefined
        ? { success: true, name: name }
        : { success: false, message: 'Please enter a name that contains at least one character.' };
};

// Validates age input. Returns whether validation succeeded and either the parsed and normalized
// value or a message the bot can use to ask the user again.
static validateAge(input) {

    // Try to recognize the input as a number. This works for responses such as "twelve" as well as "12".
    try {
        // Attempt to convert the Recognizer result to an integer. This works for "a dozen", "twelve", "12", and so on.
        // The recognizer returns a list of potential recognition results, if any.
        const results = Recognizers.recognizeNumber(input, Recognizers.Culture.English);
        let output;
        results.forEach(function (result) {
            // result.resolution is a dictionary, where the "value" entry contains the processed string.
            const value = result.resolution['value'];
            if (value) {
                const age = parseInt(value);
                if (!isNaN(age) && age >= 18 && age <= 120) {
                    output = { success: true, age: age };
                    return;
                }
            }
        });
        return output || { success: false, message: 'Please enter an age between 18 and 120.' };
    } catch (error) {
        return {
            success: false,
            message: "I'm sorry, I could not interpret that as an age. Please enter an age between 18 and 120."
        };
    }
}

// Validates date input. Returns whether validation succeeded and either the parsed and normalized
// value or a message the bot can use to ask the user again.
static validateDate(input) {
    // Try to recognize the input as a date-time. This works for responses such as "11/14/2018", "today at 9pm", "tomorrow", "Sunday at 5pm", and so on.
    // The recognizer returns a list of potential recognition results, if any.
    try {
        const results = Recognizers.recognizeDateTime(input, Recognizers.Culture.English);
        const now = new Date();
        const earliest = now.getTime() + (60 * 60 * 1000);
        let output;
        results.forEach(function (result) {
            // result.resolution is a dictionary, where the "values" entry contains the processed input.
            result.resolution['values'].forEach(function (resolution) {
                // The processed input contains a "value" entry if it is a date-time value, or "start" and
                // "end" entries if it is a date-time range.
                const datevalue = resolution['value'] || resolution['start'];
                // If only time is given, assume it's for today.
                const datetime = resolution['type'] === 'time'
                    ? new Date(`${now.toLocaleDateString()} ${datevalue}`)
                    : new Date(datevalue);
                if (datetime && earliest < datetime.getTime()) {
                    output = { success: true, date: datetime.toLocaleDateString() };
                    return;
                }
            });
        });
        return output || { success: false, message: "I'm sorry, please enter a date at least an hour out." };
    } catch (error) {
        return {
            success: false,
            message: "I'm sorry, I could not interpret that as an appropriate date. Please enter a date at least an hour out."
        };
    }
}
```

---

## <a name="test-the-bot-locally"></a>Testar o bot localmente
1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para o exemplo em [C#](https://aka.ms/cs-primitive-prompt-sample) ou em [JS](https://aka.ms/js-primitive-prompt-sample).
1. Para testá-lo, use o emulador, conforme mostrado abaixo.

![primitive-prompts](media/primitive-prompts.png)

## <a name="additional-resources"></a>Recursos adicionais

A [Biblioteca de caixas de diálogo](bot-builder-concept-dialog.md) fornece classes que automatizam muitos aspectos do gerenciamento das conversas. 

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Implementar o fluxo de conversa sequencial](bot-builder-dialog-manage-conversation-flow.md)
