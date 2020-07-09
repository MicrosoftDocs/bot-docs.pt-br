---
title: Introdução aos diálogos adaptáveis
description: Introdução aos diálogos adaptáveis
keywords: bot, adaptive dialogs
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 06/24/2020
ms.openlocfilehash: c701746a8cacdab7cd3a28f65fa8256ea66f7b05
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124326"
---
# <a name="introduction-to-adaptive-dialogs"></a>Introdução aos diálogos adaptáveis

As caixas de diálogo adaptáveis oferecem uma nova adição baseada em eventos à [biblioteca de caixas de diálogo][1] que permite que você aplique uma camada facilmente em técnicas sofisticadas de gerenciamento de conversa, como a manipulação de interrupções, a expedição e muito mais.

> [!IMPORTANT]
> Atualmente, as caixas de diálogo adaptáveis só estão disponíveis na versão .NET do SDK do bot Framework. Você pode encontrar bots de exemplo criados usando caixas de diálogo adaptáveis no [repositório BotBuilder-Samples][16] no github.

## <a name="prerequisites"></a>Pré-requisitos

* Uma compreensão dos [diálogos][1] no SDK do Bot Framework V4
* Uma compreensão geral das [solicitações][14] no SDK do Bot Framework V4

## <a name="adaptive-dialogs-defined"></a>Definição de diálogos adaptáveis

### <a name="why-adaptive-dialogs"></a>Por que caixas de diálogo adaptáveis
<!--This needs work-->

As caixas de diálogo adaptáveis têm muitas vantagens para [WaterfallDialogs][17]. Principalmente, eles:

