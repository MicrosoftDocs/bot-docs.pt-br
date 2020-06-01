---
title: Como funcionam os bots – Serviço de Bot
description: Descreve como a atividade e o HTTP funcionam no SDK do Bot Framework.
keywords: fluxo da conversa, turno, conversa de bot, diálogos, prompts, cascatas, conjunto de diálogos
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/31/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: cacd54299444caa5571f08e2aa9eb83d135421d0
ms.sourcegitcommit: eb0e5dec0ecd4e375d33825030b1ba46ff6e032c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791304"
---
# <a name="how-bots-work"></a>Como funcionam os bots

[!INCLUDE[applies-to](../includes/applies-to.md)]

Um bot é um aplicativo com o qual os usuários interagem de maneira conversacional usando texto, gráficos (cartões ou imagens) ou fala. Cada interação entre o usuário e o bot gera uma *atividade*. O Bot Framework Service, que é um componente do Serviço de Bot do Azure, envia informações entre o aplicativo do usuário conectado ao bot (como Facebook, Skype e outros, que chamamos de *canal*) e o bot. Cada canal pode incluir informações adicionais nas atividades que enviam. Antes de criar bots, é importante entender como um bot usa objetos de atividade para se comunicar com seus usuários. Vamos primeiro dar uma olhada nas atividades que são trocadas quando executamos um bot de eco simples.

![diagrama de atividade](media/bot-builder-activity.png)

Dois tipos de atividade ilustrados aqui são: *atualização de conversa* e *mensagem*.

O serviço Bot Framework pode enviar uma atualização de conversa quando um participante ingressa na conversa. Por exemplo, ao iniciar uma conversa com o Bot Framework Emulator, você verá duas atividades de atualização de conversa (uma para o usuário que participa da conversa e outra para o bot que participa da conversa). Para distinguir essas atividades de atualização de conversa, verifique se a propriedade *members added* inclui um membro que não seja o bot.

A atividade de mensagem contém informações de conversa entre as partes. Em um exemplo de bot de eco, as atividades de mensagem transmitem texto simples e o canal renderiza esse texto. Como alternativa, a atividade de mensagem pode conter o texto a ser falado, as ações sugeridas ou os cartões a serem exibidos.

Neste exemplo, o bot criou e enviou uma atividade de mensagem em resposta à atividade de mensagem de entrada que ele recebeu. No entanto, um bot pode responder de outras maneiras a uma atividade de mensagem recebida. Não é incomum para um bot responder a uma atividade de atualização de conversa enviando um texto de boas-vindas em uma atividade de mensagem. Mais informações podem ser encontradas nas [boas-vindas ao usuário](bot-builder-welcome-user.md).

### <a name="http-details"></a>Detalhes do HTTP

As atividades chegam ao bot do serviço Bot Framework por meio de uma solicitação HTTP POST. O bot responde à solicitação POST de entrada com um código de status HTTP 200. As atividades enviadas do bot para o canal são enviadas em uma HTTP POST separada para o serviço Bot Framework. Isso, por sua vez, é confirmado com um código de status HTTP 200.

O protocolo não especifica a ordem em que as solicitações POST e suas confirmações são feitas. No entanto, para se ajustar a estruturas de serviço HTTP comuns, essas solicitações normalmente são aninhadas, o que significa que a solicitação HTTP de saída é feita do bot dentro do escopo da solicitação HTTP de entrada. Esse padrão é ilustrado no diagrama acima. Como há duas conexões HTTP distintas de ponta a ponta, o modelo de segurança deve servir a ambas.

### <a name="defining-a-turn"></a>Definir um turno

Em uma conversa, as pessoas geralmente falam uma de cada vez, alternando a vez de falar. Com um bot, geralmente ele reage à entrada do usuário. Dentro do SDK do Bot Framework, um _turno_ é a atividade de entrada do usuário para o bot e qualquer atividade que o bot envia de volta para o usuário como uma resposta imediata. Você pode considerar um turno como o processamento associado à chegada de uma determinada atividade.

O objeto *contexto de turno* fornece informações sobre a atividade, como o remetente e o receptor, o canal e outros dados necessários para processar a atividade. Ele também permite a adição de informações durante o turno em várias camadas do bot.

O contexto de turno é uma das abstrações mais importantes no SDK. Não só ele transmite a atividade de entrada para todos os componentes de middleware e a lógica do aplicativo, mas também fornece o mecanismo pelo qual os componentes de middleware e a lógica do aplicativo podem enviar atividades de saída.

## <a name="the-activity-processing-stack"></a>A pilha de processamento de atividade

Vamos analisar o diagrama anterior, com foco na chegada de uma atividade de mensagem.

![pilha de processamento de atividade](media/bot-builder-activity-processing-stack.png)

