---
title: Sobre os skills | Microsoft Docs
description: Descreve como a lógica de conversação de um bot pode ser usada por outro bot usando o SDK do Bot Framework.
keywords: bot skill, host bot, skill bot.
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/09/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e4f11b6a1e8bcfce06e1518db7865d6b65ec3d62
ms.sourcegitcommit: 4e1af50bd46debfdf9dcbab9a5d1b1633b541e27
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2020
ms.locfileid: "76753770"
---
# <a name="about-skills"></a>Sobre os skills

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

<!-- Value prop: why skills -->
Desde a versão 4.7 do SDK do Bot Framework, você pode estender um bot usando outro bot (um skill).
Um skill pode ser consumido por vários outros bots, facilitando a reutilização e, dessa forma, você pode criar um bot voltado para o usuário e estendê-lo por meio do consumo de seus próprios skills ou de terceiros.

<!-- Terminology -->
- Um _skill_ é um bot que pode executar um conjunto de tarefas para outro bot e que usa um manifesto para descrever sua interface.
  Dependendo do design, um skill também pode funcionar como um bot típico voltado para o usuário.
- Um _consumidor de skill_ é um bot que pode invocar um ou mais skills. Em relação aos skills, um _bot raiz_ é um bot voltado para o usuário que também é um consumidor de skills.
- Um _manifesto de skill_ é um arquivo JSON que descreve as atividades que o skill pode executar, seus parâmetros de entrada e saída e os pontos de extremidade do skill.
  - Os desenvolvedores que não têm acesso ao código-fonte do skill podem usar as informações do manifesto para criar um consumidor de skill. Veja como [implementar um skill](./skill-implement-skill.md) para um exemplo de manifesto de skill.
  - O _esquema de manifesto do skill_ é um arquivo JSON que descreve o esquema do manifesto de skill. A versão atual é [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

Em outras palavras, o usuário interage diretamente com o bot raiz e o bot raiz delega parte de sua lógica de conversação a um skill.

<!-- Requirements/contract -->
O recurso dos skills é projetado de forma que:

- Os skills podem trabalhar com adaptadores do Bot Framework e adaptadores personalizados.
- Os skills podem trabalhar com o canal do Microsoft Teams, que faz uso intensivo de atividades de `invoke`.
- Os skills são compatíveis com a autenticação de usuário; no entanto, a autenticação do usuário é local para o skill e não pode ser transferida para outro bot.
- Um consumidor de skills pode consumir vários skills.
- Um consumidor de skills pode executar vários skills em paralelo.
- O serviço do Bot Connector fornece autenticação de bot para bot; no entanto, você pode testar um bot raiz localmente usando o Emulador.
<!--TBD: - Skills support proactive messaging. -->
<!--TBD: - A skill can also be a skill consumer. -->

## <a name="architecture"></a>Arquitetura

Um consumidor de skills e um skill são bots diferentes e você os publica de forma independente.

- O consumidor de skills e o skill se comunicam por HTTP.
- Um consumidor de skills pode invocar mais de um skill.
- Um skill pode ser invocado por mais de um consumidor.

Um skill precisa incluir lógica adicional para enviar uma atividade `endOfConversation` quando ela for concluída, para que o consumidor de skills saiba quando parar de encaminhar atividades para o skill.

Um bot raiz implementa pelo menos dois pontos de extremidade HTTP, um para receber atividades do usuário e outro para receber atividades de skills. O consumidor de skills precisa emparelhar o código que recebe a solicitação do método HTTP com um manipulador de skills.
Ele requer uma lógica adicional para gerenciar um skill, como quando invocar ou cancelar o skill e assim por diante. Além dos objetos de bot e adaptador usuais, o consumidor inclui alguns objetos relacionados ao skill, usados para trocar atividades com o skill.

Este diagrama descreve o fluxo de atividades do usuário para o bot raiz, para um skill e vice-versa.

![Diagrama de arquitetura](./media/skills-conceptual-architecture.png)

1. O adaptador do bot raiz recebe atividades do usuário e as encaminha para o manipulador de atividade do bot raiz.
1. O bot raiz usa um cliente HTTP de skill para enviar uma atividade para o skill. Ele obtém as informações de conversa do skill consumidor de uma definição de skill e um alocador de ID de conversa de skill. Isso inclui a URL de serviço que será usada pelo skill para responder à atividade.
1. O adaptador de skill recebe atividades do consumidor de skill e as encaminha para o manipulador de atividades do skill.
1. Quando o skill responde, o manipulador de skills do bot raiz recebe a atividade. Ele obtém as informações de conversa do usuário raiz do alocador de ID de conversa de skill. Em seguida, ele encaminha a atividade para o adaptador do bot raiz.
1. O adaptador do bot raiz gera internamente uma mensagem proativa para retomar a conversa com o usuário.
1. O adaptador do bot raiz envia as mensagens do skill para o usuário.

Esses objetos ajudam a gerenciar os skills e rotear o seu tráfego:

- Um _Skill do Bot Framework_ descreve informações de roteamento para um skill e pode ser lido no arquivo de configuração do consumidor de skills.
- Um _cliente HTTP de skill_ envia atividades a um skill.
- Um _manipulador de skills_ recebe atividades de um skill.
- O _alocador de ID de conversa de skill_ faz a conversão entre a referência de conversa usuário-raiz e a referência de conversa raiz-skill.
- O serviço do Bot Connector fornece a autenticação de canal e de bot para bot. Usando um objeto de _configuração de autenticação_, você pode adicionar validação de declarações a um bot (skill ou consumidor de skills) para limitar quais aplicativos ou usuários têm acesso.

Os objetos de cliente de skill e de manipulador de skill usam o _alocador de ID de conversa_ para converter entre a conversa que o bot raiz usa para interagir com o usuário e a conversa que o bot raiz usa para interagir com o skill.

## <a name="bot-to-bot-communication"></a>Comunicação de bot para bot

É importante entender determinados aspectos desse design, independentemente do bot que você está criando.

<!--
- infrastructure concerns:
  - stateless, cross-server application (memory management and middleware).
  - authentication, in both directions, plus claims validation.
- implementation concerns:
  - classes, objects, and logic you need to add to your host (and skill).
  - when to start and stop a skill.
  - managing multiple skills.
-->

### <a name="conversation-references"></a>Referências de conversa

A conversa usuário-raiz é diferente da conversa raiz-skill.

O _alocador de ID de conversa_ ajuda a gerenciar o tráfego entre um consumidor de skills e um skill. O alocador converte entre a ID de conversa que a raiz tem com o usuário e a que ela tem com o skill.
Em outras palavras, ele gera uma ID de conversa para uso entre a raiz e o skill e recupera a ID de conversa usuário-raiz original da ID de conversa raiz-skill.

<!-- Hopefully, this just gets folded into the SDK and does not need to get described in detail.
- The host needs to save or encode original conversation ID and service URL and create a conversation ID for use between it and the skill.
  - Generated conversation IDs must be usable as a URL path parameter.
  - A modified activity gets the new conversation ID and service URL (of the host, as the host provides a channel interface to the skill).
- Upon receiving an activity from the skill, host needs to decode or recover the original conversation ID and service URL so that the activity can get forwarded back to the user in the original conversation.
-->

### <a name="cross-server-coordination"></a>Coordenação entre servidores
<!-- or, Statelessness in the host -->

A raiz e o skill se comunicam por HTTP.
Portanto, a instância raiz que recebe uma atividade de um skill pode não ser a mesma instância que enviou a atividade de inicialização; em outras palavras, servidores diferentes podem manipular essas duas solicitações.

- Sempre salve o estado no consumidor de skills antes de encaminhar uma atividade para um skill.
  Isso garante que a instância que recebe o tráfego de um skill possa continuar do local em que a instância anterior parou antes de invocar o skill.
- Quando o manipulador de skills recebe uma atividade de um skill, ele a converte em um formato apropriado para o consumidor de skills e a encaminha para o adaptador do consumidor.

### <a name="skill-consumer-and-skill-state"></a>Consumidor de skills e estado de skill

O consumidor de skills e o skill gerenciam seu próprio estado separadamente. No entanto, o consumidor cria a ID de conversa que ele usa para se comunicar com o skill. Isso pode ter um efeito no estado da conversa no skill.

> [!IMPORTANT]
> Conforme observado anteriormente, quando o consumidor de skills delega uma atividade do usuário a um skill, uma instância diferente do consumidor pode receber a resposta do skill. O consumidor sempre deve salvar o estado de conversa imediatamente antes de encaminhar uma atividade para um skill.

### <a name="bot-to-bot-authentication"></a>Autenticação de bot para bot

<!-- TODO Add appropriate info about this new(?) feature to the bot basics article. -->

A autenticação de nível de serviço é gerenciada pelo serviço do Bot Connector. A estrutura usa tokens de portador e IDs de aplicativo de bot para verificar a identidade de cada bot.

O Bot Framework usa um objeto de _configuração de autenticação_ para validar o cabeçalho de autenticação em solicitações de entrada.

#### <a name="claims-validation"></a>Validação de declarações

Você pode adicionar um _validador de declarações_ à configuração de autenticação. As declarações são avaliadas após o cabeçalho de autenticação. O código de validação deve gerar um erro ou uma exceção para rejeitar a solicitação.

Há vários motivos pelos quais você pode rejeitar uma solicitação autenticada de outra forma:

- Quando o consumidor de skills deve aceitar o tráfego somente de skills com as quais ele iniciou uma conversa.
- Quando um skill faz parte de um serviço pago e os usuários que não estão no banco de dados não devem ter acesso.
- Quando você deseja restringir o acesso ao skill para consumidores de skills específicos.

<!--TODO Need a link for more information about claims and claims-based validation.-->

## <a name="skill-consumers"></a>Consumidores de skills

<!--
### Skill consumer design
Supports: Bot Framework adapter, custom adapters, MS Teams channel (what Teams calls "proactive" 1-1 conversations, created off a group/channel/team conversation)
-->

Da perspectiva do usuário, o bot raiz é o bot com o qual ele está interagindo.
Da perspectiva do skill, o consumidor de skills é o canal pelo qual ele se comunica com o usuário.

Como um consumidor de skills, um bot raiz inclui algumas lógicas adicionais para gerenciar o tráfego entre ele e um skill:

- Informações de configuração para cada skill que a raiz usa.
- Um _alocador de ID de conversa_ que permite que a alterne entre a conversa que está tendo com o usuário e a que está tendo com um skill.
- Um _cliente de skills_ que pode empacotar e encaminhar atividades para um bot de skills.
- Um _manipulador de skills_ que pode receber solicitações e desempacotar atividades de um bot skill.

### <a name="managing-skills"></a>Gerenciando skills

Dar início e permitir que um único skill seja executado até a conclusão é gerenciado com algumas adições ao host. Cenários mais complicados são possíveis, com vários skills ou threads de conversa.

#### <a name="skill-descriptions"></a>Descrições de skills

Para cada skill, adicione um objeto de _skill do Bot Framework_ ao arquivo de configuração do consumidor de skills. Cada um terá uma ID, uma ID do aplicativo e o ponto de extremidade para o skill.

| Propriedade | Descrição
| :--- | :--- |
| _ID_ | A ID ou a chave do skill, específica ao consumidor de skills. |
| _ID do Aplicativo_ | O `appId` atribuído ao recurso bot quando o skill foi registrado no Azure. |
| _Ponto de extremidade de skill_ | O ponto de extremidade de mensagens para o skill. Essa é a URL que o consumidor usará para se comunicar com o skill. |

#### <a name="skill-client-and-skill-handler"></a>Cliente de skills e manipulador de skills

<!-- Is this still accurate? -->
O consumidor de skills usa um cliente de skills para enviar atividades a um skill. O cliente:

- Obtém uma atividade para encaminhar para o skill, seja de um usuário ou gerada pelo consumidor.
- Substitui a referência de conversa original por uma para a conversa consumidora de skills.
- Adiciona um token de autenticação de bot para bot.
- Envia a atividade atualizada para o skill.

O consumidor de skills usa um manipulador de skills para receber atividades de um skill. O manipulador:

- Manipula os métodos `SendToConversation` e `ReplyToActivity` da API REST do serviço de canal.
- Impõe a validação de autenticação e declarações.
- Recupera a referência de conversa original.
- Gera uma atividade para o adaptador do consumidor. Essa atividade sinalizará que o skill foi concluído ou que será uma atividade a ser encaminhada ao usuário.

<!-- These section should probably reference a how-to. -->
#### <a name="managing-a-skill-from-a-skill-consumer"></a>Gerenciando um skill de um consumidor de skills

Você precisa adicionar lógica ao consumidor de skills para acompanhar os skills ativos.
Cabe ao consumidor saber ele gerencia os skills em geral, se ele pode manter vários skills ativos paralelamente ou não, e assim por diante.
Cenários específicos a serem considerados incluem:

- Inicio de uma nova conversa consumidora de skills. (Isso será associado a uma conversa específica de consumidor usuário).
  - Para passar parâmetros para um skill, defina a propriedade _valor_ na atividade inicial para o skill.
- Continuação de uma conversa consumidora de skills existente.
- Reconhecimento de uma atividade `endOfConversation` do skill como sinalizadora do fim de uma conversa consumidora de skills.
  - Para recuperar qualquer valor retornado de um skill, verifique a propriedade _valor_ da atividade.
  - Para verificar por que o skill está terminando, verifique o parâmetro _código_ da atividade, que pode indicar que o skill encontrou um erro.
- Cancelamento de um skill do consumidor enviando uma atividade `endOfConversation` para o skill.

## <a name="skill-bots"></a>Bots de skills

Com pequenas modificações, qualquer bot pode agir como um skill. Bots de skills:

- Implemente restrições de acesso por meio de um validador de declarações.
- Conforme apropriado, verifique os parâmetros de inicialização na propriedade _valor_ da atividade inicial.
- Envie mensagens para o usuário normalmente, por meio de atividades `message`.
- Sinalize a conclusão ou o cancelamento do skill por meio de uma atividade `endOfConversation`.
  - Forneça o valor retornado, se houver, na propriedade _valor_ da atividade.
  - Forneça um código de erro, se houver, na propriedade _código_ da atividade.

### <a name="skill-manifest"></a>Manifesto de skills

Como um consumidor de skills não tem necessariamente acesso ao código do skill, use um manifesto de skills para descrever as atividades que o skill pode receber e gerar, seus parâmetros de entrada e saída e os pontos de extremidade do skill. Para o esquema do manifesto de skill, consulte [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="additional-information"></a>Informações adicionais

Para saber mais sobre como implementar bots de skills e consumidores de skills, confira como [implementar um skill](skill-implement-skill.md) e como [implementar um consumidor de skills](skill-implement-consumer.md).

<!--
You publish skill and skill consumer bots separately. For more information, see _TBD: link to create a skills bot from scratch how-to or tutorial_.

For more about skill manifests, see the _TBD: creating a manifest section of implementing a skill bot_.
-->
