---
title: Gerenciar a conversa e o estado do usuário | Microsoft Docs
description: Saiba como salvar e recuperar dados do estado com o SDK do Construtor de Bot para .NET.
keywords: estado da conversa, estado do usuário, fluxo da conversa
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 09/18/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2a3709111b048730805b5578306c669591122dda
ms.sourcegitcommit: 633008f8db06f1bb5be7bacdb7dd8de6f8165328
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753604"
---
# <a name="manage-conversation-and-user-state"></a>Gerenciar conversa e estado do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma chave para obter bom design de bot é acompanhar o contexto de uma conversa para que o bot reconheça aspectos como as respostas às perguntas anteriores. Dependendo no que o bot for utilizado, talvez seja necessário até mesmo controlar as informações de estado ou repositório por mais tempo que o tempo de vida da conversa. Um *estado* do bot é uma informação que o bot reconhece para responder apropriadamente às mensagens recebidas. O SDK do Bot Builder fornece duas classes para armazenar e recuperar dados de estado como um objeto associado a um usuário ou a uma conversa.

- O **estado da conversa** ajuda o bot a acompanhar a conversa atual que o bot está tendo com o usuário. Se o bot precisar concluir uma sequência de etapas ou alternar entre tópicos de conversa, será possível usar as propriedades de conversa para gerenciar etapas em uma sequência ou acompanhar o tópico atual. 

- O **estado do usuário** pode ser usado para muitas finalidades, como determinar onde a conversa anterior do usuário foi interrompida ou simplesmente cumprimentando um usuário habitual pelo nome. Se você armazenar as preferências de um usuário, poderá usar essas informações para personalizar a conversa no próximo chat. Por exemplo, é possível alertar o usuário sobre um artigo de notícias relacionado a um tópico de interesse ou alertar um usuário quando um compromisso estiver disponível. 

`ConversationState` e `UserState` são classes de estado que são especializações da classe `BotState` com políticas que controlam o tempo de vida e o escopo dos objetos armazenados nelas. Os componentes que precisam armazenar o estado criam e registram uma propriedade com um tipo e usam o acessador de propriedades para acessar o estado. O gerenciador de estado do bot pode usar o armazenamento de memória, CosmosDB e o armazenamento de BLOBs. 

> [!NOTE] 
> Use o gerenciador de estado do bot para armazenar preferências, nome de usuário ou o último pedido feito, mas não o use para armazenar dados corporativos críticos. Para dados críticos, crie seus próprios componentes de armazenamento ou grave diretamente em [armazenamento](bot-builder-howto-v4-storage.md).
> Armazenamento de dados na memória destina-se somente para teste. Esse armazenamento é volátil e temporário. Os dados serão limpos sempre que o bot for reiniciado.

## <a name="using-conversation-state-and-user-state-to-direct-conversation-flow"></a>Usar o estado da conversa e do usuário para direcionar o fluxo da conversa
Ao criar um fluxo da conversa, é útil definir um sinalizador de estado para direcionar o fluxo da conversa. O sinalizador pode ser um simples tipo booliano ou um tipo que inclui o nome do tópico atual. O sinalizador pode ajudar a rastrear onde você está em uma conversa. Por exemplo, um sinalizador de tipo booliano pode lhe dizer se você está em uma conversa ou não. Porém, uma propriedade de nome de tópico informa em qual conversa você está no momento.