* Forneça flexibilidade que permita que você atualize dinamicamente o fluxo de conversa com base em contexto e eventos. Isso é especialmente prático ao lidar com alternâncias de contexto de conversa e interrupções no meio de uma conversa.
* Dê suporte e fique à frente de um sistema de eventos avançado para caixas de diálogo, portanto, as interrupções de modelagem, o cancelamento e a semântica de planejamento de execução são muito mais fáceis de descrever e gerenciar.
* Traga reconhecimento de entrada e manipulação de eventos baseados em regras
* Combine o modelo de conversa (caixa de diálogo) e a geração de saída em uma unidade coesa e autônoma.
* Suporte a pontos de extensibilidade para reconhecimento, regras de eventos e aprendizado de máquina.
* Foram projetados para serem declarativos desde o início. Isso permite ferramentas que incluem produtos como o [Bot Framework](https://aka.ms/bf-composer-docs-welcome-page) que fornecem uma tela visual para modelar as conversas.

## <a name="anatomy-of-an-adaptive-dialog"></a>Anatomia de um diálogo adaptável  

<p align="center">
    <img alt="Adaptive_dialog_anatomy" src="./media/adaptive-dialogs/adaptive-dialog-anatomy.png" style="max-width:700px;" />
</p>

### <a name="triggers"></a>Gatilhos

Todos os diálogos adaptáveis contêm uma lista de um ou mais manipuladores de eventos chamados _gatilhos_, e cada gatilho contém uma `Condition` opcional e uma lista de uma ou mais _ações_. Os gatilhos são o que permitem que você capture eventos e responda a eles, se a `Condition` for atendida, a `Actions` será executada e, quando um evento for tratado, nenhuma ação adicional será executada nesse evento. Se a `Condition` dos gatilhos não for atendida, o evento será passado para o próximo manipulador de eventos para avaliação.

Confira o artigo [_Eventos e gatilhos em diálogos adaptáveis_][3] para obter mais informações sobre os _gatilhos_ em diálogos adaptáveis.

<!--- If an event is not handled in a child dialog, it gets passed up to its parent dialog to handle and this continues until it is either handled or reaches the bots main dialog. If no event handler is found, it will be ignored and no action will be taken.

_Triggers_ enable you to catch and respond to events. The broadest trigger is the OnEvent trigger that allows you to catch and attach a set of steps to execute when a specific event is emitted by any sub-system. Adaptive dialogs supports a couple of other specialized triggers to wrap common events that your bot would handle. See [here][3] to learn more about supported triggers and their usage.-->

### <a name="actions"></a>Ações

As _ações_ definem o fluxo de conversa quando um evento específico é capturado por meio de um gatilho. Ao contrário de um diálogo em cascata em que cada etapa é uma função, cada ação em um diálogo adaptável é, em si, um diálogo. Isso torna os diálogos adaptáveis eficientes e flexíveis e permite que os diálogos adaptáveis processem com facilidade as interrupções e sejam ramificados condicionalmente com base no contexto ou no estado atual.

O SDK do Bot Framework fornece muitas ações internas para permitir que você execute várias ações, como manipulação de memória, gerenciamento de diálogos e controle do fluxo de conversa do seu bot. Como as ações são, de fato, diálogos, elas são extensíveis, possibilitando a criação de ações personalizadas próprias.

Confira o artigo [_Ações em diálogos adaptáveis_][4] para obter mais informações sobre as _ações_ em diálogos adaptáveis.

### <a name="inputs"></a>Entradas

As _entradas_ são para os diálogos adaptáveis os que as [solicitações][14] são para a classe de diálogo base. As entradas são ações especializadas que você pode usar em um diálogo adaptável para solicitar e validar as informações de um usuário e, em seguida, se a validação for aprovada, aceite a entrada na memória. Todas as classes de entrada do SDK do Bot Framework foram projetadas para fazer o seguinte:

* Execute as verificações existenciais antes da solicitação, para evitar solicitar informações que o bot já tem.
* Salve a entrada na propriedade especificada se ela corresponder ao tipo de entidade esperado.
* Aceitar restrições – mín., máx. etc.

Confira o artigo [Como solicitar a entrada de usuário usando diálogos adaptáveis][5] para obter mais informações sobre as _entradas_ em diálogos adaptáveis.

### <a name="recognizers"></a>Reconhecedores

Os _reconhecedores_ permitem que o bot entenda e extraia informações significativas da entrada do usuário. Todos os reconhecedores emitem eventos como o evento `recognizedIntent`, que é acionado quando o reconhecedor detecta uma intenção (ou extrai entidades) do enunciado de um usuário. Não é necessário usar reconhecedores com um diálogo adaptável, mas se você não os usar, nenhum evento `recognizedIntent` será disparado e, em vez disso, o evento `unknownIntent` será acionado.

Confira o artigo [_Reconhecedores em diálogos adaptáveis_][6] para obter mais informações sobre os _reconhecedores_ em diálogos adaptáveis.

### <a name="generator"></a>Gerador

O _gerador_ vincula um sistema de geração de linguagem específico a um diálogo adaptável. Isso, junto com o reconhecedor, permite a separação clara e o encapsulamento dos ativos de Reconhecimento vocal e Geração de Linguagem de um diálogo específico. Com o recurso [Geração de Linguagem][7], você pode associar o gerador a um arquivo _.lg_ ou definir o gerador como uma instância de `TemplateEngine` em que você gerencia explicitamente um ou mais arquivos _.lg_ que ativam o diálogo adaptável.

Confira o artigo [_Geração de Linguagem em diálogos adaptáveis_][7] para obter mais informações sobre os _geradores_ em diálogos adaptáveis.

### <a name="memory-scopes-and-managing-state"></a>Escopos de memória e gerenciamento de estado

Os diálogos adaptáveis oferecem um modo de acessar e gerenciar a memória. Todos os diálogos adaptáveis, por padrão, usam esse modelo, de modo que todos os componentes que consomem ou contribuem para a memória tenham um método comum para ler e gravar informações no escopo apropriado. Todas as propriedades em todos os escopos são recipientes de propriedades, o que oferece a capacidade de modificar dinamicamente as propriedades que são armazenadas.

Confira o artigo [_Escopos de memória e gerenciamento de estado em diálogos adaptáveis_][7] para obter mais informações sobre os _escopos de memória_ e o _gerenciamento de estado_ em diálogos adaptáveis.

### <a name="declarative-assets"></a>Ativos declarativos

Os diálogos adaptáveis permitem definir o diálogo como uma classe criando um objeto AdaptiveDialog e definindo os gatilhos e as ações no arquivo de origem de classes, mas você também pode criar o diálogo usando uma abordagem declarativa em que define todos os atributos do diálogo em um arquivo JSON com uma extensão de arquivo .dialog.  Nenhum código-fonte é necessário para definir os diálogos e você pode ter vários diálogos usando ambas as abordagens no mesmo bot. Em runtime, o bot vai gerar e executar o código do diálogo conforme definido nesses arquivos de diálogo declarativos.

<!--See the [_Using declarative assets_][9] article for more information on using _declarative assets_ in adaptive dialogs.-->

## <a name="tying-it-all-together"></a>Reunindo tudo isso

### <a name="the-adaptive-dialog-runtime-behavior"></a>O comportamento de runtime do diálogo adaptável

O _bot fictício de Agente de viagens_ a seguir ajudará a ilustrar o comportamento de runtime dos diálogos adaptáveis. Um aplicativo do mundo real terá várias funcionalidades como a capacidade de pesquisar e reservar voos, quartos de hotel, carros e, até mesmo, verificar o clima. Cada uma delas será tratada no próprio diálogo especializado.

O que acontece quando o usuário faz algo inesperado enquanto está no meio de uma conversa com o bot?

Considere este cenário:

```
    User: I’d like to book a flight
    Bot:  Sure. What is your destination city?
    User: How’s the weather in Seattle?
    Bot:  Its 72 and sunny in Seattle
    ...
```

O usuário respondeu à pergunta e mudou totalmente o assunto, o que exigirá um código (ação) completamente diferente que existe em outro diálogo para ser executado. Os diálogos adaptáveis permitem que você processe esse cenário, que é mostrado no seguinte diagrama:

<p align="center">
    <img alt="Adaptive_dialog_runtime_behavior" src="./media/adaptive-dialogs/adaptive-dialog-scenario-setup.png" style="max-width:700px;" />
</p>

Esse bot tem os três seguintes diálogos adaptáveis:

1. O **rootDialog** que tem o próprio modelo 'LUIS' e um conjunto de gatilhos e ações, alguns dos quais chamarão um diálogo filho projetado para tratar solicitações específicas do usuário.
2. O **bookFlightDialog** que tem o próprio modelo 'LUIS' e um conjunto de gatilhos e ações que processam as conversas sobre reservas de voos.
3. O **weatherDialog** que tem o próprio modelo 'LUIS' e um conjunto de gatilhos e ações que processa conversas sobre como obter informações sobre o clima.

Este é o fluxo quando o usuário diz: _Quero reservar um voo_

<p align="center">
    <img alt="Adaptive_dialog_conversation_flow_example" src="./media/adaptive-dialogs/adaptive-dialog-first-utterance.png" style="max-width:700px;" />
</p>

O reconhecedor do diálogo ativo (rootDialog) emite um evento `recognizedIntent` que pode ser processado com um gatilho `OnIntent`. Nesse caso, o usuário disse _"Quero reservar um voo"_ , que corresponde a uma intenção definida em `rootDialog` e faz com que o gatilho `OnIntent` contenha uma ação `BeginDialog` a ser executada, o que chama o diálogo `bookFlightDialog`. O diálogo de reserva de voo executa as ações, uma delas está solicitando a cidade para a qual você deseja ir.

O usuário pode fornecer qualquer coisa em resposta, em alguns casos, a resposta pode não ter nada a ver com a pergunta feita e, nesse caso, o usuário respondeu com _Qual é o clima em Seattle?_

<p align="center">
    <img alt="Adaptive_dialog_interruption_example" src="./media/adaptive-dialogs/adaptive-dialog-second-utterance.png" style="max-width:700px;" />
</p>

Como o diálogo `bookFlightDialog` não tem nenhum gatilho `OnIntent` para processar a solicitação do usuário, o bot propaga o tratamento da entrada desse usuário para o topo da pilha de conversa, acima de todos os diálogos de chamada até o diálogo raiz que, nesse caso, é apenas um diálogo. Além disso, como `rootDialog` tem um gatilho `OnIntent` para processar a intenção `weather`, esse gatilho executa a ação `BeginDialog` que chama o diálogo `weatherDialog`, passando a pergunta dos usuários. Quando `weatherDialog` termina de responder à pergunta dos usuários, o bot retorna o controle novamente ao diálogo de origem e o fluxo de conversa continua no último ponto antes dessa _interrupção_ e pergunta novamente ao usuário a cidade de destino.

Resumidamente:

O _reconhecedor_ de cada diálogo analisa a entrada do usuário para determinar a intenção dele. Quando a intenção é determinada, o _reconhecedor_ emite um evento `IntentRecognized`, que o diálogo processa usando um gatilho `OnIntent`. Se não houver nenhum gatilho `OnIntent` no diálogo ativo que possa processar essa intenção, o bot o enviará para o diálogo pai do diálogo. Se o diálogo pai não tiver um gatilho para processar a intenção, ele será levado para cima até atingir o diálogo raiz. Quando o gatilho que processa essa intenção é concluído, ele envia o controle novamente para o diálogo que iniciou esse processo, no qual ele pode continuar o fluxo de conversa no ponto em que parou.

## <a name="additional-information"></a>Informações adicionais

### <a name="adaptive-concepts"></a>Conceitos adaptáveis
<!-- Links will remain broken until all articles exist-->
* [Eventos e gatilhos em caixas de diálogo adaptáveis][3]
* [Ações em diálogos adaptáveis][4]
* [Como solicitar a entrada do usuário usando diálogos adaptáveis][5]
* [Reconhecedores em caixas de diálogo adaptáveis][6]
* [Geração de linguagem em caixas de diálogo adaptáveis][7]
* [Escopos de memória e gerenciamento de estado em caixas de diálogo adaptáveis][8]
<!--* [Using declarative assets][9]-->

### <a name="how-to-develop-a-bot-using-adaptive-dialogadaptive"></a>Como desenvolver um bot usando o dialogAdaptive adaptável

* [Criar um bot usando caixas de diálogo adaptáveis][10]
<!--* [Create a bot using Declarative adaptive dialogs][11]--->
* [Criar um bot combinando diálogos adaptáveis, de componentes, em cascata e personalizados][12]

[1]:https://aka.ms/bot-builder-concept-dialog
[3]:bot-builder-concept-adaptive-dialog-triggers.md
[4]:bot-builder-concept-adaptive-dialog-actions.md
[5]:bot-builder-concept-adaptive-dialog-inputs.md
[6]:bot-builder-concept-adaptive-dialog-recognizers.md
[7]:bot-builder-concept-adaptive-dialog-generators.md
[8]:bot-builder-concept-adaptive-dialog-memory-states.md
[9]:bot-builder-concept-adaptive-dialog-declarative.md
[10]:bot-builder-dialogs-adaptive.md
[11]:bot-builder-dialogs-declarative-adaptive.md
[12]:bot-builder-mixed-dialogs.md
[13]:bot-builder-concept-adaptive-dialog-inputs.md#interruptions
[14]:https://aka.ms/bot-builder-concept-dialog#prompts
[15]:https://github.com/microsoft/botbuilder-samples/tree/master/experimental/adaptive-dialog
[16]:https://github.com/microsoft/botbuilder-samples/tree/master/samples/csharp_dotnetcore
[17]: https://docs.microsoft.com/azure/bot-service/bot-builder-concept-dialog?view=azure-bot-service-4.0#waterfall-dialogs
