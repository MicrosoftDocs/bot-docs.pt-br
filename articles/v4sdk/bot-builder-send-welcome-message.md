---
title: Enviar mensagem de boas-vindas aos usuários – Serviço de Bot
description: Saiba como desenvolver seu bot para fornecer uma experiência do usuário calorosa.
keywords: visão geral, desenvolver, experiência do usuário, boas-vindas, experiência personalizada, C#, JS, mensagem de boas-vindas, bot, saudar, saudação
author: DanDev33
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/7/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 92ec5c927baa93095beaf304fe17024adf81666b
ms.sourcegitcommit: e5bf9a7fa7d82802e40df94267bffbac7db48af7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441679"
---
# <a name="send-welcome-message-to-users"></a>Enviar mensagem de boas-vinda aos usuários

[!INCLUDE[applies-to](../includes/applies-to.md)]

Ao criar um bot, o principal objetivo é envolver seu usuário em uma conversa produtiva. Uma das melhores maneiras de fazer isso é garantir que, a partir do momento em que o usuário se conecta pela primeira vez, ele entenda o objetivo e os recursos principais do bot, a razão pela qual seu bot foi criado. Este artigo fornece exemplos de código para ajudá-lo a dar boas-vindas aos usuários em seu bot.

## <a name="prerequisites"></a>Prerequisites

