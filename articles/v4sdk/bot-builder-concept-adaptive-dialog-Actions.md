---
title: Ações – SDK do Bot Framework – Caixas de diálogo adaptáveis
description: Coletar entrada do usuário usando caixas de diálogo adaptáveis
keywords: bot, usuário, eventos, gatilhos, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 04/27/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 89fc9b76fade1177f91d3f5cf7608a14ab2cff9b
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124305"
---
<!--P2: Once the samples are done, link to them in each section on the individual actions to point to them as examples of how they are used-->
# <a name="actions-in-adaptive-dialogs"></a>Ações em caixas de diálogo adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

As ações ajudam a criar e manter o fluxo de conversa de bots quando um evento é capturado por um [gatilho][triggers]. De modo semelhante às caixas de diálogo adaptáveis, que contêm uma lista de gatilhos, os gatilhos contêm uma lista de ações que, depois que o gatilho é acionado, é executada para realizar qualquer conjunto de ações necessárias, como, por exemplo, atender à solicitação de um usuário. Além de criar e manter o fluxo de conversação do bot, você pode usar ações para enviar mensagens, responder a perguntas do usuário usando uma [base de dados de conhecimento][www.qnamaker.ai], fazer cálculos e executar qualquer número de tarefas computacionais para o usuário. Com as caixas de diálogo adaptáveis, o caminho do bot, que flui em uma caixa de diálogo, pode se ramificar e fazer um loop. O bot pode fazer e responder perguntas, validar a entrada dos usuários, manipular e armazenar valores na [memória][memory-states] e tomar decisões com base na entrada do usuário.

> [!IMPORTANT]
> As ações são caixas de diálogo, e qualquer caixa de diálogo pode ser usada como uma ação, portanto, as ações têm todo o poder e a flexibilidade de que você precisa para criar um bot totalmente funcional e robusto. Embora as ações incluídas no SDK do Bot Framework sejam amplas, você também pode criar suas próprias ações personalizadas para executar praticamente qualquer tarefa ou processo especializado necessário.

## <a name="prerequisites"></a>Pré-requisitos

* [Introdução às caixas de diálogo adaptáveis][introduction]
* [Eventos e gatilhos em caixas de diálogo adaptáveis][triggers]

## <a name="actions"></a>Ações

As ações incluídas com o SDK do Bot Framework fornecem a capacidade de executar codificação condicional, tal como:

