---
title: Definir um diálogo como uma ação Fazer | Microsoft Docs
description: Saiba como configurar um diálogo como uma ação Fazer.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: d59df20821a7f63eb9ee5dea365597b1af839f75
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296743"
---
# <a name="define-a-dialogue-as-a-do-action"></a>Definir um diálogo como uma ação Fazer
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Surgirão mais detalhes sobre a disponibilidade do Designer de Conversa posteriormente este ano.

Um diálogo aborda o modelo de conversa para uma tarefa específica. Por exemplo, um bot de cafeteria que ajuda os usuários a reservar uma mesa pode definir uma tarefa chamada "Reservar mesa". A ação **Fazer** é associada a um diálogo que modela o fluxo da conversa entre o bot e o usuário. 

Os diálogos são particularmente úteis quando um bot participa de uma conversa bidirecional com o usuário para ajudar a concluir uma tarefa.  Os usuários raramente expressam todos os valores necessários para concluir uma tarefa em um único enunciado. 

A reserva de uma mesa exige o local, o tamanho do grupo de pessoas, a data e a preferência de horário. Um bot que faz reservas de mesa precisa entender e lidar com várias frases possíveis que o usuário pode dizer. 

- *reservar uma mesa*: nesse caso, nenhuma das entidades necessárias foram capturadas. O bot agora precisa participar de uma conversa com o usuário.
- *reservar uma mesa para as 19h neste sábado*: nesse caso, a data e a preferência de horário foram especificadas, mas o bot ainda precisa coletar o local desejado e o tamanho do grupo de pessoas.

Durante uma conversa, algumas entidades podem mudar com base na entrada do usuário. Por exemplo, se o usuário disser "Reservar uma mesa no Redmond para este domingo às 19h", mas o Redmond fechar às seis aos domingos, o diálogo do bot deverá lidar com essas solicitações inválidas. 

O Designer de Conversação fornece um designer de diálogo do tipo "arrastar e soltar" para ajudá-lo a visualizar o fluxo da conversa. O designer de diálogo oferece sete *estados de diálogo* que você pode usar para modelar o fluxo da conversa.

## <a name="dialogue-states"></a>Estados de diálogo

Um diálogo é composto por estados de conversa. O diálogo em si é modelado como um fluxo estruturado e direcionado que fornece ao tempo de execução da conversa uma estrutura de como executar o fluxo da conversa.

Os diálogos são recebidos com muitos estados internos que podem ser usados. Os estados internos compatíveis incluem os seguintes:

