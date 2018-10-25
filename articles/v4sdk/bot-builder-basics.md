---
title: Atividade do bot no SDK do Bot Builder | Microsoft Docs
description: Descreve como a atividade e o HTTP funcionam no SDK do Bot Builder.
keywords: fluxo da conversa, turno, conversa de bot, diálogos, prompts, cascatas, conjunto de diálogos
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 9/26/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b929020908990a97e039be6467eef033c50228db
ms.sourcegitcommit: aef7d80ceb9c3ec1cfb40131709a714c42960965
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49383132"
---
# <a name="understanding-how-bots-work"></a>Noções básicas de como funcionam os bots

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Um bot é um aplicativo com o qual os usuários interagem de maneira conversacional usando texto, gráficos (cartões ou imagens) ou fala. Cada interação entre o usuário e o bot gera uma *atividade*. O Serviço de Bot envia informações entre o aplicativo do usuário conectado ao bot (como Facebook, Skype, Slack e outros, que chamamos de *canal*) e o bot. Cada canal pode incluir informações adicionais nas atividades que enviam. Antes de criar bots, é importante entender como um bot usa objetos de atividade para se comunicar com seus usuários. Vamos primeiro dar uma olhada nas atividades que são trocadas quando executamos um bot de eco simples.

![diagrama de atividade](media/bot-builder-activity.png)

Dois tipos de atividade ilustrados aqui são: *atualização de conversa* e *mensagem*.

O serviço Bot Framework pode enviar uma atualização de conversa quando um participante ingressa na conversa. Por exemplo, ao iniciar uma conversa com o Bot Framework Emulator, você verá duas atividades de atualização de conversa (uma para o usuário que participa da conversa e outra para o bot que participa da conversa). Para distinguir essas atividades de atualização de conversa, verifique se a propriedade *members added* inclui um membro que não seja o bot. 

A atividade de mensagem contém informações de conversa entre as partes. Em um exemplo de bot de eco, as atividades de mensagem transmitem texto simples e o canal renderiza esse texto. Como alternativa, a atividade de mensagem pode conter o texto a ser falado, as ações sugeridas ou os cartões a serem exibidos.

Neste exemplo, o bot criou e enviou uma atividade de mensagem em resposta à atividade de mensagem de entrada que ele recebeu. No entanto, um bot pode responder de outras maneiras a uma atividade de mensagem recebida. Não é incomum para um bot responder a uma atividade de atualização de conversa enviando um texto de boas-vindas em uma atividade de mensagem. Mais informações podem ser encontradas nas [boas-vindas ao usuário](bot-builder-welcome-user.md).

### <a name="http-details"></a>Detalhes do HTTP

As atividades chegam ao bot do serviço Bot Framework por meio de uma solicitação HTTP POST. O bot responde à solicitação POST de entrada com um código de status HTTP 200. As atividades enviadas do bot para o canal são enviadas em uma HTTP POST separada para o serviço Bot Framework. Isso, por sua vez, é confirmado com um código de status HTTP 200.

O protocolo não especifica a ordem em que as solicitações POST e suas confirmações são feitas. No entanto, para se ajustar a estruturas de serviço HTTP comuns, essas solicitações normalmente são aninhadas, o que significa que a solicitação HTTP de saída é feita do bot dentro do escopo da solicitação HTTP de entrada. Esse padrão é ilustrado no diagrama acima. Como há duas conexões HTTP distintas de ponta a ponta, o modelo de segurança deve servir a ambas.

### <a name="defining-a-turn"></a>Definir um turno

Um turno, em relação aos nossos bots, é usado para descrever todo o processamento associado à chegada de uma atividade. 

O objeto *contexto de turno* fornece informações sobre a atividade, como o remetente e o receptor, o canal e outros dados necessários para processar a atividade. Ele também permite a adição de informações durante o turno em várias camadas do bot.

O contexto de turno é uma das abstrações mais importantes no SDK. Não só ele transmite a atividade de entrada para todos os componentes de middleware e a lógica do aplicativo, mas também fornece o mecanismo pelo qual os componentes de middleware e a lógica do aplicativo podem enviar atividades de saída.

## <a name="the-activity-processing-stack"></a>A pilha de processamento de atividade

