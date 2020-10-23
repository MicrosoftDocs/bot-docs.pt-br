---
title: Sobre o componente e caixas de diálogo de cascata-serviço bot
description: Descreve quais componentes, cascata e caixas de diálogo de prompt são e como eles funcionam no SDK do bot Framework.
keywords: fluxo de conversa, conversa de bot, caixa de diálogo de componente, caixa de diálogo em cascata, caixa de diálogo de prompt, conjunto
author: JonathanFingold
ms.author: v-jofin
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/03/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 542986d7891cd2e1889a94ce42024c3067d44325
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417129"
---
# <a name="about-component-and-waterfall-dialogs"></a>Sobre caixas de diálogo de componente e de cascata

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

As caixas de diálogo vêm em alguns tipos diferentes. Este artigo descreve as caixas de diálogo componente, cascata e prompt.
Para obter informações sobre caixas de diálogo em geral, consulte o artigo [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md) . Para obter informações sobre caixas de diálogo adaptáveis, consulte a [introdução a caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-introduction.md).

Uma _caixa de diálogo em cascata_ (ou cascata) define uma sequência de etapas, permitindo que o bot Oriente um usuário por meio de um processo linear.
Normalmente, eles são projetados para funcionar no contexto de uma _caixa de diálogo de componente_.

Uma caixa de diálogo de componente é um tipo de caixa de diálogo de contêiner que permite que caixas de diálogo no conjunto chamem outras caixas de diálogo no conjunto, como uma caixa de diálogo de prompt de chamada de cascata ou outra caixa de diálogo de cascata.
As caixas de diálogo de componente gerenciam um conjunto de caixas de diálogo _filhas_ , como caixas de diálogo de cascata, prompts e assim por diante.
Você pode criar uma caixa de diálogo de componente para manipular tarefas específicas e reutilizá-la, no mesmo bot ou em vários bots.

_Caixas de diálogo de aviso_ (prompts) são caixas de diálogo projetadas para solicitar ao usuário tipos específicos de informações, como um número, uma data ou um nome, e assim por diante.
Os prompts são projetados para trabalhar com caixas de diálogo de cascata em uma caixa de diálogo de componente.

## <a name="component-dialogs"></a>Diálogos de componente

Às vezes, você deseja gravar um diálogo reutilizável para usar em diferentes cenários, como um diálogo de endereço que pede ao usuário para fornecer valores para rua, cidade e CEP.

O *diálogo de componente* oferece uma estratégia para a criação de diálogos independentes para lidar com cenários específicos, como dividir um conjunto de diálogos grandes em partes mais gerenciáveis. Cada uma dessas partes tem seu próprio conjunto de diálogos e evita qualquer conflito de nome com o conjunto de diálogos que as contém. Consulte as [instruções sobre componentes de diálogo](bot-builder-compositcontrol.md) para obter mais informações.

## <a name="waterfall-dialogs"></a>Diálogos em cascata

Um diálogo em cascata é uma implementação específica de um diálogo, geralmente usado para coletar informações do usuário ou orientá-lo em uma série de tarefas. Cada etapa da conversa é implementada como uma função assíncrona que usa um parâmetro de *contexto da etapa de cascata* (`step`). Em cada etapa, um bot [solicita que o usuário insira uma entrada](bot-builder-prompts.md) (ou pode iniciar um diálogo filho, mas isso geralmente é um prompt), aguarde uma resposta e depois passe o resultado para a próxima etapa. O resultado da primeira função é passado como um argumento para a função seguinte, e assim por diante.

