---
title: Diálogos no SDK do Bot Builder | Microsoft Docs
description: Descreve o que é um diálogo e como ele funciona no SDK do Bot Builder.
keywords: fluxo de conversa, reconhecer intenção, turno único, multiturno, conversa de bot, diálogos, solicitações, cascatas, conjunto de diálogo
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 9/22/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2cf5da32b563c310ee201090c938da9ff410a70c
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2018
ms.locfileid: "46708518"
---
# <a name="dialogs-library"></a>Biblioteca de diálogos

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

O conceito central no SDK para gerenciar conversas é a ideia de um Diálogo. Os objetos de diálogo processam atividades de entrada e geram respostas de saída. A lógica de negócios do bot é executada direta ou indiretamente dentro de classes Dialog.

Em tempo de execução, as instâncias de diálogo são organizadas em uma pilha. O diálogo no topo da pilha é conhecido como ActiveDialog. O diálogo atual processa a atividade de entrada. Entre cada turno da conversa (que não tem relação com o tempo e pode durar vários dias), a pilha é persistida. 

Um diálogo implementa três funções principais:
- BeginDialog
- ContinueDialog
- ResumeDialog

No tempo de execução, os diálogos e a classe DialogContext trabalham juntos para escolher o diálogo adequado para lidar com a atividade. A classe DialogContext vincula a pilha Dialog persistida, a atividade de entrada e a classe DialogSet. Um DialogSet contém diálogos que o bot pode chamar.

A interface do DialogContext reflete a noção subjacente do início e da continuidade do diálogo. O padrão geral para o aplicativo é sempre chamar ContinueDialog primeiramente. Se não houver nenhuma pilha e, portanto, nenhum ActiveDialog, o aplicativo deverá começar o diálogo escolhido chamando BeginDialog em DialogContext. Isso fará com que a entrada de diálogo correspondente a DialogSet seja enviada por push à pilha (tecnicamente, o ID do diálogo é que é adicionado à pilha) e delegue uma chamada a BeginDialog no objeto de diálogo específico. Se fosse um ActiveDialog, ele simplesmente teria delegado a chamada ao ContinueDialog do diálogo, dando a esse diálogo todas as propriedades persistidas no processamento.

Observe que um **BeginDialog de um diálogo** é o código de inicialização e usa propriedades de inicialização (chamadas de "options" no código), e um **ContinueDialog do diálogo** é o código executado para continuar a execução na chegada de uma atividade após a persistência. Por exemplo, imagine um diálogo que faz uma pergunta ao usuário: a pergunta seria feita em BeginDialog e a resposta, esperada em ContinueDialog.

Para dar suporte ao aninhamento de diálogos (em que um diálogo tem um diálogo filho), há outro tipo de continuação; ela é chamada de continuidade. O DialogContext chamará o método ResumeDialog em um diálogo pai quando um diálogo filho for concluído.

