---
title: Eventos e gatilhos em caixas de diálogo adaptáveis
description: Eventos e gatilhos em caixas de diálogo adaptáveis
keywords: bot, usuário, eventos, gatilhos, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 04/27/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9acbfb7ad6bbc8f7f8cf03a45c5ee5c89bd84595
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577258"
---
# <a name="events-and-triggers-in-adaptive-dialogs"></a>Eventos e gatilhos em caixas de diálogo adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

As caixas de diálogo adaptáveis apresentam uma nova abordagem baseada em eventos para o modelo de conversas. Qualquer subsistema em seu bot pode emitir eventos e todas as caixas de diálogo adaptáveis contêm um ou mais manipuladores de eventos chamados _gatilhos_ que permitem reagir a esses eventos. Sempre que um evento é acionado, os gatilhos da caixa de diálogo adaptável ativa são avaliados e, se qualquer gatilho corresponder ao evento atual, as [ações][actions] associadas a esse gatilho serão executadas. Se um evento não for tratado na caixa de diálogo ativa, ele será passado para a caixa de diálogo pai a ser avaliada. Esse processo continuará até ser tratado ou atingir a caixa de diálogo raiz dos bots. Se nenhum manipulador de eventos (_gatilho_) for encontrado, o evento será ignorado, e nenhuma ação será executada.