No exemplo acima, o bot respondeu à atividade de mensagem com outra atividade de mensagem contendo a mesma mensagem de texto. O processamento começa com a solicitação HTTP POST, com as informações de atividade transmitidas como uma carga JSON, chegando ao servidor Web. Em C#, isso geralmente será um projeto do ASP.NET e, em um projeto Node.js do JavaScript, isso costuma ser uma das estruturas populares, como Express ou Restify.

O *adaptador*, um componente integrado do SDK, é o núcleo do runtime do SDK. A atividade é executada como JSON no corpo HTTP POST. Este JSON é desserializado para criar o objeto Atividade, que é entregue ao adaptador com uma chamada para o método *processar atividade*. Ao receber a atividade, o adaptador cria um *contexto de turno* e chama o middleware.

Conforme mencionado acima, o contexto de turno fornece o mecanismo para que o bot envie atividades de saída, geralmente em resposta a uma atividade de entrada. Para isso, o contexto de turno fornece métodos de resposta _enviar, atualizar e excluir atividade_. Cada método de resposta é executado em um processo assíncrono.

[!INCLUDE [alert-await-send-activity](../includes/alert-await-send-activity.md)]

## <a name="activity-handlers"></a>Manipuladores de atividade

Quando o bot recebe uma atividade, ele a transmite a seus *manipuladores de atividade*. Nos bastidores, há um manipulador base chamado de *manipulador de turno*. Todas as atividades são encaminhadas por ele. Esse manipulador de turno chama o manipulador de atividade específico para qualquer tipo de atividade recebida.

# <a name="c"></a>[C#](#tab/csharp)

Por exemplo, se o bot recebe uma atividade de mensagem, o manipulador de turno vê a atividade de entrada a envia para o manipulador de atividade `OnMessageActivityAsync`.

Ao criar seu bot, sua lógica do bot para tratar das mensagens e respondê-las entrará nesse manipulador `OnMessageActivityAsync`. Da mesma forma, sua lógica de tratamento de membros que estão sendo adicionados à conversa vai entrar no manipulador `OnMembersAddedAsync`, que é chamado sempre que um membro é adicionado à conversa.

