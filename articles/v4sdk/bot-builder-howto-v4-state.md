---
title: Salvar dados do usuário e da conversa | Microsoft Docs
description: Saiba como salvar e recuperar dados de estado com o SDK do Bot Framework para SDK.
keywords: estado da conversa, estado do usuário, conversa, salvar estado, gerenciar o estado do bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/26/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8c3696d0642e1b1ce9c3d3e23118a7bd9ab0023b
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224581"
---
# <a name="save-user-and-conversation-data"></a>Salvar dados do usuário e da conversa

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Um bot é inerentemente sem estado. Após a implantação do seu bot, talvez ele não seja executado no mesmo processo ou no mesmo computador de um turno para outro. Porém, talvez seu bot precise controlar o contexto de uma conversa, para que ele possa gerenciar seu comportamento e lembrar-se das respostas às perguntas anteriores. Os recursos de armazenamento e estado do SDK permitem que você adicione um estado ao seu bot.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário conhecer os [conceitos básicos sobre bot](bot-builder-basics.md) e como os bots [gerenciam o estado](bot-builder-concept-state.md).
- O código neste artigo se baseia no exemplo de **StateBot**. Você precisará de uma cópia do exemplo em [C#](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/dotnet_core/StateBot) ou [JS]().
- E do [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started) para testar o bot localmente.

## <a name="about-the-sample-code"></a>Sobre o código de exemplo

Este artigo aborda os aspectos de configuração do gerenciamento de estado do seu bot. Para adicionar o estado, configuramos as propriedades de estado, o gerenciamento de estado e o armazenamento e, depois, usamos tudo no bot.

- Cada _propriedade de estado_ contém informações de estado do seu bot.
- Cada acessador de propriedade de estado permite que você obtenha ou defina o valor da propriedade de estado associada.
- Cada objeto de gerenciamento de estado automatiza a leitura e gravação das informações de estado associadas ao armazenamento.
- A camada de armazenamento conecta-se ao armazenamento subjacente de estado, como na memória (para teste) ou ao Armazenamento do Azure Cosmos DB (para produção).

Precisamos configurar o bot com acessadores de propriedade de estado, com os quais ele pode obter e definir o estado em tempo de execução, ao lidar com uma atividade. Crie um acessador de propriedade de estado usando um objeto de gerenciamento de estado, e crie um objeto de gerenciamento de estado usando uma camada de armazenamento. Então, vamos começar no nível do armazenamento.

## <a name="configure-storage"></a>Configurar o armazenamento

Como não planejamos implantar esse bot, usaremos o _armazenamento de memória_ para configurar os estados do usuário e da conversa na próxima etapa.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

No arquivo **Startup.cs**, configure a camada de armazenamento.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    IStorage storage = new MemoryStorage();
    // ...
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No arquivo **index.js**, configure a camada de armazenamento.

```javascript
// Define state store for your bot.
const memoryStorage = new MemoryStorage();
```

---

## <a name="create-state-management-objects"></a>Criar objetos de gerenciamento de estado

Acompanhamos os estados do _usuário_ e da _conversa_ e os usamos para criar _acessadores de propriedade de estado_ na próxima etapa.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

No arquivo **Startup.cs**, faça referência à camada de armazenamento ao criar seus objetos de gerenciamento de estado.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    ConversationState conversationState = new ConversationState(storage);
    UserState userState = new UserState(storage);
    // ...
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No arquivo **index.js**, adicione `UserState` à sua instrução require.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState } = require('botbuilder');
```

Depois, faça referência à camada de armazenamento ao criar seus objetos de gerenciamento de estado da conversa e do usuário.

```javascript
// Create conversation and user state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);
```

---

## <a name="create-state-property-accessors"></a>Criar acessadores de propriedades do estado

Para _declarar_ uma propriedade de estado, primeiro crie um acessador de propriedade de estado, usando um dos nossos objetos de gerenciamento de estado. Precisamos configurar o bot para acompanhar as informações a seguir:

- O nome do usuário, que definiremos no estado do usuário.
- Mesmo que tenhamos solicitado ao usuário apenas o nome e algumas informações adicionais sobre a mensagem que ele acabou de enviar.

O bot usa o acessador para obter a propriedade de estado do contexto do turno.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Primeiro, definimos as classes para conter todas as informações que queremos gerenciar em cada tipo de estado.

- Uma classe `UserProfile` para as informações do usuário que o bot coletará.
- Uma classe `ConversationData` para incluir informações sobre quando uma mensagem chegou e quem a enviou.

```csharp
// Defines a state property used to track information about the user.
public class UserProfile
{
    public string Name { get; set; }
}
```

```csharp
// Defines a state property used to track conversation data.
public class ConversationData
{
    // The time-stamp of the most recent incoming message.
    public string Timestamp { get; set; }

    // The ID of the user's channel.
    public string ChannelId { get; set; }

