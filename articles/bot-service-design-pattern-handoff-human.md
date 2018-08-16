---
title: Fazer a transição de conversas do bot para humanos | Microsoft Docs
description: Saiba como criar para situações em que um usuário inicia uma conversa com um bot e, em seguida, deve ser entregue a um ser humano.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: f786f79011da5e50b37f9797dca694f0e132296c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296942"
---
# <a name="transition-conversations-from-bot-to-human"></a>Fazer a transição de conversas do bot para humanos

Independentemente do nível de inteligência artificial de um bot, ainda pode haver vezes quando ele precisa entregar a conversa para um ser humano. O bot deve reconhecer quando ele precisa entregar e fornecer ao usuário uma transição tranquila e clara.

## <a name="scenarios-that-require-human-involvement"></a>Cenários que exigem o envolvimento humano

Uma ampla variedade de cenários pode exigir que um bot passe o controle da conversa para um ser humano. Alguns desses cenários são *triagem*, *escalonamento*, e *supervisão*. 

### <a name="triage"></a>Triagem

Uma chamada típica do suporte técnico começa com algumas perguntas básicas que podem ser facilmente respondidas por um bot. Como o primeiro respondente das solicitações de entrada dos usuários, um bot pode coletar o nome do usuário, endereço, a descrição do problema ou outras informações pertinentes e, em seguida, passar o controle da conversa a um agente. Usar um bot para fazer a triagem de solicitações de entrada permite que os agentes dediquem seu tempo à resolução do problema em vez de coletar informações.

### <a name="escalation"></a>Escalonamento

No cenário de suporte técnico, um bot pode ser capaz de responder a perguntas básicas e resolver problemas simples, além de coletar informações, como redefinição da senha de um usuário. No entanto, se uma conversa indica que o problema do usuário é complexo o suficiente para exigir o envolvimento humano, o bot precisará escalar o problema para um agente humano. Para implementar esse tipo de cenário, um bot deve ser capaz de diferenciar entre problemas que ele pode resolver independentemente e problemas que devem ser escalados para um ser humano. Há muitas maneiras de um bot determinar se ele precisa transferir o controle da conversa para um ser humano. 

#### <a name="user-driven-menus"></a>Menus orientados ao usuário

Talvez a maneira mais simples para um bot lidar com esse dilema é apresentar ao usuário um menu de opções. As tarefas que o bot pode manipular de forma independente aparecem no menu acima de um link rotulado “Bate-papo com um agente”. Esse tipo de implementação não requer nenhum compreensão de aprendizado de máquina ou de linguagem natural. O bot simplesmente transfere o controle da conversa a um agente humano quando o usuário seleciona a opção “Bate-papo com um agente". 

#### <a name="scenario-driven"></a>Orientado ao cenário

O bot pode decidir se deseja ou não transferir o controle baseado na sua capacidade de determina se ele é capaz ou não de lidar com o cenário em questão. O bot coleta algumas informações sobre a solicitação do usuário e, em seguida, consulta sua lista interna de recursos para determinar se é capaz de resolver essa solicitação. Se o bot determina que é capaz de resolver a solicitação, ele faz isso, mas se o bot determina que a solicitação está além do escopo de problemas que ele pode resolver ele transfere o controle da conversa a um agente humano.

#### <a name="natural-language"></a>Linguagem natural

Compreensão de linguagem natural e análise de sentimento ajudam o bot decidir quando transferir o controle da conversa a um agente humano. Isso é particularmente importante ao tentar determinar quando o usuário está frustrado ou quer falar com um agente humano. 
 
O bot analisa o conteúdo das mensagens do usuário usando a <a href="https://www.microsoft.com/cognitive-services/en-us/text-analytics-api" target="blank">API de Análise de Texto</a> para inferir sentimento ou usando a <a href="https://www.luis.ai" target="_blank">API LUIS</a>. 


> [!TIP]
> Compreensão de linguagem natural pode não ser sempre o melhor método para determinar quando um bot deve transferir o controle da conversa para um ser humano. Bots, como humanos, nem sempre adivinham corretamente e respostas inválidas frustrarão o usuário. No entanto, se o usuário seleciona em um menu de opções válidas, o bot sempre responderá adequadamente a essa entrada. 

