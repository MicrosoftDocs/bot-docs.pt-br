---
title: Conversas controladas por evento e manipuladores de atividade-serviço bot
description: Familiarize-se com o manipulador de atividade bot. Saiba mais sobre como gerenciar o raciocínio de bot com base no tipo de atividade recebida de um usuário.
author: JonathanFingold
ms.author: v-jofin
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 12/09/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 66c7b2cb509cbcebe8c3f181352448c810746c07
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717612"
---
# <a name="event-driven-conversations-using-an-activity-handler"></a>Conversas controladas por evento usando um manipulador de atividade

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Um _manipulador de atividade_ é uma maneira orientada por evento de organizar a lógica de conversação para o bot.
Cada tipo ou subtipo diferente de atividade representa um tipo diferente de evento de conversação.
Nos bastidores, o manipulador de *folheio* do bot chama o manipulador de atividade individual para qualquer tipo de atividade recebida.

Por exemplo, se o bot receber uma atividade de mensagem, o manipulador de ativação veria essa atividade de entrada e a enviaria para o manipulador de atividade _de atividade na mensagem_ . Ao criar seu bot, a lógica de bot para manipular e responder a mensagens será exibida no manipulador de _atividade da mensagem_ . Da mesma forma, sua lógica para lidar com membros que estão sendo adicionados à conversa entrará no manipulador _on Members Added_ , que é chamado sempre que um membro é adicionado à conversa.

