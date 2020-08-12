---
title: Caixas de diálogo no SDK do Bot Framework – Serviço de Bot
description: Descreve o que é um diálogo e como ele funciona no SDK do Bot Framework.
keywords: fluxo de conversa, caixas de diálogo, estado da caixa de diálogo, conversa de bot, conjunto de caixas de diálogo, contexto de diálogo, pilha de diálogo
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/03/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8a8e3bd52fa1b2125d9d044d4d8c1abaf6d9191e
ms.sourcegitcommit: 7bf72623d9abf15e1444e8946535724f500643c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143255"
---
# <a name="dialogs-library"></a>Biblioteca de diálogos

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

As *caixas de diálogo* são um conceito central no SDK, fornecendo maneiras de gerenciar uma conversa de execução longa com o usuário.
Uma caixa de diálogo executa uma tarefa que pode representar parte do ou um thread de conversação completo.
Ele pode abranger apenas uma vez ou muitos e pode abranger um curto período de tempo.

Este artigo descreve as principais classes e recursos da biblioteca de caixas de diálogo.
Você deve estar familiarizado com [a forma como os bots funcionam](bot-builder-basics.md) (incluindo [o que é uma vez](bot-builder-basics.md#defining-a-turn)) e o [estado de gerenciamento](bot-builder-concept-state.md).

Cada caixa de diálogo representa uma tarefa de conversação que pode ser executada até a conclusão e retornar informações coletadas.
Cada caixa de diálogo representa uma unidade básica de fluxo de controle: ela pode começar, continuar e terminar; pausar e retomar; ou ser cancelado.

<!--
You manage a dialog through its inputs and outputs. When the dialog starts, it receives a dialog option, as an optional argument. On each turn, the dialog is passed the current turn context, which includes the current activity. When the dialog ends, it can return a value. In effect, a bot can give control to a dialog, and a dialog can give control to another dialog, and so on.
-->

Caixas de diálogo são semelhantes a um método ou função em uma linguagem de programação. Você pode passar argumentos ou parâmetros quando inicia uma caixa de diálogo e a caixa de diálogo pode produzir um valor de retorno quando ele termina.

## <a name="dialog-state"></a>Estado do diálogo

As caixas de diálogo são uma maneira de implementar uma _conversa de passagem múltipla_e, como tal, elas dependem do _estado persistente_ entre elas. Sem estado em caixas de diálogo, seu bot não saberia onde estava na conversa ou quais informações já foram reunidas.

<!--A bot is inherently stateless.-->
Para manter o local de uma caixa de diálogo na conversa, o estado da caixa de diálogo deve ser recuperado e salvo na memória cada vez. Isso é tratado por meio de um acessador de propriedade de estado de caixa de diálogo definido no estado de conversa do bot. Esse estado de caixa de diálogo coleta informações de todas as caixas de diálogo ativas e filhos de caixas de diálogo ativas.
Isso permite que o bot pegue onde parou por último e lide com uma variedade de modelos de conversa.

Em tempo de execução, a propriedade de estado da caixa de diálogo inclui informações sobre onde a caixa de diálogo está em seu processo lógico, incluindo quaisquer informações coletadas internamente na forma de um objeto de _instância de caixa de diálogo_ . Novamente, isso precisa ser lido no bot e salvo na memória cada vez.

## <a name="dialog-infrastructure"></a>Infraestrutura de diálogo

Juntamente com vários tipos de caixas de diálogo, as seguintes classes estão envolvidas no design e no controle de conversas.
Embora normalmente você não precise interagir com essas classes diretamente, estar ciente delas e sua finalidade é útil ao criar caixas de diálogo para um bot.

| Classe | Descrição
| :--   | :--
| _Conjunto de diálogo_ | Define uma coleção de caixas de diálogo que podem fazer referência entre si e trabalhar em conjunto.
| _Contexto do diálogo_ | Contém informações sobre todas as caixas de diálogo ativas.
| _Instância de caixa de diálogo_ | Contém informações sobre uma caixa de diálogo ativa.
| _Caixa de diálogo ativar resultado_ | Contém informações de status de uma caixa de diálogo ativa ou recentemente ativa. Se a caixa de diálogo ativa for encerrada, ela conterá seu valor de retorno.

> [!NOTE]
> Como parte da introdução de caixas de diálogo adaptáveis, a versão 4,9 do SDK do C# introduziu uma classe de _Gerenciador de caixas de diálogo_ que automatiza muitas das tarefas de gerenciamento de caixa de diálogo para você.
> (Caixas de diálogo adaptáveis estão em visualização na versão 4,10 do SDK do JavaScript.) Para obter mais informações, consulte [introdução às caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-Introduction.md).
> <!-- You can use a dialog manager to run any type of dialog. -->

## <a name="dialog-types"></a>Tipos de diálogo

A biblioteca de caixas de diálogo fornece alguns tipos de caixas de diálogo para facilitar o gerenciamento das conversas de bot.

| Type | Descrição
| :--  | :--
| _'_ | A classe base para todas as caixas de diálogo.
| _caixa de diálogo contêiner_ | A classe base para todas as caixas de diálogo de _contêiner_ , como componentes e caixas de diálogo adaptáveis. Ele mantém um conjunto de diálogo interno e permite tratar uma coleção de caixas de diálogo como uma unidade.
| _caixa de diálogo componente_ | Um tipo de uso geral de caixa de diálogo de contêiner que encapsula um conjunto de caixas de diálogo, permitindo a reutilização do conjunto como um todo. Quando uma caixa de diálogo de componente é iniciada, ela começa com uma caixa de diálogo designada em sua coleção. Quando o processo interno for concluído, a caixa de diálogo do componente será encerrada.
| _caixa de diálogo cascata_ | Define uma sequência de etapas, permitindo que o bot Oriente um usuário por meio de um processo linear. Normalmente, eles são projetados para funcionar no contexto de uma caixa de diálogo de componente.
| _caixas de diálogo de prompt_ | Solicitar entrada ao usuário e retornar o resultado. Um prompt será repetido até obter uma entrada válida ou cancelado. Elas foram projetadas para trabalhar com caixas de diálogo de cascata.
| _caixa de diálogo adaptável_ | Um tipo de caixa de diálogo de contêiner que permite o fluxo de conversa flexível. Ele inclui suporte interno para reconhecimento de idioma, geração de linguagem e recursos de escopo de memória. Para executar uma caixa de diálogo adaptável (ou outra caixa de diálogo que contenha uma caixa de diálogo adaptável), você deve iniciá-la em um gerenciador de caixas de diálogo.
| _caixas de diálogo de ação_ | Representam estruturas programáticas. Eles permitem que você projete um fluxo de conversa muito parecido com expressões e instruções em uma linguagem de programação tradicional, permitindo que você projete o fluxo de procedimentos em um aplicativo. Eles só funcionam dentro de uma caixa de diálogo adaptável.
| _caixas de diálogo de entrada_ | Peça à entrada do usuário, muito parecida com as caixas de diálogo de prompt, mas pode interagir com outras estruturas em uma caixa de diálogo adaptável. Eles só funcionam dentro de uma caixa de diálogo adaptável.
| _caixa de diálogo de habilidades_ | Automatiza o gerenciamento de um ou mais bots de habilidades de um consumidor de habilidades.
| _Caixa de diálogo QnA Maker_ | Automatiza o acesso a uma base de dados de conhecimento QnA Maker.

## <a name="dialog-patterns"></a>Padrões de diálogo

Há dois padrões principais para iniciar e gerenciar caixas de diálogo de um bot.

1. Para caixas de diálogo adaptáveis ou qualquer conjunto de caixas de diálogo que contenham uma caixa de diálogo adaptável, você precisa criar uma instância do Gerenciador de caixas de diálogo para a caixa de diálogo raiz e tornar seu estado de conversa (e, opcionalmente, seu estado de usuário) disponível para o gerente. Para obter mais informações, consulte [introdução a caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-introduction.md) e como [criar um bot usando caixas de diálogo adaptáveis](bot-builder-dialogs-adaptive.md).
1. Para outras caixas de diálogo, você pode usar o Gerenciador de caixas de diálogo ou apenas usar o método de extensão _Run_ da caixa de diálogo raiz. Para obter informações sobre como usar o método Run com uma caixa de diálogo de componente, consulte [sobre as caixas de diálogo componente e cascata](bot-builder-concept-waterfall-dialogs.md) e como [implementar o fluxo de conversa sequencial](bot-builder-dialog-manage-conversation-flow.md).

### <a name="the-dialog-stack"></a>A pilha de diálogo

Um contexto de caixa de diálogo contém informações sobre todas as caixas de diálogo ativas e inclui uma _pilha de caixas de diálogo_, que atua como uma pilha de _chamadas_ para todas as caixas de diálogo ativas.
Cada caixa de diálogo de contêiner tem um conjunto interno de caixas de diálogo que ele está controlando e, portanto, cada caixa de diálogo de contêiner ativo introduz um contexto de caixa de diálogo interno e uma pilha de diálogo como parte de seu estado.

Embora você não acesse a pilha diretamente, entenda que ela existe e sua função irá ajudá-lo a entender como vários aspectos da biblioteca de caixas de diálogo funcionam.

## <a name="container-dialogs"></a>Caixas de diálogo de contêiner

Uma caixa de diálogo de contêiner <!--acts as individual dialog and--> pode fazer parte de um conjunto de caixas de diálogo maior. Cada contêiner tem um conjunto de diálogo interno que também é gerenciado.

- Cada conjunto de caixas de diálogo cria um escopo para resolver IDs de caixa de diálogo.
- Atualmente, o SDK implementa dois tipos de caixas de diálogo de contêiner: caixas de diálogo de componente e caixas de diálogo adaptáveis. Embora a estrutura conceitual dos dois seja bastante diferente, elas podem ser usadas em conjunto.

### <a name="dialog-ids"></a>IDs de diálogo

Quando você adiciona uma caixa de diálogo a um conjunto de caixas de diálogo, atribui a ela uma ID exclusiva dentro desse conjunto. Caixas de diálogo dentro de um conjunto fazem referência umas às outras por suas IDs.

Quando uma caixa de diálogo faz referência a outra caixa de diálogo em tempo de execução, ela faz isso pela ID da caixa de diálogo. O contexto da caixa de diálogo tenta resolver a ID com base nas outras caixas de diálogo no conjunto de diálogo imediato. Se não houver correspondência, ele procurará uma correspondência no conjunto de diálogo contido ou externo, e assim por diante. Se nenhuma correspondência for encontrada, uma exceção ou um erro será gerado.

### <a name="component-dialogs"></a>Diálogos de componente

As caixas de diálogo de componente usam um modelo de sequência para conversas e cada caixa de diálogo no contêiner é responsável por chamar outras caixas de diálogo no contêiner. Quando a pilha de diálogo interna da caixa de diálogo do componente está vazia, o componente termina.

Considere o uso de caixas de diálogo de componente e de cascata se o bot tiver um fluxo de controle relativamente simples que não exija mais fluxo de conversa dinâmico.

As [caixas de diálogo componente e cascata](bot-builder-concept-waterfall-dialogs.md) descrevem as caixas de diálogo de componente, cascata e prompt em mais detalhes.

### <a name="adaptive-dialogs"></a>Caixas de diálogo adaptáveis

As caixas de diálogo adaptáveis usam um modelo flexível para conversas a fim de lidar com uma maior variedade de interação do usuário.
Uma caixa de diálogo adaptável pode ser projetada para terminar ou permanecer ativa quando sua pilha de caixa de diálogo interna estiver vazia.
Eles oferecem vários recursos internos, incluindo manipulação de interrupção, reconhecimento de idioma, geração de linguagem e muito mais. Com as caixas de diálogo adaptáveis, você pode se concentrar mais em modelar a conversa e menos na mecânica da caixa de diálogo.

Uma caixa de diálogo adaptável faz parte da biblioteca de caixas de diálogo e funciona com todos os outros tipos de caixas de diálogo.
Você pode criar facilmente um bot que usa muitos tipos de caixa de diálogo.

Considere o uso de caixas de diálogo adaptáveis se o bot:

- Exigir que o fluxo de conversa se ramifique ou tenha um loop com base na lógica de negócios, entrada do usuário ou outros fatores.
- Precisar se ajustar ao contexto, aceitar informações em uma ordem arbitrária ou permitir que um thread de conversação seja pausado para uma conversa simultânea e, em seguida, retorne à ação.
- Precisar de modelos de compreensão de linguagem específicos de contextos ou precisar extrair informações de entidade da entrada do usuário.
- Beneficiar-se do processamento de entrada personalizado ou da geração de resposta.

A [introdução a caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-introduction.md) e outros tópicos de caixa de diálogo adaptáveis descrevem os recursos suportados por caixas de diálogo adaptáveis: reconhecimento de idioma e suporte à geração de idioma, uso de gatilhos e ações para modelar o fluxo de conversa, o acesso a escopos de memória e assim por diante. Veja como [criar um bot usando caixas de diálogo adaptáveis](bot-builder-dialogs-adaptive.md) para obter mais informações sobre como usar um Gerenciador de caixas de diálogo para executar uma caixa de diálogo adaptável.

## <a name="other-dialogs"></a>Outras caixas de diálogo

As caixas de diálogo QnA Maker e habilidades podem ser usadas como caixas de diálogo autônomas ou como parte de uma coleção de caixas de diálogo em um contêiner.

### <a name="qna-maker-dialog"></a>Caixa de diálogo QnA Maker

A caixa de diálogo QnA Maker acessa uma base de dados de conhecimento do QnA Maker e dá suporte ao prompt de acompanhamento e aos recursos de aprendizado ativos do QnA Maker.

- Prompts de acompanhamento, também conhecidos como prompts de várias chamadas, permitem que uma base de dados de conhecimento peça ao usuário para obter mais informações antes de responder à sua pergunta.
- As sugestões de aprendizado ativo permitem que a base de dados de conhecimento aprimore ao longo do tempo. O diálogo do QnA Maker dá suporte a comentários explícitos para o recurso de aprendizado ativo.

Para obter informações sobre QnA Maker, consulte como [usar QnA Maker para responder a perguntas](bot-builder-howto-qna.md).

### <a name="skill-dialog"></a>Caixa de diálogo de habilidades

Uma caixa de diálogo de habilidades acessa e gerencia uma ou mais habilidades.
A caixa de diálogo de skill posta as atividades do bot pai para o bot de skill e retorna as respostas do skill para o usuário.

Para obter informações sobre habilidades, consulte [visão geral das habilidades](skills-conceptual.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sobre middleware](bot-builder-concept-middleware.md)