    // Track whether we have already asked the user's name
    public bool PromptedUserForName { get; set; } = false;
}
```

Em seguida, definimos uma classe que contém as informações de gerenciamento de estado das quais precisaremos para configurar a instância do nosso bot.

```csharp
public class StateBotAccessors
{
    public StateBotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }
  
    public static string UserProfileName { get; } = "UserProfile";

    public static string ConversationDataName { get; } = "ConversationData";

    public IStatePropertyAccessor<UserProfile> UserProfileAccessor { get; set; }

    public IStatePropertyAccessor<ConversationData> ConversationDataAccessor { get; set; }
  
    public ConversationState ConversationState { get; }
  
    public UserState UserState { get; }
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Passamos os objetos de gerenciamento de estado diretamente para o construtor do bot e permitimos que o próprio bot crie os acessadores de propriedade de estado.

No arquivo **index.js**, forneça os objetos de gerenciamento de estado durante a criação do bot.

```javascript
// Create the bot.
const myBot = new MyBot(conversationState, userState);
```

Em **bot.js**, defina os identificadores que você precisará para gerenciar e controlar o estado.

```javascript
// The accessor names for the conversation data and user profile state property accessors.
const CONVERSATION_DATA_PROPERTY = 'conversationData';
const USER_PROFILE_PROPERTY = 'userProfile';
```

---

## <a name="configure-your-bot"></a>Configurar seu bot

Agora estamos prontos para definir os acessadores de propriedade de estado e configurar nosso bot.
Usaremos o objeto de gerenciamento de estado de conversa para o acessador de propriedade de estado do fluxo da conversa.
Usaremos o objeto de gerenciamento de estado de usuário para o acessador de propriedade de estado do perfil do usuário.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

No arquivo **Startup.cs**, configuramos o ASP.NET para fornecer os objetos de propriedade e de gerenciamento de estado agrupado. Isso será recuperado do construtor do bot por meio da estrutura de injeção de dependência no ASP.NET Core.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddSingleton<StateBotAccessors>(sp =>
    {
        // Create the custom state accessor.
        return new StateBotAccessors(conversationState, userState)
        {
            ConversationDataAccessor = conversationState.CreateProperty<ConversationData>(StateBotAccessors.ConversationDataName),
            UserProfileAccessor = userState.CreateProperty<UserProfile>(StateBotAccessors.UserProfileName),
        };
    });
}
```

No construtor do bot, o objeto `CustomPromptBotAccessors` é fornecido quando o ASP.NET cria o bot.

```csharp
// Defines a bot for filling a user profile.
public class CustomPromptBot : IBot
{
    private readonly StateBotAccessors _accessors;

    public StateBot(StateBotAccessors accessors, ILoggerFactory loggerFactory)
    {
        // ...
        accessors = accessors ?? throw new System.ArgumentNullException(nameof(accessors));
    }

    // The bot's turn handler and other supporting code...
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor do bot (no arquivo **bot.js**), criamos os acessadores de propriedade de estado e os adicionamos ao bot. Também adicionamos referências a objetos de gerenciamento de estado, pois precisamos delas para salvar as alterações de estado feitas.

```javascript
constructor(conversationState, userState) {
    // Create the state property accessors for the conversation data and user profile.
    this.conversationData = conversationState.createProperty(CONVERSATION_DATA_PROPERTY);
    this.userProfile = userState.createProperty(USER_PROFILE_PROPERTY);

    // The state management objects for the conversation and user state.
    this.conversationState = conversationState;
    this.userState = userState;
}
```

---

## <a name="access-state-from-your-bot"></a>Estado de acesso do seu bot

As seções anteriores abordam as etapas na inicialização para adição dos nossos acessadores de propriedade de estado ao nosso bot.
Agora, podemos usar esses acessadores em tempo de execução para ler e gravar informações de estado.

1. Antes de usarmos nossas propriedades de estado, usamos cada acessador para carregar a propriedade do armazenamento e obtê-la no cache de estado.
   - Sempre que você obtiver uma propriedade de estado por meio de seu acessador, forneça um valor padrão. Caso contrário, poderá receber um erro de valor nulo.
1. Antes de sairmos do manipulador de turno:
   1. Usamos o método _set_ dos acessadores para enviar as alterações por push ao estado do bot.
   1. Usamos o método _save changes_ dos objetos de gerenciamento de estado para gravar essas alterações no armazenamento.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// The bot's turn handler.
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Get the state properties from the turn context.
        UserProfile userProfile =
            await _accessors.UserProfileAccessor.GetAsync(turnContext, () => new UserProfile());
        ConversationData conversationData =
            await _accessors.ConversationDataAccessor.GetAsync(turnContext, () => new ConversationData());

