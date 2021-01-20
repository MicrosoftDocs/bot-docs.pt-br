---
title: Como fazer um treinamento cruzado dos seus modelos LUIS e QnA Maker
description: Descrevendo os conceitos por trás de um bot com treinamento cruzado para usar LUIS para LUIS e LUIS para reconhecedores de QnA Maker
keywords: LUIS, QnA Maker, QnA, bot, treinamento cruzado, treinamento cruzado, caixas de diálogo adaptáveis, Lu
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: how-to
ms.service: bot-service
ms.date: 11/1/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d2fddf8fa49d48977437b85c7bc3deef94fee855
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577448"
---
# <a name="cross-training-your-luis-and-qna-maker-models"></a>Como fazer um treinamento cruzado dos seus modelos LUIS e QnA Maker

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Você pode cruzar os arquivos. Lu e. QnA para que cada caixa de diálogo adaptável Conheça os recursos das outras caixas de diálogo adaptáveis em seu bot. Isso permite que a caixa de diálogo adaptável ativa seja adiada para outro reconhecedor, na mesma caixa de diálogo ou em outra, quando um usuário insere uma solicitação ou entrada que não pode ser manipulada por conta própria.

## <a name="introduction-to-cross-training"></a>Introdução ao treinamento cruzado

As caixas de diálogo adaptáveis oferecem uma maneira centrada em caixa de diálogo para modelar conversas, com cada caixa de diálogo adaptável com seu próprio modelo de compreensão de linguagem (LU). Embora isso proporcione uma enorme flexibilidade aos desenvolvedores de bot, ele também pode apresentar alguns desafios. O treinamento cruzado pode ser útil quando uma caixa de diálogo adaptável pode querer saber quando o usuário expressa algo que poderia ser tratado por outra caixa de diálogo dentro do bot. Aqui estão alguns exemplos em que o treinamento cruzado pode ser útil.

- Um bot que permite aos usuários cancelar o fluxo de conversação atual ou pedir ajuda, sem duplicar as tentativas de cancelamento ou de ajuda em todas as caixas de diálogo adaptáveis.
- Um bot que dá suporte a conversas não lineares, em que um usuário pode imaginar algo que tenha esquecido anteriormente ou simplesmente mudar de ideia até uma tarefa.
- Um bot que permite que um usuário corrija as informações fornecidas anteriormente.

No artigo [lidando com interrupções em caixas de diálogo adaptáveis][interruptions], o conceito de interrupções em caixas de diálogo adaptáveis é introduzido. Ele explica como uma caixa de diálogo adaptável pai pode ser consultada quando o reconhecedor de caixa de diálogo adaptável ativa não encontrar uma correspondência adequada. Nesse caso, a caixa de diálogo ativa não sabe se uma caixa de diálogo pai ou irmã pode responder; portanto, para descobrir, ela deve enviar declarações ou pergunta ao seu pai usando o mecanismo de _consulta_ do bot Framework.

O treinamento cruzado pode se basear e melhorar os recursos fornecidos por interrupções de algumas maneiras:

