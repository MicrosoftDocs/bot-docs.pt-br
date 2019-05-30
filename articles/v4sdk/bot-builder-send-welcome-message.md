---
title: Enviar mensagem de boas-vinda aos usuários | Microsoft Docs
description: Saiba como desenvolver seu bot para fornecer uma experiência do usuário calorosa.
keywords: visão geral, desenvolver, experiência do usuário, boas-vindas, experiência personalizada, C#, JS, mensagem de boas-vindas, bot, saudar, saudação
author: DanDev33
ms.author: v-dashel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f7709d6273739be6d2b3e9e3174f24ea2734f22d
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215317"
---
# <a name="send-welcome-message-to-users"></a>Enviar mensagem de boas-vinda aos usuários

[!INCLUDE[applies-to](../includes/applies-to.md)]

Ao criar um bot, o principal objetivo é envolver seu usuário em uma conversa produtiva. Uma das melhores maneiras de fazer isso é garantir que, a partir do momento em que o usuário se conecta pela primeira vez, ele entenda o objetivo e os recursos principais do bot, a razão pela qual seu bot foi criado. Este artigo fornece exemplos de código para ajudá-lo a dar boas-vindas aos usuários em seu bot.

## <a name="prerequisites"></a>Pré-requisitos
- Entenda sobre as [Noções básicas do bot](bot-builder-basics.md). 
- Uma cópia do **Exemplo de boas-vindas ao usuário** em [Exemplo de C#](https://aka.ms/welcome-user-mvc) ou em [Exemplo de JS](https://aka.ms/bot-welcome-sample-js). O código do exemplo é usado para explicar como enviar mensagens de boas-vindas.

## <a name="about-this-sample-code"></a>Sobre este código de exemplo
Este código de exemplo mostra como detectar e recepcionar novos usuários quando eles são inicialmente conectados ao seu bot. O diagrama a seguir mostra o fluxo lógico desse bot. 

### <a name="ctabcsharp"></a>[C#](#tab/csharp)
Os dois eventos principais encontrados pelo bot são
- `OnMembersAddedAsync`, que é chamado sempre que um novo usuário é conectado ao seu bot
- `OnMessageActivityAsync`, que é chamado sempre que uma nova entrada do usuário é recebida.

![Fluxo lógico da boas-vindas ao usuário](media/welcome-user-flow.png)

Sempre que um novo usuário se conecta, ele recebe `WelcomeMessage`, `InfoMessage` e `PatternMessage` do bot. Quando uma nova entrada do usuário é recebida, WelcomeUserState é conferido para ver se `DidBotWelcomeUser` está definido como _true_. Se não, uma mensagem inicial de boas-vindas será retornada ao usuário.

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Os dois eventos principais encontrados pelo bot são
- `onMembersAdded`, que é chamado sempre que um novo usuário é conectado ao seu bot
- `onMessage`, que é chamado sempre que uma nova entrada do usuário é recebida.

![Fluxo lógico da boas-vindas ao usuário](media/welcome-user-flow-js.png)

Sempre que um novo usuário se conecta, ele recebe `welcomeMessage`, `infoMessage` e `patternMessage` do bot. Quando uma nova entrada do usuário é recebida, `welcomedUserProperty` é conferido para ver se `didBotWelcomeUser` está definido como _true_. Se não, uma mensagem inicial de boas-vindas será retornada ao usuário.

---

 Se DidBotWelcomeUser estiver definido como _true_, a entrada do usuário será avaliada. Com base no conteúdo da entrada do usuário, o bot realizará uma das seguintes ações:
- Retornar uma saudação recebida do usuário.
- Exibir um cartão de Hero com informações adicionais sobre os bots.
- Reenviar o `WelcomeMessage` explicando entradas esperadas para o bot.

## <a name="create-user-object"></a>Criar objeto de usuário
### <a name="ctabcsharp"></a>[C#](#tab/csharp)
O objeto de estado do usuário é criado na inicialização e a dependência é injetada no construtor de bot.

**Startup.cs** [!code-csharp[ConfigureServices](~/../botBuilder-samples/samples/csharp_dotnetcore/03.welcome-user/Startup.cs?range=29-33)]

**WelcomeUserBot.cs** [!code-csharp[Class](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=41-47)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Na inicialização, o armazenamento de memória e o estado do usuário são definidos no index. js.

**Index.js** [!code-javascript[DefineUserState](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/Index.js?range=8-10,33-41)]

---

## <a name="create-property-accessors"></a>Criar acessadores de propriedade
### <a name="ctabcsharp"></a>[C#](#tab/csharp)
Agora, podemos criar um acessador de propriedade que nos forneça um identificador para WelcomeUserState dentro do método OnMessageActivityAsync.
Em seguida, chamamos o método GetAsync para obter a chave com escopo definido corretamente. Em seguida, salvamos os dados de estado do usuário após cada iteração de entrada do usuário, usando o método `SaveChangesAsync`.

**WelcomeUserBot.cs** [!code-csharp[OnMessageActivityAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-71, 102-105)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Agora, podemos criar um acessador de propriedade que nos forneça um identificador para WelcomedUserProperty que persista em UserState.

**WelcomeBot.js** [!code-javascript[DefineUserState](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=7-10,16-22)]

---

## <a name="detect-and-greet-newly-connected-users"></a>Detectar e saudar usuários conectados recentemente

### <a name="ctabcsharp"></a>[C#](#tab/csharp)
Em **WelcomeUserBot**, verificamos se há uma atualização de atividade, usando `OnMembersAddedAsync()` para ver se um novo usuário foi adicionado à conversa e, em seguida, enviamos a ele um conjunto de três mensagens inicias de boas-vindas: `WelcomeMessage`, `InfoMessage` e `PatternMessage`. O código completo dessa interação é mostrado abaixo.

**WelcomeUserBot.cs** [!code-csharp[WelcomeMessages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=20-40, 55-66)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Esse código do JavaScript envia uma mensagem inicial de boas-vindas quando um usuário é adicionado. Isso é feito analisando a atividade da conversa e verificando se um novo membro foi adicionado à conversa.

**WelcomeBot.js** [!code-javascript[DefineUserState](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=65-87)]

---

## <a name="welcome-new-user-and-discard-initial-input"></a>Recepção do novo usuário e descarte da entrada inicial

### <a name="ctabcsharp"></a>[C#](#tab/csharp)
Também é importante considerar quando a entrada do usuário pode realmente conter informações úteis. Isso pode variar por canal. Para garantir que o usuário tenha uma boa experiência em todos os canais possíveis, verificamos o sinalizador de status _didBotWelcomeUser_ e, se ele for “false”, não processaremos a entrada inicial do usuário. Em vez disso, forneceremos ao usuário uma mensagem inicial de boas-vindas. O booliano _welcomedUserProperty_ é definido como "true", armazenado em UserState e nosso código agora processará essa entrada do usuário usando todas as atividades adicionais de mensagem.

**WelcomeUserBot.cs** [!code-csharp[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-82)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Também é importante considerar quando a entrada do usuário pode realmente conter informações úteis. Isso pode variar por canal. Para garantir que o usuário tenha uma boa experiência em todos os canais possíveis, verificamos a propriedade didBotWelcomeUser e, se ela não existir, nós a definiremos como “false” e não processaremos a entrada inicial do usuário. Em vez disso, forneceremos ao usuário uma mensagem inicial de boas-vindas. O booleano _didBotWelcomeUser_ é definida como “true” e o nosso código processa a entrada do usuário de todas as atividades adicionais de mensagem.

**WelcomeBot.js** [!code-javascript[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=24-38,57-59,63)]

---

## <a name="process-additional-input"></a>Processar entrada adicional

Após a recepção de um novo usuário, as informações de entrada do usuário são avaliadas a cada turno de mensagem e seu bot fornece uma resposta com base no contexto dessa entrada. O código a seguir mostra a lógica de decisão usada para gerar essa resposta. 

### <a name="ctabcsharp"></a>[C#](#tab/csharp)
Uma entrada de “intro” ou “help” chama a função `SendIntroCardAsync` para apresentar ao usuário um cartão Hero informativo. Esse código é examinado na próxima seção deste artigo.

**WelcomeUserBot.cs** [!code-csharp[SwitchOnUtterance](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=85-100)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Uma entrada de”intro” ou “help” usa CardFactory para apresentar ao usuário um cartão adaptável de introdução. Esse código é examinado na próxima seção deste artigo.

**WelcomeBot.js** [!code-javascript[SwitchOnUtterance](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=40-56)]

---

## <a name="using-hero-card-greeting"></a>Usar a saudação do cartão Hero

Conforme mencionado acima, algumas entradas do usuário geram um _cartão Hero_ em resposta à solicitação. Saiba mais sobre as saudações do cartão Hero aqui: [Enviar um cartão de introdução](./bot-builder-howto-add-media-attachments.md). Veja abaixo o código necessário para criar a resposta do cartão Hero do bot.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**WelcomeUserBot.cs** [!code-csharp[SendHeroCardGreeting](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=107-127)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**WelcomeBot.js** [!code-javascript[SendIntroCard](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=91-116)]

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
