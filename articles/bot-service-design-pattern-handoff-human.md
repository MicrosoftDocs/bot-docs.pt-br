---
title: Fazer a transição de conversas do bot para humanos – Serviço de Bot
description: Saiba como criar para situações em que um usuário inicia uma conversa com um bot e, em seguida, deve ser entregue a um ser humano.
author: arturl
ms.author: arturl
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/20/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 30379c722d288c219fd78c81906e72c70fd533ae
ms.sourcegitcommit: ee94943420ef6c28638297a6ec44ee995de27d3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86865944"
---
# <a name="transition-conversations-from-bot-to-human"></a>Fazer a transição de conversas do bot para humanos

Independentemente do nível de inteligência artificial de um bot, ainda pode haver vezes quando ele precisa entregar a conversa para um ser humano. Isso pode ser necessário porque o bot não entende o usuário (devido a uma limitação da IA) ou se a solicitação não pode ser automatizada e exige uma ação humana. Nesses casos, o bot deve reconhecer quando ele precisa entregar e fornecer ao usuário uma transição tranquila.

O Microsoft Bot Framework é uma plataforma aberta que permite aos desenvolvedores fazer a integração a uma variedade de plataformas de participação do agente. 

## <a name="handoff-protocol"></a>Protocolo de entrega

Quando um bot detecta a necessidade de entregar a conversa a um agente, ele sinaliza a intenção dele enviando um evento de início da entrega, conforme demonstrado no snippet de código C# a seguir.

```C#
var activities = GetRecentActivities();
var handoffContext = new { Skill = "credit cards" };
var handoffEvent =
    EventFactory.CreateHandoffInitiation(
        turnContext, handoffContext, new Transcript(activities));
await turnContext.SendActivityAsync(handoffEvent);
```

O evento contém dois componentes:
 
 - O contexto da solicitação de entrega que é necessário para rotear a conversa para o agente correto.
 - A transcrição da conversa. O agente pode ler a conversa que ocorreu entre o cliente e o bot antes do início da entrega.

<!-- We don't own this aka link, and for v4, I think there is an updated pattern.
You can read more about the Bot Framework handoff protocol <a href="https://aka.ms/bfhandoffprotocol" target="blank">here</a>. 
-->

## <a name="handoff-integration-models"></a>Modelos de integração de entrega

O Microsoft Bot Framework dá suporte a dois modelos de integração às plataformas de participação do agente. O protocolo de entrega é idêntico para ambos os modelos; no entanto, os detalhes de integração diferem entre os modelos e as plataformas de participação do agente.

## <a name="bot-as-an-agent"></a>Bot como um agente

No primeiro modelo, conhecido como "Bot como um agente", o bot une as classificações dos agentes humanos conectados ao hub do agente e responde às solicitações do usuário como se as solicitações viessem de qualquer outro canal do Bot Framework. A conversa entre o usuário e o bot pode ser encaminhada para um agente humano. Nesse ponto o bot se desconecta da conversa ativa.

A principal vantagem desse modo é a simplicidade: um bot existente pode ser integrado ao hub do agente com mínimo esforço, com toda a complexidade do roteamento de mensagens sob a responsabilidade do hub do agente.

![Cenário de bot como um agente](~/media/designing-bots/patterns/bot-as-agent.PNG)

## <a name="bot-as-a-proxy"></a>Bot como um proxy

O segundo modelo é conhecido como "Bot como um proxy". O usuário se comunica diretamente com o bot, até que o bot decida que precisa da ajuda de um agente humano. O componente de roteador de mensagem no bot redireciona a conversa para o hub do agente, que a expede para o agente apropriado. O bot permanece no loop e pode coletar a transcrição da conversa, filtrar mensagens ou fornecer conteúdo adicional ao agente e ao usuário.

A flexibilidade e o controle são as principais vantagens desse modelo. O bot pode dar suporte a uma variedade de canais e ter controle sobre como as conversas são encaminhadas e roteadas entre o usuário, o bot e o hub do agente.

![Cenário de bot como um proxy](~/media/designing-bots/patterns/bot-as-proxy.PNG)

## <a name="natural-language"></a>Linguagem natural

Compreensão de linguagem natural e análise de sentimento ajudam o bot decidir quando transferir o controle da conversa a um agente humano. Isso é particularmente importante ao tentar determinar quando o usuário está frustrado ou quer falar com um agente humano. 
 
O bot analisa o conteúdo das mensagens do usuário usando a <a href="https://www.microsoft.com/cognitive-services/text-analytics-api" target="blank">API de Análise de Texto</a> para inferir sentimento ou usando a <a href="https://www.luis.ai" target="_blank">API LUIS</a>. 


> [!TIP]
> Compreensão de linguagem natural pode não ser sempre o melhor método para determinar quando um bot deve transferir o controle da conversa para um ser humano. Bots, como humanos, nem sempre adivinham corretamente e respostas inválidas frustrarão o usuário. No entanto, se o usuário seleciona em um menu de opções válidas, o bot sempre responderá adequadamente a essa entrada. 


## <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/handoff-library/csharp_dotnetcore/samples" target="blank">Integração ao Omnicanal para Customer Service do Microsoft Dynamics</a> 

- <a href="https://developers.liveperson.com/third-party-bots-microsoft-bot-framework.html" target="blank">Integração com a plataforma LivePerson LiveEngage</a> 

- [Diálogos](v4sdk/bot-builder-dialog-manage-conversation-flow.md)
- <a href="https://www.microsoft.com/cognitive-services/text-analytics-api" target="blank">API de Análise de Texto do Azure Machine Learning</a>
