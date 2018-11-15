---
title: Crie seus próprio prompts para coletar entradas do usuário | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa com solicitações primitivas no SDK do Construtor de Bot.
keywords: fluxo de conversa, solicitações
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/12/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: bc223008778f0396b9bc7ff0c2ef48eb3773a105
ms.sourcegitcommit: 0702305523f8c816b2eb95dce2ea9effb9e5ee5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51562089"
---
# <a name="create-your-own-prompts-to-gather-user-input"></a>Crie seus próprio prompts para coletar entradas do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma conversa entre um bot e um usuário muitas vezes envolve solicitar ao usuário algumas informações, analisar as respostas e atuar com base nessas informações. O tópico sobre [solicitar que os usuários utilizem a biblioteca de Diálogos](bot-builder-prompts.md) detalha como solicitar informações aos usuários usando a biblioteca de **Diálogos**. Entre outras coisas, a biblioteca de **Diálogos** se encarrega de acompanhar a conversa atual e a pergunta que está sendo feita. Também fornece métodos para solicitar e validar tipos diferentes de informação, como texto, número, data, hora e assim por diante.

Em certas situações, talvez os **Diálogos** internos não sejam a solução certa para o seu bot; **Diálogos** podem acrescentar muita sobrecarga a bots simples, ser muito rígidos ou não alcançar o objetivo do seu bot. Nesses casos, ignore a biblioteca e implemente sua própria lógica de solicitação. Este tópico mostrará como configurar seu *bot Echo* básico para que você possa gerenciar a conversa usando suas próprias solicitações.

## <a name="track-prompt-states"></a>Acompanhar os estados da solicitação

Em uma conversa conduzida por solicitações, você precisa acompanhar em qual ponto da conversa você está no momento e qual pergunta está sendo feita. No código, isso se traduz em gerenciar alguns sinalizadores.

Por exemplo, você pode criar algumas propriedades as quais deseja acompanhar.

Esses estados acompanham em qual tópico e em qual solicitação estamos no momento. Para o funcionamento esperado desses sinalizadores após implantação na nuvem, nós os armazenamos no [estado da conversa](bot-builder-howto-v4-state.md). 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Estamos criando duas classes para controlar o estado. **TopicState** para acompanhar o progresso dos prompts de conversação e **UserProfile** para acompanhar as informações que o usuário fornece. Armazenaremos essa informação em nosso bot de [estado](bot-builder-howto-v4-state.md) em uma etapa posterior.

```csharp
/// <summary>
/// Contains conversation state information about the conversation in progress.
/// </summary>
public class TopicState
{
    /// <summary>
    /// Identifies the current "topic" of conversation.
    /// </summary>
    public string Topic { get; set; }

    /// <summary>
    /// Indicates whether we asked the user a question last turn, and
    /// if so, what it was.
    /// </summary>
    public string Prompt { get; set; }
}
```

```csharp
/// <summary>
/// Contains user state information for the user's profile.
/// </summary>
public class UserProfile
{
    public string UserName { get; set; }

    public int? Age { get; set; }

    public string WorkPlace { get; set; }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Em **index.js**, atualize a instrução necessária para incluir `UserState`.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState } = require('botbuilder');
```

Em seguida, crie um objeto de gerenciamento de estado do usuário e passe-o ao criar seu bot.

```javascript
// Create conversation and user state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);

// Create the bot.
const myBot = new MyBot(conversationState, userState);
```

Em **bot.js**, defina identificadores para os acessadores de propriedade de estado que usaremos para gerenciar o [estado](bot-builder-howto-v4-state.md) do bot. Defina também os prompts a serem usados para obter as informações que desejamos coletar do usuário.

Adicione este código fora da classe `MyBot`.

```javascript
// Define identifiers for our state property accessors.
const TOPIC_STATE_PROPERTY = 'topicStateProperty';
const USER_PROFILE_PROPERTY = 'userProfileProperty';

// Define the prompts to use to ask for user profile information.
const fields = {
    userName: "What is your name?",
    age: "How old are you?",
    workPlace: "Where do you work?"
}
```

---

## <a name="manage-a-topic-of-conversation"></a>Gerenciar o tópico da conversa

