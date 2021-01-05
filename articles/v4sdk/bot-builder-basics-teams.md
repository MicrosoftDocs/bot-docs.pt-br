---
title: Como os bots do Microsoft Teams funcionam
description: Uma continuação do artigo sobre Como os bots funcionam específico para os bots do Microsoft Teams
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: overview
ms.service: bot-service
ms.date: 12/09/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: af9305effe31ebcd131ce0f257fda8788ab3f27b
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97758554"
---
# <a name="how-microsoft-teams-bots-work"></a>Como os bots do Microsoft Teams funcionam

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este é uma introdução que se baseia no que você aprendeu no artigo [Como os bots funcionam](https://docs.microsoft.com/azure/bot-service/bot-builder-basics). Você deve conhecer esse artigo antes de ler isso.

As principais diferenças nos bots desenvolvidos para o Microsoft Teams está em como as atividades são tratadas. O manipulador de atividades do Microsoft Teams deriva do manipulador de atividades do Bot Framework para rotear todas as atividades de equipes antes de permitir que atividades que não sejam específicas do Teams sejam tratadas.

## <a name="teams-activity-handlers"></a>Manipuladores de atividade do Teams

Assim como qualquer outro bot, quando um bot é criado para o Microsoft Teams receber uma atividade, ele a passa para seus *manipuladores de atividade*. Nos bastidores, há um manipulador base chamado de *manipulador de turno*, por meio do qual todas as atividades são roteadas. O *manipulador de turno* chama o manipulador de atividade necessário para tratar qualquer tipo de atividade que foi recebido. O bot criado para o Microsoft Teams é diferente no sentido que ele é derivado de uma classe de _manipulador de atividade Teams_ derivada da _classe de manipulador de atividade_ do Bot Framework.  A classe de manipulador de atividade do Teams inclui vários manipuladores de atividade específicos do Microsoft Teams que serão discutidos neste artigo.

### <a name="c"></a>[C#](#tab/csharp)

Como acontece com qualquer bot criado que esteja usando o Microsoft Bot Framework, se o bot receber uma atividade de mensagem, o manipulador de turno verá a atividade de entrada e a enviará para o manipulador de atividade `OnMessageActivityAsync`. Essa funcionalidade permanecerá a mesma; no entanto, se o bot receber uma atividade de atualização de conversa, o manipulador de ativação verá essa atividade de entrada e a enviará para o manipulador de atividade `OnConversationUpdateActivityAsync` do _Teams_, que verificará primeiro se há eventos específicos do Teams e a passará para o manipulador de atividade do Bot Framework, se nada for encontrado.

Na classe de manipulador de atividade do Teams, há dois manipuladores principais de atividade do Teams: `OnConversationUpdateActivityAsync` que roteia todas as atividades de atualização de conversa e `OnInvokeActivityAsync` que roteia todas as atividades de invocação do Teams.

Não há implementação base para a maioria desses manipuladores de atividade específicos do Teams. Para implementar sua lógica para esses manipuladores de atividade específicos do Teams, adicione a lógica que você deseja em sua substituição.

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Como acontece com qualquer bot criado que esteja usando o Microsoft Bot Framework, se o bot receber uma atividade de mensagem, o manipulador de turno verá a atividade de entrada e a enviará para o manipulador de atividade `onMessage`. Essa funcionalidade permanecerá a mesma; no entanto, se o bot receber uma atividade de atualização de conversa, o manipulador de ativação verá essa atividade de entrada e a enviará para o manipulador de atividade `dispatchConversationUpdateActivity` do _Teams_, que verificará primeiro se há eventos específicos do Teams e a passará para o manipulador de atividade do Bot Framework, se nada for encontrado.

Na classe de manipulador de atividade do Teams, há dois manipuladores principais de atividade do Teams: `dispatchConversationUpdateActivity` que roteia todas as atividades de atualização de conversa e `onInvokeActivity` que roteia todas as atividades de invocação do Teams.

Para cada um desses manipuladores de atividade específicos do Teams, defina sua lógica de bot e **chame `next()` no final**. Ao chamar `next()`, você faz com que o próximo manipulador seja executado.

### <a name="python"></a>[Python](#tab/python)

Como acontece com qualquer bot criado que esteja usando o Microsoft Bot Framework, se o bot receber uma atividade de mensagem, o manipulador de turno verá a atividade de entrada e a enviará para o manipulador de atividade `on_message_activity`. Essa funcionalidade permanecerá a mesma; no entanto, se o bot receber uma atividade de atualização de conversa, o manipulador de ativação verá essa atividade de entrada e a enviará para o manipulador de atividade `on_conversation_update_activity` do _Teams_, que verificará primeiro se há eventos específicos do Teams e a passará para o manipulador de atividade do Bot Framework, se nada for encontrado.

Na classe de manipulador de atividade do Teams, há dois manipuladores principais de atividade do Teams: `on_conversation_update_activity` que roteia todas as atividades de atualização de conversa e `on_invoke_activity` que roteia todas as atividades de invocação do Teams.

Não há implementação base para a maioria desses manipuladores de atividade específicos do Teams. Para implementar sua lógica para esses manipuladores de atividade específicos do Teams, adicione a lógica que você deseja em sua substituição.

---

Todos os manipuladores de atividade descritos na seção [tratamento de atividades](bot-activity-handler-concept.md#activity-handling) das _conversas controladas por evento usando um_ artigo do manipulador de atividades continuarão a funcionar como fazem com um bot que não é de equipes, com a exceção de lidar com as atividades adicionadas e removidas dos membros, que serão diferentes no contexto de uma equipe, em que o novo membro é adicionado à equipe, em oposição a um Confira a tabela _Atividades de atualização de conversa do Teams_ na seção [Lógica do bot do Teams](#teams-bot-logic) para obter mais detalhes.

Para implementar sua lógica para esses manipuladores de atividade específicos do Teams, você substituirá esses métodos em seu bot conforme mostrado na seção [Lógica do bot do Teams](#teams-bot-logic) abaixo.

### <a name="teams-bot-logic"></a>Lógica de bot do Teams

A lógica do bot processa as atividades de entrada de um ou mais canais e gera atividades de saída em resposta.  Isso ainda é verdadeiro para o bot derivado da classe do manipulador de atividade do Teams, que primeiro verifica se há atividades do Teams e, em seguida, passa todas as outras atividades para o manipulador de atividade do Bot Framework.

#### <a name="c"></a>[C#](#tab/csharp)

#### <a name="teams-conversation-update-activities"></a>Atividades de atualização de conversa de equipes

Veja abaixo uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade `OnConversationUpdateActivityAsync` do _Teams_. O artigo [Eventos de atualização de conversa](https://aka.ms/azure-bot-subscribe-to-conversation-events) descreve como usar cada um desses eventos em um bot.

| Evento | Manipulador | Descrição |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedAsync` | Substitua isso para manipular um canal do Teams que está sendo criado. Para obter mais informações, confira [Canal criado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-created). |
| channelDeleted | `OnTeamsChannelDeletedAsync` | Substitua isso para manipular um canal do Teams que está sendo excluído. Para obter mais informações, confira [Canal excluído](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-deleted). |
| channelRenamed | `OnTeamsChannelRenamedAsync` | Substitua isso para manipular um canal do Teams que está sendo renomeado. Para obter mais informações, confira [Canal renomeado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-renamed). |
| teamRenamed | `OnTeamsTeamRenamedAsync` | `return Task.CompletedTask;` Substitua isso para manipular uma equipe do Teams que está sendo renomeada. Para obter mais informações, confira [Equipe renomeada](https://aka.ms/azure-bot-subscribe-to-conversation-events#team-renamed). |
| MembersAdded | `OnTeamsMembersAddedAsync` | Chama o método `OnMembersAddedAsync` no `ActivityHandler`. Substitua isso para manipular os membros que estão entrando em uma equipe. Para obter mais informações, confira [Membro da equipe adicionado](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Added).|
| MembersRemoved | `OnTeamsMembersRemovedAsync` | Chama o método `OnMembersRemovedAsync` no `ActivityHandler`. Substitua isso para manipular os membros que estão deixando uma equipe. Para obter mais informações, confira [Membro da equipe removido](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Removed).|


<!--
| Event | Handler | Description |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedAsync` | Override this to handle a Teams channel being created. |
| channelDeleted | `OnTeamsChannelDeletedAsync` | Override this to handle a Teams channel being deleted. |
| channelRenamed | `OnTeamsChannelRenamedAsync` | Override this to handle a Teams channel being renamed. |
| teamRenamed | `OnTeamsTeamRenamedAsync` | `return Task.CompletedTask;` Override this to handle a Teams Team being Renamed. |
| MembersAdded | `OnTeamsMembersAddedAsync` | Calls the `OnMembersAddedAsync` method in `ActivityHandler`. Override this to handle members joining a team. |
| MembersRemoved | `OnTeamsMembersRemovedAsync` | Calls the `OnMembersRemovedAsync` method in `ActivityHandler`. Override this to handle members leaving a team. |
-->

#### <a name="teams-invoke--activities"></a>Atividades de invocação do Teams

Veja uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade `OnInvokeActivityAsync` do _Teams_:

| Invocar tipos                    | Manipulador                              | Descrição                                                  |
| :-----------------------------  | :----------------------------------- | :----------------------------------------------------------- |
| CardAction.Invoke               | `OnTeamsCardActionInvokeAsync`       | Invocação de Ação de Cartão do Teams. |
| fileConsent/invoke              | `OnTeamsFileConsentAcceptAsync`      | Aceitação de Consentimento do Arquivo do Teams. |
| fileConsent/invoke              | `OnTeamsFileConsentAsync`            | Consentimento do Arquivo do Teams. |
| fileConsent/invoke              | `OnTeamsFileConsentDeclineAsync`     | Consentimento do Arquivo do Teams. |
| actionableMessage/executeAction | `OnTeamsO365ConnectorCardActionAsync` | Ação de Cartão do Conector O365 do Teams. |
| signin/verifyState              | `OnTeamsSigninVerifyStateAsync`      | Estado de Verificação de Entrada do Teams. |
| task/fetch                      | `OnTeamsTaskModuleFetchAsync`        | Busca de Módulo de Tarefa do Teams. |
| task/submit                     | `OnTeamsTaskModuleSubmitAsync`       | Envio de Módulo de Tarefa do Teams. |

As atividades de invocação listadas acima se destinam a bots conversacionais no Teams. O SDK do Bot Framework também dá suporte a invocações específicas para extensões de mensagens. Para obter mais informações, confira [O que são extensões de mensagens](https://aka.ms/azure-bot-what-are-messaging-extensions)

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="teams-conversation-update-activities"></a>Atividades de atualização de conversa de equipes

Os desenvolvedores podem lidar com atividades de atualização de conversa enviadas do Microsoft Teams por meio de dois métodos:

1. Para passar um retorno de chamada, use métodos que comecem com `on` _e_ terminem com `Event` (por exemplo, o `onTeamsMembersAddedEvent` método).
1. Ao criar uma classe derivada, substitua os métodos que começam com `on` e _não_ terminem com `Event` (por exemplo, o `onTeamsMembersAdded` método).

Os desenvolvedores devem usar apenas uma dessas opções: 1 ou 2, e não _ambos_ para a mesma atividade. Ou seja, os desenvolvedores devem passar um retorno de chamada para o `onTeamsMembersAddedEvent` método *ou* substituir o `onTeamsMembersAdded` método em uma classe derivada e não fazer ambos.

**Métodos para passar um retorno de chamada**

Veja abaixo uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade `dispatchConversationUpdateActivity` do _Teams_. O artigo [Eventos de atualização de conversa](https://aka.ms/azure-bot-subscribe-to-conversation-events) descreve como usar cada um desses eventos em um bot.

| Evento | Manipulador | Descrição |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedEvent` | Passe um retorno de chamada para esse método para manipular um canal de equipes que está sendo criado. Para obter mais informações, confira [Canal criado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-created). |
| channelDeleted | `OnTeamsChannelDeletedEvent` | Passe um retorno de chamada para esse método para manipular um canal de equipes que está sendo excluído. Para obter mais informações, confira [Canal excluído](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-deleted).|
| channelRenamed | `OnTeamsChannelRenamedEvent` | Passe um retorno de chamada para esse método para manipular um canal de equipes que está sendo renomeado. Para obter mais informações, confira [Canal renomeado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-renamed). |
| teamRenamed | `OnTeamsTeamRenamedEvent` | `return Task.CompletedTask;` Passe um retorno de chamada para esse método para manipular uma equipe de equipes que está sendo renomeada. Para obter mais informações, confira [Equipe renomeada](https://aka.ms/azure-bot-subscribe-to-conversation-events#team-renamed). |
| MembersAdded | `OnTeamsMembersAddedEvent` | Chama o método `OnMembersAddedEvent` no `ActivityHandler`. Passe um retorno de chamada para esse método para lidar com membros que ingressaram em uma equipe. Para obter mais informações, confira [Membro da equipe adicionado](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Added). |
| MembersRemoved | `OnTeamsMembersRemovedEvent` | Chama o método `OnMembersRemovedEvent` no `ActivityHandler`. Passe um retorno de chamada para esse método para lidar com membros que saem de uma equipe. Para obter mais informações, confira [Membro da equipe removido](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Removed). |

<!--
| Event | Handler | Description |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedEvent` | Override this to handle a Teams channel being created. |
| channelDeleted | `OnTeamsChannelDeletedEvent` | Override this to handle a Teams channel being deleted. |
| channelRenamed | `OnTeamsChannelRenamedEvent` | Override this to handle a Teams channel being renamed. |
| teamRenamed | `OnTeamsTeamRenamedEvent` | `return Task.CompletedTask;` Override this to handle a Teams Team being Renamed. |
| MembersAdded | `OnTeamsMembersAddedEvent` | Calls the `OnMembersAddedEvent` method in `ActivityHandler`. Override this to handle members joining a team. |
| MembersRemoved | `OnTeamsMembersRemovedEvent` | Calls the `OnMembersRemovedEvent` method in `ActivityHandler`. Override this to handle members leaving a team. |
-->

**Métodos a serem substituídos em uma classe derivada**

Abaixo está uma lista de todos os manipuladores de atividade de equipes que podem ser substituídos para lidar com atividades de atualização de conversa de equipes.

| Método | Manipulador | Descrição |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreated` | Substitua isso para manipular um canal do Teams que está sendo criado.|
| channelDeleted | `OnTeamsChannelDeletedEvent` | Substitua isso para manipular um canal do Teams que está sendo excluído.|
| channelRenamed | `OnTeamsChannelRenamedEvent` | Substitua isso para manipular um canal do Teams que está sendo renomeado.|
| teamRenamed | `OnTeamsTeamRenamedEvent` | `return Task.CompletedTask;` Substitua isso para manipular uma equipe do Teams que está sendo renomeada.|
| MembersAdded | `OnTeamsMembersAddedEvent` | Chama o método `OnMembersAddedEvent` no `ActivityHandler`. Substitua isso para manipular os membros que estão entrando em uma equipe.|
| MembersRemoved | `OnTeamsMembersRemovedEvent` | Chama o método `OnMembersRemovedEvent` no `ActivityHandler`. Substitua isso para manipular os membros que estão deixando uma equipe.|

#### <a name="teams-invoke--activities"></a>Atividades de invocação do Teams

Veja uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade `onInvokeActivity` do _Teams_:

| Invocar tipos                    | Manipulador                              | Descrição                                                  |
| :-----------------------------  | :----------------------------------- | :----------------------------------------------------------- |
| CardAction.Invoke               | `handleTeamsCardActionInvoke`       | Invocação de Ação de Cartão do Teams. |
| fileConsent/invoke              | `handleTeamsFileConsentAccept`      | Aceitação de Consentimento do Arquivo do Teams. |
| fileConsent/invoke              | `handleTeamsFileConsent`            | Consentimento do Arquivo do Teams. |
| fileConsent/invoke              | `handleTeamsFileConsentDecline`     | Consentimento do Arquivo do Teams. |
| actionableMessage/executeAction | `handleTeamsO365ConnectorCardAction` | Ação de Cartão do Conector O365 do Teams. |
| signin/verifyState              | `handleTeamsSigninVerifyState`      | Estado de Verificação de Entrada do Teams. |
| task/fetch                      | `handleTeamsTaskModuleFetch`        | Busca de Módulo de Tarefa do Teams. |
| task/submit                     | `handleTeamsTaskModuleSubmit`       | Envio de Módulo de Tarefa do Teams. |

As atividades de invocação listadas acima se destinam a bots conversacionais no Teams. O SDK do Bot Framework também dá suporte a invocações específicas para extensões de mensagens. Para obter mais informações, confira [O que são extensões de mensagens](https://aka.ms/azure-bot-what-are-messaging-extensions)

#### <a name="python"></a>[Python](#tab/python)

#### <a name="teams-conversation-update-activities"></a>Atividades de atualização de conversa de equipes

Veja abaixo uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade `on_conversation_update_activity` do _Teams_. O artigo [Eventos de atualização de conversa](https://aka.ms/azure-bot-subscribe-to-conversation-events) descreve como usar cada um desses eventos em um bot.

| Evento | Manipulador | Descrição |
| :-- | :-- | :-- |
| channelCreated | `on_teams_channel_created` | Substitua isso para manipular um canal do Teams que está sendo criado. Para obter mais informações, confira [Canal criado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-created). |
| channelDeleted | `on_teams_channel_deleted` | Substitua isso para manipular um canal do Teams que está sendo excluído. Para obter mais informações, confira [Canal excluído](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-deleted). |
| channelRenamed | `on_teams_team_renamed_activity` | Substitua isso para manipular um canal do Teams que está sendo renomeado. Para obter mais informações, confira [Canal renomeado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-renamed). |
| teamRenamed | `on_teams_channel_renamed` | `return Task.CompletedTask;` Substitua isso para manipular uma equipe do Teams que está sendo renomeada. Para obter mais informações, confira [Equipe renomeada](https://aka.ms/azure-bot-subscribe-to-conversation-events#team-renamed). |
| MembersAdded | `on_teams_members_added` | Chama o método `OnMembersAddedAsync` no `ActivityHandler`. Substitua isso para manipular os membros que estão entrando em uma equipe. Para obter mais informações, confira [Membro da equipe adicionado](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Added).|
| MembersRemoved | `on_teams_members_removed` | Chama o método `OnMembersRemovedAsync` no `ActivityHandler`. Substitua isso para manipular os membros que estão deixando uma equipe. Para obter mais informações, confira [Membro da equipe removido](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Removed).|

#### <a name="teams-invoke--activities"></a>Atividades de invocação do Teams

Veja uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade `on_invoke_activity` do _Teams_:

| Invocar tipos                    | Manipulador                              | Descrição
| :-----------------------------  | :----------------------------------- | :-
| CardAction.Invoke               | `on_teams_card_action_invoke`        | Invocação de Ação de Cartão do Teams.
| fileConsent/invoke              | `on_teams_file_consent_accept`       | Aceitação de Consentimento do Arquivo do Teams.
| fileConsent/invoke              | `on_teams_file_consent`              | Consentimento do Arquivo do Teams.
| fileConsent/invoke              | `on_teams_file_consent_decline`      | Recusa de Consentimento do Arquivo do Teams.
| actionableMessage/executeAction | `on_teams_o365_connector_card_action`| Ação de Cartão do Conector O365 do Teams.
| signin/verifyState              | `on_teams_signin_verify_state`       | Estado de Verificação de Entrada do Teams.
| task/fetch                      | `on_teams_task_module_fetch`         | Busca de Módulo de Tarefa do Teams.
| task/submit                     | `on_teams_task_module_submit`        | Envio de Módulo de Tarefa do Teams.

As atividades de invocação listadas acima se destinam a bots conversacionais no Teams. O SDK do Bot Framework também dá suporte a invocações específicas para extensões de mensagens. Para obter mais informações, confira [O que são extensões de mensagens](https://aka.ms/azure-bot-what-are-messaging-extensions)

---

## <a name="next-steps"></a>Próximas etapas

Para criar bots do Teams, consulte a [documentação](https://aka.ms/teams-docs) do Desenvolvedor do Microsoft Teams.
