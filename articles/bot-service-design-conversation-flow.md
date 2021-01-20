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
ms.openlocfilehash: bec6e643b57f6371d37bf0adbec29369040542f6
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98575964"
---
# <a name="design-and-control-conversation-flow"></a>Projetar e controlar o fluxo da conversa

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Em um aplicativo tradicional, a interface do usuário consiste em uma série de telas e um único aplicativo ou site pode usar uma ou mais telas, conforme necessário, para trocar informações com o usuário.
A maioria dos aplicativos começa com uma tela principal em que os usuários acessam primeiro e essa tela fornece uma navegação que leva a outras telas para várias funções, como o início de um novo pedido, a navegação de produtos ou a busca por ajuda.

Assim como aplicativos e sites, os bots têm uma interface do usuário, mas ela é composta por _mensagens_, em vez de telas. As mensagens podem conter botões, texto e outros elementos, ou ser totalmente baseados na fala.

Embora um aplicativo ou um site tradicional possa solicitar várias informações em uma tela ao mesmo tempo, um bot coletará a mesma quantidade de informações usando várias mensagens. Dessa forma, o processo de coleta de informações do usuário é uma experiência ativa: aquela em que o usuário está tendo uma conversa ativa com o bot.

Um bot bem projetado terá um fluxo de conversação que se sente natural. O bot deve ser capaz de lidar com a conversa básica perfeitamente e ser capaz de lidar com as interrupções ou alternar os tópicos normalmente.

## <a name="procedural-conversation-flow"></a>Fluxo da conversa de procedimento

Conversas com um bot geralmente se concentram na tarefa que um bot está tentando atingir, que é chamado de fluxo de procedimento. Esse é o momento em que o bot solicita ao usuário uma série de perguntas para coletar todas as informações necessárias antes de processar a tarefa.

Em um fluxo de conversa de procedimento, você define a ordem das perguntas e o bot fará as perguntas na ordem definida. Você pode organizar as perguntas em grupos lógicos para manter o código centralizado enquanto se mantém focado na orientação da conversa. Por exemplo, você pode criar um módulo para conter a lógica que ajuda o usuário a navegar pelos produtos e um módulo separado para conter a lógica que ajuda o usuário a criar um pedido.

Você pode estruturar esses módulos para fluírem da maneira desejada, variando de forma livre para sequencial. O SDK do bot Framework fornece uma biblioteca de caixas de diálogo que permite que você construa qualquer fluxo de conversação de que seu bot precisa. A biblioteca inclui [caixas de diálogo de cascata](/azure/bot-service/bot-builder-concept-waterfall-dialogs) para criar uma sequência de etapas e solicitações para fazer perguntas aos usuários. Para obter mais informações, consulte [biblioteca de caixas de diálogo](/azure/bot-service/bot-builder-concept-dialog).

![Fluxo de GUI do aplicativo comparado ao fluxo de conversa do bot](./media/designing-bots/core/dialogs-screens.png)

Em um aplicativo tradicional, tudo começa com a tela _principal_ .
A tela principal invoca a tela de _novo pedido_ .
A tela novo pedido permanece no controle até que seja fechada ou invoque outras telas, como a tela de _pesquisa do produto_ .
Se a tela de novo pedido fechar, o usuário retornará à tela principal.

Em um bot que usa caixas de diálogo, tudo começa com a _caixa de diálogo raiz_.
O diálogo raiz invoca o _diálogo de novo pedido_.
Nesse ponto, a caixa de diálogo novo pedido assume o controle da conversa e permanece no controle até que seja fechada ou invoque outra caixa de diálogo, como a _caixa de diálogo pesquisa de produto_.
Se a caixa de diálogo novo pedido for fechada, o controle da conversa retornará à caixa de diálogo raiz.

Para obter um exemplo de como implementar um fluxo de conversação usando as bibliotecas de diálogo, consulte [implementar o fluxo de conversa sequencial](./v4sdk/bot-builder-dialog-manage-conversation-flow.md).

## <a name="handle-interruptions"></a>Manipular interrupções

Pode ser difícil resistir à tentação de pressupor que os usuários façam tarefas de procedimento individualmente de uma forma clara e organizada.
Por exemplo, em um fluxo de conversa de procedimento usando caixas de diálogo, o usuário será iniciado na caixa de diálogo raiz e invocará a caixa de diálogo nova ordem. Na caixa de diálogo nova ordem, eles invocam a caixa de diálogo pesquisa de produto. Em seguida, ao selecionar um dos resultados listados na caixa de diálogo pesquisa de produto, eles invocam a caixa de diálogo nova ordem. Depois de concluir a ordem, elas retornam na caixa de diálogo raiz.

Embora seja ótimo se os usuários sempre percorressem esse caminho lógico e linear, ele raramente ocorre.
As pessoas nem sempre se comunicam em ordem sequencial. Eles tendem a mudar de ideia com frequência.
Considere o exemplo a seguir:

![O usuário faz uma pergunta em resposta a uma pergunta do bot](./media/bot-service-design-conversation-flow/stack-issue.png)

Embora o bot possa ser voltado para procedimentos, o usuário pode decidir fazer algo totalmente diferente ou fazer uma pergunta que não esteja relacionada ao tópico atual.
No exemplo acima, o usuário faz uma pergunta em vez de fornecer a resposta sim/não que o bot espera.
Como o bot deve responder?

- Insistir que o usuário responda à pergunta primeiro.
- Desconsiderar tudo o que o usuário fez anteriormente, redefinir toda a pilha de diálogos e começar do início, tentando responder à pergunta do usuário.
- Tentar responder a pergunta do usuário e, em seguida, retornar a essa pergunta sim/não e tentar retomar desse ponto.

Não há resposta *certa* para essa pergunta, pois a melhor solução dependerá das especificidades do cenário e de como o usuário esperaria que o bot respondesse. Veja como [lidar com interrupções de usuário](v4sdk/bot-builder-howto-handle-user-interrupt.md) para um bot projetado para lidar com alguns tipos de interrupções.

## <a name="expire-a-conversation"></a>Expirar uma conversa

Há ocasiões em que é útil reiniciar uma conversa desde o início.  Por exemplo, se um usuário não responder após um determinado período de tempo.  Métodos diferentes para encerrar uma conversa incluem:

- Acompanhe a última vez em que uma mensagem foi recebida de um usuário e limpe o estado se o tempo for maior do que um comprimento pré-configurado ao receber a próxima mensagem do usuário.
- Use um recurso de camada de armazenamento, como o recurso de [vida útil](/azure/cosmos-db/how-to-time-to-live) do cosmos DB, para limpar o estado após um período de tempo pré-configurado.

<!--
NOTE: in the future, provide guidance on an azure function queue or time trigger
- Track the last time a message was received from a user, and run a Web Job or Azure Function to clear the state and/or proactively message the user.
-->

Para obter mais informações, consulte como [expirar uma conversa](v4sdk/bot-builder-howto-expire-conversation.md).

## <a name="next-steps"></a>Próximas etapas

O gerenciamento da navegação do usuário entre caixas de diálogo e a criação de um fluxo de conversação de uma maneira que permite aos usuários atingir suas metas (mesmo de maneira não linear) é um desafio fundamental do design de bot.
O artigo de [navegação do bot de design](bot-service-design-navigation.md) revisa algumas armadilhas comuns de navegação mal projetada e discute estratégias para evitar essas interceptações.