# <a name="ctabcsharp"></a>[C#](#tab/csharp)
### <a name="conversation-and-user-state"></a>Estado do usuário e da conversa
Você pode usar o [exemplo de Bot de Echo com contador](https://aka.ms/EchoBot-With-Counter) como o ponto de partida para estas instruções. Primeiro, crie a classe `TopicState` para gerenciar o tópico atual da conversa em `TopicState.cs` conforme mostrado abaixo:

```csharp
public class TopicState
{
   public string Prompt { get; set; } = "askName";
}
``` 
Em seguida, crie a classe `UserProfile` em `UserProfile.cs` para gerenciar o estado do usuário.
```csharp
public class UserProfile
{
    public string UserName { get; set; }
    public string TelephoneNumber { get; set; }
}
``` 
A classe `TopicState` tem um sinalizador para manter o controle sobre o ponto onde nos encontramos na conversa e usa o estado da conversa para armazená-lo. O Prompt é inicializado como "askName" para iniciar a conversa. Depois que o bot receber a resposta do usuário, o Prompt será redefinido como "askNumber" para iniciar a próxima conversa. A classe `UserProfile` controla o número de telefone e o nome de usuário e armazena-os no estado do usuário.

### <a name="property-accessors"></a>Acessadores de propriedades
A classe `EchoBotAccessors` do exemplo é criada como um singleton e passada para o construtor `class EchoWithCounterBot : IBot` através da injeção de dependência. A classe `EchoBotAccessors` contém o `ConversationState`, `UserState` e o `IStatePropertyAccessor` associado. O objeto `conversationState` armazena o estado do tópico e o objeto `userState` que armazena as informações de perfil do usuário. Os objetos `ConversationState` e `UserState` serão criados posteriormente no arquivo Startup.cs. Nos objetos de estado do usuário e da conversa podemos manter tudo no escopo do usuário e da conversa. 

Atualizamos o construtor para incluir `UserState` conforme mostrado abaixo:
```csharp
public EchoBotAccessors(ConversationState conversationState, UserState userState)
{
    ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    UserState = userState ?? throw new ArgumentNullException(nameof(userState));
}
```
Crie nomes exclusivos para os acessadores `TopicState` e `UserProfile`.
```csharp
public static string UserProfileName { get; } = $"{nameof(EchoBotAccessors)}.UserProfile";
public static string TopicStateName { get; } = $"{nameof(EchoBotAccessors)}.TopicState";
```
Em seguida, defina dois acessadores. O primeiro deles armazena o tópico da conversa e o segundo armazena o nome e o número de telefone do usuário.
```csharp
public IStatePropertyAccessor<TopicState> TopicState { get; set; }
public IStatePropertyAccessor<UserProfile> UserProfile { get; set; }
```

As propriedades para obter o ConversationState já estão definidas. Porém, será preciso adicionar `UserState` como mostrado abaixo:
```csharp
public ConversationState ConversationState { get; }
public UserState UserState { get; }
```
Depois de fazer as alterações, salve o arquivo. Em seguida, atualizaremos a classe Startup para criar o objeto `UserState` para manter tudo no escopo do usuário. O arquivo `ConversationState` já existe. 
```csharp

services.AddBot<EchoWithCounterBot>(options =>
{
    ...

    IStorage dataStore = new MemoryStorage();
    
    var conversationState = new ConversationState(dataStore);
    options.State.Add(conversationState);
        
    var userState = new UserState(dataStore);  
    options.State.Add(userState);
});
```
As linhas `options.State.Add(ConversationState);` e `options.State.Add(userState);` adicionam o estado da conversa e o estado do usuário, respectivamente. Em seguida, crie e registre os acessadores de estado. Os acessadores criados aqui são passados para a classe derivada de IBot em cada turno. Modifique o código para incluir o estado do usuário, conforme mostrado abaixo:
```csharp
services.AddSingleton<EchoBotAccessors>(sp =>
{
   ...
    var userState = options.State.OfType<UserState>().FirstOrDefault();
    if (userState == null)
    {
        throw new InvalidOperationException("UserState must be defined and added before adding user-scoped state accessors.");
    }
   ...
 });
```

Em seguida, crie dois acessadores usando `TopicState` e `UserProfile`, em seguida, passe-os para a classe `class EchoWithCounterBot : IBot` por meio da injeção de dependência.
```csharp
services.AddSingleton<EchoBotAccessors>(sp =>
{
   ...
    var accessors = new EchoBotAccessors(conversationState, userState)
    {
        TopicState = conversationState.CreateProperty<TopicState>(EchoBotAccessors.TopicStateName),
        UserProfile = userState.CreateProperty<UserProfile>(EchoBotAccessors.UserProfileName),
     };

     return accessors;
 });
```

A conversa e o estado do usuário são vinculados a um singleton pelo bloco de códigos `services.AddSingleton` e são salvos por um acessador de armazenamento de estado no código que começa com `var accessors = new EchoBotAccessor(conversationState, userState)`.

### <a name="use-conversation-and-user-state-properties"></a>Usar as propriedades de conversa e estado do usuário 
No manipulador `OnTurnAsync` da classe `EchoWithCounterBot : IBot`, modifique o código para solicitar o nome do usuário e, em seguida, o número de telefone. Para controlar o ponto onde estamos na conversa, usamos a propriedade Prompt definida no TopicState. Essa propriedade é inicializada para "askName". Após obtermos o nome de usuário, podemos defini-lo como "askNumber" e definir o nome de usuário como o nome de usuário digitado. Após receber o número de telefone, envie uma mensagem de confirmação e defina o prompt como 'confirmation' já que você está no fim da conversa.

```csharp
if (turnContext.Activity.Type == ActivityTypes.Message)
{
    // Get the conversation state from the turn context.
    var convo = await _accessors.TopicState.GetAsync(turnContext, () => new TopicState());
    
    // Get the user state from the turn context.
    var user = await _accessors.UserProfile.GetAsync(turnContext, () => new UserProfile());
    
    // Ask user name. The Prompt was initialiazed as "askName" in the TopicState.cs file.
    if (convo.Prompt == "askName")
    {
        await turnContext.SendActivityAsync("What is your name?");
        
        // Set the Prompt to ask the next question for this conversation
        convo.Prompt = "askNumber";
        
        // Set the property using the accessor
        await _accessors.TopicState.SetAsync(turnContext, convo);
        
        //Save the new prompt into the conversation state.
        await _accessors.ConversationState.SaveChangesAsync(turnContext);
    }
    else if (convo.Prompt == "askNumber")
    {
        // Set the UserName that is defined in the UserProfile class
        user.UserName = turnContext.Activity.Text;
        
        // Use the user name to prompt the user for phone number
        await turnContext.SendActivityAsync($"Hello, {user.UserName}. What's your telephone number?");
        
        // Set the Prompt now that we have collected all the data
        convo.Prompt = "confirmation";
                 
        await _accessors.TopicState.SetAsync(turnContext, convo);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);

        await _accessors.UserProfile.SetAsync(turnContext, user);
        await _accessors.UserState.SaveChangesAsync(turnContext);
    }
    else if (convo.Prompt == "confirmation")
    { 
        // Set the TelephoneNumber that is defined in the UserProfile class
        user.TelephoneNumber = turnContext.Activity.Text;

        await turnContext.SendActivityAsync($"Got it, {user.UserName}. I'll call you later.");

        // reset initial prompt state
        convo.Prompt = "askName"; // Reset for a new conversation.
        
        await _accessors.TopicState.SetAsync(turnContext, convo);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);
    }
}
```   

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="conversation-and-user-state"></a>Estado do usuário e da conversa

Você pode usar o [exemplo de Bot de eco com contador](https://aka.ms/EchoBot-With-Counter-JS) como o ponto de partida para estas instruções. Este exemplo já usa o `ConversationState` para armazenar a contagem de mensagens. Precisamos adicionar um objeto `TopicStates` para acompanhar o estado da conversa e `UserState` para monitorar informações do usuário em um objeto `userProfile`. 

No arquivo `index.js` do bot principal, adicione o `UserState` à lista de requisitos:

**index.js**

```javascript
// Import required bot services. See https://aka.ms/bot-services to learn more about the different parts of a bot.
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState } = require('botbuilder');
```

Em seguida, crie o `UserState` usando `MemoryStorage` como o provedor de armazenamento. Depois, passe-o como o segundo argumento para a classe `MainDialog`.

**index.js**

```javascript
// Create conversation state with in-memory storage provider. 
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);
// Create the main bot.
const bot = new EchBot(conversationState, userState);
```

No arquivo `bot.js`, atualize o construtor para aceitar o `userState` como o segundo argumento. Em seguida, crie uma propriedade `topicState` no `conversationState` e crie uma propriedade `userProfile` no `userState`.

**bot.js**

```javascript
const TOPIC_STATE = 'topic';
const USER_PROFILE = 'user';

constructor (conversationState, userState) {
    // creates a new state accessor property.see https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors 
    this.conversationState = conversationState;
    this.topicState = this.conversationState.createProperty(TOPIC_STATE);

    // User state
    this.userState = userState;
    this.userProfile = this.userState.createProperty(USER_PROFILE);
}
```

### <a name="use-conversation-and-user-state-properties"></a>Usar as propriedades de conversa e estado do usuário

No manipulador `onTurn` da classe `MainDialog`, modifique o código para solicitar o nome do usuário e, em seguida, o número de telefone. Para controlar o ponto em que estamos na conversa, usamos a propriedade `prompt` definida no `topicState`. Essa propriedade é inicializada para "askName". Após obtermos o nome de usuário, podemos defini-lo como "askNumber" e definir o nome de usuário como o nome de usuário digitado. Após receber o número de telefone, envie uma mensagem de confirmação e defina o prompt como `undefined` porque você está no fim da conversa.

**dialogs/mainDialog/index.js**

```javascript
// see https://aka.ms/about-bot-activity-message to learn more about the message and other activity types
if (turnContext.activity.type === 'message') {
    // read from state and set default object if object does not exist in storage.
    let topicState = await this.topicState.get(turnContext, {
        //Define the topic state object
        prompt: "askName"
    });
    let userProfile = await this.userProfile.get(turnContext, {  
        // Define the user's profile object
        "userName": "",
        "telephoneNumber": ""
    });

    if(topicState.prompt == "askName"){
        await turnContext.sendActivity("What is your name?");

        // Set next prompt state
        topicState.prompt = "askNumber";

        // Update state
        await this.topicState.set(turnContext, topicState);
    }
    else if(topicState.prompt == "askNumber"){
        // Set the UserName that is defined in the UserProfile class
        userProfile.userName = turnContext.activity.text;

        // Use the user name to prompt the user for phone number
        await turnContext.sendActivity(`Hello, ${userProfile.userName}. What's your telephone number?`);

        // Set next prompt state
        topicState.prompt = "confirmation";

        // Update states
        await this.topicState.set(turnContext, topicState);
        await this.userProfile.set(turnContext, userProfile);
    }
    else if(topicState.prompt == "confirmation"){
        // Set the phone number
        userProfile.telephoneNumber = turnContext.activity.text;

        // Sent confirmation
        await turnContext.sendActivity(`Got it, ${userProfile.userName}. I'll call you later.`)

        // reset initial prompt state
        topicState.prompt = "askName"; // Reset for a new conversation

        // Update states
        await this.topicState.set(turnContext, topicState);
        await this.userProfile.set(turnContext, userProfile);
    }
    
    // Save state changes to storage
    await this.conversationState.saveChanges(turnContext);
    await this.userState.saveChanges(turnContext);
    
}
else {
    await turnContext.sendActivity(`[${turnContext.activity.type} event detected]`);
}
```

---

## <a name="start-your-bot"></a>Inicie seu bot
Execute o bot localmente.

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador. 
2. Escolha o arquivo .bot localizado no diretório em que você criou a solução do Visual Studio.

### <a name="interact-with-your-bot"></a>Interagir com o bot

Envie uma mensagem de “Olá” ao seu bot e o bot solicitará seu nome e número de telefone. Depois de fornecer essas informações, o bot enviará uma mensagem de confirmação. Se você continuar depois disso, o bot percorrerá novamente o mesmo ciclo.

![Emulador em execução](../media/emulator-v4/emulator-running-manage-state.png)

Se você decidir gerenciar o estado por conta própria, confira [gerenciar o fluxo da conversa com seus próprios prompts](bot-builder-primitive-prompts.md). Uma alternativa é usar o diálogo de cascata. A caixa de diálogo controla o estado da conversa para você, você não precisa criar sinalizadores para controlar seu estado. Para saber mais, confira [gerenciar uma conversa simples com diálogos](bot-builder-dialog-manage-conversation-flow.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como usar o estado para ajudá-lo a ler e gravar dados de bot para o armazenamento, vamos dar uma olhada em como você pode ler e gravar diretamente no armazenamento.

> [!div class="nextstepaction"]
> [Como gravar diretamente no armazenamento](bot-builder-howto-v4-storage.md).