1. **Treinamento de caixa de diálogo cruzada**. Ao treinar os modelos de LU de todas as caixas de diálogo adaptáveis em seu bot, você dá a cada caixa de diálogo a capacidade de saber se outras caixas de diálogo são capazes de responder a uma solicitação de usuário. Dessa forma, o bot não precisa consultar todo o caminho da pilha de diálogo para descobrir se outra caixa de diálogo pode processar de forma adequada uma entrada de usuário específica. Isso é descrito mais detalhadamente na seção de [treinamento cruzado de Luis para Luis](#luis-to-luis-cross-training) .

1. **Treinamento na caixa de diálogo**. Este é um treinamento cruzado de diferentes mecanismos de reconhecimento de linguagem na mesma caixa de diálogo. LUIS e QnA Maker são mecanismos de reconhecimento de linguagem diferentes. Depois que os modelos de cada um são treinados em forma cruzada, o reconhecedor de ambos pode ser consultado para determinar qual é o mais adequado para responder a uma solicitação de usuário. Isso é descrito mais detalhadamente na seção [Luis de treinamento cruzado e modelos de QnA Maker](#cross-train-luis-and-qna-maker-models) .

> [!TIP]
>
> Se os modelos de reconhecimento de linguagem associados a várias caixas de diálogo adaptáveis em um bot não forem treinados em cruz, nenhuma declarações ou pergunta de outras caixas de diálogo serão consideradas, a menos que o reconhecedor da caixa de diálogo ativa retorne uma intenção desconhecida. Quando os modelos tiverem sido treinados de forma cruzada, declarações e perguntas das caixas de diálogo pai e irmã sempre serão consideradas porque estão associadas a uma nova tentativa criada no modelo de reconhecimento de linguagem da caixa de diálogo ativa. Um pai não deve mais ser consultado para descobrir se ele pode responder à entrada dos usuários. Quando essa nova intenção é retornada, a estrutura de bot sabe consultar o pai ou irmão para tratá-la.

## <a name="luis-to-luis-cross-training"></a>Treinamento cruzado de LUIS para LUIS

Ao treinar os modelos LUIS em seu bot, você melhora a capacidade do bot de lidar com _interrupções globais_. Isso significa que, se não houver gatilhos na caixa de diálogo adaptável ativa que possa lidar com a intenção retornada pelo reconhecedor, o bot irá propagar até a caixa de diálogo pai da caixa, usando o _mecanismo de consulta_ de caixas de diálogo adaptáveis.  Se a caixa de diálogo pai não tiver um gatilho para lidar com a intenção, ela continuará a emergir até atingir a caixa de diálogo raiz. Depois que a intenção é tratada, o fluxo de conversa continua de onde parou. Para obter uma discussão mais detalhada sobre interrupções globais, consulte [manipulando interrupções globalmente][Global-interrupts] no artigo conceitos de _interrupções no controle de caixas de diálogo adaptáveis_ . Para modelos de LUIS entre treinados com outros modelos de LUIS, você usará o [reconhecedor do Luis][luis-recognizer].

Os usos comuns para interrupções globais incluem a criação de recursos básicos de gerenciamento de caixas de diálogo, como ajuda ou cancelamento na caixa de diálogo raiz que, em seguida, estão disponíveis para qualquer uma de suas caixas de diálogo filhas, conforme mencionado na introdução. As interrupções globais também permitem que os usuários alterem diretamente a direção do fluxo de conversação. Considere o exemplo a seguir neste bot de viagem fictícia.

### <a name="travel-bot"></a>Bot de viagem

O exemplo de bot de viagem a seguir demonstra o treinamento cruzado de um bot com várias caixas de diálogo adaptáveis, cada uma com seu próprio modelo LUIS.

Esse bot consiste em três caixas de diálogo adaptáveis, cada uma com seu próprio modelo de LUIS:

![Diagrama do bot de viagem](./media/adaptive-dialogs/travel-bot.png)

1. RootDialog
    - Tentativas: `BookFlight` , `BookHotel` .
1. flightDialog
    - Tentativas: `flightDestination` , `departureTime` .
1. hotelDialog
    - Tentativas: `hotelLocation` , `hotelRating` .

#### <a name="travel-bot-customer-scenario"></a>Cenário do cliente de bot de viagens

A troca a seguir demonstra um cenário potencial de uso do cliente que não pode ser manipulado pelo bot, mas pode ser após o treinamento cruzado dos modelos LUIS do bot.

> **Bot**: Olá, como posso ajudá-lo hoje mesmo?
>
> **Usuário**: Eu quero reservar um vôo
>
> **Bot**: ótimo, eu posso ajudá-lo com isso. Qual é sua data de partida?

O bot estará esperando uma resposta para a pergunta _qual é a sua data de partida?_, algo como "29 de fevereiro", mas como ele tratará uma interrupção no fluxo de conversação quando o usuário responder com algo que a caixa de diálogo de voo não pode manipular? Algo semelhante ao seguinte:

>
> **Usuário**: preciso reservar uma sala primeiro

No exemplo acima, quando o usuário solicitou o livro de um vôo, o reconhecedor da caixa de diálogo raiz retornou a `BookFlight` intenção. Isso é executado `flightDialog` , uma caixa de diálogo adaptável que processa vôos de reservas, mas não sabe nada sobre hotéis. Quando o usuário solicita uma reserva de Hotel, a caixa de diálogo voo não pode entender, pois o expressão "preciso reservar uma sala primeiro" não corresponde a nenhuma intenções no modelo de caixas de diálogo de voo LUIS.

Após o treinamento cruzado dos arquivos. Lu, seu bot agora será capaz de detectar que o usuário está solicitando algo que outra caixa de diálogo possa responder, de modo que ele passa a solicitação até seu pai, nesse caso, a caixa de diálogo raiz, que detecta a `BookHotel` intenção. Isso é executado `hotelDialog` , uma caixa de diálogo adaptável que processa reservas de Hotel. Depois que a caixa de diálogo do Hotel concluir a solicitação de reserva do Hotel, o controle será passado de volta para a caixa de diálogo voo para concluir a reserva de voo.

#### <a name="cross-train-the-luis-models-of-the-travel-bot"></a>Treine os modelos LUIS do bot de viagem

Para habilitar esse bot de viagem fictícia para lidar com a interrupção no exemplo anterior, você precisa atualizar o modelo LUIS da caixa de diálogo de voo, contido no arquivo **flightDialog.Lu** , para incluir uma nova intenção chamada `_interruption` e, em seguida, adicionar o declarações para a `BookHotel` intenção. O arquivo **flightDialog.Lu** é usado para criar seu aplicativo Luis associado à caixa de diálogo voo.

> [!TIP]
>
> O treinamento cruzado de todos os modelos de LUIS em um bot típico pode ser um processo muito envolvido e entediante. Há um comando incluído na interface de linha de comando do bot Framework (BF CLI) que automatiza esse trabalho para você. Isso é discutido em detalhes na seção [de comando do bot Framework CLI entre os treinamentos](#the-bot-framework-cli-cross-train-command) abaixo.

Antes dessa atualização, o arquivo de exemplo de reserva de voo. Lu é semelhante a este:

```lu
# flightDestination
- book a flight

# departureTime
- I need to depart next thursday
```

Após o treinamento cruzado com o arquivo de reservas. Lu do Hotel, ele ficaria assim:

```lu
# flightDestination
- book a flight

# departureTime
- I need to depart next thursday

# _Interruption
- reserve a hotel room
```

O expressão _reserva uma sala de Hotel_ está associado à `_interruption` intenção. Quando a `_interruption` intenção é detectada, ela exibe qualquer expressão associado a ela à caixa de diálogo pai, cujo reconhecedor retorna a `BookHotel` intenção. Quando LUIS de treinamento cruzado para LUIS, você precisa incluir todas as declarações de usuário de todas as intenções da caixa de diálogo com a qual você está com treinamento cruzado.

![Diagrama do bot de viagem após o treinamento cruzado](./media/adaptive-dialogs/after-cross-train.png)

> [!IMPORTANT]
>
> As previsões de LUIS são influenciadas pelo número de declarações em cada tentativa. Se você tiver uma intenção com 100 de exemplo declarações e uma intenção com 20 exemplos de declarações, a intenção de 100-expressão terá uma taxa mais alta de previsão e será mais provável que seja selecionada. Isso pode afetar os modelos com treinamentos cruzados porque todos os declarações dos modelos de todas as caixas de diálogo pai e irmã se tornam declarações da nova `_Interruption` intenção. Em alguns casos, isso pode resultar em uma caixa de diálogo pai ou irmã respondendo ao usuário quando as correspondências aceitáveis tiverem sido retornadas pelo reconhecedor de caixas de diálogo atuais antes do treinamento cruzado. Minimize os efeitos disso, se necessário, limitando o número de declarações de exemplo na nova `_Interruption` tentativa.

## <a name="cross-train-luis-and-qna-maker-models"></a>LUIS de treinamento cruzado e modelos de QnA Maker

Um bot bem projetado pode responder a perguntas relevantes de produtos ou serviços feitas por um usuário, independentemente de qual caixa de diálogo está ativa no momento. O LUIS é ideal para lidar com fluxos de conversação, enquanto QnA Maker é ideal para lidar com perguntas frequentes e de usuário. Ter acesso a ambos em suas caixas de diálogo adaptáveis pode melhorar a capacidade de bots para atender às necessidades dos usuários.

Para habilitar essa funcionalidade, _treine_ os arquivos. Lu e. QnA para incluir as informações exigidas pelo reconhecedor para determinar qual resposta, LUIS ou QnA Maker, é a mais adequada para o usuário. Para um modelo de LUIS entre treinados com um modelo de QnA Maker, você usará o [conjunto de reconhecedor com treinamento cruzado][cross-trained-recognizer-set-concept].

Antes de criar seus aplicativos LUIS e QnA Maker base de dados de conhecimento, você precisa _treinar_ os arquivos. Lu e. QnA para incluir as informações exigidas pelo reconhecedor do bot para determinar se o Luis ou a resposta QnA Maker é mais adequada para o usuário.

Para cada caixa de diálogo adaptável que tem um arquivo. Lu e. QnA associado, as seguintes atualizações são feitas durante o treinamento cruzado desses arquivos:

1. Em arquivos. Lu, uma nova tentativa chamada `DeferToRecognizer_qna_<dialog-file-name>` é adicionada. Cada variação de pergunta e pergunta do arquivo. QnA correspondente torna-se um expressão associado a essa nova intenção.<!-- Answers are not copied to the .lu file from the .qna file.-->

1. Em arquivos. QnA, uma nova resposta chamada `intent=DeferToRecognizer_luis_<dialog-file-name>` é adicionada, juntamente com cada expressão de cada tentativa no arquivo. Lu correspondente. Esses declarações se tornam perguntas associadas a essa resposta. Além disso, todos os declarações dos arquivos. Lu referenciados também se tornam perguntas associadas a essa resposta.

Quando um usuário é inverso com o bot, o `CreateCrossTrainedRecognizer` reconhecedor envia essa entrada do usuário para Luis e a base de dados de conhecimento QnA Maker a ser processada.

### <a name="recognizer-responses"></a>Respostas do reconhecedor

A tabela a seguir mostra a matriz de respostas possíveis e a ação resultante realizada pelo bot.

<!--![Response table](./media/adaptive-dialogs/luis-qna-maker-runtime-cross-train-results-matrix.png)-->

| Retornos do reconhecedor LUIS | Reconhecedor QnA Maker retorna | Resultados finais                     |
| ----------------------- | ---------------------------- | --------------------------------- |
| Tentativa de LUIS válida       |  Adiar para **Luis** intenção    |  Selecione resposta do reconhecedor **Luis** . Manipule usando o `OnIntent` gatilho.|
| Adiar para **QnA Maker** intenção | Tentativa de QnA Maker válida |  Selecione resposta do reconhecedor **QnA Maker** . Manipule usando o `OnQnAMatch` gatilho.|
| Adiar para **QnA Maker** intenção | Adiar para **Luis** intenção | O `UnknownIntent` evento é emitido pelo reconhecedor. Manipule usando o `OnUnknownIntent` gatilho.|
| Tentativa de LUIS válida | Tentativa de QnA Maker válida | O `ChooseIntent` evento é emitido pelo reconhecedor. Manipule usando o `OnChooseIntent` gatilho.|

> [!IMPORTANT]
>
> O treinamento cruzado de todos os modelos LUIS e QnA Maker em um bot típico pode ser um processo muito envolvido e entediante. Há um comando incluído na interface de linha de comando do bot Framework (BF CLI) que automatiza esse trabalho para você. Isso é discutido em detalhes na seção [de comando do bot Framework CLI entre os treinamentos](#the-bot-framework-cli-cross-train-command) abaixo.
>
> A execução desse comando em um projeto de bot que tenha modelos LUIS e QnA Maker irá automaticamente fazer o treinamento automático do LUIS para o LUIS em todas as caixas de diálogo adaptáveis em todo o projeto, bem como LUIS a QnA Maker de treinar em todas as caixas de diálogo adaptáveis que têm ambos os modelos, ou seja, os arquivos. Lu e. QnA.

### <a name="cross-train-multiple-luis-and-qna-maker-models"></a>Treinamento cruzado de vários modelos de LUIS e de QnA Maker

O treinamento cruzado de um bot com modelos LUIS e de QnA Maker melhora as interrupções globais, conforme descrito anteriormente em [Luis a Luis cross training](#luis-to-luis-cross-training). Isso também se aplica a QnA Maker. Por exemplo:

- Quando o modelo LUIS da caixa de diálogo raiz tem treinamento cruzado com o modelo de QnA Maker da caixa de diálogo raiz, o comando cria a `DeferToRecognizer_qna` intenção em RootDialog.Lu, com todas as perguntas listadas como declarações.
- Em seguida, quando o filho da caixa de diálogo raiz tem treinamento cruzado, ele pega essas intenções e, por sua vez, passa-as para sua caixa de diálogo filho e isso continua até que não haja mais caixas de diálogo filhas.
- Quando um usuário faz qualquer pergunta associada ao RootDialog. QnA quando a caixa de diálogo ativa é um filho ou descendente, a caixa de diálogo ativa não poderá responder, mas, como ele foi treinado de forma cruzada, saberá que outra caixa de diálogo é capaz de responder e, em seguida, irá emergir a pergunta para seu pai. A pergunta, por sua vez, é até a caixa de diálogo raiz, que responde à pergunta antes de retornar o controle de volta para o fluxo de conversação anterior.

A vantagem das interrupções globais nesse cenário é a capacidade que ele fornece para usar uma base de dados de conhecimento QnA Maker associada à caixa de diálogo raiz para lidar com todas as perguntas que o usuário pode ter, independentemente de onde estão em sua conversa com o bot.

> [!NOTE]
>
> As interrupções globais resultam em várias transações para os serviços LUIS e QnA Maker. Quanto mais profunda for a hierarquia de diálogo, mais transações poderão ocorrer em uma determinada solicitação de usuário. Esse aumento nas transações pode ser algo a considerar ao projetar o bot.

## <a name="the-bot-framework-cli-cross-train-command"></a>O comando de treinamento cruzado da CLI do bot Framework

Treinamento cruzado seu bot pode se tornar rapidamente uma tarefa desafiadora e propenso a erros até mesmo um bot minimamente complexo, especialmente quando você ainda estiver fazendo atualizações frequentes nos modelos LUIS ou QnA Maker. O SDK do bot Framework fornece uma ferramenta para automatizar esse processo. Para obter informações sobre o comando de treinamento cruzado da CLI do bot Framework, consulte a seção _conjunto de reconhecedor com treinamento cruzado_ do [Guia de referência de reconhecedores em caixas de diálogo adaptáveis](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#cross-trained-recognizer-set).

## <a name="source-code-updates"></a>Atualizações de código-fonte

Depois de treinar o LUIS e os modelos de QnA Maker, você precisa ter certeza e usar o conjunto de reconhecedores com treinamento cruzado como o reconhecedor de caixas de diálogo adaptáveis em seu código-fonte, conforme explicado no [Guia de referência de reconhecedores em caixas de diálogo adaptáveis][crosstrainedrecognizerset-ref-guide].

## <a name="additional-information"></a>Informações adicionais

- Como [criar um bot entre treinados para usar os reconhecedores Luis e QnA Maker](bot-builder-howto-cross-train.md)
- A seção [conjunto de reconhecedor com treinamento cruzado][crosstrainedrecognizerset-ref-guide] dos reconhecedores em caixas de diálogo adaptáveis – guia de referência.

<!------------------------------------------------------------------------------------------------------------->
[intents]: bot-builder-concept-adaptive-dialog-recognizers.md#intents
[utterances]: bot-builder-concept-adaptive-dialog-recognizers.md#utterances
[interruptions]: bot-builder-concept-adaptive-dialog-interruptions.md
[Global-interrupts]: bot-builder-concept-adaptive-dialog-interruptions.md#handling-interruptions-globally
[luis]: /azure/cognitive-services/luis/what-is-luis
[luis-recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md#luis-recognizer
[cross-trained-recognizer-set-concept]: /azure/cognitive-services/luis/what-is-luis
[luis-recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set
[luis-build]: bot-builder-howto-bf-cli-deploy-luis.md#create-and-train-a-luis-app-then-publish-it-using-the-build-command
[bf-luiscross-train]: https://aka.ms/botframework-cli#bf-luiscross-train
[lu-templates]: ../file-format/bot-builder-lu-file-format.md
[qnamaker]: /azure/cognitive-services/QnAMaker/Overview/overview
[qnamaker-recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md#qna-maker-recognizer
[qna-file-format]: ../file-format/bot-builder-qna-file-format.md
[qnamaker-build]: bot-builder-howto-bf-cli-deploy-qna.md#create-a-qna-maker-knowledge-base-and-publish-it-to-production-using-the-build-command
[recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md
[cross-trained-recognizer-set-concept]: bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set
[crosstrainedrecognizerset-ref-guide]: ../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#cross-trained-recognizer-set
[bf-cli]: bf-cli-overview.md
[language-generation]: bot-builder-concept-adaptive-dialog-generators.md