Para implementar a lógica desses manipuladores, você substituirá esses métodos no seu bot como visto na seção [Lógica do bot](#bot-logic) abaixo. Para cada um desses manipuladores, não há nenhuma implementação base e, portanto, basta adicionar a lógica desejada na substituição.

Há algumas situações em que é melhor substituir o manipulador de turno base, por exemplo, ao [salvar o estado](bot-builder-concept-state.md) no final de um turno. Ao fazer isso, chame primeiro `await base.OnTurnAsync(turnContext, cancellationToken);` para fazer com que a implementação base de `OnTurnAsync` seja executada antes do código adicional. A implementação base é, entre outras coisas, responsável por chamar o resto dos manipuladores de atividade, como `OnMessageActivityAsync`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript `ActivityHandler` usa um emissor de eventos e um padrão de ouvinte.
Por exemplo, use o método `onMessage` para registrar um ouvinte de eventos para atividades de mensagem. Você pode registrar mais de um ouvinte. Quando o bot recebe uma atividade de mensagem, o manipulador de atividades vê essa atividade de entrada e a envia a cada um dos ouvintes de atividade `onMessage`, na ordem em que elas foram registradas.

Ao criar seu bot, a lógica do bot para tratar das mensagens e respondê-las entrará nos ouvintes `onMessage`. Da mesma forma, a lógica de tratamento de membros que estão sendo adicionados à conversa vai entrar nos ouvintes `onMembersAdded`, que são chamados sempre que um membro é adicionado à conversa.
Para adicionar esses ouvintes, você os registrará em seu bot, como visto na seção [Lógica de bot](#bot-logic) abaixo. Para cada ouvinte, inclua a lógica de bot e, em seguida, **não se esqueça de chamar `next()` no final**. Ao chamar `next()`, você faz com que o próximo ouvinte seja executado.

Lembre-se de [salvar o estado](bot-builder-concept-state.md) antes que o ciclo seja concluído. Você pode fazer isso substituindo o método `run` do manipulador de atividade e salvando o estado após a conclusão do método `run` do pai.

Não existem situações comuns de substituição do manipulador de turno base, ou seja, tenha cuidado ao tentar fazer isso.
Há um manipulador especial chamado `onDialog`. O manipulador `onDialog` é executado no final, depois que o restante dos manipuladores foi executado, e não está vinculado a nenhum tipo de atividade. Assim como com todos os manipuladores acima, não deixe de chamar `next()` para garantir a conclusão do processo.

# <a name="python"></a>[Python](#tab/python)

Por exemplo, se o bot recebe uma atividade de mensagem, o manipulador de turno vê a atividade de entrada a envia para o manipulador de atividade `on_message_activity`.

Ao criar seu bot, sua lógica do bot para tratar das mensagens e respondê-las entrará nesse manipulador `on_message_activity`. Da mesma forma, sua lógica de tratamento de membros que estão sendo adicionados à conversa vai entrar no manipulador `on_members_added`, que é chamado sempre que um membro é adicionado à conversa.

Para implementar a lógica desses manipuladores, você substituirá esses métodos no seu bot como visto na seção [Lógica do bot](#bot-logic) abaixo. Para cada um desses manipuladores, não há nenhuma implementação base e, portanto, basta adicionar a lógica desejada na substituição.

Há algumas situações em que é melhor substituir o manipulador de turno base, por exemplo, ao [salvar o estado](bot-builder-concept-state.md) no final de um turno. Ao fazer isso, chame primeiro `await super().on_turn(turnContext);` para fazer com que a implementação base de `on_turn` seja executada antes do código adicional. A implementação base é, entre outras coisas, responsável por chamar o resto dos manipuladores de atividade, como `on_message_activity`.

---

## <a name="middleware"></a>Middleware

O Middleware é muito parecido com qualquer outro middleware de mensagens, compreendendo um conjunto linear de componentes que são executados em ordem, dando a cada um a chance de operar na atividade. O estágio final do pipeline de middleware é um retorno de chamada para o manipulador de turno na classe do bot que o aplicativo registrou com o método *processar atividade* do adaptador. O manipulador de turno é geralmente `OnTurnAsync` em C# e `onTurn` em JavaScript.

O manipulador de turnos recebe um contexto de turno como seu argumento, normalmente a lógica do aplicativo em execução na função do manipulador de turnos processará o conteúdo da atividade de entrada e gerará uma ou mais atividades em resposta, enviando-as usando a função *enviar atividade* no contexto do turno. Chamar *enviar atividade* no contexto de turno faz com que os componentes de middleware sejam invocados nas atividades de saída. Os componentes de middleware são executados antes e depois da função do manipulador de turno do bot. A execução é aninhada de forma inerente e, por isso, às vezes é chamada de Boneca Russa. Para obter mais informações detalhadas sobre o middleware, consulte o [tópico sobre middleware](~/v4sdk/bot-builder-concept-middleware.md).

## <a name="bot-structure"></a>Estrutura do bot

Nas seções a seguir, examinaremos as _partes essenciais_ de um EchoBot que você pode criar facilmente usando os modelos fornecidos para [**CSharp**](../dotnet/bot-builder-dotnet-sdk-quickstart.md) ou [**JavaScript**](../javascript/bot-builder-javascript-quickstart.md) ou [**Python**](../python/bot-builder-python-quickstart.md).

<!--Need to add section calling out the controller in code, and explaining it further-->

Um bot é um aplicativo Web, e modelos são fornecidos para cada linguagem.

[!INCLUDE [VSIX templates](~/includes/vsix-templates-versions.md)]

# <a name="c"></a>[C#](#tab/csharp)

O modelo VSIX gera um aplicativo Web [ASP.NET MVC Core](https://dotnet.microsoft.com/apps/aspnet/mvc). Se você examinar os conceitos básicos do [ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/index?view=aspnetcore-2.1&tabs=aspnetcore2x), verá um código semelhante em arquivos como **Program.cs** e **Startup.cs**. Esses arquivos são necessários para todos os aplicativos Web e não são específicos do bot.

### <a name="appsettingsjson-file"></a>Arquivo appsettings.json

O arquivo **appsettings.json** especifica as informações de configuração para o bot, como a ID do aplicativo e a senha, entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para este bot Eco, você não precisa fazer nada aqui no momento; a ID do aplicativo e a senha podem ser deixadas indefinidas por enquanto.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!-- TODO: Update this aka link to point to samples/javascript_nodejs/02.echobot (instead of samples/javascript_nodejs/02.a.echobot) once work-in-progress is merged into master. -->

O gerador Yeoman cria um tipo de aplicativo Web [restify](http://restify.com/). Se você examinar o início rápido do restify nos documentos, verá um aplicativo semelhante ao arquivo gerado **index.js**. Descrevemos alguns dos principais arquivos gerados pelo modelo. Em alguns arquivos, o código não será copiado, mas você o verá quando executar o bot; é possível consultar o exemplo [echobot Node.js](https://aka.ms/js-echobot-sample).

### <a name="packagejson"></a>package.json

**package.json** especifica as dependências e suas versões associadas para o bot. Isso é configurado pelo modelo e seu sistema.

### <a name="env-file"></a>arquivo. env

O arquivo **.env** especifica as informações de configuração para o bot, como o número da porta, a ID do aplicativo e a senha, entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para este bot Eco, você não precisa fazer nada aqui no momento; a ID do aplicativo e a senha podem ser deixadas indefinidas por enquanto.

Para usar o arquivo de configuração **.env**, o modelo precisará de um pacote adicional incluído.  Primeiro, obtenha o pacote `dotenv` do npm:

`npm install dotenv`

# <a name="python"></a>[Python](#tab/python)

### <a name="requirementstxt"></a>requirements.txt

**requirements.txt** especifica as dependências e suas versões associadas para o bot.  Tudo isso é configurado pelo modelo e seu sistema.

As dependências devem ser instaladas usando `pip install -r requirements.txt`

### <a name="configpy"></a>config.py

O arquivo **config.py** especifica as informações de configuração do bot, como o número da porta, a ID do aplicativo e a senha, entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para este bot Eco, você não precisa fazer nada aqui no momento; a ID do aplicativo e a senha podem ser deixadas indefinidas por enquanto.

---

### <a name="bot-logic"></a>Lógica do bot

A lógica do bot processa as atividades de entrada de um ou mais canais e gera atividades de saída em resposta.

# <a name="c"></a>[C#](#tab/csharp)

A lógica principal do bot é definida no código do bot; aqui, ele se chama `Bots/EchoBot.cs`. `EchoBot` deriva de `ActivityHandler`, que por sua vez deriva da interface `IBot`. `ActivityHandler` define vários manipuladores para diferentes tipos de atividades, como os dois definidos aqui: `OnMessageActivityAsync` e `OnMembersAddedAsync`. Esses métodos são protegidos, mas podem ser substituídos, já que estamos derivando de `ActivityHandler`.

Os manipuladores definidos em `ActivityHandler` são:

| Evento | Manipulador | Descrição |
| :-- | :-- | :-- |
| Qualquer tipo de atividade recebido | `OnTurnAsync` | Chama um dos outros manipuladores com base no tipo de atividade recebido. |
| Atividade de mensagem recebida | `OnMessageActivityAsync` | Substitua-o para lidar com uma atividade `message`. |
| Atividade de atualização de conversa recebida | `OnConversationUpdateActivityAsync` | Em uma atividade `conversationUpdate`, chama um manipulador se algum membro que não seja o bot ingressa na conversa ou sai dela. |
| Membros que não são bot ingressaram na conversa | `OnMembersAddedAsync` | Substitua-o para lidar com membros que ingressam em uma conversa. |
| Membros que não são bot saíram da conversa | `OnMembersRemovedAsync` | Substitua-o para lidar com membros que saem de uma conversa. |
| Atividade de evento recebida | `OnEventActivityAsync` | Em uma atividade `event`, chama um manipulador específico ao tipo de evento. |
| Atividade de evento de resposta de token recebida | `OnTokenResponseEventAsync` | Substitua-o para manipular eventos de resposta de token. |
| Atividade de evento de resposta não token recebida | `OnEventAsync` | Substitua-o para lidar com outros tipos de eventos. |
| Atividade de reação de mensagem recebida | `OnMessageReactionActivityAsync` | Em uma atividade `messageReaction`, chamará um manipulador se uma ou mais reações forem adicionadas ou removidas de uma mensagem. |
| Reações de mensagem adicionadas a uma mensagem | `OnReactionsAddedAsync` | Substitua isso para manipular reações adicionadas a uma mensagem. |
| Reações de mensagem removidas de uma mensagem | `OnReactionsRemovedAsync` | Substitua isso para manipular reações removidas de uma mensagem. |
| Outro tipo de atividade recebido | `OnUnrecognizedActivityTypeAsync` | Substitua-o para lidar com qualquer tipo de atividade sem tratamento. |

Esses manipuladores diferentes têm um `turnContext` que fornece informações sobre a atividade de entrada, que corresponde à solicitação HTTP de entrada. As atividades podem ser de vários tipos e, portanto, cada manipulador fornece uma atividade fortemente tipada em seu parâmetro de contexto de turno; na maioria dos casos, `OnMessageActivityAsync` sempre será manipulado e é geralmente o mais comum.

Como nas versões anteriores de 4.x dessa estrutura, também há a opção de implementar o método público `OnTurnAsync`. Atualmente, a implementação base desse método lida com a verificação de erros e, em seguida, chama cada um dos manipuladores específicos (como os dois que definimos neste exemplo) dependendo do tipo de atividade de entrada. Na maioria dos casos, você pode deixar esse método de lado e usar manipuladores individuais, mas se a situação exige uma implementação personalizada de `OnTurnAsync`, ele ainda é uma opção.

> [!IMPORTANT]
> Se você substituir o método `OnTurnAsync`, precisará chamar `base.OnTurnAsync` para obter a implementação base e chamar todos os outros manipuladores `On<activity>Async`, ou chamar esses manipuladores por conta própria. Caso contrário, esses manipuladores não serão chamados e o código não será executado.

No exemplo, estamos recebendo um novo usuário ou ecoando a mensagem que o usuário enviou usando a chamada `SendActivityAsync`. A atividade de saída corresponde à solicitação HTTP POST de saída.

```cs
public class MyBot : ActivityHandler
{
    protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
    {
        await turnContext.SendActivityAsync(MessageFactory.Text($"Echo: {turnContext.Activity.Text}"), cancellationToken);
    }

    protected override async Task OnMembersAddedAsync(IList<ChannelAccount> membersAdded, ITurnContext<IConversationUpdateActivity> turnContext, CancellationToken cancellationToken)
    {
        foreach (var member in membersAdded)
        {
            await turnContext.SendActivityAsync(MessageFactory.Text($"welcome {member.Name}"), cancellationToken);
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A lógica principal do bot é definida no código do bot; aqui, ele se chama `bots\echoBot.js`. `EchoBot` deriva de `ActivityHandler`. `ActivityHandler` define vários eventos para diferentes tipos de atividades e você pode modificar o comportamento do bot registrando ouvintes de eventos, como ocorre com `onMessage` e `onConversationUpdate` aqui.

Use estes métodos para registrar os ouvintes para cada tipo de evento:

| Evento | Método de registro | Descrição |
| :-- | :-- | :-- |
| Qualquer tipo de atividade recebido | `onTurn` | Registra um ouvinte para quando qualquer atividade for recebida. |
| Atividade de mensagem recebida | `onMessage` | Registra um ouvinte para quando uma atividade `message` for recebida. |
| Atividade de atualização de conversa recebida | `onConversationUpdate` | Registra um ouvinte para quando qualquer atividade `conversationUpdate` for recebida. |
| Membros ingressaram na conversa | `onMembersAdded` | Registra um ouvinte para quando os membros ingressarem na conversa, incluindo o bot. |
| Membros saíram da conversa | `onMembersRemoved` | Registra um ouvinte para quando os membros saírem da conversa, incluindo o bot. |
| Atividade de reação de mensagem recebida | `onMessageReaction` | Registra um ouvinte para quando qualquer atividade `messageReaction` for recebida. |
| Reações de mensagem adicionadas a uma mensagem | `onReactionsAdded` | Registra um ouvinte para quando as reações forem adicionadas a uma mensagem. |
| Reações de mensagem removidas de uma mensagem | `onReactionsRemoved` | Registra um ouvinte para quando as reações forem removidas de uma mensagem. |
| Atividade de evento recebida | `onEvent` | Registra um ouvinte para quando qualquer atividade `event` for recebida. |
| Atividade de evento de resposta de token recebida | `onTokenResponseEvent` | Registra um ouvinte para quando um evento `tokens/response` for recebido. |
| Outro tipo de atividade recebido | `onUnrecognizedActivityType` | Registra um ouvinte para quando um manipulador para o tipo específico de atividade não estiver definido. |
| Manipuladores de atividade concluídos | `onDialog` | Chamado após a conclusão de qualquer manipulador aplicável. |

Chame a função de continuação `next` de cada manipulador para permitir que o processamento continue. Se `next` não for chamado, o processamento da atividade terminará.

Por exemplo, esse bot registra ouvintes para mensagens e atualizações de conversa. Quando ele recebe uma mensagem do usuário, ele ecoa de volta a mensagem que o usuário enviou.

```javascript
const { ActivityHandler } = require('botbuilder');

class MyBot extends ActivityHandler {
    constructor() {
        super();
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        this.onMessage(async (context, next) => {
            await context.sendActivity(`You said '${ context.activity.text }'`);
            // By calling next() you ensure that the next BotHandler is run.
            await next();
        });
        this.onConversationUpdate(async (context, next) => {
            await context.sendActivity('[conversationUpdate event detected]');
            // By calling next() you ensure that the next BotHandler is run.
            await next();
        });
    }
}

module.exports.MyBot = MyBot;
```

# <a name="python"></a>[Python](#tab/python)

A lógica principal do bot é definida no código do bot; aqui, ele se chama `bots/echo_bot.py`. `EchoBot` deriva de `ActivityHandler`, que por sua vez deriva da interface `Bot`. `ActivityHandler` define vários manipuladores para diferentes tipos de atividades, como os dois definidos aqui: `on_message_activity` e `on_members_added`. Esses métodos são protegidos, mas podem ser substituídos, já que estamos derivando de `ActivityHandler`.

Os manipuladores definidos em `ActivityHandler` são:

| Evento | Manipulador | Descrição |
| :-- | :-- | :-- |
| Qualquer tipo de atividade recebido | `on_turn` | Chama um dos outros manipuladores com base no tipo de atividade recebido. |
| Atividade de mensagem recebida | `on_message_activity` | Substitua-o para lidar com uma atividade `message`. |
| Atividade de atualização de conversa recebida | `on_conversation_update_activity` | Em uma atividade `conversationUpdate`, chama um manipulador se algum membro que não seja o bot ingressa na conversa ou sai dela. |
| Membros que não são bot ingressaram na conversa | `on_members_added_activity` | Substitua-o para lidar com membros que ingressam em uma conversa. |
| Membros que não são bot saíram da conversa | `on_members_removed_activity` | Substitua-o para lidar com membros que saem de uma conversa. |
| Atividade de evento recebida | `on_event_activity` | Em uma atividade `event`, chama um manipulador específico ao tipo de evento. |
| Atividade de evento de resposta de token recebida | `on_token_response_event` | Substitua-o para manipular eventos de resposta de token. |
| Atividade de evento de resposta não token recebida | `on_event_activity` | Substitua-o para lidar com outros tipos de eventos. |
| Atividade de reação de mensagem recebida | `on_message_reaction_activity` | Em uma atividade `messageReaction`, chamará um manipulador se uma ou mais reações forem adicionadas ou removidas de uma mensagem. |
| Reações de mensagem adicionadas a uma mensagem | `on_reactions_added` | Substitua isso para manipular reações adicionadas a uma mensagem. |
| Reações de mensagem removidas de uma mensagem | `on_reactions_removed` | Substitua isso para manipular reações removidas de uma mensagem. |
| Outro tipo de atividade recebido | `on_unrecognized_activity_type` | Substitua-o para lidar com qualquer tipo de atividade sem tratamento. |

Esses manipuladores diferentes têm um `turn_context` que fornece informações sobre a atividade de entrada, que corresponde à solicitação HTTP de entrada. As atividades podem ser de vários tipos e, portanto, cada manipulador fornece uma atividade fortemente tipada em seu parâmetro de contexto de turno; na maioria dos casos, `on_message_activity` sempre será manipulado e é geralmente o mais comum.

Como nas versões anteriores de 4.x dessa estrutura, também há a opção de implementar o método público `on_turn`. Atualmente, a implementação base desse método lida com a verificação de erros e, em seguida, chama cada um dos manipuladores específicos (como os dois que definimos neste exemplo) dependendo do tipo de atividade de entrada. Na maioria dos casos, você pode deixar esse método de lado e usar manipuladores individuais, mas se a situação exige uma implementação personalizada de `on_turn`, ele ainda é uma opção.

> [!IMPORTANT]
> Se você substituir o método `on_turn`, precisará chamar `super().on_turn` para obter a implementação base e chamar todos os outros manipuladores `on_<activity>`, ou chamar esses manipuladores por conta própria. Caso contrário, esses manipuladores não serão chamados e o código não será executado.

No exemplo, estamos recebendo um novo usuário ou ecoando a mensagem que o usuário enviou usando a chamada `send_activity`. A atividade de saída corresponde à solicitação HTTP POST de saída.

```py
class MyBot(ActivityHandler):
    async def on_members_added_activity(
        self, members_added: [ChannelAccount], turn_context: TurnContext
    ):
        for member in members_added:
            if member.id != turn_context.activity.recipient.id:
                await turn_context.send_activity("Hello and welcome!")

    async def on_message_activity(self, turn_context: TurnContext):
        return await turn_context.send_activity(
            f"Echo: {turn_context.activity.text}"
        )
```

---

### <a name="access-the-bot-from-your-app"></a>Acessar o bot no seu aplicativo

# <a name="c"></a>[C#](#tab/csharp)

#### <a name="set-up-services"></a>Configurar serviços

O método `ConfigureServices` no arquivo `Startup.cs` carrega os serviços conectados e suas chaves de `appsettings.json` ou do Azure Key Vault (se houver), conecta o estado e assim por diante. Aqui estamos adicionando MVC e definindo a versão de compatibilidade em nossos serviços, configurando o adaptador e o bot para ficarem disponíveis para o controlador de bot por meio de injeção de dependência.

<!-- want to explain the singleton vs transient here?-->

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the credential provider to be used with the Bot Framework Adapter.
    services.AddSingleton<ICredentialProvider, ConfigurationCredentialProvider>();

    // Create the Bot Framework Adapter.
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkHttpAdapter>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

O método `Configure` termina a configuração do aplicativo especificando que ele deve usar MVC e alguns outros arquivos. Todos os bots usando o Bot Framework precisarão dessa chamada de configuração; no entanto, ela já estará definida em exemplos ou no modelo VSIX quando você compilar seu bot. `ConfigureServices` e `Configure` são chamados pelo runtime quando o aplicativo é iniciado.

#### <a name="bot-controller"></a>Controlador de Bot

O controlador, seguindo a estrutura MVC padrão, permite que você determine o encaminhamento de mensagens e solicitações HTTP POST. Para nosso bot, transmitimos a solicitação de entrada para o método *processar atividade assíncrona* conforme explicado na seção de [pilha de processamento de atividade](#the-activity-processing-stack) acima. Nessa chamada, especificamos o bot e outras informações de autorização que possam ser necessárias.

O controlador implementa `ControllerBase`, contém o adaptador e o bot que definimos em `Startup.cs` (que estão disponíveis aqui por meio da injeção de dependência) e transmite as informações necessárias ao bot quando ele recebe uma entrada HTTP POST.

Aqui, você verá a classe precedida de atributos de rota e controlador. Eles ajudam a estrutura encaminhar as mensagens adequadamente e saber qual controlador deve ser usado. Se você alterar o valor no atributo de rota, alterará o ponto de extremidade que o emulador ou outros canais usam para acessar seu bot.

```cs
// This ASP Controller is created to handle a request. Dependency Injection will provide the Adapter and IBot
// implementation at runtime. Multiple different IBot implementations running at different endpoints can be
// achieved by specifying a more specific type for the bot constructor argument.
[Route("api/messages")]
[ApiController]
public class BotController : ControllerBase
{
    private readonly IBotFrameworkHttpAdapter Adapter;
    private readonly IBot Bot;

    public BotController(IBotFrameworkHttpAdapter adapter, IBot bot)
    {
        Adapter = adapter;
        Bot = bot;
    }

    [HttpPost]
    public async Task PostAsync()
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await Adapter.ProcessAsync(Request, Response, Bot);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="indexjs"></a>index.js

O `index.js` configura o bot e o serviço de hospedagem que encaminhará as atividades para a lógica do bot.

#### <a name="required-libraries"></a>Bibliotecas necessárias

Na parte superior do seu arquivo `index.js`, você encontrará uma série de módulos ou bibliotecas que estão sendo solicitados. Esses módulos oferecerão a você acesso a um conjunto de funções que talvez você queira incluir em seu aplicativo.

```javascript
const dotenv = require('dotenv');
const path = require('path');
const restify = require('restify');

// Import required bot services.
// See https://aka.ms/bot-services to learn more about the different parts of a bot.
const { BotFrameworkAdapter } = require('botbuilder');

// This bot's main dialog.
const { MyBot } = require('./bot');

// Import required bot configuration.
const ENV_FILE = path.join(__dirname, '.env');
dotenv.config({ path: ENV_FILE });
```

#### <a name="set-up-services"></a>Configurar serviços

As próximas partes configuram o servidor e o adaptador que permitem que seu bot se comunique com o usuário e envie respostas. O servidor escutará na porta especificada do arquivo de configuração ou fará fall-back para _3978_ para estabelecer conexão com o emulador. O adaptador atuará como o condutor para o bot, direcionando a comunicação de entrada e saída, autenticação e assim por diante.

```javascript
// Create HTTP server
const server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, () => {
    console.log(`\n${ server.name } listening to ${ server.url }`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/bot-framework-www-portal-emulator`);
    console.log(`\nTo talk to your bot, open the emulator select "Open Bot"`);
});

// Create adapter.
// See https://aka.ms/about-bot-adapter to learn more about how bots work.
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

// Catch-all for errors.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${ error }`);
    // Send a message to the user
    await context.sendActivity(`Oops. Something went wrong!`);
};

// Create the main dialog.
const myBot = new MyBot();
```

#### <a name="forwarding-requests-to-the-bot-logic"></a>Encaminhando solicitações para a lógica do bot

O adaptador `processActivity` envia as atividades de entrada para a lógica do bot.
O terceiro parâmetro em `processActivity` é um manipulador de funções que é chamado para executar a lógica do bot depois que a [atividade](#the-activity-processing-stack) recebida foi pré-processada pelo adaptador e roteada por meio de algum middleware. A variável context do turno, passada como um argumento para o manipulador de funções, pode ser usada para fornecer informações sobre a atividade de entrada, o remetente e o destinatário, o canal, a conversa, etc. O processamento de atividade é roteado para o método `run` do bot. `run` é definido em `ActivityHandler`; ele executa verificação de erros e, em seguida, chama os manipuladores de eventos do bot com base no tipo de atividade recebida.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        // Route to main dialog.
        await myBot.run(context);
    });
});
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="apppy"></a>app.py

O `app.py` configura o bot e o serviço de hospedagem que encaminhará as atividades para a lógica do bot.

#### <a name="required-libraries"></a>Bibliotecas necessárias

Na parte superior do seu arquivo `app.py`, você encontrará uma série de módulos ou bibliotecas que estão sendo solicitados. Esses módulos oferecerão a você acesso a um conjunto de funções que talvez você queira incluir em seu aplicativo.

```py
from botbuilder.core import BotFrameworkAdapterSettings, TurnContext, BotFrameworkAdapter
from botbuilder.schema import Activity, ActivityTypes

from bots import MyBot

# Create the loop and Flask app
LOOP = asyncio.get_event_loop()
app = Flask(__name__, instance_relative_config=True)
app.config.from_object("config.DefaultConfig")
```

#### <a name="set-up-services"></a>Configurar serviços

As próximas partes configuram o servidor e o adaptador que permitem que seu bot se comunique com o usuário e envie respostas. O servidor escutará na porta especificada do arquivo de configuração ou fará fall-back para _3978_ para estabelecer conexão com o emulador. O adaptador atuará como o condutor para o bot, direcionando a comunicação de entrada e saída, autenticação e assim por diante.

```py
# Create adapter.
# See https://aka.ms/about-bot-adapter to learn more about how bots work.
SETTINGS = BotFrameworkAdapterSettings(app.config["APP_ID"], app.config["APP_PASSWORD"])
ADAPTER = BotFrameworkAdapter(SETTINGS)

# Catch-all for errors.
async def on_error(context: TurnContext, error: Exception):
    # This check writes out errors to console log .vs. app insights.
    # NOTE: In production environment, you should consider logging this to Azure
    #       application insights.
    print(f"\n [on_turn_error] unhandled error: {error}", file=sys.stderr)

    # Send a message to the user
    await context.send_activity("The bot encountered an error or bug.")
    await context.send_activity("To continue to run this bot, please fix the bot source code.")
    # Send a trace activity if we're talking to the Bot Framework Emulator
    if context.activity.channel_id == 'emulator':
        # Create a trace activity that contains the error object
        trace_activity = Activity(
            label="TurnError",
            name="on_turn_error Trace",
            timestamp=datetime.utcnow(),
            type=ActivityTypes.trace,
            value=f"{error}",
            value_type="https://www.botframework.com/schemas/error"
        )
        # Send a trace activity, which will be displayed in Bot Framework Emulator
        await context.send_activity(trace_activity)

ADAPTER.on_turn_error = on_error

# Create the Bot
BOT = MyBot()
```

#### <a name="forwarding-requests-to-the-bot-logic"></a>Encaminhando solicitações para a lógica do bot

O adaptador `process_activity` envia as atividades de entrada para a lógica do bot.
O terceiro parâmetro em `process_activity` é um manipulador de funções que é chamado para executar a lógica do bot depois que a [atividade](#the-activity-processing-stack) recebida foi pré-processada pelo adaptador e roteada por meio de algum middleware. A variável context do turno, passada como um argumento para o manipulador de funções, pode ser usada para fornecer informações sobre a atividade de entrada, o remetente e o destinatário, o canal, a conversa, etc. O processamento de atividade é roteado para o método `on_turn` do bot. `on_turn` é definido em `ActivityHandler`; ele executa verificação de erros e, em seguida, chama os manipuladores de eventos do bot com base no tipo de atividade recebida.

```py
# Listen for incoming requests on /api/messages
@app.route("/api/messages", methods=["POST"])
def messages():
    # Main bot message handler.
    if "application/json" in request.headers["Content-Type"]:
        body = request.json
    else:
        return Response(status=415)

    activity = Activity().deserialize(body)
    auth_header = (
        request.headers["Authorization"] if "Authorization" in request.headers else ""
    )

    try:
        task = LOOP.create_task(
            ADAPTER.process_activity(activity, auth_header, BOT.on_turn)
        )
        LOOP.run_until_complete(task)
        return Response(status=201)
    except Exception as exception:
        raise exception
```

---

## <a name="manage-bot-resources"></a>Gerenciar recursos do bot

Os recursos de bot, como ID do aplicativo, senhas, chaves ou segredos para serviços conectados, precisarão ser gerenciados adequadamente. Para obter mais informações sobre como fazer isso, confira [Gerenciar recursos de bot](bot-file-basics.md).

## <a name="additional-resources"></a>Recursos adicionais

- Para entender a função do estado dos bots, consulte [gerenciar o estado](bot-builder-concept-state.md).

- Para entender os principais conceitos de desenvolvimento de bots para o Microsoft Teams, confira [Como os bots do Microsoft Teams funcionam](bot-builder-basics-teams.md)
