---
title: Conversas no SDK do Bot Builder | Microsoft Docs
description: Descreve o que é uma conversa no SDK do Bot Builder.
keywords: fluxo de conversa, reconhecer intenção, turno único, multiturno
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/11/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 32486cb024dfe852a7478ccba4a0eedc476431b0
ms.sourcegitcommit: ee63d9dc1944a6843368bdabf5878950229f61d0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42795025"
---
# <a name="conversation-flow"></a>Fluxo de conversa
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma vez que um bot pode ser considerado como uma interface do usuário conversacional, o fluxo de conversa é como ocorre a interação com o usuário e pode assumir diferentes formas. Ter o fluxo de conversa certo ajuda a melhorar a interação do usuário e o desempenho do bot.

Projetar o fluxo de conversa de um bot envolve decidir como um bot responderá quando o usuário disser alguma coisa ao bot. Um bot primeiro reconhece a tarefa ou o tópico de conversa com base em uma mensagem do usuário. Para determinar a tarefa ou o tópico (conhecido como a *intenção*) associado à mensagem de um usuário, o bot pode procurar palavras ou padrões no texto da mensagem do usuário ou aproveitar serviços como [LUIS (Serviço Inteligente de Reconhecimento Vocal)](bot-builder-concept-luis.md) e QnA Maker. 

Quando o bot reconhecer a intenção do usuário, dependendo do cenário, o bot poderá atender à solicitação do usuário com uma resposta única, completando a conversa em um turno ou poderá exigir uma série de turnos. Para fluxos de conversa multiturno, o SDK do Bot Builder fornece [gerenciamento de estado](./bot-builder-howto-v4-state.md) para controlar uma conversa, [solicitações](bot-builder-prompts.md) de informações e [diálogos](bot-builder-dialog-manage-conversation-flow.md) para encapsular os fluxos de conversa. 

Em um bot complexo com vários subsistemas, pode ser que você use vários serviços para reconhecer a intenção, um para cada subcomponente do bot. A [Ferramenta de expedição](bot-builder-tutorial-dispatch.md) obtém os resultados de vários serviços em um só lugar quando você combina subsistemas de conversa em um bot. 
<!-- 
A conversation identifies a series of activities sent between a bot and a user on a specific channel and represents an interaction between one or more bots and either a _direct_ conversation with a specific user or a _group_ conversation with multiple users.
A bot communicates with a user on a channel by receiving activities from, and sending activities to the user.

- Each user has an ID that is unique per channel.
- Each conversation has an ID that is unique per channel.
- The channel sets the conversation ID when it starts the conversation.
- The bot cannot start a conversation; however, once it has a conversation ID, it can resume that conversation.
- Not all channels support group conversations.
-->

## <a name="single-turn-conversation"></a>Conversa de turno único

O fluxo de conversa mais simples é de turno único. Em um fluxo de turno único, o bot conclui a tarefa em um turno, consistindo de uma mensagem do usuário e uma resposta do bot. 



<!-- 
The EchoBot sample in the BotBuilder SDK is a single-turn bot. Here are other examples of single turn conversation flow:
* A bot for getting the weather report, that just tells the user what the weather is, when they say "What's the weather?".
* An IoT bot that responds to "turn on the lights" by calling an IoT service. -->

<!-- The following isn't always true, it's a generalization --> O tipo mais simples de bot de turno único não precisa controlar estado de conversa. Sempre que receber uma mensagem, responderá com base apenas no contexto da mensagem de entrada atual, sem conhecimento de turnos de conversa anteriores.

![Bot de tempo de turno único](./media/concept-conversation/weather-single-turn.png)