Em uma conversa sequencial, por exemplo, ao coletar informações do usuário, você precisa saber quando o usuário entrou na conversa, e onde na conversa ele está. Acompanhe isso no manipulador de turnos do bot principal configurando e verificando os sinalizadores de solicitação definidos acima e, depois, agindo adequadamente. O exemplo a seguir mostra como você pode usar esses sinalizadores para coletar as informações de perfil do usuário durante a conversa.

O código do bot é apresentado aqui e discutido na próxima seção.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No caso do ASP.NET Core, precisamos configurar nosso bot e a injeção de dependência primeiro.

Renomeie o arquivo **EchoWithCounterBot.cs** como **PrimitivePromptsBot.cs** e atualize também o nome da classe. Essa classe contém a lógica do bot. Atualizaremos isso em breve.

Renomeie o arquivo **EchoBotAccessors.cs** como **BotAccessors.cs** e atualize também o nome da classe. Essa classe contém os objetos de gerenciamento de estado e os acessadores de propriedades de estado do bot. Atualize a definição da seguinte forma.

```csharp
using Microsoft.Bot.Builder;
using System;

/// <summary>
/// Contains the state and state property accessors for the primitive prompts bot.
/// </summary>
public class BotAccessors
{
    public const string TopicStateName = "PrimitivePrompts.TopicStateAccessor";

    public const string UserProfileName = "PrimitivePrompts.UserProfileAccessor";

    public ConversationState ConversationState { get; }

    public UserState UserState { get; }

    public IStatePropertyAccessor<TopicState> TopicStateAccessor { get; set; }

    public IStatePropertyAccessor<UserProfile> UserProfileAccessor { get; set; }

    public BotAccessors(ConversationState conversationState, UserState userState)
    {
        if (conversationState is null)
        {
            throw new ArgumentNullException(nameof(conversationState));
        }

        if (userState is null)
        {
            throw new ArgumentNullException(nameof(userState));
        }

        this.ConversationState = conversationState;
        this.UserState = userState;
    }
}
```