- Entenda sobre as [Noções básicas do bot](bot-builder-basics.md).
- Uma cópia do **Exemplo de boas-vindas ao usuário** em [Exemplo de C#](https://aka.ms/welcome-user-mvc), [Exemplo de JS](https://aka.ms/bot-welcome-sample-js) ou [Exemplo de Python](https://aka.ms/bot-welcome-python-sample-code). O código do exemplo é usado para explicar como enviar mensagens de boas-vindas.

## <a name="about-this-sample-code"></a>Sobre este código de exemplo

Este código de exemplo mostra como detectar e recepcionar novos usuários quando eles são inicialmente conectados ao seu bot. O diagrama a seguir mostra o fluxo lógico desse bot.

### <a name="c"></a>[C#](#tab/csharp)

Os dois principais eventos encontrados pelo bot são:

- `OnMembersAddedAsync`, que é chamado sempre que um novo usuário é conectado ao seu bot
- `OnMessageActivityAsync`, que é chamado sempre que uma nova entrada do usuário é recebida.

![Fluxo lógico da boas-vindas ao usuário](media/welcome-user-flow.png)

Sempre que um novo usuário se conecta, ele recebe `WelcomeMessage`, `InfoMessage` e `PatternMessage` do bot.
Quando uma nova entrada do usuário é recebida, WelcomeUserState é conferido para ver se `DidBotWelcomeUser` está definido como _true_. Se não, uma mensagem inicial de boas-vindas será retornada ao usuário.

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Os dois principais eventos encontrados pelo bot são:

- `onMembersAdded`, que é chamado sempre que um novo usuário é conectado ao seu bot
- `onMessage`, que é chamado sempre que uma nova entrada do usuário é recebida.

![Fluxo lógico da boas-vindas ao usuário](media/welcome-user-flow-js.png)

Sempre que um novo usuário se conecta, ele recebe `welcomeMessage`, `infoMessage` e `patternMessage` do bot.
Quando uma nova entrada do usuário é recebida, `welcomedUserProperty` é conferido para ver se `didBotWelcomeUser` está definido como _true_. Se não, uma mensagem inicial de boas-vindas será retornada ao usuário.
Se `DidBotWelcomeUser` for _true_, a entrada do usuário será avaliada. Com base no conteúdo da entrada do usuário, o bot realizará uma das seguintes ações:

- Retornar uma saudação recebida do usuário.
- Exibir um cartão de Hero com informações adicionais sobre os bots.
- Reenviar o `WelcomeMessage` explicando entradas esperadas para o bot.

### <a name="python"></a>[Python](#tab/python)

Os dois principais eventos encontrados pelo bot são:

- `on_members_added_activity`, que é chamado sempre que um novo usuário é conectado ao seu bot
- `on_message_activity`, que é chamado sempre que uma nova entrada do usuário é recebida.

![Fluxo lógico da boas-vindas ao usuário](media/welcome-user-flow-python.png)

Sempre que um novo usuário se conectar, ele receberá uma *mensagem de boas-vindas*, uma *mensagem de informações* e uma *mensagem padrão* do bot.
Quando uma nova entrada de usuário é recebida, a propriedade `welcome_user_state.did_welcome_user` é verificada para ver se ela está definida como *true*. Se não estiver definido como *true*, uma mensagem inicial de boas-vindas será retornada ao usuário. Se estiver definido como *true*, com base no conteúdo da entrada do usuário, esse bot fará um dos seguintes:

- Retornar uma saudação recebida do usuário.
- Exibir um cartão de Hero com informações adicionais sobre os bots.

---

## <a name="create-user-state"></a>Criar estado do usuário

### <a name="c"></a>[C#](#tab/csharp)

O objeto de estado do usuário é criado na inicialização e a dependência é injetada no construtor de bot.

**Startup.cs** [!code-csharp[define state](~/../botBuilder-samples/samples/csharp_dotnetcore/03.welcome-user/Startup.cs?range=30-34)]

**Bots\WelcomeUserBot.cs** [!code-csharp[consume state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=41-47)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Na inicialização, o estado do usuário é definido em `index.js` e consumido pelo construtor do bot.

**index.js** [!code-javascript[define state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/index.js?range=51-55)]

**bots/welcomeBot.js** [!code-javascript[consume state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomeBot.js?range=16-22)]

### <a name="python"></a>[Python](#tab/python)

Na inicialização, o estado do usuário é definido em `app.py` e consumido pelo construtor do bot.

**app.py** [!code-python[define state](~/../botbuilder-samples/samples/python/03.welcome-user/app.py?range=61-66)]

**bots/welcome-user-bot.py** [!code-python[consume state](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=23-29)]

---

## <a name="create-property-accessors"></a>Criar acessadores de propriedade

### <a name="c"></a>[C#](#tab/csharp)

Agora, criamos um acessador de propriedade que nos fornece um identificador para `WelcomeUserState` dentro do método `OnMessageActivityAsync`.
Em seguida, chame o método `GetAsync` para obter a chave de escopo correta. Em seguida, salvamos os dados de estado do usuário após cada iteração de entrada do usuário, usando o método `SaveChangesAsync`.

**Bots\WelcomeUserBot.cs** [!code-csharp[Get state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-71)]
[!code-csharp[Save state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range= 103-105)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Criamos agora um acessador de propriedade que nos fornece um identificador para welcomedUserProperty que é persistente dentro de userState.

**bots/welcomeBot.js** [!code-javascript[Get state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=24-27)]
[!code-javascript[Save state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=89-97)]

### <a name="python"></a>[Python](#tab/python)

Este exemplo cria um acessador de propriedade de estado de conversa, `user_state_accessor`, no construtor do bot.

**bots/welcome-user-bot.py** [!code-python[constructor](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=31)]

Ele usa o acessador de propriedade no manipulador de `on_message_activity` e substitui o manipulador de `on_turn` para salvar o estado antes do final da rodada.

[!code-python[Get state](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=80-83)]
[!code-python[save state](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=39-43)]

---

## <a name="detect-and-greet-newly-connected-users"></a>Detectar e saudar usuários conectados recentemente

### <a name="c"></a>[C#](#tab/csharp)

Em **WelcomeUserBot**, verificamos se há uma atualização de atividade, usando `OnMembersAddedAsync()` para ver se um novo usuário foi adicionado à conversa e, em seguida, enviamos a ele um conjunto de três mensagens inicias de boas-vindas: `WelcomeMessage`, `InfoMessage` e `PatternMessage`. O código completo dessa interação é mostrado abaixo.

**Bots\WelcomeUserBot.cs** [!code-csharp[Define messages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=20-39)]
[!code-csharp[Send messages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=55-66)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Esse código do JavaScript envia uma mensagem inicial de boas-vindas quando um usuário é adicionado. Isso é feito analisando a atividade da conversa e verificando se um novo membro foi adicionado à conversa.

**bots/welcomeBot.js** [!code-javascript[onMembersAdded](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=63-86)]

### <a name="python"></a>[Python](#tab/python)

O `on_members_added_activity` verifica se um novo usuário foi adicionado e, em seguida, envia três mensagens de boas-vindas iniciais: uma *mensagem de boas-vindas*, uma *mensagem de informações* e uma *mensagem padrão*.

**bots/welcome-user-bot.py** [!code-python[on_members_added_activity](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=45-74)]

---

## <a name="welcome-new-user-and-discard-initial-input"></a>Recepção do novo usuário e descarte da entrada inicial

### <a name="c"></a>[C#](#tab/csharp)

Também é importante considerar quando a entrada do usuário pode realmente conter informações úteis. Isso pode variar para cada canal. Para garantir que o usuário tenha uma boa experiência em todos os canais possíveis, verificamos o sinalizador de status _didBotWelcomeUser_ e, se ele for “false”, não processaremos a entrada inicial do usuário. Em vez disso, forneceremos ao usuário uma mensagem inicial de boas-vindas. O booliano _welcomedUserProperty_ é definido como "true", armazenado em UserState e nosso código agora processará essa entrada do usuário usando todas as atividades adicionais de mensagem.

**Bots\WelcomeUserBot.cs** [!code-csharp[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-84)]
[!code-csharp[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=101-105)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Também é importante considerar quando a entrada do usuário pode realmente conter informações úteis. Isso pode variar para cada canal. Para garantir que o usuário tenha uma boa experiência em todos os canais possíveis, verificamos a propriedade didBotWelcomedUser. Caso ela não exista, definimos como "false" e não processamos a entrada inicial do usuário. Em vez disso, forneceremos ao usuário uma mensagem inicial de boas-vindas. Em seguida, o bool didBotWelcomeUser é definido como "true" e nosso código processa a entrada do usuário de todas as atividades de mensagem adicionais.

**bots/welcomeBot.js** [!code-javascript[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=24-39)]
[!code-javascript[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=57-61)]

### <a name="python"></a>[Python](#tab/python)

Também é importante considerar quando a entrada do usuário pode realmente conter informações úteis. Isso pode variar para cada canal. Para garantir que o usuário tenha uma boa experiência em todos os canais possíveis, `on_message_activity` verifica a propriedade `did_welcome_user`. Na primeira vez, ele o define como *falso* e não processa a entrada do usuário. Em vez disso, ele fornece ao usuário uma mensagem inicial de boas-vindas. Em seguida, ele define `did_welcome_user` como *true* e processa a entrada do usuário de todas as atividades de mensagem adicionais.

**bots/welcome-user-bot.py** [!code-python[DidBotWelcomeUser](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=85-95)]

---

## <a name="process-additional-input"></a>Processar entrada adicional

Após a recepção de um novo usuário, as informações de entrada do usuário são avaliadas a cada turno de mensagem e o bot fornece uma resposta com base no contexto dessa entrada. O código a seguir mostra a lógica de decisão usada para gerar essa resposta.

### <a name="c"></a>[C#](#tab/csharp)

Uma entrada de “intro” ou “help” chama a função `SendIntroCardAsync` para apresentar ao usuário um cartão Hero informativo. Esse código é examinado na próxima seção deste artigo.

**Bots\WelcomeUserBot.cs** [!code-csharp[SwitchOnUtterance](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=85-100)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Uma entrada de”intro” ou “help” usa CardFactory para apresentar ao usuário um cartão adaptável de introdução. Esse código é examinado na próxima seção deste artigo.

**bots/welcomeBot.js** [!code-javascript[SwitchOnUtterance](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=40-56)]

### <a name="python"></a>[Python](#tab/python)

Uma entrada de um usuário de *introdução* ou *ajuda* faz com que o bot chame `__send_intro_card`, que apresenta ao usuário um cartão adaptável introdutório.

**bots/welcome-user-bot.py** [!code-python[SwitchOnUtterance](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=101-106&highlight=97-106)]

---

## <a name="using-hero-card-greeting"></a>Usar a saudação do cartão Hero

Conforme mencionado acima, algumas entradas do usuário geram um *cartão Hero* em resposta à solicitação. Saiba mais sobre as saudações do cartão Hero aqui: [Enviar um cartão de introdução](./bot-builder-howto-add-media-attachments.md). Veja abaixo o código necessário para criar a resposta do cartão Hero do bot.

### <a name="c"></a>[C#](#tab/csharp)

**Bots\WelcomeUserBot.cs** [!code-csharp[SendHeroCardGreeting](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=107-125)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/welcomeBot.js** [!code-javascript[SendIntroCard](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=99-124)]

### <a name="python"></a>[Python](#tab/python)

**bots/welcome-user-bot.py** [!code-python[SendIntroCard](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=108-143)]

---

## <a name="test-the-bot"></a>Testar o bot

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, veja [Exemplo de C#](https://aka.ms/welcome-user-mvc) ou [Exemplo de JS](https://aka.ms/bot-welcome-sample-js) no arquivo LEIAME.
1. Use o emulador para testar o bot, conforme mostrado abaixo.

![test welcome bot sample](media/welcome-user-emulator-1.png)

Testar saudação do cartão Hero.

![test welcome bot card](media/welcome-user-emulator-2.png)

## <a name="additional-resources"></a>Recursos adicionais

Saiba mais sobre várias respostas de mídia em [Adicionar mídia a mensagens](./bot-builder-howto-add-media-attachments.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Coletar a entrada do usuário](bot-builder-prompts.md)
