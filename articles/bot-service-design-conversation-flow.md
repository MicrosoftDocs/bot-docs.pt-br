---
title: Criar e controlar o fluxo de conversa – Serviço de Bot
description: Saiba como fornecer uma boa experiência do usuário com bots. Compreenda o fluxo de conversa de procedimento, a manipulação de interrupções e outros conceitos de design.
keywords: design, controle, fluxo da conversa, manipular interrupções, visão geral
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/19/2019
ms.openlocfilehash: 446ed26854d503aa60fa29f957ce1c35d053b30a
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92415146"
---
# <a name="design-and-control-conversation-flow"></a>Projetar e controlar o fluxo da conversa

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Em um aplicativo tradicional, a interface do usuário consiste em uma série de telas e um único aplicativo ou site pode usar uma ou mais telas, conforme necessário, para trocar informações com o usuário.
A maioria dos aplicativos começa com uma tela principal em que os usuários acessam primeiro e essa tela fornece uma navegação que leva a outras telas para várias funções, como o início de um novo pedido, a navegação de produtos ou a busca por ajuda.

Assim como aplicativos e sites, os bots têm uma interface do usuário, mas ela é composta por **mensagens**, em vez de telas. As mensagens podem conter botões, texto e outros elementos, ou ser totalmente baseados na fala.

Embora um aplicativo ou um site tradicional possa solicitar várias informações em uma tela ao mesmo tempo, um bot coletará a mesma quantidade de informações usando várias mensagens. Dessa forma, o processo de coleta de informações do usuário é uma experiência ativa: aquela em que o usuário está tendo uma conversa ativa com o bot.

Um bot bem projetado terá um fluxo de conversa que parece natural. O bot deve conseguir lidar com a conversa central de forma contínua e manipular as interrupções ou mudar um tópico de conversa normalmente.

## <a name="procedural-conversation-flow"></a>Fluxo da conversa de procedimento

Geralmente, a conversa com um bot se concentra na tarefa que um bot está tentando concluir, que é chamado de fluxo de procedimento. Esse é o momento em que o bot solicita ao usuário uma série de perguntas para coletar todas as informações necessárias antes de processar a tarefa.

Em um fluxo de conversa de procedimento, você define a ordem das perguntas e o bot fará as perguntas na ordem definida. Você pode organizar as perguntas em *módulos* lógicos para manter o código centralizado e permanecer concentrado guiando a conversa. Por exemplo, você pode criar um módulo para conter a lógica que ajuda o usuário a navegar pelos produtos e um módulo separado para conter a lógica que ajuda o usuário a criar um pedido.

Você pode estruturar esses módulos para fluírem da maneira desejada, variando de forma livre para sequencial. O SDK do Bot Framework fornece várias bibliotecas que permitem construir qualquer fluxo de conversa necessário para o bot. Por exemplo, a biblioteca `prompts` permite que você solicite aos usuários a entrada, a biblioteca `waterfall` permite que você defina uma sequência de pares de pergunta/resposta, a biblioteca `dialog control` permite que você modularize a lógica de fluxo de conversa, etc. Todas essas bibliotecas são vinculadas por meio de um objeto `dialogs`. Vamos examinar mais detalhadamente como os módulos são implementados como `dialogs` para projetar e gerenciar fluxos de conversa e ver como esse fluxo é semelhante ao fluxo de aplicativo tradicional.

![Fluxo de GUI do aplicativo comparado ao fluxo de conversa do bot](./media/designing-bots/core/dialogs-screens.png)

Em um aplicativo tradicional, tudo começa com a **tela principal**.
A **tela principal** invoca a **tela de novo pedido**.
A **tela de novo pedido** permanece no controle até que seja fechada ou invoque outras telas, como a **tela de pesquisa de produtos**.
Se a **tela de novo pedido** fechar, o usuário retornará à **tela principal**.

