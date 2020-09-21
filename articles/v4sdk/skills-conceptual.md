---
title: Visão geral de skills | Microsoft Docs
description: Descreve os conceitos de como a lógica de conversação em um bot pode ser usada por outro bot usando o SDK do bot Framework.
keywords: bot skill, host bot, skill bot, skill consumer.
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/15/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 936166bf6d07bd63141953c3644a040d0af6f71c
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824886"
---
# <a name="skills-overview"></a>Visão geral de skills

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

<!-- Value prop: why skills -->
Desde a versão 4.7 do SDK do Bot Framework, você pode estender um bot usando outro bot (um skill).
Um skill pode ser consumido por vários outros bots, facilitando a reutilização e, dessa forma, você pode criar um bot voltado para o usuário e estendê-lo por meio do consumo de seus próprios skills ou de terceiros.

<!-- Terminology -->
- Um _skill_ é um bot que pode executar um conjunto de tarefas para outro bot.
  Um bot pode ser um skill e um bot voltado para usuários.
- Um _consumidor de skill_ é um bot que pode invocar um ou mais skills.
  Um consumidor de skill voltado para usuários também é chamado de _bot raiz_.
- Um _manifesto de habilidade_ é um arquivo JSON que descreve as ações que a habilidade pode executar, os respectivos parâmetros de entrada e saída e os pontos de extremidade da habilidade.
  - Os desenvolvedores que não têm acesso ao código-fonte do skill podem usar as informações do manifesto para criar um consumidor de skill.
  - O _esquema de manifesto do skill_ é um arquivo JSON que descreve o esquema do manifesto de skill. A versão atual é [2.1.0](https://schemas.botframework.com/schemas/skills/v2.1/skill-manifest.json).
  - Confira como [implementar um skill](./skill-implement-skill.md), [escrever um manifesto de skill versão 2.1](skills-write-manifest-2-1.md) e [escrever um manifesto de skill versão 2.0](skills-write-manifest-2-0.md) para acessar exemplos de manifestos de skills.

Em outras palavras, o usuário interage diretamente com o bot raiz e o bot raiz delega parte de sua lógica de conversação a um skill.

<!-- Requirements/contract -->
O recurso dos skills é projetado de forma que:

- Skills e consumidores se comunicam por HTTP usando o protocolo Bot Framework.
- Um consumidor de skills pode consumir vários skills.
- Um consumidor de skill pode consumir um skill independentemente da linguagem usada para implementar o skill. Por exemplo, um bot C# pode consumir um skill implementado usando Python.
- Um skill também pode ser um consumidor de skill e chamar outros skills.
- Os skills são compatíveis com a autenticação de usuário; no entanto, a autenticação do usuário é local para o skill e não pode ser transferida para outro bot.
- Os skills podem trabalhar com adaptadores do Bot Framework e adaptadores personalizados.

Este diagrama mostra algumas das possíveis permutações.

![Diagrama de bloco](./media/skills-block-diagram.png)

<!--TBD: - Skills support proactive messaging. -->

## <a name="conceptual-architecture"></a>Arquitetura conceitual

Um consumidor de skills e um skill são bots diferentes e você os publica de forma independente.

- Um consumidor de habilidades requer uma lógica adicional para gerenciar uma habilidade, como quando chamar ou cancelar a habilidade, e assim por diante. Além dos objetos de bot e adaptador usuais, o consumidor inclui alguns objetos relacionados ao skill, usados para trocar atividades com o skill. Um consumidor de habilidades implementa pelo menos dois pontos de extremidade HTTP:
  - Um _ponto de extremidade de mensagens_ recebe atividades do usuário ou do canal. Esse é o ponto de extremidade de mensagens usual que todos os bots implementam.
  - Um _ponto de extremidade do host de habilidades_ para receber atividades de uma habilidade. Isso atua como uma URL de retorno de chamada, a URL de serviço para a qual a habilidade responde. (O consumidor de habilidades precisa emparelhar o código que recebe a solicitação do método HTTP da habilidade com um manipulador de habilidades.)
- Uma habilidade requer uma lógica adicional para enviar uma `endOfConversation` atividade quando ela é concluída, para que o consumidor de habilidades saiba quando parar de encaminhar atividades para a habilidade.

Este diagrama descreve o fluxo de atividades do usuário para o bot raiz, para um skill e vice-versa.

![Diagrama de arquitetura](./media/skills-conceptual-architecture.png)

1. O adaptador do bot raiz recebe atividades do usuário e as encaminha para o manipulador de atividade do bot raiz.
   (As atividades do usuário são recebidas no ponto de extremidade do sistema de mensagens do bot raiz.)
1. O bot raiz usa um cliente HTTP de skill para enviar uma atividade para o skill. O cliente obtém as informações de conversa de habilidades do consumidor de uma definição de habilidade e uma fábrica de ID de conversa de habilidade. Isso inclui a URL de serviço que será usada pelo skill para responder à atividade.
1. O adaptador de skill recebe atividades do consumidor de skill e as encaminha para o manipulador de atividades do skill.
   (As atividades do consumidor são recebidas no ponto de extremidade do sistema de mensagens do bot de habilidades.)
1. Quando o skill responde, o manipulador de skills do bot raiz recebe a atividade. Ele obtém as informações de conversa do usuário raiz do alocador de ID de conversa de skill. Em seguida, ele encaminha a atividade para o adaptador do bot raiz.
   (As atividades da habilidade são recebidas no ponto de extremidade do host de habilidades do bot raiz.)
1. O adaptador do bot raiz gera internamente uma mensagem proativa para retomar a conversa com o usuário.
1. O adaptador do bot raiz envia as mensagens do skill para o usuário.

Esses objetos ajudam a gerenciar os skills e rotear o seu tráfego:

- Um _Skill do Bot Framework_ descreve informações de roteamento para um skill e pode ser lido no arquivo de configuração do consumidor de skills.
- Um _cliente HTTP de skill_ envia atividades a um skill.
- Um _manipulador de skills_ recebe atividades de um skill.
- O _alocador de ID de conversa de skill_ faz a conversão entre a referência de conversa usuário-raiz e a referência de conversa raiz-skill.
- O serviço do Bot Connector fornece a autenticação de canal e de bot para bot. Usando um objeto de _configuração de autenticação_ , você pode adicionar a validação de declarações a um consumidor de habilidades ou habilidades para limitar quais aplicativos ou usuários têm acesso.

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

Os bots raiz e de skill se comunicam por HTTP.
Portanto, a instância do bot raiz que recebe uma atividade de um skill pode não ser a mesma instância que enviou a atividade de inicialização; em outras palavras, servidores diferentes podem manipular essas duas solicitações.

- Sempre salve o estado no consumidor de skills antes de encaminhar uma atividade para um skill.
  Isso garante que a instância que recebe o tráfego de um skill possa continuar do local em que a instância anterior parou antes de chamar o skill.
- Quando o manipulador de skills recebe uma atividade de um skill, ele a converte em um formato apropriado para o consumidor de skills e a encaminha para o adaptador do consumidor.

### <a name="skill-consumer-and-skill-state"></a>Consumidor de skills e estado de skill

O consumidor de skills e o skill gerenciam seu próprio estado separadamente. No entanto, o consumidor cria a ID de conversa que ele usa para se comunicar com o skill. Isso pode ter um efeito no estado da conversa no skill.

> [!IMPORTANT]
> Conforme observado anteriormente, quando o consumidor de skills delega uma atividade do usuário a um skill, uma instância diferente do consumidor pode receber a resposta do skill. O consumidor sempre deve salvar o estado de conversa imediatamente antes de encaminhar uma atividade para um skill.

### <a name="bot-to-bot-authentication"></a>Autenticação de bot para bot

<!-- TODO Add appropriate info about this new(?) feature to the bot basics article. -->

A autenticação de nível de serviço é gerenciada pelo serviço do Bot Connector. A estrutura usa tokens de portador e IDs de aplicativo de bot para verificar a identidade de cada bot. (A estrutura de bot usa um objeto de _configuração de autenticação_ para validar o cabeçalho de autenticação em solicitações de entrada.)

> [!IMPORTANT]
> Isso exige que todos os bots (o consumidor de habilidades e quaisquer habilidades consumidas) tenham credenciais de aplicativo válidas.

#### <a name="claims-validation"></a>Validação de declarações

Você deve adicionar um _validador de declarações_ à configuração de autenticação. As declarações são avaliadas após o cabeçalho de autenticação. Gere um erro ou uma exceção no código de validação para rejeitar a solicitação.

Há vários motivos pelos quais você pode rejeitar uma solicitação autenticada de outra forma:

- Quando o consumidor de skills deve aceitar o tráfego somente de skills com as quais ele iniciou uma conversa.
- Quando um skill faz parte de um serviço pago e os usuários que não estão no banco de dados não devem ter acesso.
- Quando você deseja restringir o acesso ao skill para consumidores de skills específicos.

> [!IMPORTANT]
> Se você não fornecer um validador de declarações, seu bot gerará um erro ou exceção ao receber uma atividade de outro bot, se o bot é uma habilidade ou um consumidor de habilidades.

<!--TODO Need a link for more information about claims and claims-based validation.-->

## <a name="additional-information"></a>Informações adicionais

Da perspectiva do usuário, ele está interagindo com o bot raiz.
Da perspectiva do skill, o consumidor de skills é o canal pelo qual ele se comunica com o usuário.

- Confira mais informações sobre bots e manifestos de skill em [sobre bots de skill](skills-about-skill-bots.md).
- Confira mais informações [sobre consumidores de skill](skills-about-skill-consumers.md).
