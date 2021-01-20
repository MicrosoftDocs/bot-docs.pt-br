---
title: Fazer a transição de conversas do bot para humanos – Serviço de Bot
description: Saiba como criar para situações em que um usuário inicia uma conversa com um bot e, em seguida, deve ser entregue a um ser humano.
author: arturl
ms.author: arturl
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/21/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 08fc45a665b9f980e6f5f7163fb20d2f4347c654
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576003"
---
# <a name="transition-conversations-from-bot-to-human"></a>Fazer a transição de conversas do bot para humanos

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Independentemente do nível de inteligência artificial de um bot, ainda pode haver vezes quando ele precisa entregar a conversa para um ser humano. Isso pode ser necessário porque o bot não entende o usuário (devido a uma limitação da IA) ou se a solicitação não pode ser automatizada e exige uma ação humana. Nesses casos, o bot deve reconhecer quando ele precisa entregar e fornecer ao usuário uma transição tranquila.

O Microsoft Bot Framework é uma plataforma aberta que permite aos desenvolvedores fazer a integração a uma variedade de plataformas de participação do agente.


<!-- We don't own this aka link, and for v4, I think there is an updated pattern.
You can read more about the Bot Framework [handoff protocol](https://aka.ms/handoff-library/#protocol) <a href=" " target="blank">here</a>.
-->

## <a name="handoff-integration-models"></a>Modelos de integração de entrega

O Microsoft Bot Framework dá suporte a dois modelos de integração às plataformas de participação do agente. O protocolo de entrega é idêntico para ambos os modelos; no entanto, os detalhes de integração diferem entre os modelos e as plataformas de participação do agente.

O objetivo não é oferecer uma solução universal para integração com o sistema de qualquer cliente, mas sim fornecer uma **linguagem comum** e **práticas recomendadas** para desenvolvedores de bot e integradores de sistemas que criam sistemas de ia de conversação com o humano no loop.

### <a name="bot-as-an-agent"></a>Bot como um agente

No primeiro modelo, conhecido como "Bot como um agente", o bot une as classificações dos agentes humanos conectados ao hub do agente e responde às solicitações do usuário como se as solicitações viessem de qualquer outro canal do Bot Framework. A conversa entre o usuário e o bot pode ser encaminhada para um agente humano. Nesse ponto o bot se desconecta da conversa ativa.

A principal vantagem desse modo é a simplicidade: um bot existente pode ser integrado ao hub do agente com mínimo esforço, com toda a complexidade do roteamento de mensagens sob a responsabilidade do hub do agente.

![Cenário de bot como um agente](~/media/designing-bots/patterns/bot-as-agent-2.PNG)

### <a name="bot-as-a-proxy"></a>Bot como um proxy

O segundo modelo é conhecido como "Bot como um proxy". O usuário se comunica diretamente com o bot, até que o bot decida que precisa da ajuda de um agente humano. O componente de roteador de mensagem no bot redireciona a conversa para o hub do agente, que a expede para o agente apropriado. O bot permanece no loop e pode coletar a transcrição da conversa, filtrar mensagens ou fornecer conteúdo adicional ao agente e ao usuário.

A flexibilidade e o controle são as principais vantagens desse modelo. O bot pode dar suporte a uma variedade de canais e ter controle sobre como as conversas são encaminhadas e roteadas entre o usuário, o bot e o hub do agente.

![Cenário de bot como um proxy](~/media/designing-bots/patterns/bot-as-proxy-2.PNG)

## <a name="handoff-protocol"></a>Protocolo de entrega

O protocolo é centralizado em volta de eventos para inicialização, enviado pelo bot para o canal e atualização de status, enviado pelo canal para o bot.


### <a name="handoff-initiation"></a>Início da entrega

O evento de *início de entrega* é criado pelo bot para iniciar a entrega.

O evento contém dois componentes:

- O **contexto da solicitação de entrega** que é necessário para rotear a conversa para o agente correto.
- A **transcrição da conversa**. O agente pode ler a conversa que ocorreu entre o cliente e o bot antes do início da entrega.

Estes são os campos de evento de início da entrega:

- **Nome** – o `name` é um campo **obrigatório** que é definido como `"handoff.initiate"` .
- **Valor** -o `value` campo é um objeto que contém o conteúdo JSON específico do Hub do Agent, como a habilidade do agente necessária e assim por diante.  Esse campo é **opcional**.

    ```json
    { "Skill" : "credit cards" }
    ```

- **Anexos** – o `attachments` é um campo **opcional** que contém a lista de `Attachment` objetos. O bot Framework define o tipo de anexo "transcrição" que é usado para enviar a transcrição de conversa para o Hub do agente, se necessário. Os anexos podem ser enviados embutidos (sujeitos a um limite de tamanho) ou offline fornecendo `ContentUrl` .

    ```C#
    handoffEvent.Attachments = new List<Attachment> {
        new Attachment {
            Content = transcript,
            ContentType = "application/json",
            Name = "Transcript",
        }};
    ```

    > [!NOTE]
    > Os hubs de agente **devem ignorar** os tipos de anexos que eles não entendem.

- **Conversa** -o `conversation` é um campo **obrigatório** do tipo `ConversationAccount` que descreve a conversa que está sendo enviada. Criticamente, ele deve incluir a conversa `Id` que pode ser usada para correlação com os outros eventos.

Quando um bot detecta a necessidade de entregar a conversa a um agente, ele sinaliza sua intenção enviando um evento de início de entrega.
Em C#, um método de API de nível superior `CreateHandoffInitiation` pode ser usado como demonstrado no trecho de código abaixo.

```C#
var activities = GetRecentActivities();
var handoffContext = new { Skill = "credit cards" };
var handoffEvent =
    EventFactory.CreateHandoffInitiation(
        turnContext, handoffContext, new Transcript(activities));
await turnContext.SendActivityAsync(handoffEvent);
```

### <a name="handoff-status"></a>Status da entrega

O evento de *status de entrega* é enviado para o bot pelo Hub do agente. O evento informa o bot sobre o status da operação de entrega iniciada.

> [!NOTE]
> Os bots **não são necessários** para manipular o evento, no entanto, eles **não devem** rejeitá-lo.

A seguir estão os campos de evento de status de entrega:

- **Nome** – o `name` é um campo **obrigatório** que é definido como `"handoff.status"` .

- **Valor** -o `value` é um campo **obrigatório** que descreve o status atual da operação de entrega. É um objeto JSON que contém o  campo obrigatório `state` e um campo opcional `message` , conforme definido abaixo.

O `state` tem um dos seguintes valores:

- `accepted`-Um agente aceitou a solicitação e assumiu o controle da conversa.
- `failed`-A solicitação de entrega falhou. O `message` pode conter informações adicionais relevantes para a falha.
- `completed` -A solicitação de entrega foi concluída.

O formato e o valor possível do `message` campo não são especificados.

- Conclusão de entrega bem-sucedida:

    ```json
    { "state" : "completed" }
    ```

- Falha na operação de entrega devido a um tempo limite:

    ```json
    { "state" : "failed", "message" : "Cannot find agent with requested skill" }
    ```

-   - Conversa `Conversation` é um campo **obrigatório** do tipo `ConversationAccount` que descreve a conversa que foi aceita ou rejeitada. O `Id` da conversa deve ser o mesmo que o HandoffInitiation que iniciou a entrega.

## <a name="handoff-library"></a>Biblioteca de entrega

A [biblioteca de entrega](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/handoff-library) foi criada para complementar o SDK do bot Framework V4 no suporte à entrega; especificamente

- Implementa as adições ao SDK do bot Framework para dar suporte à entrega a um agente (também conhecido como *escalonamento*.
- Contém definições de três tipos de evento para operações de entrega de sinalização.

> [!NOTE]
> As integrações com hubs de agente específicos não fazem parte da biblioteca.

## <a name="additional-resources"></a>Recursos adicionais

- [Integração ao Omnicanal para Customer Service do Microsoft Dynamics](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/handoff-library/csharp_dotnetcore/samples)
- [Integração com a plataforma LivePerson LiveEngage](https://developers.liveperson.com/third-party-bots-microsoft-bot-framework.html)
- [Diálogos](v4sdk/bot-builder-dialog-manage-conversation-flow.md)
- [Serviços cognitivas](https://www.microsoft.com/cognitive-services/text-analytics-api)