O diagrama a seguir mostra uma sequência de etapas de cascata e as operações de pilha que ocorrem. Os detalhes sobre o uso da pilha de diálogos estão abaixo na seção [usando diálogos](#using-dialogs).

![Conceito de diálogo](media/bot-builder-dialog-concept.png)

Nas etapas em cascata, o contexto do diálogo em cascata é armazenado em seu *contexto de etapas em cascata*. Isso é semelhante ao contexto do diálogo, pois ele fornece acesso ao contexto e estado do turno atual. Use o objeto de contexto da etapa de cascata para interagir com um conjunto de caixas de diálogo de dentro da etapa de cascata.

Você pode manipular um valor retornado de um diálogo a partir de uma etapa em cascata em um diálogo ou a partir do manipulador de turnos do bot, embora você normalmente só precise verificar o status do resultado do turno do diálogo com a lógica de turnos do bot.
Dentro de uma etapa de cascata, o diálogo fornece o valor retornado na propriedade _result_ do contexto da etapa de cascata.

### <a name="waterfall-step-context-properties"></a>Propriedades de contexto da etapa em cascata

O contexto da etapa em cascata contém o seguinte:

- *Opções*: contém informações de entrada para o diálogo.
- *Valores*: contém informações que você pode adicionar ao contexto e são repassados para as etapas seguintes.
- *Resultado*: contém o resultado da etapa anterior.

Além disso, o *próximo* método (**NextAsync** em C#, em **seguida** no js e no Python) continua na próxima etapa da caixa de diálogo de cascata na mesma vez, permitindo que o bot ignore uma determinada etapa, se necessário.

## <a name="prompts"></a>Solicitações

Prompts, na biblioteca de diálogos, fornecem uma maneira fácil de pedir ao usuário informações e avaliar sua resposta. Por exemplo, para um *prompt de número*, especifique a pergunta ou as informações que você está solicitando e o prompt verifica automaticamente se ele recebeu uma resposta com um número válido. Se tiver recebido, a conversa pode continuar; caso contrário, ele solicitará novamente ao usuário uma resposta válida.

Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicitará a entrada, em seguida, ele retornará o valor válido ou iniciará tudo novamente com um novo prompt.

Os prompts têm *opções de prompt* fornecidas quando são chamados, nas quais você pode especificar o texto do prompt, o prompt de repetição se a validação falhar e as opções para responder o prompt. Em geral, as propriedades de prompt e de nova tentativa de prompt são atividads, embora exista alguma variação sobre como isso é manipulado em linguagens de programação diferentes.

Além disso, você pode optar por adicionar alguma validação personalizada ao seu prompt quando ele é criado. Por exemplo, digamos que quiséssemos obter o tamanho de um grupo usando o prompt de número, mas o tamanho desse grupo deve ser maior do que 2 e menor que 12. O prompt primeiro verifica se recebeu um número válido, em seguida, executa a validação personalizada se esta tiver sido fornecida. Se a validação personalizada falhar, ele solicitará novamente ao usuário a informação, conforme descrito acima.

Quando um prompt for concluído, ele retorna explicitamente o valor resultante que foi solicitado. Quando esse valor é retornado, podemos ter certeza de que ele passou a validação interna do prompt e qualquer validação personalizada adicional que possa ter sido fornecida.

Para obter exemplos sobre como usar vários prompts, dê uma olhada em como usar a [biblioteca de diálogos para coletar a entrada do usuário](bot-builder-prompts.md).

### <a name="prompt-types"></a>Tipos de prompt

Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicitará a entrada, em seguida, ele retornará o valor válido ou reiniciará na parte superior com um novo prompt. A biblioteca de diálogos oferece uma série de prompts básicos, cada um deles usado para coletar um tipo de resposta. Os prompts básicos podem interpretar a entrada de linguagem natural, como "dez" ou "uma dúzia" para um número, ou "amanhã" ou "sextas-feira às 10h" para uma data e hora.

| Prompt | Descrição | Retornos |
|:----|:----|:----|
| _Prompt de anexo_ | Solicita um ou mais anexos, como um documento ou uma imagem. | Uma coleção de objetos _attachment_. |
| _Prompt de escolha_ | Solicita a escolha dentre um conjunto de opções. | Um objeto _found choice_. |
| _Prompt de confirmação_ | Solicita uma confirmação. | Um valor booliano. |
| _Prompt de data e hora_ | Solicita uma data e hora. | Uma coleção de objetos _date-time resolution_. |
| _Prompt de número_ | Solicita um número. | Um valor numérico. |
| _Texto do prompt_ | Solicita a entrada de texto geral. | Uma cadeia de caracteres. |

Para solicitar uma entrada ao usuário, defina um prompt usando uma das classes internas, como _text prompt_, e adicione-o ao seu conjunto de diálogos. Os prompts têm IDs fixas que devem ser exclusivas dentro de um conjunto de caixas de diálogo. Você pode ter um validador personalizado para cada prompt, e para alguns prompts, você pode especificar uma _localidade padrão_.

### <a name="prompt-locale"></a>Localidade do prompt

A localidade é usada para determinar o comportamento específico do idioma dos prompts **choice**, **confirm**, **date-time** e **number**. Para qualquer entrada fornecida pelo usuário, se o canal forneceu uma propriedade de _localidade_ na mensagem do usuário, então essa é a usada. Caso contrário, se a _localidade padrão_ do prompt for definida, fornecendo-a ao chamar o construtor do prompt ou configurando-a mais tarde, então essa é a usada. Se nenhuma dessas são fornecidas, o inglês ("en-us") é usado como a localidade. Observação: A localidade é um código ISO 639 de 2, 3 ou 4 caracteres que representa um idioma ou uma família de idiomas.

### <a name="prompt-options"></a>Opções de prompt

O segundo parâmetro do método de _prompt_ do contexto da etapa usa um objeto _opções de prompt_, que tem as propriedades a seguir.

| Propriedade | Descrição |
| :--- | :--- |
| _Prompt_ | A atividade inicial para enviar o usuário, para solicitar sua entrada. |
| _Prompt de nova tentativa_ | A atividade de enviar o usuário se a sua primeira entrada não tiver sido validada. |
| _Opções_ | Uma lista de opções para o usuário escolher, para ser usado com um prompt de escolha. |
| _Validações_ | Parâmetros adicionais a serem usados com um validador personalizado. |
| _Estilo_ | Define como as opções de um prompt de escolha ou prompt de confirmação serão apresentadas a um usuário. |

Você sempre deve especificar a atividade do prompt inicial a ser enviado ao usuário, bem como um prompt de nova tentativa para instâncias em que a entrada do usuário não é validada.

Se a entrada do usuário não for válida, o prompt de nova tentativa será enviado ao usuário. Se não houver uma nova tentativa especificada, o prompt inicial será enviado novamente. No entanto, se uma atividade for enviada de volta ao usuário de dentro do validador, nenhum aviso de nova tentativa será enviado.

#### <a name="prompt-validation"></a>Validação da prompt

Você pode validar uma resposta do prompt antes de retornar o valor para a próxima etapa da cascata. Uma função de validador tem um parâmetro de _contexto do validador de prompt_ e retorna um valor booleano que indica se a entrada passa na validação.
O contexto do validador de prompt inclui as seguintes propriedades:

| Propriedade | Descrição |
| :--- | :--- |
| _Contexto_ | O contexto de turnos atual para o bot. |
| _Reconhecido_ | Um _resultado do reconhecedor de prompts_ que contém informações sobre a entrada do usuário, conforme processado pelo reconhecedor. |
| _Opções_ | Contém as _opções de prompt_ que foram fornecidas na chamada para iniciar o prompt. |

O resultado do reconhecedor de prompts tem as seguintes propriedades:

| Propriedade | Descrição |
| :--- | :--- |
| _Êxito_ | Indica se o reconhecedor foi capaz de analisar a entrada. |
| _Valor_ | O valor retornado do reconhecedor. Se necessário, o código de validação pode modificar esse valor. |

## <a name="using-dialogs"></a>Usar os diálogos

Diálogos podem ser considerados como uma pilha programática, que chamamos de *pilha de diálogos*, com o manipulador de turnos sendo o responsável pelo direcionamento e servindo como o fallback se a pilha estiver vazia. O item mais alto nessa pilha é considerado a caixa de *diálogo ativa*e o contexto da caixa de diálogo direciona todas as entradas para a caixa de diálogo ativa.

Quando um diálogo é iniciado, ele é enviado para a pilha e será o diálogo ativo. Ele permanece como o diálogo ativo até que seja concluído, removido pelo método [replace dialog](#repeating-a-dialog) ou quando outro diálogo é enviado para a pilha (pelo manipulador de turnos ou pelo próprio diálogo ativo) e torna-se o diálogo ativo. Quando esse novo diálogo termina, ele é retirado da pilha e o diálogo seguinte abaixo se torna o diálogo ativo. Isso permite [repetir um diálogo](#repeating-a-dialog) ou [ramificar uma conversa](#branch-a-conversation), o que será discutido abaixo.

Você pode iniciar ou continuar uma caixa de diálogo raiz usando o método _executar_ extensão da caixa de diálogo. No código do bot, chamar o método de extensão de execução da caixa de diálogo continuará a caixa de diálogo existente ou iniciará uma nova instância da caixa de diálogo se a pilha estiver vazia no momento. O controle e a entrada do usuário vão para a caixa de diálogo ativa na pilha.

O método Run requer um *acessador de propriedade de estado* para acessar o estado da caixa de diálogo. O acessador é criado e usado da mesma maneira que outros acessadores de estado, mas é criado como sua própria propriedade baseada no estado da conversa. Detalhes sobre como gerenciar o estado podem ser encontrados no [tópico de gerenciamento de estado](bot-builder-concept-state.md), e o uso do estado do diálogo é mostrado nas instruções sobre o [fluxo sequencial da conversa](bot-builder-dialog-manage-conversation-flow.md).

De dentro de uma caixa de diálogo, você tem acesso ao contexto da caixa de diálogo e pode usá-lo para iniciar outras caixas de diálogo, terminar a caixa de diálogo atual e executar outras operações.

### <a name="to-start-a-dialog"></a>Para iniciar uma caixa de diálogo

De dentro de uma caixa de diálogo de cascata, passe a *ID da caixa* de diálogo que você deseja iniciar no método *begin*Dialog, *prompt*ou *replace* do contexto da caixa de diálogo.

- Os métodos prompt e BEGIN DIALOG enviarão uma nova instância da caixa de diálogo referenciada para a parte superior da pilha.
- O método replace dialog destacará o diálogo atual na fila e enviará o diálogo de substituição por push para a fila. O diálogo de substituição é cancelado, e todas as informações contidas nessa instância são descartadas.

Use o parâmetro _options_ para passar informações para a nova instância do diálogo.
As opções passadas para o novo diálogo podem ser acessadas por meio da propriedade *options* do contexto da etapa em qualquer etapa do diálogo.
Confira as instruções em [Criar fluxo de conversa avançado usando branches e loops](bot-builder-dialog-manage-complex-conversation-flow.md) para obter o código de exemplo.

### <a name="to-continue-a-dialog"></a>Para continuar um diálogo

Em uma caixa de diálogo de cascata, use a propriedade *valores* do contexto da etapa para persistir o estado entre as ativações.
Qualquer valor adicionado a essa coleção em um turno anterior estará disponível nos turnos seguintes.
Confira as instruções em [Criar fluxo de conversa avançado usando branches e loops](bot-builder-dialog-manage-complex-conversation-flow.md) para obter o código de exemplo.

### <a name="to-end-a-dialog"></a>Para encerrar uma caixa de diálogo

Em uma caixa de diálogo de cascata, use o método *End Dialog* para encerrar uma caixa de diálogo ao exibi-la fora da pilha. O método _End Dialog_ pode retornar um resultado opcional para o contexto pai (como a caixa de diálogo que o chamou, ou o manipulador de folheio do bot). Isso geralmente é chamado de dentro do diálogo para encerrar a instância atual de si mesmo.

Você pode chamar o método end dialog de qualquer lugar onde se tenha um contexto de diálogo, mas ele será exibido ao bot que foi chamado no diálogo ativo atual.

> [!TIP]
> É uma prática recomendada chamar explicitamente o método *end dialog* no final do diálogo.

### <a name="to-clear-all-dialogs"></a>Para limpar todos os diálogos

Caso você queira remover todas as caixas de diálogo da pilha, é possível limpar a pilha de caixas chamando o método *cancel all dialogs* do contexto da caixa de diálogo.

### <a name="repeating-a-dialog"></a>Repetindo um diálogo

Você pode substituir um diálogo por ele mesmo, criando um loop, usando o método *replace dialog*.
Essa é uma ótima maneira de lidar com [interações complexas](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) e uma técnica para gerenciar menus.

> [!NOTE]
> Se você precisar persistir o estado interno do diálogo atual, precisará passar informações para a nova instância do diálogo na chamada para o método *replace dialog* e inicializar o diálogo adequadamente.

### <a name="branch-a-conversation"></a>Ramificar uma conversa

O contexto do diálogo mantém uma pilha de diálogos e, para cada diálogo na pilha, controla qual é a etapa a seguir. O método *begin dialog* cria um diálogo filho e o envia para o topo da pilha e o método *end dialog* retira o primeiro diálogo da pilha. *End dialog* geralmente é chamado de dentro do diálogo que está terminando.

Um diálogo pode iniciar um novo diálogo no mesmo conjunto de diálogo chamando o método *begin dialog* do contexto do diálogo e fornecendo o ID do novo diálogo, o que transformará o novo diálogo em diálogo ativo. O diálogo original ainda está na pilha, mas as chamadas para o método *continue dialog* do contexto do diálogo só são enviadas para o diálogo que está no topo da pilha, o *diálogo ativo*. Quando um diálogo é retirado da pilha, o contexto do diálogo é retomado com a próxima etapa da cascata na pilha em que ele parou em relação ao diálogo original.

Assim, você pode criar uma ramificação dentro do fluxo da sua conversa incluindo uma etapa em um diálogo que possa escolher condicionalmente um diálogo para iniciar a partir de um conjunto de diálogos disponíveis.

## <a name="additional-information"></a>Informações adicionais

<!--:adaptive, components, QnA Maker, skill:-->
- Confira mais informações sobre caixas de diálogo adaptáveis em [Introdução às caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-Introduction.md).
- Confira mais informações em [Sobre os skills](skills-conceptual.md).
