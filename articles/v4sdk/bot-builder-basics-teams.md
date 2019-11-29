---
title: Como os bots do Microsoft Teams funcionam
description: Uma continuação do artigo sobre Como os bots funcionam específico para os bots do Microsoft Teams
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: overview
ms.service: bot-service
ms.date: 10/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3a7e60e1fa72402b5f783676f5281579a2be5371
ms.sourcegitcommit: 490810d278d1c8207330b132f28a5eaf2b37bd07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73592204"
---
# <a name="how-microsoft-teams-bots-work"></a>Como os bots do Microsoft Teams funcionam

Este é uma introdução que se baseia no que você aprendeu no artigo [Como os bots funcionam](https://docs.microsoft.com/azure/bot-service/bot-builder-basics). Você deve conhecer esse artigo antes de ler isso.

As principais diferenças nos bots desenvolvidos para o Microsoft Teams está em como as atividades são tratadas. O manipulador de atividades do Microsoft Teams deriva do manipulador de atividades do Bot Framework para rotear todas as atividades de equipes antes de permitir que atividades específicas que não sejam do Teams sejam tratadas.

## <a name="teams-activity-handlers"></a>Manipuladores de atividade do Teams

Assim como qualquer outro bot, quando um bot é criado para o Microsoft Teams receber uma atividade, ele a passa para seus *manipuladores de atividade*. Nos bastidores, há um manipulador base chamado de *manipulador de turno*, por meio do qual todas as atividades são roteadas. O *manipulador de turno* chama o manipulador de atividade necessário para tratar qualquer tipo de atividade que foi recebido. O bot criado para o Microsoft Teams é diferente no sentido que ele é derivado de uma classe `Activity Handler` do _Teams_ derivada da classe `Activity Handler` do Bot Framework.  A classe `Activity Handler` do _Teams_ inclui vários manipuladores de atividade específicos do Microsoft Teams que serão discutidos neste artigo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Como acontece com qualquer bot criado que esteja usando o Microsoft Bot Framework, se o bot receber uma atividade de mensagem, o manipulador de turno verá a atividade de entrada e a enviará para o manipulador de atividade `OnMessageActivityAsync`. Essa funcionalidade permanece a mesma; no entanto, se o bot receber uma atividade de atualização de conversa, o manipulador de ativação verá essa atividade de entrada e a enviará para o manipulador de atividade do `OnConversationUpdateActivityAsync` _Teams_, que verificará primeiro se há eventos específicos do Teams e a passará para o manipulador de atividade do Bot Framework, se nenhum for encontrado.

Na classe de manipulador de atividade do Teams, há dois manipuladores principais de atividade do Teams: `OnConversationUpdateActivityAsync` que roteia todas as atividades de atualização de conversa e `OnInvokeActivityAsync` que roteia todas as atividades de invocação do Teams.  Todos os manipuladores de atividade descritos na seção [Lógica do bot](https://aka.ms/how-bots-work#bot-logic) do artigo `How bots work` continuarão funcionando como acontece com um bot que não é do Teams, com exceção da manipulação das atividades adicionadas e removidas por membros. Estas serão diferentes no contexto de uma equipe, em que o novo membro é adicionado a ela diferentemente de um thread de mensagem.  Confira a tabela _Atividades de atualização de conversa do Teams_ na seção [Lógica do bot](#bot-logic) para obter mais detalhes.

Para implementar sua lógica desses manipuladores de atividade específicos do Teams, você substituirá esses métodos em seu bot conforme mostrado na seção [Lógica do bot](#bot-logic) abaixo. Para cada um desses manipuladores, não há nenhuma implementação base e, portanto, basta adicionar a lógica desejada na substituição.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Como acontece com qualquer bot criado que esteja usando o Microsoft Bot Framework, se o bot receber uma atividade de mensagem, o manipulador de turno verá a atividade de entrada e a enviará para o manipulador de atividade `onMessage`. Essa funcionalidade permanece a mesma; no entanto, se o bot receber uma atividade de atualização de conversa, o manipulador de ativação verá essa atividade de entrada e a enviará para o manipulador de atividade do `dispatchConversationUpdateActivity` _Teams_, que verificará primeiro se há eventos específicos do Teams e a passará para o manipulador de atividade do Bot Framework, se nenhum for encontrado.

Na classe de manipulador de atividade do Teams, há dois manipuladores principais de atividade do Teams: `dispatchConversationUpdateActivity` que roteia todas as atividades de atualização de conversa e `onInvokeActivity` que roteia todas as atividades de invocação do Teams.  Todos os manipuladores de atividade descritos na seção [Lógica do bot](https://aka.ms/how-bots-work#bot-logic) do artigo `How bots work` continuarão funcionando como acontece com um bot que não é do Teams, com exceção da manipulação das atividades adicionadas e removidas por membros. Estas serão diferentes no contexto de uma equipe, em que o novo membro é adicionado a ela diferentemente de um thread de mensagem.  Confira a tabela _Atividades de atualização de conversa do Teams_ na seção [Lógica do bot](#bot-logic) para obter mais detalhes.

Assim como acontece com qualquer manipulador de bot, para implementar sua lógica para os manipuladores do Teams, você substituirá os métodos em seu bot conforme visto na seção [Lógica do bot](#bot-logic) abaixo. Para cada um desses manipuladores, defina a lógica do bot e **não se esqueça de chamar `next()` no final**. Ao chamar `next()`, você faz com que o próximo manipulador seja executado.

---

### <a name="bot-logic"></a>Lógica do bot

A lógica do bot processa as atividades de entrada de um ou mais canais e gera atividades de saída em resposta.  Isso ainda é verdadeiro para o bot derivado da classe do manipulador de atividade do Teams, que primeiro verifica se há atividades do Teams e, em seguida, passa todas as outras atividades para o manipulador de atividade do Bot Framework.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="teams-conversation-update-activities"></a>Atividades de atualização de conversa de equipes

Veja abaixo uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade do _Teams_ `OnConversationUpdateActivityAsync`. O artigo [Eventos de atualização de conversa](https://aka.ms/azure-bot-subscribe-to-conversation-events) descreve como usar cada um desses eventos em um bot.

| Evento | Manipulador | DESCRIÇÃO |
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

Veja uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade do _Teams_ `OnInvokeActivityAsync`:

| Invocar tipos                    | Manipulador                              | DESCRIÇÃO                                                  |
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="teams-conversation-update-activities"></a>Atividades de atualização de conversa de equipes

Veja abaixo uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade do _Teams_ `dispatchConversationUpdateActivity`. O artigo [Eventos de atualização de conversa](https://aka.ms/azure-bot-subscribe-to-conversation-events) descreve como usar cada um desses eventos em um bot.

| Evento | Manipulador | DESCRIÇÃO |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedEvent` | Substitua isso para manipular um canal do Teams que está sendo criado. Para obter mais informações, confira [Canal criado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-created). |
| channelDeleted | `OnTeamsChannelDeletedEvent` | Substitua isso para manipular um canal do Teams que está sendo excluído. Para obter mais informações, confira [Canal excluído](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-deleted).|
| channelRenamed | `OnTeamsChannelRenamedEvent` | Substitua isso para manipular um canal do Teams que está sendo renomeado. Para obter mais informações, confira [Canal renomeado](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-renamed). |
| teamRenamed | `OnTeamsTeamRenamedEvent` | `return Task.CompletedTask;` Substitua isso para manipular uma equipe do Teams que está sendo renomeada. Para obter mais informações, confira [Equipe renomeada](https://aka.ms/azure-bot-subscribe-to-conversation-events#team-renamed). |
| MembersAdded | `OnTeamsMembersAddedEvent` | Chama o método `OnMembersAddedEvent` no `ActivityHandler`. Substitua isso para manipular os membros que estão entrando em uma equipe. Para obter mais informações, confira [Membro da equipe adicionado](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Added). |
| MembersRemoved | `OnTeamsMembersRemovedEvent` | Chama o método `OnMembersRemovedEvent` no `ActivityHandler`. Substitua isso para manipular os membros que estão deixando uma equipe. Para obter mais informações, confira [Membro da equipe removido](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Removed). |

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

#### <a name="teams-invoke--activities"></a>Atividades de invocação do Teams

Veja uma lista de todos os manipuladores de atividade do Teams chamados do manipulador de atividade do _Teams_ `onInvokeActivity`:

| Invocar tipos                    | Manipulador                              | DESCRIÇÃO                                                  |
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

---

## <a name="next-steps"></a>Próximas etapas
Para criar bots do Teams, consulte a [documentação](https://aka.ms/teams-docs) do Desenvolvedor do Microsoft Teams.