        if (string.IsNullOrEmpty(userProfile.Name))
        {
            // First time around this is set to false, so we will prompt user for name.
            if (conversationData.PromptedUserForName)
            {
                // Set the name to what the user provided
                userProfile.Name = turnContext.Activity.Text?.Trim();

                // Acknowledge that we got their name.
                await turnContext.SendActivityAsync($"Thanks {userProfile.Name}.");

                // Reset the flag to allow the bot to go though the cycle again.
                conversationData.PromptedUserForName = false;
            }
            else
            {
                // Prompt the user for their name.
                await turnContext.SendActivityAsync($"What is your name?");

                // Set the flag to true, so we don't prompt in the next turn.
                conversationData.PromptedUserForName = true;
            }

            // Save user state and save changes.
            await _accessors.UserProfileAccessor.SetAsync(turnContext, userProfile);
            await _accessors.UserState.SaveChangesAsync(turnContext);
        }
        else
        {
            // Add message details to the conversation data.
            conversationData.Timestamp = turnContext.Activity.Timestamp.ToString();
            conversationData.ChannelId = turnContext.Activity.ChannelId.ToString();

            // Display state data
            await turnContext.SendActivityAsync($"{userProfile.Name} sent: {turnContext.Activity.Text}");
            await turnContext.SendActivityAsync($"Message received at: {conversationData.Timestamp}");
            await turnContext.SendActivityAsync($"Message received from: {conversationData.ChannelId}");
        }

        // Update conversation state and save changes.
        await _accessors.ConversationDataAccessor.SetAsync(turnContext, conversationData);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);
    }
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// The bot's turn handler.
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Get the state properties from the turn context.
        const userProfile = await this.userProfile.get(turnContext, {});
        const conversationData = await this.conversationData.get(
            turnContext, { promptedForUserName: false });

        if (!userProfile.name) {
            // First time around this is undefined, so we will prompt user for name.
            if (conversationData.promptedForUserName) {
                // Set the name to what the user provided.
                userProfile.name = turnContext.activity.text;

                // Acknowledge that we got their name.
                await turnContext.sendActivity(`Thanks ${userProfile.name}.`);

                // Reset the flag to allow the bot to go though the cycle again.
                conversationData.promptedForUserName = false;
            } else {
                // Prompt the user for their name.
                await turnContext.sendActivity('What is your name?');

                // Set the flag to true, so we don't prompt in the next turn.
                conversationData.promptedForUserName = true;
            }
            // Save user state and save changes.
            await this.userProfile.set(turnContext, userProfile);
            await this.userState.saveChanges(turnContext);
        } else {
            // Add message details to the conversation data.
            conversationData.timestamp = turnContext.activity.timestamp.toLocaleString();
            conversationData.channelId = turnContext.activity.channelId;

            // Display state data.
            await turnContext.sendActivity(`${userProfile.name} sent: ${turnContext.activity.text}`);
            await turnContext.sendActivity(`Message received at: ${conversationData.timestamp}`);
            await turnContext.sendActivity(`Message received from: ${conversationData.channelId}`);
        }
        // Update conversation state and save changes.
        await this.conversationData.set(turnContext, conversationData);
        await this.conversationState.saveChanges(turnContext);
    }
}
```

---

## <a name="test-the-bot"></a>Testar o bot

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para o exemplo em [C#](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/dotnet_core/StateBot) ou em [JS](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/js/stateBot).
1. Use o emulador para testar o bot, conforme mostrado abaixo.

![exemplo de teste de estado do bot](media/state-bot-testing-emulator.png)

## <a name="additional-resources"></a>Recursos adicionais

**Privacidade:** se você pretende armazenar dados pessoais do usuário, garanta a conformidade com o [Regulamento Geral sobre a Proteção de Dados](https://blog.botframework.com/2018/04/23/general-data-protection-regulation-gdpr).

**Gerenciamento de estado:** todas as chamadas de gerenciamento de estado são assíncronas e last-writer-wins por padrão. Na prática, você deve obter, definir e salvar o estado o mais próximo possível em seu bot.

**Dados comerciais críticos:** use o estado do bot para armazenar preferências, nome de usuário ou o último pedido feito, mas não o use para armazenar dados corporativos críticos. Para dados críticos, [crie seus próprios componentes de armazenamento](bot-builder-custom-storage.md) ou grave diretamente no [armazenamento](bot-builder-howto-v4-storage.md).

**Recognizer-Text:** o exemplo usa as bibliotecas Microsoft/Recognizer-Text para analisar e validar a entrada do usuário. Para saber mais, confira a página [visão geral](https://github.com/Microsoft/Recognizers-Text#microsoft-recognizers-text-overview).

## <a name="next-step"></a>Próxima etapa

Agora que você sabe como configurar o estado para ajudar você a ler e a gravar dados no armazenamento, vamos aprender a fazer uma série de perguntas ao usuário, validar suas respostas e salvar as entradas.

> [!div class="nextstepaction"]
> [Crie seus próprios prompts para coletar entradas do usuário](bot-builder-primitive-prompts.md).
