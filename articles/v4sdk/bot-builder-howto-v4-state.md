---
title: Salvar dados do usuário e da conversa | Microsoft Docs
description: Saiba como salvar e recuperar dados de estado com o SDK do Bot Framework para SDK.
keywords: estado da conversa, estado do usuário, conversa, salvar estado, gerenciar o estado do bot
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f306fdbe39fd7863faf0ec32f99604c3a99da955
ms.sourcegitcommit: 312a4593177840433dfee405335100ce59aac347
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933739"
---
# <a name="save-user-and-conversation-data"></a>Salvar dados do usuário e da conversa

[!INCLUDE[applies-to](../includes/applies-to.md)]

Um bot é inerentemente sem estado. Após a implantação do seu bot, talvez ele não seja executado no mesmo processo ou no mesmo computador de um turno para outro. Porém, talvez seu bot precise controlar o contexto de uma conversa, para que ele possa gerenciar seu comportamento e lembrar-se das respostas às perguntas anteriores. Os recursos de armazenamento e estado do SDK do Bot Framework permitem que você adicione um estado ao seu bot. Os bots usam o gerenciamento de estado e os objetos de armazenamento para gerenciar e manter o estado. O gerenciador de estado fornece uma camada de abstração que permite acessar propriedades de estado usando acessadores de propriedades, independentemente do tipo de armazenamento subjacente.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário conhecer os [conceitos básicos sobre bot](bot-builder-basics.md) e como os bots [gerenciam o estado](bot-builder-concept-state.md).
- O código neste artigo baseia-se no **exemplo de bot de gerenciamento de estado**. Você precisará de uma cópia do exemplo contida em [CSharp](https://aka.ms/statebot-sample-cs) ou em [JavaScript](https://aka.ms/statebot-sample-js).

## <a name="about-this-sample"></a>Sobre este exemplo
Ao receber entrada do usuário, este exemplo verifica o estado da conversa armazenada para ver se o nome foi solicitado a esse usuário anteriormente. Se não foi, o nome do usuário será solicitado e essa entrada será armazenada no estado do usuário. Se foi, o nome armazenado no estado do usuário será usado para conversar com ele e os respectivos dados de entrada, bem como a hora de recebimento e a identificação do canal de entrada, serão retornados ao usuário. Os valores de hora e identificação do canal são recuperados dos dados da conversa do usuário e, em seguida, salvos no estado da conversa. O diagrama a seguir mostra a relação entre o bot, o perfil do usuário e as classes de dados da conversa.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

![exemplo de bot de estado](media/StateBotSample-Overview.png)

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

![exemplo de bot de estado](media/StateBotSample-JS-Overview.png)

---

## <a name="define-classes"></a>Definir classes

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

A primeira etapa na configuração de gerenciamento de estado é definir as classes que conterão todas as informações que desejamos gerenciar no estado do usuário e da conversa. Para este exemplo, definimos o seguinte:

- Em **UserProfile.cs**, definimos uma classe `UserProfile` para as informações do usuário que o bot coletará. 
- Em **ConversationData.cs**, definimos uma classe `ConversationData` para controlar o estado da nossa conversa durante a coleta de informações do usuário.

O exemplo de código a seguir mostra a criação da definição da classe UserProfile.

**UserProfile.cs**  
[!code-csharp[UserProfile](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/UserProfile.cs?range=7-11)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A primeira etapa exige o serviço botbuilder, que inclui definições de `UserState` e `ConversationState`.

**index.js**  
[!code-javascript[BotService](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/index.js?range=7-9)]

---

## <a name="create-conversation-and-user-state-objects"></a>Criar objetos de estado da conversa e do usuário

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em seguida, registramos `MemoryStorage`, que é usado para criar os objetos `UserState` e `ConversationState`. Os objetos de estado da conversa e do usuário são criados em `Startup` e a dependência é injetada no construtor de bot. Outros serviços de um bot que são registrados: um provedor de credenciais, um adaptador e a implementação do bot.

**Startup.cs**  
[!code-csharp[ConfigureServices](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Startup.cs?range=26-29)]
[!code-csharp[ConfigureServices](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Startup.cs?range=51-57)]

**Bots/StateManagementBot.cs** [!code-csharp[StateManagement](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Bots/StateManagementBot.cs?range=15-22)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Em seguida, registramos `MemoryStorage`, que é então usado para criar os objetos `UserState` e `ConversationState`.

**index.js**  
[!code-javascript[DefineMemoryStore](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/index.js?range=33-39)]

---

## <a name="add-state-property-accessors"></a>Adicionar acessadores de propriedade do estado

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Agora podemos criar acessadores de propriedade usando o método `CreateProperty`, que fornece um identificador ao objeto `BotState`. Cada acessador de propriedade de estado permite que você obtenha ou defina o valor da propriedade de estado associada. Antes de usarmos nossas propriedades de estado, usamos cada acessador para carregar a propriedade do armazenamento e obtê-la no cache de estado. Para obter a chave de escopo corretamente associada à propriedade do estado, podemos chamar o método `GetAsync`.

**Bots/StateManagementBot.cs** [!code-csharp[StateAccessors](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Bots/StateManagementBot.cs?range=38-46)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Agora, podemos criar acessadores de propriedade para `UserState` e `ConversationState`. Cada acessador de propriedade de estado permite que você obtenha ou defina o valor da propriedade de estado associada. Usamos cada acessador para carregar a propriedade associada no armazenamento e recuperar o seu estado atual contido no cache.

**bots/stateManagementBot.js** [!code-javascript[BotService](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=6-19)]

---

## <a name="access-state-from-your-bot"></a>Estado de acesso do seu bot

As seções anteriores abordam as etapas de tempo de inicialização para adição dos acessadores de propriedade do estado ao nosso bot. Agora, podemos usar esses acessadores em tempo de execução para ler e gravar informações de estado. O código de exemplo abaixo usa o seguinte fluxo lógico:

- Se userProfile.Name está vazio e conversationData.PromptedUserForName é _true_, recuperamos o nome de usuário fornecido e o armazenamos no estado do usuário.
- Se userProfile.Name está vazio e é conversationData.PromptedUserForName é _false_, solicitamos o nome do usuário.
- Se userProfile.Name foi armazenado anteriormente, recuperamos a hora e a identificação de canal da mensagem contidos na entrada do usuário, retornamos todos os dados ao usuário e armazenamos os dados recuperados no estado da conversa.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots/StateManagementBot.cs** [!code-csharp[OnMessageActivityAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=38-85)]

Antes de encerramos o manipulador de turno, usamos o método _SaveChangesAsync()_ dos objetos de gerenciamento de estado para gravar todas as alterações no estado no armazenamento.

**Bots/StateManagementBot.cs** [!code-csharp[OnTurnAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=24-31)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/stateManagementBot.js** [!code-javascript[OnMessage](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=21-58)]

Antes de encerramos cada turno de diálogo, usamos o método _SaveChanges ()_ dos objetos de gerenciamento de estado para persistir todas as alterações gravando o estado no armazenamento.

**bots/stateManagementBot.js** [!code-javascript[OnDialog](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=60-67)]

---

## <a name="test-the-bot"></a>Testar o bot

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, veja [Exemplo de C#](https://aka.ms/statebot-sample-cs) ou [Exemplo de JS](https://aka.ms/statebot-sample-js) no arquivo LEIAME.
1. Use o emulador para testar o bot, conforme mostrado abaixo.

![exemplo de teste de estado do bot](media/state-bot-testing-emulator.png)

## <a name="additional-resources"></a>Recursos adicionais

**Privacidade:** se você pretende armazenar dados pessoais do usuário, garanta a conformidade com o [Regulamento Geral sobre a Proteção de Dados](https://blog.botframework.com/2018/04/23/general-data-protection-regulation-gdpr).

**Gerenciamento de estado:** todas as chamadas de gerenciamento de estado são assíncronas e last-writer-wins por padrão. Na prática, você deve obter, definir e salvar o estado o mais próximo possível em seu bot.

**Dados comerciais críticos:** use o estado do bot para armazenar preferências, nome de usuário ou o último pedido feito, mas não o use para armazenar dados corporativos críticos. Para dados críticos, [crie seus próprios componentes de armazenamento](bot-builder-custom-storage.md) ou grave diretamente no [armazenamento](bot-builder-howto-v4-storage.md).

**Recognizer-Text:** o exemplo usa as bibliotecas Microsoft/Recognizer-Text para analisar e validar a entrada do usuário. Para saber mais, confira a página [visão geral](https://github.com/Microsoft/Recognizers-Text#microsoft-recognizers-text-overview).

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como configurar o estado para ajudar você a ler e a gravar dados no armazenamento, vamos aprender a fazer uma série de perguntas ao usuário, validar suas respostas e salvar as entradas.

> [!div class="nextstepaction"]
> [Crie seus próprios prompts para coletar entradas do usuário](bot-builder-primitive-prompts.md).