### <a name="supervision"></a>Supervisão

Em alguns casos, o agente humano deseja monitorar a conversa em vez de assumir o controle.

Por exemplo, considere um cenário de suporte técnico onde um bot está se comunicando com um usuário para diagnosticar problemas do computador. Um modelo de aprendizado de máquina ajuda o bot a determinar a causa mais provável do problema, no entanto, antes de recomendar ao usuário que ele tome uma ação específica, o bot pode confirmar o diagnóstico e a solução com o agente humano de forma privada e solicitar autorização para continuar. O agente, em seguida, clica em um botão, o bot apresenta a solução para o usuário e o problema é resolvido. O bot ainda está realizando a maior parte do trabalho, mas o agente mantém o controle sobre a decisão final. 

## <a name="transitioning-control-of-the-conversation"></a>Fazer a transição do controle da conversa 

Quando um bot decide transferir o controle de uma conversa para um humano, ele pode informar ao usuário que está sendo transferido e colocar a conversa em um estado de “espera” até que ele confirme que um agente está disponível. 

Quando o bot está aguardando um ser humano, ele pode responder automaticamente todas as mensagens recebidas do usuário com uma resposta padrão como “aguardando na fila”. Além disso, você pode fazer com que o bot tire a conversa do estado de “espera” se o usuário enviou determinadas mensagens, como “deixa para lá” ou “cancele”.

Você especifica como os agentes serão atribuídos aos usuários em espera ao criar seu bot. Por exemplo, o bot pode implementar um sistema de fila simples: primeiro a entrar, primeiro a sair. Uma lógica mais complexa seria atribuir usuários a agentes com base na localização geográfica, idioma ou algum outro fator. O bot também pode apresentar algum tipo de interface do usuário para o agente que eles podem usar para selecionar um usuário. Quando um agente fica disponível, ele se conecta ao bot e ingressa na conversa.

> [!IMPORTANT]
> Mesmo depois de um agente estar envolvido, o bot permanece sendo o facilitador nos bastidores da conversa. O usuário e o agente nunca se comunicam diretamente um com o outro; eles apenas encaminham as mensagens por meio do bot. 

## <a name="routing-messages-between-user-and-agent"></a>Encaminhamento de mensagens entre o usuário e agente

Depois que o agente se conecta ao bot, o bot começa a encaminhar mensagens entre o usuário e o agente. Embora possa parecer ao usuário e ao agente que eles estão conversando diretamente entre si, eles estão na verdade trocando mensagens por meio do bot. O bot recebe mensagens do usuário e envia essas mensagens para o agente e, por sua vez, recebe mensagens do agente e envia essas mensagens para o usuário. 

> [!NOTE]
> Em cenários mais avançados, o bot pode assumir a responsabilidade além de simplesmente encaminhar mensagens entre o usuário e o agente. Por exemplo, o bot pode decidir qual resposta é apropriada e simplesmente solicitar ao agente uma confirmação para continuar.

## <a name="sample-code"></a>Exemplo de código

Para obter um exemplo completo que mostra como entregar as conversas do bot para humanos usando o SDK do Bot Builder para o Node.js, consulte o <a href="https://github.com/palindromed/Bot-HandOff" target="_blank">exemplo de entrega de Bot</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

::: moniker range="azure-bot-service-4.0"

- [Caixas de diálogo](v4sdk/bot-builder-dialog-manage-conversation-flow.md)
- <a href="https://www.microsoft.com/cognitive-services/en-us/text-analytics-api" target="blank">API de Análise de Texto do Azure Machine Learning</a>

::: moniker-end

::: moniker range="azure-bot-service-3.0"

- [Gerenciar o fluxo da conversa com caixas de diálogo (.NET)](~/dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Gerenciar o fluxo da conversa com caixas de diálogo (Node.js)](~/nodejs/bot-builder-nodejs-manage-conversation-flow.md)
- <a href="https://www.microsoft.com/cognitive-services/en-us/text-analytics-api" target="blank">API de Análise de Texto do Azure Machine Learning</a>


::: moniker-end

