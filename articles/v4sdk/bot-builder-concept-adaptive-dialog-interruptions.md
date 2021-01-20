---
title: Como lidar com interrupções em caixas de diálogo adaptáveis
description: Descreve o conceito de tratamento de interrupções ao coletar entrada do usuário em caixas de diálogo adaptáveis
keywords: bot, usuário, interrupções, consultoria, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 07/27/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5fc32851da40f905b628ecb35217956054cb28e4
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577458"
---
# <a name="handling-interruptions-in-adaptive-dialogs"></a>Como lidar com interrupções em caixas de diálogo adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Quando você cria um fluxo de conversa que solicita a entrada do usuário, você precisa decidir o que deve acontecer quando um usuário responde de uma maneira que leva a conversa em uma direção diferente. É natural em conversas humanas discutir temporariamente um tópico diferente antes de retornar ao assunto original e ser capaz de lidar diretamente com cenários em que o usuário interrompe seu fluxo de conversa é um aspecto importante de um bot robusto.

> [!IMPORTANT]
> É possível lidar com a manipulação de interrupções em caixas de diálogo adaptáveis usando o mecanismo de _consulta_ do SDK do bot Framework, que permite que uma caixa de diálogo consulte sua caixa de diálogo pai. Esse mecanismo de consulta também permite [extração de entidade flexível](#flexible-entity-extraction) e [confirmação e correção](#confirmation-and-correction), discutidos posteriormente neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

- [Introdução às caixas de diálogo adaptáveis][introduction]
- [Solicitar entrada do usuário em caixas de diálogo adaptáveis][inputs]
- Uma compreensão básica dos conceitos de [reconhecimento de linguagem][language-understanding] , especialmente os conceitos de [intenção][intents] e [entidade][entities] .

## <a name="building-a-conversational-flow"></a>Criando um fluxo de conversação

Suponha que você esteja criando um bot para fazer pedidos de uma cafeteria local.  Você pode criar um fluxo de conversação parecido com este:

> **Usuário**: gostaria de pedir um café.
>
> **Bot**: que tipo de café você gostaria?
>
> **Usuário**: expresso.
>
> **Bot**: Qual tamanho você deseja?
>
> **Usuário**: torne-o um grande, por favor.
>
> **Bot**: sem problemas, um grande expresso estará pronto em 5 minutos.

O que acontece se, quando você perguntou o tipo de café, não recebe uma resposta, mas sim uma pergunta? Essa é uma maneira que o usuário pode interromper o fluxo de conversação. Considere o seguinte exemplo:

> **Usuário**: gostaria de pedir um café.
>
> **Bot**: que tipo de café você gostaria?
>
> **Usuário**: que tipos de café você tem?

Se você não tiver antecipado interrupções de usuário, supondo algum nível básico de tratamento de erros, a conversa poderá ser algo assim:

> **Usuário**: gostaria de pedir um café.
>
> **Bot**: que tipo de café você gostaria?
>
> **Usuário**: que tipos de café você tem?
>
> **Bot**: Desculpe, não entendi sua resposta.
>
> **Bot**: que tipo de café você gostaria?

As caixas de diálogo adaptáveis fornecem uma resposta a esse dilema usando _interrupções_. As interrupções são uma técnica disponível em uma caixa de diálogo adaptável que permite que o bot entenda e responda à entrada do usuário que não pertença diretamente à informação específica de que o bot está solicitando o usuário.

### <a name="examples-of-interruptions-to-a-conversational-flow"></a>Exemplos de interrupções em um fluxo de conversação

Ao contrário de um formulário com um conjunto predefinido de campos que um usuário preencheria, um bot permite que o usuário insira algo em um campo de entrada e um bot robusto precisará ser capaz de descobrir como responder adequadamente a uma ampla gama de entradas de usuário.  Interrupções é uma ferramenta que permite que o bot responda a entradas de usuário inesperadas ou entrada que não se ajusta diretamente ao fluxo de conversação de uma determinada caixa de diálogo. Os cenários que podem se beneficiar do tratamento de interrupções podem incluir:

- Um bot que faz reservas de voo.
   - Sendo perguntado a previsão de temperatura para o destino no meio da reserva de um vôo.
   - O usuário que solicita a adição de um aluguel de carro na confirmação de reserva de voo final.
- Um bot sendo perguntado em que horário a loja se abre no meio da ordenação de um café.
- Solicitar uma definição médica ou legal e, em seguida, retornar para concluir a série de perguntas.
- Uma solicitação de _ajuda_ no meio de qualquer conversa.
- Uma solicitação para _Cancelar_ no meio de qualquer conversa.
- Iniciar um processo sobre desde o início quando estiver no meio de um conjunto de etapas.

## <a name="interruptions"></a>Interrupções

As interrupções podem ser manipuladas localmente dentro de uma caixa de diálogo, bem como globalmente por meio de um novo roteamento para outra caixa de diálogo. Você pode usar interrupções como uma técnica para:

- Detecte e manipule a resposta de um usuário como uma intenção relevante localmente dentro do escopo da caixa de diálogo ativa, o que significa que a caixa de diálogo adaptável que contém o gatilho que contém a ação de entrada que solicitou o usuário é a caixa de diálogo que manipula as interrupções _locais_ .

- Detecte que uma caixa de diálogo diferente seria mais adequada para lidar com a entrada do usuário e, em seguida, usar o mecanismo de consulta de caixa de diálogo adaptável para habilitar um manipulador de caixa de diálogo diferente na entrada do usuário.

<!--- Need a concise definition for the Bot Framework SDK's consultation mechanism.     -->

Quando uma ação de entrada estiver ativa, as caixas de diálogo adaptáveis farão o seguinte ao receber uma resposta do usuário:

- Execute o reconhecedor configurado na caixa de diálogo adaptável que contém a ação de entrada.
- Avalie o valor da propriedade _permitir interrupções_ .
   - Se **verdadeiro**: avaliar gatilhos na caixa de diálogo adaptável que contém a ação de entrada. Se nenhum gatilho for acionado, a caixa de diálogo de adaptação pai será consultada e seu reconhecedor será executado e seus gatilhos serão avaliados. Se nenhum gatilho for acionado, esse processo continuará até que a caixa de diálogo adaptável raiz seja atingida.
   - Se **false**: avalie a propriedade _Value_ e atribua seu valor à propriedade associada à entrada. Se o _valor_ for avaliado como nulo, execute o reconhecedor de entidade interna para essa ação de entrada para resolver um valor para essa ação de entrada. Se o reconhecedor interno voltou sem resultado, emita um prompt novamente.

### <a name="the-allow-interruptions-property"></a>A propriedade permitir interrupções

_Permitir interrupções_ é uma propriedade da classe da _caixa de diálogo de entrada_ , a qual todas as entradas derivam, portanto, está disponível ao [solicitar entrada do usuário em caixas de diálogo adaptáveis][inputs]. A propriedade _permitir interrupções_ usa uma _expressão booliana_, que pode ser um valor booliano (_true/false_) ou uma [expressão adaptável][adaptive-expressions] que é resolvida para um booliano.

A propriedade _permitir interrupções_ é definida quando você cria suas [entradas][inputs] e padrões como true. Alguns exemplos:

| _Permitir valor de interrupções_                      | Explicação                                                                                                                                      |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| "true"                                           | Permitir interrupções nesta entrada.                                                                                                               |
| "false"                                          | Não permitir interrupções nesta entrada.                                                                                                        |
| "ativar. reconhecível. Score <= 0,7"                   | Permita interrupções somente se não tivermos uma classificação de alta confiança de uma intenção.                                                        |
| "ativar. reconhecível. Score >= 0,9 \| \| ! @personName " | Permitir interrupções somente se você tiver uma classificação de alta confiança de uma intenção ou quando não obtiver um valor para a `personName` entidade.  |

> [!TIP]
>
> `personName` é uma entidade predefinida fornecida pelo [Luis](https://www.luis.ai/) que detecta nomes de pessoas. Ele é muito útil porque já está treinado, portanto, você não precisa adicionar declarações de exemplo que contenham `personName` as tentativas de bots. Para obter mais informações sobre como usar entidades predefinidas em em seu bot, consulte o [formato de arquivo. Lu](/file-format/bot-builder-lu-file-format.md).

### <a name="handling-interruptions-locally"></a>Manipulando interrupções localmente

Você pode lidar com interrupções localmente adicionando gatilhos para corresponder às possíveis tentativas. Você pode adicionar qualquer gatilho que assina um `recognition` evento, como `OnIntent` ou `OnQnAMatch` . Considere este exemplo:

> **Usuário**: gostaria de pedir um café.
>
> **Bot**: que tipo de café você gostaria?
>
> **Usuário**: expresso.
>
> **Bot**: Qual tamanho você deseja?
>
> **Usuário**: quais tamanhos você tem?
>
> **Bot**: oferecemos os seguintes tamanhos de expresso:    <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Alta $2.95    <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Moca $3.65  <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Ventilação, $4.15
>
> **Bot**: Qual tamanho você deseja?
>
> **Usuário**: moca.
>
> **Bot**: sem problemas, um moca expresso estará pronto em 5 minutos.

Neste exemplo, o bot começou em sua caixa de diálogo raiz. Quando o usuário solicitou a ordem de um café, o reconhecedor da caixa de diálogo raiz retornou a `order` intenção, fazendo com que ele inicie a caixa de diálogo de _pedido_ para iniciar e processar o pedido.  Quando o usuário interrompeu o fluxo de conversa do pedido com uma pergunta, o reconhecedor da caixa de diálogo do _pedido_ retornou a `sizes` intenção que foi tratada localmente, o que significa pela caixa de diálogo _ordem_ .

O que acontece nos casos em que a caixa de diálogo ativa não consegue detectar uma intenção da resposta dos usuários? O mecanismo de consulta oferece uma solução habilitando a caixa de diálogo do bot para enviar a resposta para o pai de caixas de diálogo adaptáveis ativas, que é discutido a seguir.

### <a name="handling-interruptions-globally"></a>Manipulando interrupções globalmente

_Interrupções globais_ são interrupções que não são manipuladas pela caixa de diálogo adaptável ativa. Se não houver gatilhos na caixa de diálogo adaptável ativa que possa lidar com a intenção, que inclui qualquer gatilho que assina um evento de _reconhecimento_ , como os `OnIntent` `OnQnAMatch` gatilhos ou, o bot o enviará à caixa de diálogo pai da caixa de diálogo, usando o mecanismo de _consulta_ de caixas de diálogo adaptáveis. Se a caixa de diálogo pai não tiver um gatilho para lidar com a intenção, ela continuará a emergir até atingir a caixa de diálogo raiz.

Os usos comuns para interrupções globais incluem a criação de recursos básicos de gerenciamento de caixas de diálogo, como _ajuda_ ou _cancelamento_ na caixa de diálogo raiz que, em seguida, estão disponíveis para qualquer uma de suas caixas de diálogo filhas.

Depois que a interrupção é tratada, o fluxo de conversa continua de onde parou com duas exceções possíveis. A primeira exceção ocorre quando você usa a ação [EditActions][editactions] para modificar a sequência de ações. A segunda exceção ocorre quando a interrupção é uma solicitação para cancelar; nesse caso, você pode usar a ação [CancelAllDialogs][cancelalldialogs] para finalizar o fluxo de conversação, bem como a caixa de diálogo adaptável ativa, conforme demonstrado no exemplo a seguir:

> **Bot**: boa manhã, como posso ajudá-lo?

O bot começa na caixa de diálogo _raiz_ , com a boas-vindas do usuário.

> **Usuário**: gostaria de pedir um café.

O usuário expressão gostaria _de pedir um café._ é enviado para o reconhecedor LUIS que retorna a `order` intenção, junto com uma `coffee`  entidade. O gatilho associado à `order` intenção é acionado e chama a caixa de diálogo de _pedido_ para processar o pedido.

> **Bot**: que tipo de café você gostaria?
>
> **Usuário**: nunca se preocupe, cancele meu pedido.
>
> **Bot**: sem problemas, tenha um bom dia!

Isso encerra o fluxo de conversação entre o bot e o usuário. Nesse caso, a `order` caixa de diálogo é fechada e o controle retorna para a caixa de diálogo raiz.

## <a name="flexible-entity-extraction"></a>Extração de entidade flexível

Sempre que uma caixa de diálogo adaptável for iniciada, as opções passadas para a caixa de diálogo por meio de _BEGIN DIALOG_ tornam-se Propriedades dessa caixa de diálogo e podem ser acessadas desde que estejam no [escopo][dialog-scope]. Você acessa essas propriedades por nome: `dialog.<propertyName>` .

> [!NOTE]
> Esse mesmo conceito também se aplica às entidades disponíveis no [escopo de ativação][turn-scope].

Você pode criar o bot para usar esses valores quando estiver presente e avisar o usuário quando eles não estiverem.
Quando sua caixa de diálogo de entrada pode aceitar entrada de várias fontes diferentes, você pode usar a `coalesce` função predefinida para usar o primeiro valor não nulo. `coalesce` está disponível como parte das [expressões adaptáveis][adaptive-expressions].

Há situações em que talvez seja necessário solicitar informações ao usuário mesmo quando a propriedade não for `null` . Nessas situações, você pode definir _sempre avisar_ para `true` .

Outra maneira de que a extração de entidade flexível pode ser muito útil é quando o usuário responde a uma solicitação de informações e, além de responder à sua pergunta, eles também fornecem informações adicionais relevantes. Por exemplo, ao registrar um novo usuário, você pode solicitar o nome dos usuários e responder com ele, junto com sua idade:

> **Bot**: Qual é seu nome?
>
> **Usuário**: meu nome é Vishwac e tenho 36 anos de idade

Ou um bot de viagem solicitando o aeroporto de partida dos usuários e o usuário responde com a cidade de partida junto com a cidade de destino também:

> **Bot**: Qual é sua cidade de partida?
>
> **Usuário**: preciso voar do Detroit para Seattle

A extração de entidade flexível permite que você manipule essas situações normalmente. Para fazer isso, você precisará definir tentativas com declarações específicos em seus arquivos. Lu que você esperaria que um usuário inserisse, no primeiro exemplo acima, o modelo a seguir define a `getUserProfile` intenção com uma lista de possíveis declarações que atribuem os valores inseridos pelo usuário às `@firstName` `@userAge` variáveis e:

```lu
# getUserProfile
    - {userName=vishwac}, {userAge=36}
    - I'm {userName=vishwac} and I'm {userAge=36} years old
    - call me {userName=vishwac}, I'm {userAge=36}.
    - my name is {userName=vishwac} and I'm {userAge=36}
    - {userName=vishwac} is my name and I'm {userAge=36} years of age.
    - you can call me {userName=vishwac}, I'm {userAge=36}
```

## <a name="cross-train-your-language-understanding-models-to-handle-interruptions"></a>Treine os modelos de reconhecimento de linguagem para lidar com as interrupções

O treinamento cruzado de seus modelos de reconhecimento de linguagem é uma abordagem para lidar com as interrupções sem problemas. Ao treinar os modelos LUIS em seu bot, cada caixa de diálogo adaptável pode estar ciente dos recursos de outras caixas de diálogo e transferir o fluxo de conversação para a caixa de diálogo criada para lidar com qualquer solicitação de usuário específica. O treinamento cruzado também pode facilitar o uso de vários reconhecedores dentro de uma caixa de diálogo adaptável, bem como entre várias caixas de diálogo que utilizam tecnologias diferentes, como LUIS e QnA Maker, para permitir que o bot determine a melhor tecnologia a ser usada para responder a um usuário. Para obter mais informações, consulte [fazer um treinamento cruzado do bot para usar os reconhecedores Luis e QnA Maker][cross-train-concepts].

## <a name="confirmation-and-correction"></a>Confirmação e correção

_Confirmação e correção_ habilita o cenário em que você pede confirmação ao usuário e não apenas fornece uma confirmação, mas também inclui entrada que inclui tentativas de usuário adicionais em sua resposta de confirmação.

## <a name="additional-information"></a>Informações adicionais

- Como [lidar com interrupções de usuário em caixas de diálogo adaptáveis](bot-builder-howto-handle-user-interrupts-adaptive.md).
- [Treine seu bot para usar os reconhecedores Luis e QnA Maker][cross-train-concepts].
- [Expressões adaptáveis][adaptive-expressions].
- [Formato de arquivo .lu](/file-format/bot-builder-lu-file-format.md)
- [Tentativas em seu aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-concept-intent)
- [Reconheça quais são os enunciados ideais para o aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-concept-utterance)

[introduction]:bot-builder-adaptive-dialog-introduction.md
[inputs]: bot-builder-concept-adaptive-dialog-Inputs.md
[language-understanding]: bot-builder-concept-adaptive-dialog-recognizers.md#language-understanding
[intents]: bot-builder-concept-adaptive-dialog-recognizers.md#intents
[entities]: bot-builder-concept-adaptive-dialog-recognizers.md#entities
[entities-lu]: ../file-format/bot-builder-lu-file-format.md#list-entity
[utterances-lu]: ../file-format/bot-builder-lu-file-format.md#utterances
[adaptive-expressions]: bot-builder-concept-adaptive-expressions.md
[dialog-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#dialog-scope
[turn-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#turn-scope
[cancelalldialogs]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#cancelalldialogs
[editactions]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#editactions
[cross-train-concepts]: bot-builder-concept-cross-train.md