Em um bot, tudo começa com o **diálogo raiz**.
O **diálogo raiz** invoca o **diálogo de novo pedido**.
Nesse ponto, o **diálogo de novo pedido** assume o controle da conversa e permanece no controle até que ele seja fechado ou invoque outros diálogos, como o **diálogo de pesquisa de produtos**.
Se o **diálogo de novo pedido** for fechado, o controle da conversa retornará ao **diálogo raiz**.

Para obter exemplos de como implementar o fluxo da conversa, confira como [criar seus próprios prompts para coletar entrada do usuário](./v4sdk/bot-builder-primitive-prompts.md) e use as caixas de diálogo para [implementar o fluxo da conversa sequencial](./v4sdk/bot-builder-dialog-manage-conversation-flow.md).

## <a name="handle-interruptions"></a>Manipular interrupções

Pode ser difícil resistir à tentação de pressupor que os usuários façam tarefas de procedimento individualmente de uma forma clara e organizada.
Por exemplo, em um fluxo de conversa de procedimento que usa `dialogs`, o usuário começará no diálogo raiz, invocará o novo diálogo de pedido nele e, em seguida, invocará o diálogo de pesquisa de produtos. Em seguida, o usuário selecionará um produto e confirmará, saindo do diálogo de pesquisa de produtos, concluirá o pedido, saindo do novo diálogo de pedido, e chegará novamente ao diálogo raiz.

Embora seja ótimo se os usuários sempre percorressem esse caminho lógico e linear, ele raramente ocorre.
As pessoas não se comunicam em `dialogs` sequenciais. Eles tendem a mudar de ideia com frequência.
Considere o exemplo a seguir:

![O usuário faz uma pergunta em resposta a uma pergunta do bot](./media/bot-service-design-conversation-flow/stack-issue.png)

Embora o bot possa ser voltado para procedimentos, o usuário pode decidir fazer algo totalmente diferente ou fazer uma pergunta que não esteja relacionada ao tópico atual.
No exemplo acima, o usuário faz uma pergunta em vez de fornecer a resposta sim/não que o bot espera.
Como o bot deve responder?

- Insistir que o usuário responda à pergunta primeiro.
- Desconsiderar tudo o que o usuário fez anteriormente, redefinir toda a pilha de diálogos e começar do início, tentando responder à pergunta do usuário.
- Tentar responder a pergunta do usuário e, em seguida, retornar a essa pergunta sim/não e tentar retomar desse ponto.

Não há resposta *certa* para essa pergunta, pois a melhor solução dependerá das especificidades do cenário e de como o usuário esperaria que o bot respondesse. Para obter mais informações, confira [Manipular as interrupções do usuário](v4sdk/bot-builder-howto-handle-user-interrupt.md).

## <a name="expire-a-conversation"></a>Expirar uma conversa

Há ocasiões em que é útil reiniciar uma conversa desde o início.  Por exemplo, se um usuário não responder após um determinado período de tempo.  Métodos diferentes para expirar uma conversa incluem:

- Acompanhe a última vez em que uma mensagem foi recebida de um usuário e limpe o estado se o tempo for maior do que um comprimento pré-configurado ao receber a próxima mensagem do usuário.
- Use um recurso de camada de armazenamento, como o CosmosDb time to Live, para limpar o estado após um período de tempo pré-configurado.
<!--
NOTE: in the future, provide guidance on an azure function queue or time trigger

- Track the last time a message was received from a user, and run a Web Job or Azure Function to clear the state and/or proactively message the user.
-->

Para obter mais informações, consulte [como: expirar uma conversa](v4sdk/bot-builder-howto-expire-conversation.md)

## <a name="next-steps"></a>Próximas etapas

O gerenciamento da navegação do usuário em diálogos e o projeto do fluxo de conversa de uma maneira que permita que os usuários atinjam suas metas (mesmo em uma forma não linear) são um desafio fundamental do design de bot.
O [próximo artigo](~/bot-service-design-navigation.md) examina algumas armadilhas comuns de navegação mal projetada e aborda estratégias para evitar essas armadilhas.