Vamos analisar o diagrama anterior, com foco na chegada de uma atividade de mensagem.

![pilha de processamento de atividade](media/bot-builder-activity-processing-stack.png)

No exemplo acima, o bot respondeu à atividade de mensagem com outra atividade de mensagem contendo a mesma mensagem de texto. O processamento começa com a solicitação HTTP POST, com as informações de atividade transmitidas como uma carga JSON, chegando ao servidor Web. Em C#, isso geralmente será um projeto do ASP.NET e, em um projeto Node.js do JavaScript, isso costuma ser uma das estruturas populares, como Express ou Restify.

O *adaptador*, um componente integrado do SDK, serve como o condutor da estrutura. O serviço usa as informações de atividade para criar um objeto de atividade e chama o método *process activity* do adaptador ao passar as informações de autenticação e o objeto de atividade (essa chamada é encapsulada dentro das bibliotecas de C#, mas você a verá em JavaScript). Ao receber a atividade, o adaptador criará um objeto de contexto de turno e chamará o [middleware](#middleware). O processamento continuará para a lógica do bot após o middleware, o pipeline ser concluído e o adaptador descartar o objeto de contexto do turno.

O *manipulador de turnos*, que compõe a maior parte da lógica do aplicativo, usa um contexto de turno como seu argumento. O manipulador de turnos normalmente processará o conteúdo da atividade de entrada e gerará uma ou mais atividades em resposta, enviando-as usando o método *send activity* do contexto. A chamada do método de atividade de envio enviará uma atividade para o canal do usuário, a menos que o processamento seja de alguma forma interrompido. A atividade passará pelos eventuais [manipuladores de eventos](#response-event-handlers) registrados antes de serem enviados para o canal.

## <a name="middleware"></a>Middleware

O middleware é um conjunto linear de componentes que são adicionados e executados em ordem, dando a cada um a chance de funcionar na atividade antes e depois do manipulador de turnos do bot e de ter acesso ao contexto de turno para tal atividade. A menos que o middleware [entre em curto circuito](~/v4sdk/bot-builder-concept-middleware.md#short-circuiting), o estágio final do pipeline de middleware é um retorno de chamada para invocar o manipulador de turnos do bot antes de retornar para a pilha. Para obter mais informações detalhadas sobre o middleware, consulte o [tópico sobre middleware](~/v4sdk/bot-builder-concept-middleware.md).

## <a name="generating-responses"></a>Geração de respostas

O contexto de turnos fornece métodos de resposta de atividade para permitir que o código responda a uma atividade:

* Os métodos _send activity_ e _send activities_ enviam uma ou mais atividades para a conversa.
* Se compatível com o canal, o _atividade de atualização_ método atualiza uma atividade em uma conversa.
* Se compatível com o canal, o método _excluir atividade_ remove uma atividade da conversa.

Cada método de resposta é executado em um processo assíncrono. Quando é chamado, o método de resposta da atividade clona a lista de [manipuladores de eventos](#response-event-handlers) associada antes de começar a chamar os manipuladores, o que significa que conterá todos os manipuladores adicionados até este ponto, mas não conterá nada adicionado após o início do processo.

Isso também significa que a ordem de suas respostas em chamadas de atividade independentes não é garantida, especialmente quando uma tarefa é mais complexa que outra. Se seu bot puder gerar várias respostas para uma atividade de entrada, certifique-se de que elas façam sentido na ordem em que forem recebidas pelo usuário. A única exceção a isso é o método *send activities*, que permite que você envie um conjunto ordenado de atividades.

> [!IMPORTANT]
> O encadeamento que manipula o giro do bot primário lida com a disposição do objeto de contexto quando isso é feito. **Certifique-se que `await` todas as chamadas de atividade** para que o segmento principal espere na atividade gerada antes de terminar o processamento e a eliminação do contexto de turno. Do contrário, uma resposta (incluindo seus manipuladores) demora um tempo significativo e tenta agir no objeto de contexto, podendo vir a receber um erro `Context was disposed`. 

## <a name="response-event-handlers"></a>Manipuladores de eventos de resposta

Além da lógica de middleware aplicativo, os manipuladores de resposta (às vezes também chamados de manipuladores de eventos ou manipuladores de eventos de atividade) podem ser adicionados ao objeto de contexto. Esses manipuladores são chamados quando a [resposta](#generating-responses) associada acontece no objeto de contexto atual, antes de executar a resposta real. Esses manipuladores são úteis quando você sabe que deseja fazer algo, antes ou depois do evento real, para cada atividade desse tipo para o restante da resposta atual.

> [!WARNING]
> Tenha o cuidado de não chamar um método de resposta de atividade a partir de seu respectivo manipulador de eventos de resposta, por exemplo, chamando o método de atividade de envio de dentro de um manipulador _na atividade de envio_. Isso pode gerar um loop infinito.

Lembre-se: cada nova atividade obtém um novo thread para executar. Quando o encadeamento para processar a atividade é criado, a lista de manipuladores dessa atividade é copiada para esse novo encadeamento. Nenhum manipulador adicionado após esse ponto será executado para esse evento de atividade específico.

Os manipuladores registrados em um objeto de contexto são tratados de maneira muito semelhante a como o adaptador gerencia o [pipeline de middleware](~/v4sdk/bot-builder-concept-middleware.md#the-bot-middleware-pipeline). Ou seja, os manipuladores são chamados na ordem em que são adicionados e chamar o _próximo_ delegado passa o controle para o próximo manipulador de eventos registrado. Se um manipulador não chamar o próximo delegado, nenhum dos manipuladores de eventos subsequentes serão chamados, o evento [entra em curto](~/v4sdk/bot-builder-concept-middleware.md#short-circuiting) e o adaptador não envia a resposta para o canal.

## <a name="bot-structure"></a>Estrutura do bot

Vamos examinar o bot de eco com o contador [[C#](https://aka.ms/EchoBotWithStateCSharp) | [JS](https://aka.ms/EchoBotWithStateJS)] de exemplo e examinar as principais partes do bot.

# <a name="ctabcs"></a>[C#](#tab/cs)

Um bot é um tipo de aplicativo Web do [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.1). Se examinar os conceitos básicos do [ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/index?view=aspnetcore-2.1&tabs=aspnetcore2x), você verá código semelhante em arquivos como Program.cs e Startup.cs. Esses arquivos são necessários para todos os aplicativos Web e não são específicos do bot. O código em alguns desses arquivos não será copiado aqui, mas você pode consultar a amostra do bot de eco com contador.

### <a name="echowithcounterbotcs"></a>EchoWithCounterBot.cs

A lógica principal do bot é definida na classe `EchoWithCounterBot` que deriva da interface `IBot`. `IBot` define um único método `OnTurnAsync`. Seu aplicativo precisa implementar esse método. `OnTurnAsync` tem turnContext, que fornece informações sobre a atividade de entrada. A atividade de entrada corresponde à solicitação HTTP de entrada. As atividades de entrada podem ser de vários tipos e, portanto, primeiro verificamos se seu bot recebeu uma mensagem. Se for uma mensagem, obteremos o estado de conversa do contexto de turno, incrementaremos o contador de turnos e persistiremos o valor do contador de novo turno para o estado da conversa. Em seguida, enviaremos uma mensagem de volta para o usuário usando a chamada SendActivityAsync. A atividade de saída corresponde à solicitação HTTP de saída.

```cs
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context.
        var oldState = await _accessors.CounterState.GetAsync(turnContext, () => new CounterState());

        // Bump the turn count for this conversation.
        var newState = new CounterState { TurnCount = oldState.TurnCount + 1 };

        // Set the property using the accessor.
        await _accessors.CounterState.SetAsync(turnContext, newState);

        // Save the new turn count into the conversation state.
        await _accessors.ConversationState.SaveChangesAsync(turnContext);

        // Echo back to the user whatever they typed.
        var responseMessage = $"Turn {newState.TurnCount}: You sent '{turnContext.Activity.Text}'\n";
        await turnContext.SendActivityAsync(responseMessage);
    }
    else
    {
        await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
    }
}
```

### <a name="startupcs"></a>Startup.cs

O método `ConfigureServices` carrega os serviços conectados do arquivo [.bot](bot-builder-basics.md#the-bot-file), captura todos os erros que ocorrem durante o turno de uma conversa e os registra em log, configura seu provedor de credenciais e cria um objeto de estado da conversa para armazenar dados de conversa na memória.

```csharp
services.AddBot<EchoWithCounterBot>(options =>
{
    // Creates a logger for the application to use.
    ILogger logger = _loggerFactory.CreateLogger<EchoWithCounterBot>();

    var secretKey = Configuration.GetSection("botFileSecret")?.Value;
    var botFilePath = Configuration.GetSection("botFilePath")?.Value;

    // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
    BotConfiguration botConfig = null;
    try
    {
        botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
    }
    catch
    {
        //...
    }

    services.AddSingleton(sp => botConfig);

    // Retrieve current endpoint.
    var environment = _isProduction ? "production" : "development";
    var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
    if (!(service is EndpointService endpointService))
    {
        throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
    }

    options.CredentialProvider = new SimpleCredentialProvider(endpointService.AppId, endpointService.AppPassword);

    // Catches any errors that occur during a conversation turn and logs them.
    options.OnTurnError = async (context, exception) =>
    {
        logger.LogError($"Exception caught : {exception}");
        await context.SendActivityAsync("Sorry, it looks like something went wrong.");
    };

    // The Memory Storage used here is for local bot debugging only. When the bot
    // is restarted, everything stored in memory will be gone.
    IStorage dataStore = new MemoryStorage();

    // ...

    // Create Conversation State object.
    // The Conversation State object is where we persist anything at the conversation-scope.
    var conversationState = new ConversationState(dataStore);

    options.State.Add(conversationState);
});
```

Ele também cria e registra `EchoBotAccessors` que são definidos no arquivo **EchoBotStateAccessors.cs** e são transmitidos para o construtor `EchoWithCounterBot` público usando a estrutura de injeção de dependência no ASP.NET Core.

```csharp
// Create and register state accessors.
// Accessors created here are passed into the IBot-derived class on every turn.
services.AddSingleton<EchoBotAccessors>(sp =>
{
    var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
    // ...
    var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
    // ...

    // Create the custom state accessor.
    // State accessors enable other components to read and write individual properties of state.
    var accessors = new EchoBotAccessors(conversationState)
    {
        CounterState = conversationState.CreateProperty<CounterState>(EchoBotAccessors.CounterStateName),
    };

    return accessors;
});
```

O método `Configure` termina a configuração do seu aplicativo, especificando que o aplicativo use o Bot Framework e alguns outros arquivos. Todos os bots que usam o Bot Framework precisarão dessa chamada de configuração. `ConfigureServices` e `Configure` são chamados pelo tempo de execução quando o aplicativo é iniciado.

### <a name="counterstatecs"></a>CounterState.cs

Esse arquivo contém uma classe simples que nosso bot usa para manter o estado atual. Ele contém apenas um `int` que usamos para incrementar o contador.

```cs
public class CounterState
{
    public int TurnCount { get; set; } = 0;
}
```

### <a name="echobotaccessorscs"></a>EchoBotAccessors.cs

A classe `EchoBotAccessors` é criada como um singleton na classe `Startup` e transmitida para a classe derivada do IBot. Nesse caso, `public class EchoWithCounterBot : IBot`. O bot usa o acessador para persistir dados da conversa. O construtor de `EchoBotAccessors` é transmitido em um objeto de conversa que é criado no arquivo Startup.cs.

```cs
public class EchoBotAccessors
{
    public EchoBotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    public static string CounterStateName { get; } = $"{nameof(EchoBotAccessors)}.CounterState";

    public IStatePropertyAccessor<CounterState> CounterState { get; set; }

    public ConversationState ConversationState { get; }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

A seção do sistema contém basicamente os arquivos **package.json**, **env**, **index. js** e **README.md**. O código em alguns arquivos não será copiado aqui, mas você o verá ao executar o bot.

### <a name="packagejson"></a>package.json

**package.json** especifica as dependências e suas versões associadas para o bot. Isso é configurado pelo modelo e seu sistema.

### <a name="env-file"></a>arquivo. env

O arquivo **.env** especifica as informações de configuração para o bot, como o número da porta, a ID do aplicativo e a senha, entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para este bot Eco, você não precisa fazer nada aqui no momento; a ID do aplicativo e a senha podem ser deixadas indefinidas por enquanto.

Para usar o arquivo de configuração **.env**, o modelo precisará de um pacote adicional incluído.  Primeiro, obtenha o pacote `dotenv` do npm:

`npm install dotenv`

### <a name="indexjs"></a>index.js

O `index.js` configura o bot e o serviço de hospedagem que encaminhará as atividades para a lógica do bot.

#### <a name="required-libraries"></a>Bibliotecas necessárias

Na parte superior do seu arquivo `index.js`, você encontrará uma série de módulos ou bibliotecas que estão sendo solicitados. Esses módulos oferecerão a você acesso a um conjunto de funções que talvez você queira incluir em seu aplicativo.

```javascript
// Import required packages
const path = require('path');
const restify = require('restify');

// Import required bot services. See https://aka.ms/bot-services to learn more about the different parts of a bot.
const { BotFrameworkAdapter, ConversationState, MemoryStorage } = require('botbuilder');
// Import required bot configuration.
const { BotConfiguration } = require('botframework-config');

const { EchoBot } = require('./bot');

// Read botFilePath and botFileSecret from .env file
// Note: Ensure you have a .env file and include botFilePath and botFileSecret.
const ENV_FILE = path.join(__dirname, '.env');
const env = require('dotenv').config({ path: ENV_FILE });
```

#### <a name="bot-configuration"></a>Configuração de bot

A próxima parte carrega informações de seu arquivo de configuração do bot.

```javascript
// Get the .bot file path
// See https://aka.ms/about-bot-file to learn more about .bot file its use and bot configuration.
const BOT_FILE = path.join(__dirname, (process.env.botFilePath || ''));
let botConfig;
try {
    // Read bot configuration from .bot file.
    botConfig = BotConfiguration.loadSync(BOT_FILE, process.env.botFileSecret);
} catch (err) {
    console.error(`\nError reading bot file. Please ensure you have valid botFilePath and botFileSecret set for your environment.`);
    console.error(`\n - The botFileSecret is available under appsettings for your Azure Bot Service bot.`);
    console.error(`\n - If you are running this bot locally, consider adding a .env file with botFilePath and botFileSecret.`);
    console.error(`\n - See https://aka.ms/about-bot-file to learn more about .bot file its use and bot configuration.\n\n`);
    process.exit();
}

// For local development configuration as defined in .bot file
const DEV_ENVIRONMENT = 'development';

// Define name of the endpoint configuration section from the .bot file
const BOT_CONFIGURATION = (process.env.NODE_ENV || DEV_ENVIRONMENT);

// Get bot endpoint configuration by service name
// Bot configuration as defined in .bot file
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);
```

#### <a name="bot-adapter-http-server-and-bot-state"></a>Adaptador de bot, servidor HTTP e estado do bot

As próximas partes configuram o servidor e o adaptador que permitem que seu bot se comunique com o usuário e envie respostas. O servidor escutará na porta especificada do arquivo de configuração **BotConfiguration.bot** ou fará fallback para _3978_ a fim de estabelecer conexão com o emulador. O adaptador atuará como o condutor para o bot, direcionando a comunicação de entrada e saída, autenticação e assim por diante.

Também criamos um objeto de estado que usa `MemoryStorage` como o provedor de armazenamento. Esse estado é definido como `ConversationState`, o que significa apenas que ele está mantendo o estado da conversa. `ConversationState` armazenará as informações que interessam a você, o que neste caso é simplesmente um contador de turnos, na memória.

```javascript
// Create bot adapter.
// See https://aka.ms/about-bot-adapter to learn more about bot adapter.
const adapter = new BotFrameworkAdapter({
    appId: endpointConfig.appId || process.env.microsoftAppID,
    appPassword: endpointConfig.appPassword || process.env.microsoftAppPassword
});

// Catch-all for any unhandled errors in your bot.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${ error }`);
    // Send a message to the user
    context.sendActivity(`Oops. Something went wrong!`);
    // Clear out state
    await conversationState.clear(context);
    // Save state changes.
    await conversationState.saveChanges(context);
};

// Define a state store for your bot. See https://aka.ms/about-bot-state to learn more about using MemoryStorage.
// A bot requires a state store to persist the dialog and user state between messages.
let conversationState;

// For local development, in-memory storage is used.
// CAUTION: The Memory Storage used here is for local bot debugging only. When the bot
// is restarted, anything stored in memory will be gone.
const memoryStorage = new MemoryStorage();
conversationState = new ConversationState(memoryStorage);

// Create the main dialog.
const bot = new EchoBot(conversationState);

// Create HTTP server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator`);
    console.log(`\nTo talk to your bot, open echoBot-with-counter.bot file in the Emulator`);
});
```

#### <a name="bot-logic"></a>Lógica do bot

O adaptador `processActivity` envia as atividades de entrada para a lógica do bot.
O terceiro parâmetro em `processActivity` é um manipulador de funções que será chamado para executar a lógica do bot depois que a [atividade](#the-activity-processing-stack) recebida tiver sido pré-processada pelo adaptador e roteada por meio de algum middleware. A variável context do turno, passada como um argumento para o manipulador de funções, pode ser usada para fornecer informações sobre a atividade de entrada, o remetente e o destinatário, o canal, a conversa, etc. O processamento de atividade é roteado para o `onTurn` do EchoBot.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        // Route to main dialog.
        await bot.onTurn(context);
    });
});
```

### <a name="echobot"></a>EchoBot

Todo o processamento de atividade é roteado para o manipulador `onTurn` dessa classe. Quando a classe é criada, um objeto de estado é transmitido. Com esse objeto de estado, o construtor cria um acessador `this.countProperty` para persistir o contador de turnos para esse bot.

Em cada turno, verificamos primeiro se o bot recebeu uma mensagem. Se o bot não tiver recebido uma mensagem, repetiremos o tipo de atividade recebido. Em seguida, criamos uma variável de estado que armazena as informações de conversa do seu bot. Se a variável de contagem é `undefined`, ela é definida como 1 (o que ocorrerá quando seu bot for iniciado pela primeira vez) ou aumentada a cada nova mensagem. Enviamos a contagem de volta ao usuário junto com a mensagem enviada por ele. Finalmente, definimos a contagem e salvamos as alterações no estado.

```javascript
const { ActivityTypes } = require('botbuilder');

// Turn counter property
const TURN_COUNTER_PROPERTY = 'turnCounterProperty';

class EchoBot {

    constructor(conversationState) {
        // Creates a new state accessor property.
        // See https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors
        this.countProperty = conversationState.createProperty(TURN_COUNTER_PROPERTY);
        this.conversationState = conversationState;
    }

    async onTurn(turnContext) {
        // Handle message activity type. User's responses via text or speech or card interactions flow back to the bot as Message activity.
        // Message activities may contain text, speech, interactive cards, and binary or unknown attachments.
        // see https://aka.ms/about-bot-activity-message to learn more about the message and other activity types
        if (turnContext.activity.type === ActivityTypes.Message) {
            // read from state.
            let count = await this.countProperty.get(turnContext);
            count = count === undefined ? 1 : ++count;
            await turnContext.sendActivity(`${ count }: You said "${ turnContext.activity.text }"`);
            // increment and set turn counter.
            await this.countProperty.set(turnContext, count);
        } else {
            // Generic handler for all other activity types.
            await turnContext.sendActivity(`[${ turnContext.activity.type } event detected]`);
        }
        // Save state changes
        await this.conversationState.saveChanges(turnContext);
    }
}

exports.EchoBot = EchoBot;
```

---

### <a name="the-bot-file"></a>O arquivo de bot

O arquivo **.bot** contém informações, incluindo o ponto de extremidade, a ID do aplicativo e a senha e as referências aos serviços que são usados pelo bot. Esse arquivo é criado quando você começa a criar um bot a partir de um modelo, mas é possível criar um personalizado por meio do emulador ou de outras ferramentas. Você pode especificar o arquivo .bot a ser usado ao testar o bot com o [emulador](../bot-service-debug-emulator.md).

```json
{
    "name": "echobot-with-counter",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "endpoint": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "id": "1"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o gerenciamento de estado, consulte [Como gerenciar os estados de usuário e conversa](bot-builder-howto-v4-state.md)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um bot](~/bot-service-quickstart.md)
