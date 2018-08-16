---
title: Visão geral das tarefas | Microsoft Docs
description: Visão geral das tarefas no bot de Designer de Conversa.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 685a0296f1bfa5452c28f4ec4d2e4e439f09baca
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297155"
---
# <a name="tasks-in-conversation-designer-bots"></a>Tarefas nos bots de Designer de Conversa
> [!IMPORTANT]
> Designer de Conversa ainda não está disponível para todos os clientes. Mais detalhes sobre a disponibilidade do Designer de Conversa serão informados ainda este ano.

Bots no Designer de Conversa são compostos por um grupo de tarefas relacionadas. Um **tarefa** é uma ação que o bot executa a em resposta a uma solicitação do usuário ou atividade específica. Por exemplo, um bot de cafeteria pode incluir estas tarefas: "encontrar localizações","reservar uma mesa", "pedir comida"e"gerenciar reservas". Cada tarefa corresponde a uma diferente meta de usuário. 

## <a name="triggers-and-actions"></a>Gatilhos e ações
Uma tarefa executará uma ação quando uma condição do gatilho for atendida. Todas as tarefas seguem este modelo: **SE disparar**, **EXECUTAR ação**.

Um **gatilho** pode ser um dos dois tipos:
1. **Usuário entra ou sai de uma conversa** (iniciado por atividade): uma tarefa disparada por "usuário entra ou sai de uma conversa" executará uma ação quando um usuário inicia ou finaliza uma conversa com o bot. Esse gatilho é útil para enviar uma mensagem introdutória para o usuário. 
2. **Usuário diz ou digita algo para o bot** (inciado por mensagem): uma tarefa disparada por "usuário diz ou digita algo para o bot" executa uma ação em resposta à mensagem do usuário. A mensagem do usuário é interpretada por um **reconhecedor**.

Quando um gatilho de condição for atendido, uma tarefa executará uma das seguintes ações:
- **dar uma resposta**: uma resposta pode ser qualquer combinação de texto exibido, texto falado ou cartão avançado. Com essa ação, o bot envia uma resposta ao usuário e conclui a tarefa. Uma resposta é uma conversa de turno único, significando que a tarefa não espera por mensagens subsequentes do usuário.
- **iniciar um diálogo**: um diálogo é uma conversa de múltiplos turnos entre o bot e o usuário. Diálogos são particularmente úteis quando o bot precisa participar de uma conversa bidirecional com um usuário de modo a coletar as informações necessárias para concluir uma tarefa.
- **executar uma função de script**: o bot pode executar o script personalizado que você grava para chamar um serviço de back-end para concluir uma tarefa.

## <a name="tips-for-modeling-tasks"></a>Dicas para tarefas de modelagem

1. Cada bot deve ser projetado para executar várias tarefas diferentes do cliente. Você deve conceber a lista dessas funções e criar uma tarefa para cada um delas.
2. Para configurar os gatilhos, pense em como é possível detectar a intenção do cliente para executar uma tarefa. Como o bot irá *saber* o que o cliente quer fazer?
3. Se você estiver usando o reconhecimento vocal como o reconhecedor, certifique-se de incluir exemplos suficientes para as diferentes maneiras que o usuário pode expressar a intenção de executar uma tarefa. "Agendar uma mesa" deve disparar a mesma ação que "fazer uma reserva" ou "reservar uma mesa".
4. Considere adicionar exemplos para a intenção do reconhecimento vocal que sejam gramaticalmente corretos.
5. Se você planeja publicar o bot como uma habilidade da Cortana, considere adicionar frases de exemplo que funcionem bem com os gatilhos da Cortana. "Pedir que o nome do bot faça algo". 
6. Para identificadores de código, grave padrões de expressão regular para determinar a intenção do usuário. Reconhecedores de código também podem retornar entidades que você pode usar na sua tarefa.
7. Se **dar uma resposta** é a *ação* da tarefa, ela pode incluir opcionalmente um cartão adaptável para renderização em canais com suporte.

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Reconhecedor do LUIS](conversation-designer-luis.md)