Atualize o método `ConfigureServices` do arquivo **Startup.cs**, começando pelo local onde o objeto `IStorage` é configurado.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<PrimitivePromptsBot>(options =>
    {
        // ...

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, everything stored in memory will be gone.
        IStorage dataStore = new MemoryStorage();

        var conversationState = new ConversationState(dataStore);
        options.State.Add(conversationState);

        var userState = new UserState(dataStore);
        options.State.Add(userState);
    });

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        var userState = options.State.OfType<UserState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState, userState)
        {
            TopicStateAccessor = conversationState.CreateProperty<TopicState>(BotAccessors.TopicStateName),
            UserProfileAccessor = userState.CreateProperty<UserProfile>(BotAccessors.UserProfileName),
        };

        return accessors;
    });
}
```

Por fim, atualize a lógica do bot no arquivo **PrimitivePromptsBot.cs**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;

public class PrimitivePromptsBot : IBot
{
    public const string ProfileTopic = "profile";

    /// <summary>
    /// Describes a field in the user profile.
    /// </summary>
    private class UserFieldInfo
    {
        /// <summary>
        /// The ID to use for this field.
        /// </summary>
        public string Key { get; set; }

        /// <summary>
        /// The prompt to use to ask for a value for this field.
        /// </summary>
        public string Prompt { get; set; }

        /// <summary>
        /// Gets the value of the corresponding field.
        /// </summary>
        public Func<UserProfile, string> GetValue { get; set; }

        /// <summary>
        /// Sets the value of the corresponding field.
        /// </summary>
        public Action<UserProfile, string> SetValue { get; set; }
    }

    /// <summary>
    /// The prompts for the user profile, indexed by field name.
    /// </summary>
    private static List<UserFieldInfo> UserFields { get; } = new List<UserFieldInfo>
    {
        new UserFieldInfo {
            Key = nameof(UserProfile.UserName),
            Prompt = "What is your name?",
            GetValue = (profile) => profile.UserName,
            SetValue = (profile, value) => profile.UserName = value,
        },
        new UserFieldInfo {
            Key = nameof(UserProfile.Age),
            Prompt = "How old are you?",
            GetValue = (profile) => profile.Age.HasValue? profile.Age.Value.ToString() : null,
            SetValue = (profile, value) =>
            {
                if (int.TryParse(value, out int age))
                {
                    profile.Age = age;
                }
            },
        },
        new UserFieldInfo {
            Key = nameof(UserProfile.WorkPlace),
            Prompt = "Where do you work?",
            GetValue = (profile) => profile.WorkPlace,
            SetValue = (profile, value) => profile.WorkPlace = value,
        },
    };

    /// <summary>
    /// The state and state accessors for the bot.
    /// </summary>
    private BotAccessors Accessors { get; }

    public PrimitivePromptsBot(BotAccessors accessors)
    {
        Accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));
    }

    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        if (turnContext.Activity.Type is ActivityTypes.Message)
        {
            // Use the state property accessors to get the topic state and user profile.
            TopicState topicState = await Accessors.TopicStateAccessor.GetAsync(
                turnContext,
                () => new TopicState { Topic = ProfileTopic, Prompt = null },
                cancellationToken);
            UserProfile userProfile = await Accessors.UserProfileAccessor.GetAsync(
                turnContext,
                () => new UserProfile(),
                cancellationToken);

            // Check whether we need more information.
            if (topicState.Topic is ProfileTopic)
            {
                // If we're expecting input, record it in the user's profile.
                if (topicState.Prompt != null)
                {
                    UserFieldInfo field = UserFields.First(f => f.Key.Equals(topicState.Prompt));
                    field.SetValue(userProfile, turnContext.Activity.Text.Trim());
                }

                // Determine which fields are not yet set.
                List<UserFieldInfo> emptyFields = UserFields.Where(f => f.GetValue(userProfile) is null).ToList();

                if (emptyFields.Any())
                {
                    // If all the fields are empty, send a welcome message.
                    if (emptyFields.Count == UserFields.Count)
                    {
                        await turnContext.SendActivityAsync("Welcome new user, please fill out your profile information.");
                    }

                    // We have at least one empty field. Prompt for the next empty field,
                    // and update the prompt flag to indicate which prompt we just sent,
                    // so that the response can be captured at the beginning of the next turn.
                    UserFieldInfo field = emptyFields.First();
                    await turnContext.SendActivityAsync(field.Prompt);
                    topicState.Prompt = field.Key;
                }
                else
                {
                    // Our user profile is complete!
                    await turnContext.SendActivityAsync($"Thank you, {userProfile.UserName}. Your profile is complete.");
                    topicState.Prompt = null;
                    topicState.Topic = null;
                }
            }
            else if (turnContext.Activity.Text.Trim().Equals("hi", StringComparison.InvariantCultureIgnoreCase))
            {
                await turnContext.SendActivityAsync($"Hi. {userProfile.UserName}.");
            }
            else
            {
                await turnContext.SendActivityAsync("Hi. I'm the Contoso cafe bot.");
            }

            // Use the state property accessors to update the topic state and user profile.
            await Accessors.TopicStateAccessor.SetAsync(turnContext, topicState, cancellationToken);
            await Accessors.UserProfileAccessor.SetAsync(turnContext, userProfile, cancellationToken);

            // Save any state changes to storage.
            await Accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
            await Accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Em **bot.js**, atualize a definição da classe `MyBot`.

Configuramos os acessadores de propriedade de estado no construtor do bot: `topicStateAccessor` e `userProfileAccessor`. O estado do tópico rastreia o tópico de conversa e o perfil do usuário rastreia as informações coletadas para o usuário.

```javascript
constructor(conversationState, userState) {
    // Create state property accessors.
    this.topicStateAccessor = conversationState.createProperty(TOPIC_STATE_PROPERTY);
    this.userProfileAccessor = userState.createProperty(USER_PROFILE_PROPERTY);

    // Track the conversation and user state management objects.
    this.conversationState = conversationState;
    this.userState = userState;
}
```

Em seguida, atualize o manipulador de turnos para usar o estado do bot para controlar o fluxo de conversa e salvar as informações do usuário coletadas.

```javascript
async onTurn(turnContext) {
    // Handle only message activities from the user.
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Get state properties using their accessors, providing default values.
        const topicState = await this.topicStateAccessor.get(turnContext, {
            prompt: undefined,
            topic: 'profile'
        });
        const userProfile = await this.userProfileAccessor.get(turnContext, {
            "userName": undefined,
            "age": undefined,
            "workPlace": undefined
        });

        if (topicState.topic === 'profile') {
            // If a prompt flag is set in the conversation state, use it to capture the incoming value
            // into the appropriate field of the user profile.
            if (topicState.prompt) {
                userProfile[topicState.prompt] = turnContext.activity.text;
            }

            // Determine which fields are not yet set.
            const empty_fields = [];
            Object.keys(fields).forEach(function (key) {
                if (!userProfile[key]) {
                    empty_fields.push({
                        key: key,
                        prompt: fields[key]
                    });
                }
            });

            if (empty_fields.length) {

                // If all the fields are empty, send a welcome message.
                if (empty_fields.length == Object.keys(fields).length) {
                    await turnContext.sendActivity('Welcome new user, please fill out your profile information.');
                }

                // We have at least one empty field. Prompt for the next empty field.
                await turnContext.sendActivity(empty_fields[0].prompt);

                // update the flag to indicate which prompt we just sent
                // so that the response can be captured at the beginning of the next turn.
                topicState.prompt = empty_fields[0].key;

            } else {
                // Our user profile is complete!
                await turnContext.sendActivity('Thank you. Your profile is complete.');
                topicState.prompt = null;
                topicState.topic = null;

            }
        } else if (turnContext.activity.text && turnContext.activity.text.match(/hi/ig)) {
            // Check to see if the user said "hi" and respond with a greeting
            await turnContext.sendActivity(`Hi ${userProfile.userName}.`);
        } else {
            // Default message
            await turnContext.sendActivity("Hi. I'm the Contoso bot.");
        }

        // Save state changes
        await this.conversationState.saveChanges(turnContext);
        await this.userState.saveChanges(turnContext);
    }
}
```

---

O exemplo de código acima inicializa o sinalizador _topic_ para `profile` para poder iniciar a conversa da coleção de perfil. O bot avança na conversa atualizando o sinalizador _prompt_ para um valor que representa a pergunta atual que está sendo solicitada. Com esse sinalizador definido com o valor adequado, o bot saberá o que fazer com a próxima mensagem recebida do usuário e irá processá-la adequadamente.

Por fim, os sinalizadores são redefinidos quando o bot termina de solicitar informações. Caso contrário, seu bot fica preso em um loop e não sai da pergunta final.

Para estender esse padrão para gerenciar fluxos de conversa mais complexos, defina outros sinalizadores ou ramifique a conversa com base nas entradas do usuário.

## <a name="manage-multiple-topics-of-conversations"></a>Gerenciar vários tópicos de conversas

Quando você conseguir lidar com um tópico de conversa, estenda a lógica do bot para lidar com vários tópicos de conversa. Vários tópicos podem ser tratados através da verificação de condições adicionais e, em seguida, o caminho apropriado deve ser seguido.

Você pode estender o exemplo acima para permitir outros recursos e tópicos de conversa, como reservar uma mesa ou fazer um pedido. Adicione mais sinalizadores ao estado do tópico conforme necessário para acompanhar a conversa.

Talvez você também considere útil dividir o código em funções e métodos independentes, facilitando o acompanhamento do fluxo da conversa. Um padrão comum é fazer com que o bot avalie a mensagem e o estado e, em seguida, delegar o controle a funções que implementam detalhes do recurso.

Para ajudar seus usuários a navegar melhor pelos vários tópicos de conversa, considere o uso de um menu principal. Por exemplo, com as [ações sugeridas](bot-builder-howto-add-suggested-actions.md), você pode permitir que seus usuários escolham em quais tópicos participar, em vez de tentar adivinhar o que o bot pode fazer.

## <a name="validate-user-input"></a>Valide a entrada do usuário

A biblioteca de **Diálogos** fornece maneiras internas para validar a entrada do usuário, mas também podemos fazer isso com nossas próprias solicitações. Por exemplo, se perguntarmos a idade do usuário, convém assegurar que a resposta seja um número, não algo como "Bob".

Analisar um número ou uma data e hora é uma tarefa complexa que está além do escopo deste tópico. Felizmente, podemos aproveitar uma biblioteca. Para analisar essas informações, usamos a biblioteca de [Identificadores de Textos da Microsoft](https://github.com/Microsoft/Recognizers-Text). Este pacote está disponível por meio do NuGet e npm. Você também pode fazer o download diretamente do repositório. (Também vale mencionar que ele está incluído na biblioteca de **Diálogos**, embora essa biblioteca não esteja sendo usada aqui.)

Essa biblioteca é particularmente útil para análise de entradas complexas como datas, horas ou números de telefone. Neste exemplo, estamos validando um número do tamanho do grupo para uma reserva de jantar, mas a mesma ideia pode ser estendida para operações de validação muito mais complexas.

No exemplo a seguir, mostramos apenas o uso de `RecognizeNumber`. Encontre detalhes sobre como usar outros métodos da Reconhecedora na biblioteca na [documentação desse repositório](https://github.com/Microsoft/Recognizers-Text/blob/master/README.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para usar a biblioteca **Microsoft.Recognizers.Text.Number**, inclua o pacote do NuGet e adicione-o usando as instruções em seu arquivo de bot.

```csharp
using System.Linq;
using Microsoft.Recognizers.Text;
using Microsoft.Recognizers.Text.Number;
```

É possível manipular a validação de muitas maneiras. Aqui, atualizaremos nossa classe auxiliar para incluir a validação.

Adicione os seguintes membros à classe interna `UserFieldInfo` do bot.

```csharp
/// <summary>Delegate for validating input.</summary>
/// <param name="turnContext">The current turn context. turnContext.Activity.Text contains the input to validate.</param>
/// <returns><code>true</code> if the input is valid; otherwise, <code>false</code>.</returns>
public delegate Task<bool> ValidatorDelegate(
    ITurnContext turnContext,
    CancellationToken cancellationToken = default(CancellationToken));

