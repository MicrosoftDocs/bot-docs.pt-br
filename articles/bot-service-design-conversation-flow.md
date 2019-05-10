---
title: Projetar e controlar o fluxo da conversa | Microsoft Docs
description: Saiba como projetar e controlar o fluxo da conversa no bot para fornecer uma ótima experiência do usuário.
keywords: design, controle, fluxo da conversa, manipular interrupções, visão geral
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/19/2018
ms.openlocfilehash: ae019f7da32969821db0c59a4710e32b57623b14
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563693"
---
::: moniker range="azure-bot-service-3.0"

# <a name="design-and-control-conversation-flow"></a>Projetar e controlar o fluxo da conversa

[!INCLUDE [pre-release-label](./includes/pre-release-label-v3.md)]

Em um aplicativo tradicional, a interface do usuário é uma série de telas. 
Um único aplicativo ou site pode usar uma ou mais telas, conforme necessário, para trocar informações com o usuário. 
A maioria dos aplicativos começa com uma tela principal, à qual os usuários inicialmente chegam e fornece uma navegação que leva a outras telas para várias funções, como o início de um novo pedido, a navegação de produtos ou a busca por ajuda.

Assim como aplicativos e sites, os bots têm uma interface do usuário, mas ela é composta por **diálogos**, em vez de telas. Os diálogos ajudam a preservar o local em uma conversa, avisam os usuários quando necessário e executam a validação de entrada. Eles são úteis para gerenciar conversas de vários turnos e coleções simples "baseada em formulários" de informações para realizar atividades como a reserva de um voo.

Os diálogos permitem ao desenvolvedor do bot separar logicamente várias áreas da funcionalidade do bot e guiar o fluxo da conversa. Por exemplo, você pode criar um diálogo para conter a lógica que ajuda o usuário a navegar pelos produtos e um diálogo separado para conter a lógica que ajuda o usuário a criar um pedido. 

Os diálogos podem ou não ter interfaces gráficas. Eles podem conter botões, texto e outros elementos, ou ser totalmente baseados na fala. Os diálogos também contêm ações para executar tarefas, como invocar outros diálogos ou processar a entrada do usuário.

## <a name="using-dialogs-to-manage-conversation-flow"></a>Usando diálogos para gerenciar o fluxo da conversa

[!INCLUDE [Dialog flow example](./includes/snippet-dotnet-manage-conversation-flow-intro.md)]

Para obter um passo a passo detalhado de como gerenciar o fluxo da conversa usando diálogos e o SDK do Bot Framework, consulte:

- [Gerenciar o fluxo da conversa com diálogos (.NET)](./dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Gerenciar o fluxo da conversa com diálogos (Node.js)](./nodejs/bot-builder-nodejs-manage-conversation-flow.md)

## <a name="dialog-stack"></a>Pilha de diálogos

Quando um diálogo invoca outro, o Construtor de Bot adiciona o novo diálogo ao topo da pilha de diálogos. 
O diálogo que está no topo da pilha está no controle da conversa. 
Cada nova mensagem enviada pelo usuário estará sujeita ao processamento por esse diálogo até ele ser fechado ou redirecionar para outro diálogo. 
Quando um diálogo é fechado, ele é removido da pilha e o diálogo anterior na pilha assume o controle da conversa. 

> [!IMPORTANT]
> Entender o conceito de como a pilha de diálogos é construída e desconstruída pelo Construtor de Bot, conforme os diálogos invocam um ao outro, são fechados e assim por diante, é crucial para a criação efetiva do fluxo de conversa de um bot. 

## <a name="dialogs-stacks-and-humans"></a>Diálogos, pilhas e humanos

Pode ser difícil resistir à tentação de pressupor que os usuários navegarão entre os diálogos, criando uma pilha de diálogos e, em algum momento, navegarão de volta na direção em que vieram, desempilhando os diálogos individualmente de uma forma clara e organizada. 
Por exemplo, o usuário começará no diálogo raiz, invocará o novo diálogo de pedido nele e, em seguida, invocará o diálogo de pesquisa de produtos. 
Em seguida, o usuário selecionará um produto e confirmará, saindo do diálogo de pesquisa de produtos, concluirá o pedido, saindo do novo diálogo de pedido, e chegará novamente ao diálogo raiz. 

Embora seja ótimo se os usuários sempre percorressem esse caminho lógico e linear, ele raramente ocorre. 
As pessoas não se comunicam em "pilhas". Eles tendem a mudar de ideia com frequência. 
Considere o exemplo a seguir: 

![bot](./media/bot-service-design-conversation-flow/stack-issue.png)

Embora o bot possa ter construído logicamente uma pilha de diálogos, o usuário pode decidir fazer algo totalmente diferente ou fazer uma pergunta que pode não estar relacionada ao tópico atual. 
No exemplo, o usuário faz uma pergunta em vez de fornecer a resposta sim/não que o diálogo espera. 
Como o diálogo deve responder?

- Insistir que o usuário responda à pergunta primeiro. 
- Desconsiderar tudo o que o usuário fez anteriormente, redefinir toda a pilha de diálogos e começar do início, tentando responder à pergunta do usuário. 
- Tentar responder a pergunta do usuário e, em seguida, retornar a essa pergunta sim/não e tentar retomar desse ponto. 