Para outras maneiras de organizar a lógica de bot, consulte a seção [lógica de bot](bot-builder-basics.md#bot-logic) em **como os bots funcionam**.

# <a name="c"></a>[C#](#tab/csharp)

Para implementar sua lógica para esses manipuladores, você substituirá esses métodos em seu bot, como na seção do [manipulador de atividade de exemplo](#sample-activity-handler) abaixo. Para cada um desses manipuladores, não há nenhuma implementação base e, portanto, basta adicionar a lógica desejada na substituição.

Há algumas situações em que é melhor substituir o manipulador de turno base, por exemplo, ao [salvar o estado](bot-builder-concept-state.md) no final de um turno. Ao fazer isso, chame primeiro `await base.OnTurnAsync(turnContext, cancellationToken);` para fazer com que a implementação base de `OnTurnAsync` seja executada antes do código adicional. A implementação base é, entre outras coisas, responsável por chamar o resto dos manipuladores de atividade, como `OnMessageActivityAsync`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript `ActivityHandler` usa um emissor de eventos e um padrão de ouvinte.
Por exemplo, use o método `onMessage` para registrar um ouvinte de eventos para atividades de mensagem. Você pode registrar mais de um ouvinte. Quando o bot recebe uma atividade de mensagem, o manipulador de atividades vê essa atividade de entrada e a envia a cada um dos ouvintes de atividade `onMessage`, na ordem em que elas foram registradas.

Ao criar seu bot, a lógica do bot para tratar das mensagens e respondê-las entrará nos ouvintes `onMessage`. Da mesma forma, a lógica de tratamento de membros que estão sendo adicionados à conversa vai entrar nos ouvintes `onMembersAdded`, que são chamados sempre que um membro é adicionado à conversa.
Para adicionar esses ouvintes, você os registrará em seu bot, como visto na seção [Lógica de bot](bot-builder-basics.md#bot-logic) abaixo. Para cada ouvinte, inclua a lógica de bot e, em seguida, **não se esqueça de chamar `next()` no final**. Ao chamar `next()`, você faz com que o próximo ouvinte seja executado.

Lembre-se de [salvar o estado](bot-builder-concept-state.md) antes que o ciclo seja concluído. Você pode fazer isso substituindo o método `run` do manipulador de atividade e salvando o estado após a conclusão do método `run` do pai.

Não existem situações comuns de substituição do manipulador de turno base, ou seja, tenha cuidado ao tentar fazer isso.
Há um manipulador especial chamado `onDialog`. O manipulador `onDialog` é executado no final, depois que o restante dos manipuladores foi executado, e não está vinculado a nenhum tipo de atividade. Assim como com todos os manipuladores acima, não deixe de chamar `next()` para garantir a conclusão do processo.

# <a name="python"></a>[Python](#tab/python)

Ao criar seu bot, sua lógica do bot para tratar das mensagens e respondê-las entrará nesse manipulador `on_message_activity`. Da mesma forma, sua lógica de tratamento de membros que estão sendo adicionados à conversa vai entrar no manipulador `on_members_added`, que é chamado sempre que um membro é adicionado à conversa.

Por exemplo, se o bot recebe uma atividade de mensagem, o manipulador de turno vê a atividade de entrada a envia para o manipulador de atividade `on_message_activity`.

Para implementar sua lógica para esses manipuladores, você substituirá esses métodos em seu bot, como na seção do [manipulador de atividade de exemplo](#sample-activity-handler) abaixo. Para cada um desses manipuladores, não há nenhuma implementação base e, portanto, basta adicionar a lógica desejada na substituição.

Há algumas situações em que é melhor substituir o manipulador de turno base, por exemplo, ao [salvar o estado](bot-builder-concept-state.md) no final de um turno. Ao fazer isso, chame primeiro `await super().on_turn(turnContext);` para fazer com que a implementação base de `on_turn` seja executada antes do código adicional. A implementação base é, entre outras coisas, responsável por chamar o resto dos manipuladores de atividade, como `on_message_activity`.

---

## <a name="activity-handling"></a>Manipulação de atividades

A lógica do bot processa as atividades de entrada de um ou mais canais e gera atividades de saída em resposta.

### <a name="c"></a>[C#](#tab/csharp)

A lógica principal do bot é definida no código do bot. Para implementar um bot como um manipulador de atividade, derive a classe bot de `ActivityHandler` , que implementa a `IBot` interface. `ActivityHandler` define vários manipuladores para diferentes tipos de atividades, como `OnMessageActivityAsync` e `OnMembersAddedAsync` . Esses métodos são protegidos, mas podem ser substituídos, já que estamos derivando de `ActivityHandler` .

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
| Atividade de atualização de instalação recebida | `OnInstallationUpdateActivityAsync` | Em uma `installationUpdate` atividade, o chama um manipulador com base no fato de o bot ter sido instalado ou desinstalado. |
| Bot instalado | `OnInstallationUpdateAddAsync` | Substitua isso para adicionar lógica para quando o bot for instalado em uma unidade organizacional. |
| Bot desinstalado | `OnInstallationUpdateRemoveAsync` | Substitua isso para adicionar lógica para quando o bot for desinstalado em uma unidade organizacional. |
| Outro tipo de atividade recebido | `OnUnrecognizedActivityTypeAsync` | Substitua-o para lidar com qualquer tipo de atividade sem tratamento. |

Esses manipuladores diferentes têm um `turnContext` que fornece informações sobre a atividade de entrada, que corresponde à solicitação HTTP de entrada. As atividades podem ser de vários tipos e, portanto, cada manipulador fornece uma atividade fortemente tipada em seu parâmetro de contexto de turno; na maioria dos casos, `OnMessageActivityAsync` sempre será manipulado e é geralmente o mais comum.

Como nas versões anteriores de 4.x dessa estrutura, também há a opção de implementar o método público `OnTurnAsync`. Atualmente, a implementação base desse método lida com a verificação de erros e, em seguida, chama cada um dos manipuladores específicos (como os dois que definimos neste exemplo) dependendo do tipo de atividade de entrada. Na maioria dos casos, você pode deixar esse método de lado e usar manipuladores individuais, mas se a situação exige uma implementação personalizada de `OnTurnAsync`, ele ainda é uma opção.

> [!IMPORTANT]
> Se você substituir o método `OnTurnAsync`, precisará chamar `base.OnTurnAsync` para obter a implementação base e chamar todos os outros manipuladores `On<activity>Async`, ou chamar esses manipuladores por conta própria. Caso contrário, esses manipuladores não serão chamados e o código não será executado.

### <a name="javascript"></a>[JavaScript](#tab/javascript)

A lógica principal do bot é definida no código do bot. Para implementar um bot como um manipulador de atividade, estenda `ActivityHandler` . `ActivityHandler` define vários eventos para diferentes tipos de atividades, e você pode modificar o comportamento do bot registrando ouvintes de eventos, como com `onMessage` e `onConversationUpdate` .

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
| Atividade de atualização de instalação recebida | `onInstallationUpdate` | Registra um ouvinte para quando qualquer atividade `installationUpdate` for recebida. |
| Bot instalado | `onInstallationUpdateAdd` | Registra um ouvinte para quando o bot é instalado em uma unidade organizacional. |
| Bot desinstalado | `onInstallationUpdateRemove` | Registra um ouvinte para quando o bot é desinstalado em uma unidade organizacional. |
| Outro tipo de atividade recebido | `onUnrecognizedActivityType` | Registra um ouvinte para quando um manipulador para o tipo específico de atividade não estiver definido. |
| Manipuladores de atividade concluídos | `onDialog` | Chamado após a conclusão de qualquer manipulador aplicável. |

Chame a função de continuação `next` de cada manipulador para permitir que o processamento continue. Se `next` não for chamado, o processamento da atividade terminará.

### <a name="python"></a>[Python](#tab/python)

A lógica principal do bot é definida no código do bot. Para implementar um bot como um manipulador de atividade, derive a classe bot de `ActivityHandler` , que, por sua vez, deriva da `Bot` classe abstract. `ActivityHandler` define vários manipuladores para diferentes tipos de atividades, como `on_message_activity` e `on_members_added` . Esses métodos são protegidos, mas podem ser substituídos, já que estamos derivando de `ActivityHandler` .

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
| Atividade de atualização de instalação recebida | `on_installation_update` | Em uma `installationUpdate` atividade, o chama um manipulador com base no fato de o bot ter sido instalado ou cancelado. |
| Bot instalado | `on_installation_update_add` | Substitua isso para adicionar lógica para quando o bot for instalado em uma unidade organizacional. |
| Bot desinstalado | `on_installation_update_remove` | Substitua isso para adicionar lógica para quando o bot for desinstalado em uma unidade organizacional. |
| Outro tipo de atividade recebido | `on_unrecognized_activity_type` | Substitua-o para lidar com qualquer tipo de atividade sem tratamento. |

Esses manipuladores diferentes têm um `turn_context` que fornece informações sobre a atividade de entrada, que corresponde à solicitação HTTP de entrada. As atividades podem ser de vários tipos e, portanto, cada manipulador fornece uma atividade fortemente tipada em seu parâmetro de contexto de turno; na maioria dos casos, `on_message_activity` sempre será manipulado e é geralmente o mais comum.

Como nas versões anteriores de 4.x dessa estrutura, também há a opção de implementar o método público `on_turn`. Atualmente, a implementação base desse método lida com a verificação de erros e, em seguida, chama cada um dos manipuladores específicos (como os dois que definimos neste exemplo) dependendo do tipo de atividade de entrada. Na maioria dos casos, você pode deixar esse método de lado e usar manipuladores individuais, mas se a situação exige uma implementação personalizada de `on_turn`, ele ainda é uma opção.

> [!IMPORTANT]
> Se você substituir o método `on_turn`, precisará chamar `super().on_turn` para obter a implementação base e chamar todos os outros manipuladores `on_<activity>`, ou chamar esses manipuladores por conta própria. Caso contrário, esses manipuladores não serão chamados e o código não será executado.

---

## <a name="sample-activity-handler"></a>Manipulador de atividade de exemplo

Por exemplo, você pode manipular _os membros adicionados_ aos usuários de boas-vindas a uma conversa e manipular a _mensagem_ para retornar mensagens que eles enviam para o bot.

### <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[C# activity handler](~/../botbuilder-samples/samples/csharp_dotnetcore/02.echo-bot/Bots/EchoBot.cs?range=12-31)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[JavaScript activity handler](~/../botbuilder-samples/samples/javascript_nodejs/02.echo-bot/bot.js?range=6-29)]

### <a name="python"></a>[Python](#tab/python)

[!code-python[Python activity handler](~/../botbuilder-samples/samples/python/02.echo-bot/bots/echo_bot.py?range=8-19)]

---

## <a name="next-steps"></a>Próximas etapas

- O canal do Microsoft Teams apresenta algumas atividades específicas de equipes às quais seu bot precisará dar suporte para funcionar corretamente com as equipes. Para entender os principais conceitos de desenvolvimento de bots para o Microsoft Teams, confira [Como os bots do Microsoft Teams funcionam](bot-builder-basics-teams.md)
- Um manipulador de atividade é uma boa maneira de criar um bot que não precisa controlar o estado de conversação entre as ativações. A [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md) fornece maneiras de gerenciar uma conversa de execução longa com o usuário.