- [**Inicial**](#start-state): representa o estado inicial de um fluxo da conversa. Todos os diálogos precisam ter, pelo menos, um estado inicial definido.
- [**Retorno**](#return-state): representa a conclusão do fluxo da conversa específico. Considerando que os fluxos da conversa são combináveis, o returno instrui o tempo de execução da conversa a retornar a execução dos possíveis chamadores desse diálogo.
- [**Decisão**](#decision-state): representa um ponto de branch no fluxo da conversa.
- [**Processo**](#process-state): representa um estado em que o bot está executando a lógica de negócios.
- [**Prompt**](#prompt-state): representa um estado em que você pode solicitar a entrada do usuário. 
- [**Comentários**](#feedback-state): representa um estado que você pode fornecer comentários ou uma confirmação para o usuário. Por exemplo, um diálogo que confirma que a reserva foi feita.
- [**Módulo**](#module-state): representa uma chamada a outro diálogo. Como os fluxos de diálogo são combináveis por padrão, esse estado pode chamar um diálogo compartilhado ou outro diálogo, conforme definido nessa tarefa.

Cada estado de conversa está conectado a outro usando conectores de diálogo no designer de diálogo.

Cada estado de diálogo tem um editor de estado associado usado para especificar as propriedades desse estado, incluindo nomes de função de retorno de chamada para scripts personalizados. O **Editor de Estado** localizado como um painel de redimensionamento na parte inferior da porta de exibição principal do designer de diálogo. Para abrir o editor, clique duas vezes em um estado no designer de diálogo e um **Editor de Estado** exibirá as propriedades desse estado.
<!-- TODO: insert screenshot of the wrench in horizontal menu -->

As subseções a seguir fornecem mais detalhes sobre cada um desses estados de diálogo internos.

### <a name="start-state"></a>Estado inicial

O estado inicial indica o ponto de partida de um diálogo. O valor obrigatório é o **nome** do estado. O nome usa como padrão "Inicial" e você pode editá-lo para renomear esse estado.

### <a name="return-state"></a>Estado de retorno

O estado de retorno representa a conclusão do branch específico do fluxo da conversa. Como os diálogos são combináveis, o estado também instrui o tempo de execução da conversa a retornar a execução para o diálogo do chamador. O valor obrigatório é o **nome** do estado. O nome usa como padrão "Retorno" e você pode editá-lo para renomear esse estado.

### <a name="decision-state"></a>Estado de decisão

Um estado de decisão representa o branch no fluxo da conversa. Você pode escrever um script personalizado para avaliar qual branch seguir. Dependendo da entrada do usuário e da lógica de negócios, o script retornará um dos valores possíveis de transição. Cada valor de transição solicita que o tempo de execução da conversa execute um branch diferente do diálogo.

Propriedades obrigatórias para o estado de decisão:
- **Nome**: nome exclusivo do estado de decisão.
- **Código a ser executado na execução**: nome da função de retorno de chamada que implementa a lógica de negócios para determinar qual branch da conversa assumir. 

#### <a name="example-code-for-decision-state"></a>Código de exemplo para o estado de decisão

A função de retorno de chamada de exemplo a seguir retorna uma decisão que instrui o tempo de execução da conversa sobre qual branch executar.

```javascript
module.exports.fnDecisionState = function(context) {
    var a = context.taskEntities['a'];
    if (a[0].value === '0') {
        return 'yes';
    }
    else if (a[0].value === '1') {
        return 'no';
    }
}
```

### <a name="process-state"></a>Estado de processo

O estado de processo representa um ponto no diálogo em que o bot está conduzindo a conversa para um ponto adiante ou tentando executar a ação de conclusão da tarefa final. 

Propriedades obrigatórias para o estado do processo:
- **Nome**: nome exclusivo do estado do processo
- **Código a ser executado na execução**: nome da função de retorno de chamada que implementa a lógica de negócios.

#### <a name="example-code-for-process-state"></a>Código de exemplo para o estado do processo

A função de retorno de chamada de exemplo a seguir obtém o clima e retorna as informações meteorológicas para o usuário.

```javascript
module.exports.fnGetWeather = function(context) {
    var options =  {
        host: 'mock',
        path: '/get?a=b',
        method: 'get'
    };
    return http.request(options).then(function(response) {
        context.contextEntities['x'].value= response.statusCode;
        var jsonBody = JSON.parse(response.body);
          // understand response
        if (response.statusCode != "200") {
            // error
        }
    });
}
```

### <a name="prompt-state"></a>Estado de prompt

O estado de prompt solicita ao usuário uma informação específica. Os estados de prompt incorporam um sistema de subdiálogos e, por definição, são estados complexos. 

Em um estado de prompt, você pode definir a resposta real a ser fornecida ao usuário e, opcionalmente, incluir um cartão adaptável. Em seguida, você pode especificar um gatilho para analisar e compreender a resposta do usuário. Esse gatilho pode ser o LUIS ou um reconhecedor de código personalizado que usa o regex.  

Se o usuário fornecer uma entrada inválida, o bot pode solicitar novamente ao usuário as mesmas informações. Esse comportamento também pode ser definido no editor de estado de prompt. 

#### <a name="prompting-the-user"></a>Solicitando ao usuário

A solicitação da resposta permite que você especifique a mensagem a ser usada ao **Solicitar ao usuário** a entrada. Por exemplo, para coletar a data e a hora, as possíveis respostas podem ser "Quando você deseja chegar?" ou "Quando você gostaria de jantar conosco?"

#### <a name="prompt-listening-for-user-input"></a>Solicitar a escuta da entrada do usuário

Depois que o usuário for solicitado a responder, o tempo de execução da conversa escutará automaticamente a entrada do usuário e tentará compreender o que foi dito. Configure um gatilho com base no LUIS ou em um reconhecedor de código personalizado para tentar entender a entrada e a intenção do usuário. Isso é semelhante ao gatilho de tarefas.

#### <a name="re-prompting-the-user"></a>Fornecendo um novo prompt ao usuário

Use a seção de novo prompt para especificar uma resposta para cada tentativa. Cada linha da seção de novo prompt corresponde à cadeia de caracteres de novo prompt usada para esse turno específico. A primeira resposta será usada para o primeiro novo prompt, a segunda para o segundo e assim por diante. Por exemplo: 

*Desculpe, não entendi. Quando você deseja chegar?*
*Lamento. Estou tendo dificuldades em compreendê-lo. Vamos tentar novamente: quando você deseja chegar?*

#### <a name="prompt-callback-functions"></a>Funções de retorno de chamada de prompt

Você pode especificar duas funções diferentes de retorno de chamada em um estado de prompt. 

1. **Antes de cada prompt e novo prompt**: execute essa função antes de cada prompt ou novo prompt. Essa função de retorno de chamada espera um valor retornado booliano, em que verdadeiro significa a execução desse prompt ou novo prompt e falso significa a não execução desse prompt ou novo prompt. Use `getTurnIndex()` para obter o índice de turno atual para essa execução de prompt.
2. **Na resposta**: execute essa função sempre que um prompt foi gerado, mas antes de ele ser enviado ao usuário (incluindo o novo prompt de resposta). Isso permite que o script modifique a mensagem enviada ao usuário.

#### <a name="sample-code"></a>Exemplo de código

Este trecho de código mostra um exemplo de retorno de chamada **Antes da execução**.

```javascript
module.exports.fnBeforeExecuting = function(context) {
    if(context.responses[0].text === "C") {
        return false;
    }
    return true;
}
```

Este trecho de código mostra um exemplo de retorno de chamada **No prompt**.

```javascript
module.exports.fnOnPrompting = function(context) {
    // include a hint card
    var activity = context.responses.slice(-1).pop();
    activity.attachments.push({
        "contentType": "application/vnd.microsoft.card.hero",
        "content": {
            "buttons": [
                {
                    "type": "imBack",
                    "title": "1",
                    "value": "1"
                },
                {
                    "type": "imBack",
                    "title": "2",
                    "value": "2"
                }
            ]
        }
    });
}
```

Este trecho de código mostra um exemplo de retorno de chamada **Antes do novo prompt**.

```javascript
module.exports.fnBeforeReprompting = function(context) {
    if(context.responses[0].text === "C") {
        return false;
    }
    return true;
}
```

### <a name="feedback-state"></a>Estado de comentários

Use esse estado para fornecer uma resposta ao usuário. Os casos de uso típicos para isso incluem o resultado final após a tentativa da conclusão da tarefa ou para fornecer uma resposta ao usuário em condições de falha, etc. 

Cada estado de comentários exige um nome exclusivo, alguns valores de resposta possíveis e, opcionalmente, pode incluir definições de cartão adaptável. Saiba mais sobre a [definição de cartões adaptáveis](conversation-designer-adaptive-cards.md).

Cada estado de comentários também permite uma função de retorno de chamada **Na resposta**, em que você pode escrever seu script personalizado para modificar o conteúdo da atividade, se necessário, antes de ele ser enviado para o usuário. 


```javascript
module.exports.fnOnResponding = function(context) {
    // include a hint card
    var activity = context.responses.slice(-1).pop();
    activity.attachments.push({
        "contentType": "application/vnd.microsoft.card.hero",
        "content": {
            "buttons": [
                {
                    "type": "imBack",
                    "title": "1",
                    "value": "1"
                },
                {
                    "type": "imBack",
                    "title": "2",
                    "value": "2"
                }
            ]
        }
    });

}
```

### <a name="module-state"></a>Estado de módulo

Um estado de módulo é usado para adicionar uma referência para executar um subdiálogo. Use esse estado para criar uma cadeia de caracteres dos diálogos. 

Cada estado de módulo exige um nome exclusivo e um ponteiro para um diálogo específico a ser executado. As possíveis opções para diálogos a serem executados precisam residir em **Diálogos compartilhados** ou nas **Tarefas** específicas.

## <a name="multiple-dialogues-under-a-task"></a>Vários diálogos em uma tarefa

Uma tarefa pode ter vários diálogos. Para adicionar um diálogo a uma tarefa, basta selecionar a tarefa e clicar no botão **Adicionar** no painel de árvore à esquerda e, em seguida, clicar em **Adicionar diálogo**. Isso adicionará um novo diálogo sob a tarefa selecionada. 

Como os diálogos são combináveis, você pode associar o fluxo de diálogo raiz a tarefa que chama outros diálogos na tarefa. Isso permite que você encapsule subtarefas e permita a reutilização. Também permite que você encadeie esses diálogos em um fluxo de conversa usando estados de *módulo*.

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Modelos de resposta](conversation-designer-response-templates.md)