/// <summary>By default, evaluate all input as valid.</summary>
private static readonly ValidatorDelegate NoValidator =
    async (ITurnContext turnContext, CancellationToken cancellationToken) => true;

/// <summary>Gets or sets the validation function to use.</summary>
public ValidatorDelegate ValidateInput { get; set; } = NoValidator;
```

Em seguida, atualize a entrada _idade_ no `UserFields` do bot para definir a validação a ser usada.
Como validaremos a entrada antes de definir o valor para a idade, podemos simplificar um pouco a função `SetValue` e tirar proveito da biblioteca de reconhecedores de texto.

```csharp
private static List<UserFieldInfo> UserFields { get; } = new List<UserFieldInfo>
{
    // ...
    new UserFieldInfo {
        Key = nameof(UserProfile.Age),
        Prompt = "How old are you?",
        GetValue = (profile) => profile.Age.HasValue? profile.Age.Value.ToString() : null,
        SetValue = (profile, value) =>
        {
            // As long as the input validates, this should work.
            List<ModelResult> result = NumberRecognizer.RecognizeNumber(value, Culture.English);
            profile.Age = int.Parse(result.First().Text);
        },
        ValidateInput = async (turnContext, cancellationToken) =>
        {
            try
            {
                // Recognize the input as a number. This works for responses such as
                // "twelve" as well as "12".
                List<ModelResult> result = NumberRecognizer.RecognizeNumber(
                    turnContext.Activity.Text, Culture.English);

                // Attempt to convert the Recognizer result to an integer
                int.TryParse(result.First().Text, out int age);

                if (age < 18)
                {
                    await turnContext.SendActivityAsync(
                        "You must be 18 or older.",
                        cancellationToken: cancellationToken);
                    return false;
                }
                else if (age > 120)
                {
                    await turnContext.SendActivityAsync(
                        "You must be 120 or younger.",
                        cancellationToken: cancellationToken);
                    return false;
                }
            }
            catch
            {
                await turnContext.SendActivityAsync(
                    "I couldn't understand your input. Please enter your age in years.",
                    cancellationToken: cancellationToken);
                return false;
            }

            // If we got through this, the number is valid.
            return true;
        },
    },
    // ...
};
```

Por fim, podemos atualizar nosso manipulador de turnos para validar todas as entradas antes de salvar um valor na propriedade.
Padrões de validação para nossa função NoValidator, que aceita qualquer entrada. Portanto, o comportamento do prompt de idade é o único que será alterado. Se a entrada não for validada, não definiremos o campo e o bot solicitará uma entrada para esse campo novamente no próximo turno.

Aqui, estamos apenas examinando a parte do manipulador de turnos que precisamos atualizar.

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type is ActivityTypes.Message)
    {
        // ...
        // Check whether we need more information.
        if (topicState.Topic is ProfileTopic)
        {
            // If we're expecting input, record it in the user's profile.
            if (topicState.Prompt != null)
            {
                UserFieldInfo field = UserFields.First(f => f.Key.Equals(topicState.Prompt));
                if (await field.ValidateInput(turnContext, cancellationToken))
                {
                    field.SetValue(userProfile, turnContext.Activity.Text.Trim());
                }
            }

            // ...
        }
        //...
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para usar a biblioteca **Reconhecedores**, adicione o pacote e torne-o necessário em seu código de bot (no **bot.js**):

```bash
npm i @microsoft/recognizers-text-suite --save
```

```javascript
// Required packages for this bot.
const Recognizers = require('@microsoft/recognizers-text-suite');
```

Em seguida, atualize os metadados `fields` para incluir reconhecimento de texto e código de validação:

```javascript
// Define the prompts to use to ask for user profile information.
const fields = {
    userName: { prompt: "What is your name?" },
    age: {
        prompt: "How old are you?",
        recognize: (turnContext) => {
            var result = Recognizers.recognizeNumber(
                turnContext.activity.text, Recognizers.Culture.English);
            return parseInt(result[0].resolution.value);
        },
        validate: async (turnContext) => {
            try {
                // Recognize the input as a number. This works for responses such as
                // "twelve" as well as "12".
                var result = Recognizers.recognizeNumber(
                    turnContext.activity.text, Recognizers.Culture.English);
                var age = parseInt(result[0].resolution.value);
                if (age < 18) {
                    await turnContext.sendActivity("You must be 18 or older.");
                    return false;
                }
                if (age > 120 ) {
                    await turnContext.sendActivity("You must be 120 or younger.");
                    return false;
                }
            } catch (_) {
                await turnContext.sendActivity(
                    "I couldn't understand your input. Please enter your age in years.");
                return false;
            }
            return true;
        }
    },
    workPlace: { prompt: "Where do you work?" }
}
```

No manipulador de turnos do nosso bot, atualize os blocos a seguir, nos quais podemos registrar a entrada do usuário e solicitar ao usuário. É necessário atualizar essas seções para levar em conta as alterações aos metadados do campo.

```javascript
async onTurn(turnContext) {
    // Handle only message activities from the user.
    if (turnContext.activity.type === ActivityTypes.Message) {
        // ...

        if (topicState.topic === 'profile') {
            // If a prompt flag is set in the conversation state, use it to capture the incoming value
            // into the appropriate field of the user profile.
            if (topicState.prompt) {
                const field = fields[topicState.prompt];
                // If the prompt has validation, check whether the input validates.
                if (!field.validate || await field.validate(turnContext)) {
                    // Set the field, using a recognizer if one is defined.
                    userProfile[topicState.prompt] = (field.recognize)
                        ? field.recognize(turnContext)
                        : turnContext.activity.text;
                }
            }

            // ...

            if (empty_fields.length) {

                // ...

                // We have at least one empty field. Prompt for the next empty field.
                await turnContext.sendActivity(empty_fields[0].prompt.prompt);

                // ...

            } // ...
        } // ...

        // Save state changes
        await this.conversationState.saveChanges(turnContext);
        await this.userState.saveChanges(turnContext);
    }
}
```

---

## <a name="next-step"></a>Próxima etapa

Agora que você sabe como gerenciar os estados de solicitação, vamos ver como você pode aproveitar a biblioteca de **Diálogos** para solicitar informações aos usuários.

> [!div class="nextstepaction"]
> [Solicitar informações aos usuários usando Diálogos](bot-builder-prompts.md)