As solicitações e as cascatas são ambos exemplos concretos de diálogos fornecidos pelo SDK. Muitos cenários são criados pela composição essas abstrações, mas, nos bastidores, a lógica executada é sempre com o mesmo início, ou seja, o padrão de continuar e retomar descrito aqui. A implementação de uma classe Dialog do zero é um tópico relativamente avançado, mas um exemplo está incluído nas [amostras](https://github.com/Microsoft/BotBuilder-samples).

A biblioteca **Diálogos** no SDK do Bot Builder inclui recursos integrados, como _solicitações_, _diálogos de cascata_ e _diálogos de componente_, para ajudar a gerenciar a conversa do bot. Você pode usar solicitações para perguntar aos usuários diferentes tipos de informações, cascatas para combinar várias etapas em uma sequência e diálogos de componente para empacotar sua lógica de diálogo em classes separadas que podem ser integradas a outros bots.
## <a name="waterfall-dialogs-and-prompts"></a>Solicitações e diálogos de cascata

A biblioteca **Diálogo** vem com um conjunto de tipos de solicitação que você pode usar para coletar vários tipos de informações do usuário. Por exemplo, para solicitar informações a um usuário, você pode usar o **TextPrompt**; para pedir um número a um usuário, você pode usar o **NumberPrompt**; para pedir data e hora, você pode usar o **DateTimePrompt**. As solicitações são um tipo específico de diálogo. Para usar uma solicitação em um diálogo de cascata, adicione a cascata e a solicitação ao mesmo conjunto de diálogo. 

Devido à natureza da resposta da interação solicitação-resposta, a implementação de uma solicitação exige pelo menos duas etapas em um diálogo de cascata: uma para enviar a solicitação e outra para capturar e processar a resposta.  Se você tiver uma solicitação adicional, poderá combiná-las usando uma única função para processar primeiro a resposta do usuário e, em seguida, iniciar a solicitação seguinte.

Uma `WaterfallDialog` é uma implementação específica de um diálogo usada para coletar informações do usuário ou orientá-lo por uma série de tarefas. As tarefas são implementadas como uma matriz de funções, em que os resultados da primeira função são passados como um argumento para a função seguinte e assim por diante. Cada função normalmente representa uma etapa no processo geral. Em cada etapa, um bot solicita que o usuário insira uma entrada, aguarda uma resposta e depois passa o resultado para a próxima etapa. 

Solicitações e Cascata são ambas diálogos, conforme mostrado na hierarquia de classe a seguir. 

![classes de diálogo](media/bot-builder-dialog-classes.png)

Um diálogo de cascata é composto de uma sequência de etapas de cascata. Cada etapa é um delegado assíncrono que usa um parâmetro de _contexto da etapa de cascata_ (`step`). O padrão é que a última coisa feita em uma etapa de cascata seja iniciar um diálogo filho (normalmente uma solicitação) ou terminar a cascata em si. O diagrama a seguir mostra uma sequência de etapas de cascata e as operações de pilha que ocorrem.

![Conceito de diálogo](media/bot-builder-dialog-concept.png)

Você pode manipular um valor retornado de um diálogo dentro de uma etapa de cascata em um diálogo ou no manipulador do turno de seu bot.
Dentro de uma etapa de cascata, o diálogo fornece o valor retornado na propriedade _result_ do contexto da etapa de cascata.
Normalmente, você só precisa verificar o status do resultado do turno do diálogo na lógica de turno de seu bot.

## <a name="repeating-a-dialog"></a>Repetindo um diálogo

Para repetir um diálogo, utilize o método *replace dialog*. O método *replace dialog* do contexto do diálogo destacará o diálogo atual na fila, enviará o diálogo de substituição por push para o topo da fila e o iniciará. Você pode usar esse método para criar um loop substituindo um diálogo por ele próprio. Observe que, se você precisar persistir o estado interno do diálogo atual, precisará passar informações para a nova instância do diálogo na chamada para o método _replace dialog_ e inicializar o diálogo adequadamente. As opções passadas para o novo diálogo podem ser acessadas por meio da propriedade _options_ do contexto da etapa em qualquer etapa do diálogo. Isso é uma ótima maneira de lidar com um fluxo de conversa complexo ou gerenciar menus.

## <a name="branch-a-conversation"></a>Ramificar uma conversa

O contexto do diálogo mantém uma _pilha de diálogos_ e, para cada diálogo na pilha, controla a etapa a seguir. O método _begin dialog_ envia um diálogo para o topo da pilha e o método _end dialog_ retira o primeiro diálogo da pilha.

Um diálogo pode iniciar um novo diálogo no mesmo conjunto de diálogo chamando o método _begin dialog_ do contexto do diálogo e fornecendo o ID do novo diálogo, o que transformará o novo diálogo em diálogo ativo. O diálogo original ainda está na pilha, mas as chamadas para o método _continue dialog_ do contexto do diálogo só são enviadas para o diálogo que está no topo da pilha, o _diálogo ativo_. Quando um diálogo é retirado da pilha, o contexto do diálogo é retomado com a próxima etapa da cascata na pilha em que ele parou em relação ao diálogo original.

Assim, você pode criar uma ramificação dentro do fluxo da sua conversa incluindo uma etapa em um diálogo que possa escolher condicionalmente um diálogo para iniciar a partir de um conjunto de diálogos disponíveis.

## <a name="component-dialog"></a>Diálogo de componente
Às vezes você deseja codificar um diálogo reutilizável para usar em cenários diferentes. Um exemplo pode ser um diálogo de endereço que pede ao usuário para fornecer valores de rua, cidade e CEP. 

O ComponentDialog fornece um nível de isolamento porque tem um DialogSet separado. Por ter um DialogSet separado, ele evita conflitos de nome com o pai que contém o diálogo, cria seu próprio tempo de execução interno do diálogo independente (criando seu próprio DialogContext) e expede a atividade para ele. Essa expedição secundária significa que ele teve a oportunidade de interceptar a atividade. Isso poderá ser muito útil se você quiser implementar recursos como "help" e "cancel".  Confira o exemplo de Modelo de Bot do Enterprise. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Use a biblioteca de caixa de diálogo para coletar entrada do usuário](bot-builder-prompts.md)