<!--TODO P3: preBubble/consultation/postBubble phases - Possibly document in an advanced section at some point.
From: v-jofin: (https://github.com/MicrosoftDocs/bot-docs-pr/pull/2109#discussion_r418164608)
There is also the post-bubble/trailing-edge process where everyone who didn't handle the event on the leading-edge gets a second chance on the trailing edge, from the root back down to the leaf.
From Stevenic: (https://github.com/MicrosoftDocs/bot-docs-pr/pull/2109#discussion_r425335486)
"If anything it seems like these details would be in an "Advanced Event Concepts" section as I'm not sure most devs ever need to worry about this or even understand it. I had to create this mechanism to get our consultation stuff to work but it's a super advanced concept."
--->

## <a name="prerequisites"></a>Pré-requisitos

* [Como funcionam os bots][how-bots-work]
* [Introdução às caixas de diálogo adaptáveis][introduction]
* [Bibliotecas de caixa de diálogo][concept-dialog]

## <a name="anatomy-of-a-trigger"></a>Anatomia de um gatilho

 Um gatilho é composto de uma condição e uma ou mais ações. O SDK do Bot Framework oferece vários gatilhos, cada um com um conjunto de condições predefinidas que examinam o eventName ou o eventValue. Você pode adicionar outras condições a um gatilho, dando a você controle adicional quando o gatilho é executado.

O SDK do Bot Framework fornece vários gatilhos predefinidos projetados para lidar com tipos de eventos comuns.  Por exemplo, o gatilho `OnIntent` é acionado sempre que o [reconhecedor][recognizers] detecta uma [intenção][recognizer-intents]. Se você estiver usando um reconhecedor [LUIS][luis], ele também retornará uma [pontuação de previsão][luis-prediction-scores] que mede o grau de confiança que o LUIS tem para seus resultados de previsão. Para aumentar a confiabilidade e a precisão do bot, talvez seja conveniente executar o gatilho `OnIntent` somente no caso de uma classificação de confiança de 80% ou superior. Você pode fazer isso adicionando uma _condição_. Todos os gatilhos contêm uma propriedade `Condition` opcional que, quando definida, deve ser avaliada como _true_ para que o gatilho seja executado. A propriedade `Condition` é uma cadeia de caracteres, mas deve conter uma [expressão adaptável][adaptive-expressions] válida para funcionar. A propriedade `Condition` dos exemplos acima seria semelhante a: `Condition = "#<IntentName>.Score >= 0.8"`. As expressões adaptáveis permitem condições sofisticadas que podem lidar praticamente com qualquer cenário que você possa ter.

Todos os gatilhos também contêm uma lista de _Ações_. As ações representam o que o bot faz em resposta a um gatilho.  Esse é o coração do gatilho. Saiba mais sobre ações e quais ações internas são fornecidas no SDK do Bot Framework no artigo [Ações em caixas de diálogo adaptáveis][actions].

## <a name="trigger-types"></a>Tipos de gatilho

Os _gatilhos_ permitem que você capture e responda a eventos. O gatilho mais amplo do qual todos os outros gatilhos são derivados é o `OnCondition` gatilho que permite que você capture e anexe uma lista de ações a serem executadas quando um evento específico é emitido por qualquer um dos subsistemas de bots.

Os gatilhos são listados nas seções a seguir, categorizados e agrupados por tipo de gatilho.

Cada tabela nas seções a seguir lista todos os gatilhos com suporte de caixas de diálogo adaptáveis atualmente no SDK, bem como os eventos nos quais eles se baseiam.

### <a name="base-trigger"></a>Gatilho base

O gatilho `OnCondition` é o gatilho base do qual todos os gatilhos derivam. Ao definir gatilhos em uma caixa de diálogo adaptável, eles são definidos como uma lista de `OnCondition` objetos.

Para obter mais informações e um exemplo, consulte a seção [gatilho base][base-trigger] no artigo gatilhos pré-criados de caixas de diálogo adaptáveis.

### <a name="recognizer-event-triggers"></a>Gatilhos de evento de reconhecedores

Os [reconhecedores][recognizers] extraem informações significativas da entrada de um usuário na forma de _intenções_ e _entidades_ e, quando fazem isso, eles emitem eventos. Por exemplo, o evento `recognizedIntent` é acionado quando o reconhecedor seleciona uma intenção (ou extrai entidades) de determinado enunciado de usuário. Você lida com esses eventos usando o `OnIntent` gatilho.

A lista a seguir mostra alguns dos _disparadores de eventos do reconhecedor_ disponíveis no SDK do bot Framework:

* **Escolha a intenção**. O `OnChooseIntent` gatilho é executado quando há ambiguidade entre as tentativas de vários reconhecedores em um [CrossTrainedRecognizerSet][recognizer-cross-trained-recognizer-set].
* **Intenção reconhecida**. O `OnIntent` gatilho é executado quando a intenção especificada é reconhecida.
* **Correspondência de QnA**. O `OnQnAMatch` gatilho é executado quando o [QnAMakerRecognizer][qna-maker-recognizer] retornou uma `QnAMatch` intenção.
* **Nenhuma intenção foi reconhecida**. O `OnUnknownIntent` gatilho é executado quando a entrada do usuário não é reconhecida ou nenhuma correspondência é encontrada em nenhum dos `OnIntent` gatilhos. Você também pode usar isso como o seu primeiro gatilho no diálogo raiz no lugar de `OnBeginDialog` para realizar as tarefas necessárias quando o diálogo for iniciado pela primeira vez.

Para obter informações detalhadas e exemplos, consulte a seção [disparadores de eventos do Recognizer][recognizer-event-triggers] no artigo gatilhos de caixas de diálogo adaptáveis predefinidas.

### <a name="dialog-event-triggers"></a>Gatilhos de evento de diálogo

Os gatilhos de caixa de diálogo tratam eventos específicos da caixa de diálogo relacionados ao "ciclo de vida" da caixa de diálogo.  Atualmente, há seis gatilhos de caixa de diálogo no SDK do Bot Framework, e todos eles derivam da classe `OnDialogEvent`.

> [!TIP]
> Eles não são como manipuladores de eventos de interrupção normais, em que as ações do filho continuarão em execução depois que as ações dos manipuladores forem concluídas. Para todos os eventos abaixo, o bot executará um novo conjunto de ações e encerrará o turno quando essas ações forem concluídas.

Para o _gatilho de caixa de diálogo_ :

* Tome uma atitude imediatamente quando a caixa de diálogo for iniciada, mesmo antes de o reconhecedor ser chamado, use o `OnBeginDialog` gatilho.
* Impedir que uma caixa de diálogo seja cancelada quando qualquer uma de suas caixas de diálogo filho executar uma `CancelAllDialogs` ação, use o `OnCancelDialog` gatilho.
* Tomar medidas quando todas as ações e eventos de ambiguidade tiverem sido processados, use o `OnEndOfActions` gatilho.
* Manipular uma condição de erro, use o `OnError` gatilho.

Para obter informações detalhadas e exemplos, consulte a seção [eventos de diálogo][dialog-event-triggers] no artigo disparadores de caixas de diálogo adaptáveis.

### <a name="activity-event-triggers"></a>Gatilhos de evento de atividade

Os gatilhos de atividade permitem associar ações a qualquer atividade de entrada do cliente, como quando um novo usuário ingressa e o bot inicia uma nova conversa. Informações adicionais sobre atividades podem ser encontradas no [esquema de Atividade do Bot Framework][botframework-activity].

Todos os eventos de atividade têm um evento base de `ActivityReceived` e são refinados pelo seu _tipo de atividade_. A classe base da qual todos os gatilhos de atividade derivam é `OnActivity`.

* **Atualização de conversa**. Use isso para manipular eventos acionados quando um usuário inicia uma nova conversa com o bot.
* A **conversa foi encerrada**. Ações a serem executadas no recebimento de uma atividade com o tipo `EndOfConversation`.
* **Evento recebido**. Ações a serem executadas no recebimento de uma atividade com o tipo `Event`.
* **Transferência ao humano**. Ações a serem executadas no recebimento de uma atividade com o tipo `HandOff`.
* **Conversa invocada**. Ações a serem executadas no recebimento de uma atividade com o tipo `Invoke`.
* O **usuário está digitando**. Ações a serem executadas no recebimento de uma atividade com o tipo `Typing`.

Para obter informações detalhadas e exemplos, consulte a seção [gatilhos de eventos de atividade][activity-event-triggers] no artigo gatilhos pré-criados de caixas de diálogo adaptáveis.

### <a name="message-event-triggers"></a>Gatilhos de evento de mensagem

Os gatilhos de **evento de mensagem** permitem reagir a qualquer evento de mensagem, como quando uma mensagem é atualizada (`MessageUpdate`) ou excluída (`MessageDeletion`) ou quando alguém reage (`MessageReaction`) a uma mensagem (por exemplo, algumas das reações de mensagens comuns incluem Curtida, Coração, Gargalhada, Surpreso, Triste e Irritado).

Os eventos de mensagem são um tipo de evento de atividade e, como tal, todos eles têm um evento base de `ActivityReceived` e são refinados pelo _tipo de atividade_. A classe base da qual todos os gatilhos de mensagem derivam é `OnActivity`.

* **Mensagem recebida**. Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReceived`.
* **Mensagem excluída**. Ações a serem executadas no recebimento de uma atividade com o tipo `MessageDelete`.
* **Reação de mensagem**. Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReaction`.
* **Mensagem atualizada**. Ações a serem executadas no recebimento de uma atividade com o tipo `MessageUpdate`.

Para obter informações detalhadas e exemplos, consulte a seção [gatilhos de eventos de mensagem][message-event-triggers] no artigo gatilhos pré-criados de caixas de diálogo adaptáveis.

### <a name="custom-event-trigger"></a>Gatilho de evento personalizado

Você pode emitir seus eventos adicionando a ação [EmitEvent][emitevent-action] a qualquer gatilho, depois pode manipular esse evento personalizado em qualquer gatilho em qualquer caixa de diálogo em seu bot definindo um gatilho de _evento personalizado_. Um gatilho de evento personalizado é o gatilho `OnDialogEvent` que, na verdade, torna-se um gatilho personalizado quando você define a propriedade `Event` com o mesmo valor que a propriedade `EventName` de EmitEvent.

> [!TIP]
> Você pode permitir que outras caixas de diálogo em seu bot manipulem o evento personalizado definindo a propriedade `BubbleEvent` de EmitEvent como true.
Para obter mais informações e um exemplo, consulte a seção [eventos personalizados] [Custom-Events] no artigo de gatilhos pré-criados de caixas de diálogo adaptáveis.

Para obter informações detalhadas e um exemplo, consulte a seção [disparadores de eventos personalizados] [Custom-Event-triggers] no artigo de gatilhos pré-criados de caixas de diálogo adaptáveis.

## <a name="additional-information"></a>Informações adicionais

* [Introdução às caixas de diálogo adaptáveis][introduction]
* [Bibliotecas de caixa de diálogo][concept-dialog]
* [Ações em caixas de diálogo adaptáveis][actions]
* Para obter informações mais detalhadas sobre gatilhos em caixas de diálogo adaptáveis, incluindo exemplos, consulte o [Guia de referência disparadores em caixas de diálogo adaptáveis][triggers-ref].

<!-- Adaptive dialog links-->
[introduction]:bot-builder-adaptive-dialog-introduction.md
[actions]:bot-builder-concept-adaptive-dialog-actions.md
[inputs]:bot-builder-concept-adaptive-dialog-inputs.md

[emitevent-action]:../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#emitevent
[recognizer-intents]:bot-builder-concept-adaptive-dialog-recognizers.md#intents
[recognizers]:bot-builder-concept-adaptive-dialog-recognizers.md
[recognizer-cross-trained-recognizer-set]:bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set
[qna-maker-recognizer]:bot-builder-concept-adaptive-dialog-recognizers.md#qna-maker-recognizer

<!-- Reference article links-->
[triggers-ref]: ../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md
[base-trigger]:../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#base-trigger
[recognizer-event-triggers]:../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#recognizer-event-triggers
[dialog-event-triggers]:../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#dialog-event-triggers
[activity-event-triggers]:../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#activity-event-triggers
[message-event-triggers]:../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#message-event-triggers
[custom-event-trigger]:../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#custom-event-trigger

<!-- BF SDK links-->
[botframework-activity]:https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md
[adaptive-expressions]:bot-builder-concept-adaptive-expressions.md
[concept-dialog]:bot-builder-concept-dialog.md
[how-bots-work]:bot-builder-basics.md

<!-- External links-->
[luis]:https://www.luis.ai/home
[luis-prediction-scores]:/azure/cognitive-services/luis/luis-concept-prediction-score

