---
title: Conversas no SDK do Bot Builder | Microsoft Docs
description: Descreve o que é uma conversa no SDK do Bot Builder.
keywords: fluxo de conversa, reconhecer intenção, turno único, multiturno, conversa de bot
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/01/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 65d6edf123dac2e237ddde4fbe8b37c6913434ae
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2018
ms.locfileid: "46706952"
---
# <a name="conversation-flow"></a>Fluxo de conversa
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Projetar o fluxo de conversa de um bot envolve decidir como um bot responderá quando o usuário disser alguma coisa ao bot. Um bot primeiro reconhece a tarefa ou o tópico de conversa com base em uma mensagem do usuário. Para determinar a tarefa ou o tópico (conhecido como a *intenção*) associado à mensagem de um usuário, o bot pode procurar palavras ou padrões no texto da mensagem do usuário ou aproveitar serviços como [Serviço Inteligente de Reconhecimento Vocal](bot-builder-concept-luis.md) e [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/overview/overview).

Quando o bot reconhecer a intenção do usuário, dependendo do cenário, o bot poderá atender à solicitação do usuário com uma resposta única, completando a conversa em um turno ou poderá exigir uma série de turnos. Para fluxos de conversa multiturno, o SDK do Bot Builder fornece [gerenciamento de estado](./bot-builder-howto-v4-state.md) para controlar uma conversa, [solicitações](bot-builder-prompts.md) de informações e [diálogos](bot-builder-dialog-manage-conversation-flow.md) para encapsular os fluxos de conversa.

Em um bot complexo com vários subsistemas, pode ser que você use vários serviços para reconhecer a intenção, um para cada subcomponente do bot. A [ferramenta de expedição](bot-builder-tutorial-dispatch.md) obtém os resultados de vários serviços em um só lugar quando você combina subsistemas de conversa em um bot.

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

<!-- The following isn't always true, it's a generalization -->

O tipo mais simples de bot de turno único não precisa controlar estado de conversa. Sempre que receber uma mensagem, responderá com base apenas no contexto da mensagem de entrada atual, sem conhecimento de turnos de conversa anteriores.

![Bot de tempo de turno único](./media/concept-conversation/weather-single-turn.png)