Não há i,a resposta *certa* para essa pergunta, pois a melhor solução dependerá das especificidades do cenário e de como o usuário esperaria que o bot respondesse. No entanto, à medida que a complexidade da conversa aumenta, os **diálogos** tornam-se mais difíceis de gerenciar. Em situações complexas de branch, pode ser mais fácil criar seu próprio fluxo de lógica de controle para manter o controle de conversa do usuário.

## <a name="next-steps"></a>Próximas etapas

O gerenciamento da navegação do usuário em diálogos e o projeto do fluxo de conversa de uma maneira que permita que os usuários atinjam suas metas (mesmo em uma forma não linear) são um desafio fundamental do design de bot. 
O [próximo artigo](./bot-service-design-navigation.md) examina algumas armadilhas comuns de navegação mal projetada e aborda estratégias para evitar essas armadilhas. 

::: moniker-end

::: moniker range="azure-bot-service-4.0"

# <a name="design-and-control-conversation-flow"></a>Projetar e controlar o fluxo da conversa

Em um aplicativo tradicional, a interface do usuário consiste em uma série de telas e um único aplicativo ou site pode usar uma ou mais telas, conforme necessário, para trocar informações com o usuário.
A maioria dos aplicativos começa com uma tela principal em que os usuários acessam primeiro e essa tela fornece uma navegação que leva a outras telas para várias funções, como o início de um novo pedido, a navegação de produtos ou a busca por ajuda.

Assim como aplicativos e sites, os bots têm uma interface do usuário, mas ela é composta por **mensagens**, em vez de telas. As mensagens podem conter botões, texto e outros elementos, ou ser totalmente baseados na fala.

Embora um aplicativo ou um site tradicional possa solicitar várias informações em uma tela ao mesmo tempo, um bot coletará a mesma quantidade de informações usando várias mensagens. Dessa forma, o processo de coleta de informações do usuário é uma experiência ativa: aquela em que o usuário está tendo uma conversa ativa com o bot.

Um bot bem projetado terá um fluxo de conversa que parece natural. O bot deve conseguir lidar com a conversa central de forma contínua e manipular as interrupções ou mudar um tópico de conversa normalmente.

## <a name="procedural-conversation-flow"></a>Fluxo da conversa de procedimento

Geralmente, a conversa com um bot se concentra na tarefa que um bot está tentando concluir, que é chamado de fluxo de procedimento. Esse é o momento em que o bot solicita ao usuário uma série de perguntas para coletar todas as informações necessárias antes de processar a tarefa.

Em um fluxo de conversa de procedimento, você define a ordem das perguntas e o bot fará as perguntas na ordem definida. Você pode organizar as perguntas em *módulos* lógicos para manter o código centralizado e permanecer concentrado guiando a conversa. Por exemplo, você pode criar um módulo para conter a lógica que ajuda o usuário a navegar pelos produtos e um módulo separado para conter a lógica que ajuda o usuário a criar um pedido.

Você pode estruturar esses módulos para fluírem da maneira desejada, variando de forma livre para sequencial. O SDK do Bot Framework fornece várias bibliotecas que permitem construir qualquer fluxo de conversa necessário para o bot. Por exemplo, a biblioteca `prompts` permite que você solicite aos usuários a entrada, a biblioteca `waterfall` permite que você defina uma sequência de pares de pergunta/resposta, a biblioteca `dialog control` permite que você modularize a lógica de fluxo de conversa, etc. Todas essas bibliotecas são vinculadas por meio de um objeto `dialogs`. Vamos examinar mais detalhadamente como os módulos são implementados como `dialogs` para projetar e gerenciar fluxos de conversa e ver como esse fluxo é semelhante ao fluxo de aplicativo tradicional.

![bot](./media/designing-bots/core/dialogs-screens.png)

Em um aplicativo tradicional, tudo começa com a **tela principal**.
A **tela principal** invoca a **tela de novo pedido**.
A **tela de novo pedido** permanece no controle até que seja fechada ou invoque outras telas, como a **tela de pesquisa de produtos**.
Se a **tela de novo pedido** for fechada, o usuário retornará à **tela principal**.

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

![bot](./media/bot-service-design-conversation-flow/stack-issue.png)

Embora o bot possa ser voltado para procedimentos, o usuário pode decidir fazer algo totalmente diferente ou fazer uma pergunta que não esteja relacionada ao tópico atual.
No exemplo acima, o usuário faz uma pergunta em vez de fornecer a resposta sim/não que o bot espera.
Como o bot deve responder?

- Insistir que o usuário responda à pergunta primeiro.
- Desconsiderar tudo o que o usuário fez anteriormente, redefinir toda a pilha de diálogos e começar do início, tentando responder à pergunta do usuário.
- Tentar responder a pergunta do usuário e, em seguida, retornar a essa pergunta sim/não e tentar retomar desse ponto.

Não há resposta *certa* para essa pergunta, pois a melhor solução dependerá das especificidades do cenário e de como o usuário esperaria que o bot respondesse. Para obter mais informações, confira [Manipular as interrupções do usuário](v4sdk/bot-builder-howto-handle-user-interrupt.md).

## <a name="next-steps"></a>Próximas etapas

O gerenciamento da navegação do usuário em diálogos e o projeto do fluxo de conversa de uma maneira que permita que os usuários atinjam suas metas (mesmo em uma forma não linear) são um desafio fundamental do design de bot.
O [próximo artigo](~/bot-service-design-navigation.md) examina algumas armadilhas comuns de navegação mal projetada e aborda estratégias para evitar essas armadilhas.

::: moniker-end