Um bot de clima tem um fluxo de turno único, apenas fornece ao usuário um boletim meteorológico, sem fluxos de ida e volta solicitando a cidade ou a data. Toda a lógica para exibir o boletim meteorológico é baseada na mensagem que o bot acabou de receber. Em cada turno de uma conversa, o bot recebe um [contexto de turno](bot-builder-concept-activity-processing.md#turn-context), que o bot poderá usar para determinar o que fazer em seguida e como a conversa fluirá. 

## <a name="multiple-turns"></a>Multiturnos

A maioria dos tipos de conversa não pode ser completada em um turno único, portanto, um bot também pode ter um fluxo de conversa multiturno. Alguns cenários que exigem várias conversas incluem:

 * Um bot que solicita ao usuário informações adicionais necessárias para concluir uma tarefa. O bot precisa rastrear se possui todos os parâmetros para cumprir a tarefa.
 * Um bot que guia o usuário através de etapas de um processo como, por exemplo, fazer um pedido. O bot precisa rastrear onde o usuário está na sequência de etapas.

Por exemplo, um bot de clima terá um fluxo multiturno se o bot responder a "como está o clima?" perguntando a cidade.

![bot de clima multiturno](./media/concept-conversation/weather-multi-turn.png)

Quando o usuário responder à solicitação do bot para a cidade e o bot receber "Seattle", o bot precisará ter algum contexto salvo para reconhecer que a mensagem atual é a resposta a uma solicitação anterior e parte de uma solicitação para obter o clima. Os bots multiturnos controlam o estado para responder adequadamente às novas mensagens.

<!--
```
// TBD: snippet showing receiving message and using ConversationProperties
```
-->

Consulte [Gerenciar estado](bot-builder-storage-concept.md) para uma visão geral do estado de gerenciamento e consulte [Como usar propriedades da conversa e do usuário](bot-builder-howto-v4-state.md) para obter um exemplo.

> [!NOTE]
> Conversas multiturnos com clientes da API REST precisarão controlar o próprio estado, por exemplo, em um banco de dados ou armazenamento de tabelas. 

## <a name="conversation-topics"></a>Tópicos de conversa

É possível projetar o bot para lidar com mais de um tipo de tarefa. Por exemplo, é possível ter um bot que forneça diferentes fluxos de conversa para saudar o usuário, fazer um pedido, cancelar e obter ajuda. Uma maneira de lidar com essa alternância entre conversas para diferentes tarefas ou tópicos de conversa é reconhecer a intenção (o que o usuário deseja fazer) da mensagem atual. 

### <a name="recognize-intent"></a>Reconhecer intenção

O SDK do Bot Builder fornece _reconhecedores_ que processam cada mensagem de entrada para determinar a intenção, de modo que o bot possa iniciar o fluxo de conversa apropriado. Antes de _receber retorno de chamada_, os reconhecedores verificam o conteúdo da mensagem do usuário para determinar a intenção e, em seguida, retornam a intenção ao bot usando o objeto de contexto de turno no retorno de chamada de recebimento, armazenado como **Intenção Principal** no objeto de [contexto de turno](bot-builder-concept-activity-processing.md#turn-context). 

O reconhecedor que determina a **Intenção Principal** pode simplesmente usar expressões regulares, o LUIS (Serviço Inteligente de Reconhecimento Vocal) ou outra lógica desenvolvida como middleware. A seguir, são apresentados exemplos de reconhecedores:
   
* Você configura um reconhecedor usando expressões regulares para detectar todas as vezes que um usuário disser a palavra ajuda.
* Você usa o LUIS (Serviço Inteligente de Reconhecimento Vocal) para treinar um serviço com exemplos de maneiras pelas quais o usuário pode pedir ajuda e mapeá-lo para a intenção "Ajuda".
* Você cria o próprio middleware de reconhecimento que inspecionará as atividades de entrada e retornará a intenção "traduzir" todas as vezes que detectar uma mensagem em outro idioma.

Para obter mais informações, consulte [Reconhecimento vocal com LUIS](bot-builder-concept-luis.md). <!-- TODO: ADD THIS TOPIC OR SNIPPET-->

### <a name="consider-how-to-interrupt-conversation-flow-or-change-topics"></a>Considerar como interromper o fluxo de conversa ou alterar tópicos

Uma maneira de controlar onde você está em uma conversa é usar o [estado de conversa](bot-builder-howto-v4-state.md) para salvar informações sobre o tópico atualmente ativo ou quais etapas em uma sequência foram concluídas.

Quando um bot tornar-se mais complexo, você também poderá imaginar uma sequência de fluxos de conversa ocorrendo em uma pilha, por exemplo, o bot invocará o novo fluxo de pedido e, em seguida, invocará o fluxo de pesquisa do produto. Em seguida, o usuário selecionará um produto e confirmará, completará o fluxo de pesquisa do produto e concluirá o pedido.

No entanto, a conversa raramente segue esse caminho lógico e linear. Os usuários não comunicam-se em "pilhas", em vez disso, tendem a mudar de ideia frequentemente. Considere o exemplo a seguir:

![Usuário diz algo inesperado](./media/concept-conversation/interruption.png)

Embora o bot possa ter construído logicamente uma pilha de fluxos, o usuário pode decidir fazer algo totalmente diferente ou fazer uma pergunta que não esteja relacionada ao tópico atual. No exemplo, o usuário faz uma pergunta em vez de fornecer a resposta sim/não que o fluxo espera. Como o fluxo deve responder?

* Insista em que o usuário responda a pergunta primeiro.
* Desconsidere tudo o que o usuário fez anteriormente, redefina toda a pilha de fluxo e comece do início, tentando responder à pergunta do usuário.
* Tente responder a pergunta do usuário e, em seguida, retorne a essa pergunta sim/não e tente retomar a partir desse ponto.

Não há resposta certa para essa pergunta, pois a melhor solução dependerá das especificidades do cenário e de como o usuário esperaria que o bot respondesse. Para obter mais informações, consulte [Controlar interrupção de usuário](bot-builder-howto-handle-user-interrupt.md).

> [!TIP]
> Se estiver usando o SDK do Bot Builder para Node.Js, poderá usar [Diálogos](bot-builder-dialog-manage-conversation-flow.md) para gerenciar o fluxo de conversas.

## <a name="conversation-lifetime"></a>Tempo de vida da conversa

<!-- Note: these activities are dependent on whether the channel actually sends them. Also, we should add links --> Um bot receberá uma atividade de _atualização de conversa_ sempre que uma conversa for adicionada, outros membros forem adicionados ou removidos de uma conversa ou metadados de conversa foram alterados.
Talvez você queira que o bot reaja às atividades de atualização de conversa, cumprimentando os usuários ou apresentando-se.

Um bot recebe uma atividade de _fim de conversa_ para indicar que o usuário terminou a conversa. Um bot pode enviar uma atividade de _fim de conversa_ para indicar ao usuário que a conversa está terminando. Se você estiver armazenando informações sobre a conversa, convém limpar essas informações quando a conversa terminar.

<!--  Types of conversations

Your bot can support multi-turn interactions where it prompts users for multiple peices of information. It can be focused on a very specific task or support multiple types of tasks. 
The Bot Builder SDK has some built-in support for Language Understatnding (LUIS) and QnA Maker for adding natural language "question and answer" features to your bot.

<!--TODO: Add with links when these topics are available:
[Conversation flow] and other design articles.
[Using recognizers] [Using state and storage] and other how tos.
-->
## <a name="conversations-channels-and-users"></a>Conversas, canais e usuários

As conversas podem ser uma conversa _direta_ com um usuário específico ou uma conversa de _grupo_ com vários usuários.
Um bot comunica-se com um usuário em um canal, recebendo atividades e enviando atividades ao usuário.

- Cada usuário tem uma ID exclusiva por canal.
- Cada conversa tem uma ID exclusiva por canal.
- O canal define a ID da conversa quando inicia a conversa.
- O bot não pode iniciar uma conversa, no entanto, após ter uma ID de conversa poderá retomar essa conversa.
- Nem todos os canais dão suporte a conversas em grupo.

## <a name="next-steps"></a>Próximas etapas

Para conversas complexas, como algumas das destacadas acima, precisamos ser capazes de persistir informações por mais de um turno. A seguir, verificaremos o estado e armazenamento.

> [!div class="nextstepaction"]
> [Estado e armazenamento](bot-builder-storage-concept.md)

<!-- In addition, your bot can send activities back to the user, either _proactively_, in response to internal logic, or _reactively_, in response to an activity from the user or channel.-->
<!--TODO: Link to messaging how tos.-->

<!--  TODO: Change to next steps, one for each of LUIS and State
## See also

- Activities
- Adapter
- Context
- Proactive messaging
- State
-->

[QnAMaker]:(bot-builder-luis-and-qna.md#using-qna-maker)

<!-- TODO: Update when the Dispatch concept is pushed -->
[Dispatch]:(bot-builder-concept-luis.md)
