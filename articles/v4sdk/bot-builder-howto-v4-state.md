---
title: Salvar dados do usuário e de conversa – Serviço de Bot
description: Saiba como o SDK do bot Framework gerencia dados de usuário e de conversa (estado). Consulte exemplos de código que configuram o armazenamento para esses dados e que leem e gravam.
keywords: estado da conversa, estado do usuário, conversa, salvar estado, gerenciar o estado do bot
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/7/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8bf5a60575fdf1ac9318d054b2bd5a35723ab9a9
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577338"
---
# <a name="save-user-and-conversation-data"></a>Salvar dados do usuário e da conversa

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Um bot é inerentemente sem estado. Após a implantação do seu bot, talvez ele não seja executado no mesmo processo ou no mesmo computador de um turno para outro. Porém, talvez seu bot precise controlar o contexto de uma conversa, para que ele possa gerenciar seu comportamento e lembrar-se das respostas às perguntas anteriores. Os recursos de armazenamento e estado do SDK do Bot Framework permitem que você adicione um estado ao seu bot. Os bots usam o gerenciamento de estado e os objetos de armazenamento para gerenciar e manter o estado. O gerenciador de estado fornece uma camada de abstração que permite acessar propriedades de estado usando acessadores de propriedades, independentemente do tipo de armazenamento subjacente.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário conhecer os [conceitos básicos sobre bot](bot-builder-basics.md) e como os bots [gerenciam o estado](bot-builder-concept-state.md).
- O código neste artigo baseia-se no **exemplo de bot de gerenciamento de estado**. Você precisará de uma cópia do exemplo em [C#](https://aka.ms/statebot-sample-cs), [JavaScript](https://aka.ms/statebot-sample-js) ou [Python](https://aka.ms/bot-state-python-sample-code).

## <a name="about-this-sample"></a>Sobre este exemplo

Ao receber entrada do usuário, este exemplo verifica o estado da conversa armazenada para ver se o nome foi solicitado a esse usuário anteriormente. Se não foi, o nome do usuário será solicitado e essa entrada será armazenada no estado do usuário. Se foi, o nome armazenado no estado do usuário será usado para conversar com ele e os respectivos dados de entrada, bem como a hora de recebimento e a identificação do canal de entrada, serão retornados ao usuário. Os valores de hora e identificação do canal são recuperados dos dados da conversa do usuário e, em seguida, salvos no estado da conversa. O diagrama a seguir mostra a relação entre o bot, o perfil do usuário e as classes de dados da conversa.

## <a name="c"></a>[C#](#tab/csharp)

![Exemplo de bot de estado em C#](media/StateBotSample-Overview.png)

## <a name="javascript"></a>[JavaScript](#tab/javascript)

![Exemplo de bot de estado do JavaScript](media/StateBotSample-JS-Overview.png)

## <a name="python"></a>[Python](#tab/python)

![Exemplo de bot de estado do Python](media/StateBotSample-Python-Overview.png)

---

## <a name="define-classes"></a>Definir classes

## <a name="c"></a>[C#](#tab/csharp)

A primeira etapa na configuração de gerenciamento de estado é definir as classes que contêm as informações a serem gerenciadas no estado do usuário e da conversa. O exemplo usado neste artigo define as seguintes classes:

- No **UserProfile.cs**, você define uma `UserProfile` classe para as informações do usuário que o bot coletará.
- No **ConversationData.cs**, você define uma `ConversationData` classe para controlar nosso estado de conversa durante a coleta de informações do usuário.

Os exemplos de código a seguir mostram as definições para as classes `UserProfile` e `ConversationData`.

**UserProfile.cs**

[!code-csharp[UserProfile](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/UserProfile.cs?range=7-11)]

**ConversationData.cs**

[!code-csharp[ConversationData](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/ConversationData.cs?range=6-17)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Essa etapa não é necessária no JavaScript.

## <a name="python"></a>[Python](#tab/python)

A primeira etapa na configuração de gerenciamento de estado é definir as classes que contêm as informações a serem gerenciadas no estado do usuário e da conversa. O exemplo usado neste artigo define as seguintes classes:

- O **user_profile.py** contém a classe `UserProfile` que armazena as informações do usuário coletadas pelo bot.
- O **conversation_data. py** contém a classe `ConversationData` que controla o estado da conversa durante a coleta de informações do usuário.

Os exemplos de código a seguir mostram as definições para as classes `UserProfile` e `ConversationData`.

**user_profile.py**

[!code-python[user_profile](~/../botbuilder-samples/samples/python/45.state-management/data_models/user_profile.py?range=5-7)]

**conversation_data.py**

[!code-python[conversation_data](~/../botbuilder-samples/samples/python/45.state-management/data_models/conversation_data.py?range=5-14)]

---

## <a name="create-conversation-and-user-state-objects"></a>Criar objetos de estado da conversa e do usuário

## <a name="c"></a>[C#](#tab/csharp)

Em seguida, você registra `MemoryStorage` que é usado para `UserState` criar `ConversationState` objetos e. Os objetos de estado da conversa e do usuário são criados em `Startup` e a dependência é injetada no construtor de bot. Outros serviços de um bot que são registrados: um provedor de credenciais, um adaptador e a implementação do bot.

**Startup.cs**

[!code-csharp[ConfigureServices](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Startup.cs?range=24-27)]
[!code-csharp[ConfigureServices](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Startup.cs?range=49-55)]

**Bots/StateManagementBot.cs**

[!code-csharp[Bot constructor](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Bots/StateManagementBot.cs?range=15-22)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Em seguida, registre `MemoryStorage` que é usado para criar `UserState` objetos e `ConversationState` . Eles são criados em **index.js** e consumidos quando o bot é criado.

**index.js**

[!code-javascript[index.js](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/index.js?range=34-40)]

**bots/stateManagementBot.js**

[!code-javascript[bot constructor](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=10-12)]
[!code-javascript[bot constructor](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=17-19)]

## <a name="python"></a>[Python](#tab/python)

Em seguida, você registra `MemoryStorage` que é usado para `UserState` criar `ConversationState` objetos e. Eles são criados em **app.py** e consumidos quando o bot é criado.

**app.py**

[!code-python[app.py](~/../botbuilder-samples/samples/python/45.state-management/app.py?range=68-71)]

**bots/state_management_bot.py**

[!code-python[bot constructor](~/../botbuilder-samples/samples/python/45.state-management/bots/state_management_bot.py?range=13-25)]

---

## <a name="add-state-property-accessors"></a>Adicionar acessadores de propriedade do estado

## <a name="c"></a>[C#](#tab/csharp)

Agora você cria acessadores de propriedade usando o `CreateProperty` método que fornece um identificador para o `BotState` objeto. Cada acessador de propriedade de estado permite que você obtenha ou defina o valor da propriedade de estado associada. Antes de usar as propriedades de estado, use cada acessador para carregar a propriedade do armazenamento e obtê-la do cache de estado. Para obter a chave com escopo correto associada à propriedade State, chame o `GetAsync` método.

**Bots/StateManagementBot.cs**

[!code-csharp[Create accessors](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Bots/StateManagementBot.cs?range=42,45)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Agora você cria acessadores de propriedade para `UserState` e `ConversationState` . Cada acessador de propriedade de estado permite que você obtenha ou defina o valor da propriedade de estado associada. Você usa cada acessador para carregar a propriedade associada do armazenamento e recuperar seu estado atual do cache.

**bots/stateManagementBot.js**

[!code-javascript[Create accessors](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=13-15)]

## <a name="python"></a>[Python](#tab/python)

Agora você cria acessadores de propriedade para `UserProfile` e `ConversationData` . Cada acessador de propriedade de estado permite que você obtenha ou defina o valor da propriedade de estado associada. Você usa cada acessador para carregar a propriedade associada do armazenamento e recuperar seu estado atual do cache.

**bots/state_management_bot.py**

[!code-python[Create accessors](~/../botbuilder-samples/samples/python/45.state-management/bots/state_management_bot.py?range=27-30)]

---

## <a name="access-state-from-your-bot"></a>Estado de acesso do seu bot

As seções anteriores abordam as etapas de tempo de inicialização para adição dos acessadores de propriedade do estado ao nosso bot. Agora, você pode usar esses acessadores em tempo de execução para ler e gravar informações de estado. O código de exemplo abaixo usa o seguinte fluxo lógico:

## <a name="c"></a>[C#](#tab/csharp)

- Se userProfile.Name estiver vazio e conversationData. PromptedUserForName for _true_, você recuperará o nome de usuário fornecido e o armazenará no estado do usuário.
- Se userProfile.Name estiver vazio e conversationData. PromptedUserForName for _false_, você solicitará o nome do usuário.
- Se userProfile.Name tiver sido armazenado anteriormente, você recuperará a hora da mensagem e a ID do canal da entrada do usuário, ecoará todos os dados de volta para o usuário e armazenará os dados recuperados no estado da conversa.

**Bots/StateManagementBot.cs**

[!code-csharp[OnMessageActivityAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=38-85)]

Antes de sair do manipulador de ativação, use o método _SaveChangesAsync ()_ do State Management Objects para gravar todas as alterações de estado de volta no armazenamento.

**Bots/StateManagementBot.cs**

[!code-csharp[OnTurnAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=24-31)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

- Se userProfile.Name estiver vazio e conversationData. PromptedUserForName for _true_, você recuperará o nome de usuário fornecido e o armazenará no estado do usuário.
- Se userProfile.Name estiver vazio e conversationData. PromptedUserForName for _false_, você solicitará o nome do usuário.
- Se userProfile.Name tiver sido armazenado anteriormente, você recuperará a hora da mensagem e a ID do canal da entrada do usuário, ecoará todos os dados de volta para o usuário e armazenará os dados recuperados no estado da conversa.

**bots/stateManagementBot.js**

[!code-javascript[OnMessage](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=21-58)]

Antes de sair de cada caixa de diálogo, use o método _SaveChanges ()_ dos objetos de gerenciamento de estado para manter todas as alterações gravando o estado de volta para o armazenamento.

**bots/stateManagementBot.js**

[!code-javascript[OnDialog](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=72-81)]

## <a name="python"></a>[Python](#tab/python)

- Se `user_profile.name` estiver vazio e `conversation_data.prompted_for_user_name` for *true*, o bot vai recuperar o nome fornecido pelo usuário e vai armazená-lo no estado do usuário.
- Se `user_profile.name` estiver vazio e `conversation_data.prompted_for_user_name` for *false*, o bot solicitará o nome do usuário.
- Se `user_profile.name` tiver sido armazenado anteriormente, o bot recupera a **hora da mensagem** e a **ID do canal** da entrada do usuário, retorna os dados para o usuário e armazena os dados recuperados no estado da conversa.

**bots/state_management_bot.py**

[!code-python[state_message_activity](~/../botbuilder-samples/samples/python/45.state-management/bots/state_management_bot.py?range=47-89)]

Antes de sair de cada caixa de diálogo, o bot usa o método `save_changes` do State Management Objects para manter todas as alterações ao gravar as informações de estado no armazenamento.

**bots/state_management_bot.py**

[!code-python[state_storage](~/../botbuilder-samples/samples/python/45.state-management/bots/state_management_bot.py?range=32-36)]

---

## <a name="test-the-bot"></a>Testar o bot

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, veja [Exemplo de C#](https://aka.ms/statebot-sample-cs) ou [Exemplo de JS](https://aka.ms/statebot-sample-js) no arquivo LEIAME.
1. Use o Emulador para testar o bot, conforme mostrado abaixo.

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