Um bot de clima pode ter um fluxo de turno único, apenas fornecendo ao usuário um boletim meteorológico, sem fluxos de ida e volta solicitando a cidade ou a data. Toda a lógica para exibir o boletim meteorológico é baseada na mensagem que o bot acabou de receber. Em cada turno de uma conversa, o bot recebe um [contexto de turno](bot-builder-concept-activity-processing.md#turn-context), que o bot poderá usar para determinar o que fazer em seguida e como a conversa fluirá.

## <a name="multiple-turns"></a>Multiturnos

A maioria dos tipos de conversa não pode ser completada em um turno único, portanto, um bot também pode ter um fluxo de conversa multiturno. Alguns cenários que exigem várias conversas incluem:

* Um bot que solicita ao usuário informações adicionais necessárias para concluir uma tarefa. O bot precisa rastrear se possui todos os parâmetros para cumprir a tarefa.
* Um bot que guia o usuário através de etapas de um processo como, por exemplo, fazer um pedido. O bot precisa rastrear onde o usuário está na sequência de etapas.

Por exemplo, um bot de clima pode ter um fluxo multiturno se o bot responder a "como está o clima?" perguntando a cidade.

![bot de clima multiturno](./media/concept-conversation/weather-multi-turn.png)

Quando o usuário responder à solicitação do bot para a cidade e o bot receber "Seattle", o bot precisará ter algum contexto salvo para reconhecer que a mensagem atual é a resposta a uma solicitação anterior e parte de uma solicitação para obter o clima. Os bots multiturnos controlam o estado para responder adequadamente às novas mensagens.

Ver [Como gerenciar conversa e estado do usuário](bot-builder-howto-v4-state.md) para obter mais informações.

> [!NOTE]
> Conversas multiturnos com clientes da API REST precisarão controlar o próprio estado, por exemplo, em um banco de dados ou armazenamento de tabelas.

## <a name="conversation-topics"></a>Tópicos de conversa

É possível projetar o bot para lidar com mais de um tipo de tarefa. Por exemplo, é possível ter um bot que forneça diferentes fluxos de conversa para saudar o usuário, fazer um pedido, cancelar um pedido e obter ajuda. Uma maneira de lidar com essa alternância entre conversas para diferentes tarefas ou tópicos de conversa é reconhecer a intenção (o que o usuário deseja fazer) da mensagem atual.

### <a name="recognize-intent"></a>Reconhecer intenção

O SDK do Bot Builder fornece _reconhecedores_ que processam uma mensagem para determinar a intenção, de modo que o bot possa iniciar o fluxo de conversa apropriado. Chame o método assíncrono de _reconhecer_ do reconhecedor para determinar a intenção do usuário de seu conteúdo da mensagem. Em seguida, você pode chamar o método de _obtenção da intenção de pontuação superior_ no resultado para obter a previsão superior do reconhecedor.

Um reconhecedor poderia usar expressões regulares, reconhecimento de idioma ou outra lógica que você desenvolver. Veja a seguir exemplos de reconhecedores possíveis:

* Um reconhecedor que usa o QnA Maker para detectar quando um usuário faz uma pergunta abordada na base de dados de conhecimento.
* Um reconhecedor que usa o LUIS (Serviço Inteligente de Reconhecimento Vocal) para treinar um serviço com exemplos de maneiras pelas quais o usuário pode pedir ajuda e mapeá-lo para a intenção `Help`.
* Um reconhecedor personalizado que usa expressões regulares para procurar comandos.
* Um reconhecedor personalizado que usa um serviço para mover a entrada.

### <a name="consider-how-to-interrupt-conversation-flow-or-change-topics"></a>Considerar como interromper o fluxo de conversa ou alterar tópicos

Uma maneira de controlar onde você está em uma conversa é usar o [estado de conversa](bot-builder-howto-v4-state.md) para salvar informações sobre o tópico atualmente ativo ou quais etapas em uma sequência foram concluídas.

Quando um bot tornar-se mais complexo, você também poderá imaginar uma sequência de fluxos de conversa ocorrendo em uma pilha, por exemplo, o bot invocará o novo fluxo de pedido e, em seguida, invocará o fluxo de pesquisa do produto. Em seguida, o usuário selecionará um produto e confirmará, completará o fluxo de pesquisa do produto e concluirá o pedido.

No entanto, conversas raramente seguem esse caminho lógico e linear. Os usuários não comunicam-se em "pilhas", em vez disso, tendem a mudar de ideia frequentemente. Considere o exemplo a seguir:

![Usuário diz algo inesperado](./media/concept-conversation/interruption.png)

Embora o bot possa ter construído logicamente uma pilha de fluxos, o usuário pode decidir fazer algo totalmente diferente ou fazer uma pergunta que não esteja relacionada ao tópico atual. No exemplo, o usuário faz uma pergunta em vez de fornecer a resposta sim/não que o fluxo espera. Como o fluxo deve responder?

* Insista em que o usuário responda a pergunta primeiro.
* Desconsidere tudo o que o usuário fez anteriormente, redefina toda a pilha de fluxo e comece do início, tentando responder à pergunta do usuário.
* Tente responder a pergunta do usuário e, em seguida, retorne a essa pergunta sim/não e tente retomar a partir desse ponto.

Não há resposta certa para essa pergunta, pois a melhor solução dependerá das especificidades do cenário e de como o usuário esperaria que o bot respondesse. Consulte como [usar as caixas de diálogo](bot-builder-dialog-manage-conversation-flow.md) e [tratar interrupções](bot-builder-howto-handle-user-interrupt.md) para gerenciar o fluxo da conversa.

## <a name="conversation-lifetime"></a>Tempo de vida da conversa

<!-- Note: these activities are dependent on whether the channel actually sends them. Also, we should add links --> Um bot receberá uma atividade de _atualização de conversa_ sempre que uma conversa for adicionada, outros membros forem adicionados ou removidos de uma conversa ou metadados de conversa foram alterados.
Talvez você queira que o bot reaja às atividades de atualização de conversa, cumprimentando os usuários ou apresentando-se.

Um bot recebe uma atividade de _fim de conversa_ para indicar que o usuário terminou a conversa. Um bot pode enviar uma atividade de _fim de conversa_ para indicar ao usuário que a conversa está terminando.
Se você estiver armazenando informações sobre a conversa, convém limpar essas informações quando a conversa terminar.

<!--  Types of conversations -->

Seu bot pode dar suporte a interações de multiturnos em que ele solicita ao usuários várias informações. Ele pode se concentrar em uma tarefa muito específica ou dar suporte a vários tipos de tarefas.
O SDK do Bot Builder tem algum suporte interno para o Serviço Inteligente de Reconhecimento Vocal (LUIS) e o QnA Maker para adicionar recursos de "Perguntas e respostas" de linguagem natural ao seu bot.

## <a name="conversations-channels-and-users"></a>Conversas, canais e usuários

As conversas podem ser uma conversa _direta_ com um usuário específico ou uma conversa de _grupo_ com vários usuários.
Um bot comunica-se com um usuário em um canal, recebendo atividades e enviando atividades ao usuário.

* Cada usuário tem uma ID exclusiva por canal.
* Cada conversa tem uma ID exclusiva por canal.
* O canal define a ID da conversa quando inicia a conversa.
* O bot não pode iniciar uma conversa, no entanto, após ter uma ID de conversa poderá retomar essa conversa.
* Nem todos os canais dão suporte a conversas em grupo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar fluxo de conversa simples com diálogos](bot-builder-dialog-manage-conversation-flow.md)

<!-- In addition, your bot can send activities back to the user, either _proactively_, in response to internal logic, or _reactively_, in response to an activity from the user or channel.-->
<!--TODO: Link to messaging how tos.-->