* Ramificação e looping.
* Tarefas de gerenciamento de caixa de diálogo, como iniciar uma nova caixa de diálogo ou cancelar, encerrar ou repetir uma caixa de diálogo.
* Tarefas de gerenciamento de memória, como criar, excluir ou editar uma propriedade salva na [memória][memory-states].
* Acesso a recursos externos, como enviar uma [solicitação HTTP](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#httprequest).
* Executar uma [solicitação de logon OAuth][oauthinput-inputs] e muitas outras.

>[!TIP]
> Ao contrário de uma caixa de diálogo em cascata em que cada etapa é uma função, cada ação de uma caixa de diálogo adaptável é uma caixa de diálogo totalmente funcional com todo o poder e a flexibilidade necessários. Isso permite que caixas de diálogo adaptáveis por design possam:
>
> * Fornecer uma maneira mais fácil de lidar com interrupções. <!--TODO P1: [interruptions][interruptions-inputs]-->
> * Executar ramificação condicional com base no contexto ou no estado.

As caixas de diálogo adaptáveis fornecem suporte para as seguintes ações:

### <a name="activities"></a>Atividades

Usado para enviar qualquer atividade, como responder a um usuário.

* **Atividade de envio**. Enviar qualquer atividade, como responder a um usuário.
* **Atualizar uma atividade**. Permite atualizar uma atividade que foi enviada.
* **DeleteActivity**. Permite excluir uma atividade que foi enviada.
* **Obter membros da atividade**. Permite obter uma lista de membros da atividade e salvá-la em uma propriedade na [memória][memory-states].

Para obter informações detalhadas e exemplos, consulte a seção [atividades](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#activities) no __Guia de referência ações em caixas de diálogo adaptáveis__.

### <a name="requesting-user-input"></a>Solicitar entrada do usuário

O SDK do bot Framework define uma variedade de ações usadas para coletar e validar a entrada do usuário. Essas ações importantes e especializadas são abordadas no artigo [solicitando entrada do usuário usando caixas de diálogo adaptáveis](bot-builder-concept-adaptive-dialog-inputs.md) .

### <a name="create-a-condition"></a>Criar uma condição

Instruções condicionais como instruções IF e loops for permitem que o bot tome decisões e controle o fluxo de conversa. Essas condições são especificadas por um conjunto de instruções condicionais com expressões boolianas que são avaliadas como um valor booliano de true ou false.

As ações condicionais fornecidas pelo SDK do bot Framework incluem:

* **If/Else**. Usado para criar instruções IF e if-else que são usadas para executar o código somente se a condição especificada for verdadeira.
* **Comutador**. Usada para criar um menu com várias opções.
* **Loop for each**. Executa um loop por meio de um conjunto de valores armazenados em uma matriz.
* **Para cada loop de página**. Executa um loop por meio de um grande conjunto de valores armazenados em uma página de uma matriz por vez.
  * **Sair de um loop**. Use para interromper um loop.
  * **Continuar um loop**. Usado para continuar o loop.
* **Ir**para. Passa imediatamente para a ação especificada e continua a execução. Determinada por actionID.

Para obter informações detalhadas e exemplos, consulte a seção [instruções condicionais](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#conditional-statements) no __guia ações em caixas de diálogo adaptáveis-referência__.

### <a name="dialog-management"></a>Gerenciamento de caixa de diálogo

As ações de gerenciamento de diálogo são projetadas para dar a você o controle de qualquer ação relacionada à caixa de diálogo. as ações de gerenciamento de diálogo fornecidas pelo SDK do bot Framework incluem:

* **Iniciar uma nova caixa de diálogo**. Isso inicia a execução de outra caixa de diálogo. Quando essa caixa de diálogo for concluída, a execução do gatilho atual será retomada.
* **Cancelar uma caixa de diálogo**. Cancela a caixa de diálogo ativa. Use quando desejar que a caixa de diálogo seja fechada imediatamente, mesmo que isso signifique parar o processo intermediário.
* **Cancelar todas as caixas de diálogo**. Cancela todas as caixas de diálogo ativas, incluindo qualquer caixa de diálogo pai ativa. Use caso você queira remover todas as caixas de diálogo da pilha; é possível limpar a pilha de caixas de diálogo chamando o método cancelar todas as caixas de diálogo no contexto da caixa de diálogo. Emite o evento `CancelAllDialogs`.
* **Terminar esta caixa de diálogo**. Encerra a caixa de diálogo ativa.  Use quando desejar que a caixa de diálogo seja concluída e retorne os resultados antes de ser encerrada. Emite o evento `EndDialog`.
* **Ativar caixa de diálogo de encerramento**. Encerra o turno atual da conversa sem encerrar a caixa de diálogo.
* **Repita esta caixa de diálogo**. Usado para reiniciar a caixa de diálogo pai.
* **Substitua esta caixa de diálogo**. Substitui a caixa de diálogo atual por uma nova caixa de diálogo.
* **GetConversationMembers**. Permite obter uma lista de membros da conversa e salvá-la em uma propriedade na [memória][memory-states].
* **EditActions**. Permite editar imediatamente a sequência de ação atual com base na entrada do usuário. Especialmente útil ao lidar com interrupções.

Para obter informações detalhadas e exemplos, consulte a seção de [Gerenciamento de diálogo](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#dialog-management) no __guia ações em caixas de diálogo adaptáveis-referência__.

### <a name="manage-properties"></a>Gerenciar propriedades

As ações gerenciar Propriedades possibilitam criar, atualizar e excluir uma propriedade.  Para obter mais informações sobre propriedades, consulte o SDK do bot Framework [gerenciamento de estado] [Bot-Builder-Concept-state.md] e o [estado de gerenciamento em artigos de caixas de diálogo adaptáveis][memory-states] .

* **Editar uma matriz**. Permite executar operações de edição em uma matriz.
* **Excluir uma propriedade**. Permite remover uma propriedade da [memória][memory-states].
* **Excluir Propriedades**. Permite excluir mais de uma propriedade em uma única ação.
* **Criar ou atualizar uma propriedade**. Permite definir o valor de uma propriedade na [memória][memory-states].
* **Criar ou atualizar Propriedades**. Permite inicializar uma ou mais propriedades em uma única ação.

Para obter informações detalhadas e exemplos, consulte a seção [gerenciar Propriedades](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#manage-properties) no __Guia de referência ações em caixas de diálogo adaptáveis__.

### <a name="access-external-resources"></a>Acessar recursos externos

Essas ações permitem que você acesse recursos externos, como habilidades, envio de uma solicitação HTTP, emissão de um evento personalizado ou chamada de código personalizado, etc.

* **Iniciar uma caixa de diálogo de habilidade**. Use a caixa de diálogo adaptável de skill para executar um skill.
* **Enviar uma solicitação HTTP**. Permite fazer solicitações HTTP para qualquer ponto de extremidade.
* **Emitir um evento personalizado**. Permite gerar um evento personalizado para o qual o bot pode responder usando um [gatilho personalizado][custom-event-trigger].
* **Desconectar um usuário**. Permite desconectar o usuário atualmente conectado.
* **Chame o código personalizado**. Permite chamar seu código personalizado.

Para obter informações detalhadas e exemplos, consulte a seção [acessar recursos externos](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#access-external-resources) no __Guia de referência ações em caixas de diálogo adaptáveis__.

### <a name="debugging-options"></a>Opções de depuração

* **Registrar no console**. Grava no console e, opcionalmente, envia a mensagem como uma atividade de rastreamento.
* **Emitir um evento de rastreamento**. Usado para enviar uma atividade de rastreamento com carga de te especificado.

Para obter informações detalhadas e exemplos, consulte a seção [Opções de depuração](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#debugging-options) no __guia ações em caixas de diálogo adaptáveis-referência__.

## <a name="additional-information"></a>Informações adicionais

* Confira mais informações sobre as ações específicas para a coleta de entradas de usuários no artigo [Solicitar entrada do usuário usando caixas de diálogo adaptáveis][inputs].
* Confira mais sobre expressões adaptáveis no artigo [Expressões adaptáveis][adaptive-expressions].
* Para obter informações detalhadas e exemplos sobre todas as ações abordadas neste artigo, consulte o artigo [ações em caixas de diálogo adaptáveis –](../adaptive-dialog/adaptive-dialog-prebuilt-actions.md) referência de guia de referência.

[introduction]:bot-builder-adaptive-dialog-introduction.md
[triggers]:bot-builder-concept-adaptive-dialog-triggers.md
[www.qnamaker.ai]:https://www.qnamaker.ai/
[oauthinput-inputs]:../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#oauthinput
[concept-dialog]:bot-builder-concept-dialog.md
[interruptions-inputs]:../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#interruptions
[inputs]:bot-builder-concept-adaptive-dialog-inputs.md
[custom-event-trigger]:bot-builder-concept-adaptive-dialog-triggers.md#custom-event-trigger
[generators]:bot-builder-concept-adaptive-dialog-generators.md
[adaptive-expressions]:bot-builder-concept-adaptive-expressions.md
[memory-states]:bot-builder-concept-adaptive-dialog-memory-states.md
