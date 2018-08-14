---
title: Referência de API para o objeto de contexto | Microsoft Docs
description: Saiba como referenciar o objeto de contexto no bot do Designer de Conversa.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 5ba70189c16815539524d3c9046da03ed0344b9b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296620"
---
# <a name="api-reference"></a>Referência de API
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Surgirão mais detalhes sobre a disponibilidade do Designer de Conversa posteriormente este ano.

O Designer de Conversa oferece a capacidade de adicionar uma lógica de negócios personalizada ao bot. Essas funções de script são implementadas no editor de **Scripts**. As funções são conectadas em vários locais, como os modelos de resposta condicional, os *gatilhos* ou as *ações* da tarefa, ou os estados de diálogo, e todos eles recebem um objeto `context` que é do tipo **[IConversationContext]** em seus parâmetros de função.

O exemplo de código a seguir mostra a assinatura de uma função de resposta condicional com o objeto de **contexto** passado.

```javascript
/**
* @param {IConversationContext} context
*/
module.exports.NewConditionalResponse_onRun = function(context) {
    // Business logic here
    return true; // Returns a boolean
}
```

Por meio do objeto `context`, você pode acessar informações sobre a conversa entre o usuário e o bot.

## <a name="script-callback-functions"></a>Funções de retorno de chamada de script

As funções de retorno de chamada de script personalizadas que você cria podem assumir várias formas. Embora você possa fornecer diferentes nomes a elas, funcionalmente, elas usam um dos formatos a seguir.

| Formato | Parâmetro | Tipo de retorno | DESCRIÇÃO |
| ---- | ---- | ---- | ---- |
| Antes da função de resposta | context | **void** ou **promise** | Função executada antes do recebimento de uma resposta. |
| Função de processo | context | **void** ou **promise** | Função que executa a lógica de negócios. |
| Função de decisão | context | **string** ou **promise** | Função que toma decisões com base na lógica de negócios. A cadeia de caracteres retornada deve corresponder a uma condição de um bloco de [decisão](conversation-designer-dialogues.md#decision-state). |
| Função do reconhecedor de código | context | **boolean** ou **promise** | Lógica de negócios personalizada executada quando ocorre um **Gatilho de script**. Retorne `true` para indicar uma correspondência. Caso contrário, retorne `false` para cancelar a correspondência. |
| Função onRecognize | context | **boolean** ou **promise** | Função executada somente se há uma correspondência de um reconhecedor LUIS. Use essa função de retorno de chamada para processar entidades LUIS e retornar um valor **booliano** apropriado. Retorne `true` para indicar uma correspondência. Caso contrário, retorne `false` para cancelar a correspondência. |

## <a name="iconversationcontext-interface"></a>Interface IConversationContext

A interface `IConversationContext` acompanha informações de conversa entre o usuário e o bot. Todas as funções personalizadas que são conectadas por meio do Designer de Conversa receberão um objeto `context` como parâmetro de argumento.

## <a name="context-properties"></a>Propriedades de contexto
O objeto `context` expõe as propriedades a seguir.

| NOME |  Código | DESCRIÇÃO |
| ---- | ---- | ---- |
| `request` | `context.request` | Obtém o objeto de solicitação que contém a atividade do bot.  |
| | `context.request.attachment` | Uma atividade de anexo que pode conter um cartão adaptável. |
| | `context.request.text` | Uma atividade de texto que contém a mensagem de texto de entrada do cliente. |
| | `context.request.speak` | Uma atividade de fala que contém o texto falado (se disponível) do cliente. |
| | `context.request.type` | Especifica o tipo de atividade (padrão: "mensagem"). |
| `responses` | `context.responses` | Mantém uma matriz de atividades que será enviada novamente para o cliente ao final do estado atual ou da execução code-behind. |
| | `context.responses.push` | Adiciona uma atividade à resposta. |
| `global` | `context.global` | Um objeto JavaScript que contém os dados de conversa definidos. Esse objeto é persistente em toda a conversa. |
| `local` | `context.local` | Um objeto JavaScript que contém os dados da tarefa definidos. Esse objeto é persistente durante uma tarefa específica. As intenções do LUIS sempre são retornadas para o contexto local. Caso deseje persistir os resultados do LUIS, considere a possibilidade de copiá-los para o contexto `context.global`. |
| | `context.local['@description']` | Retorna as Entidades brutas recebidas do LUIS. |
| `sticky` | `context.sticky` | Indica o nome da tarefa atual |
| `currentTemplate` | `context.currentTemplate` | Um [modelo de resposta condicional](conversation-designer-response-templates.md#conditional-response-templates) chamado para as avaliações de exibição e de fala. Esse objeto contém três propriedades: <br/>1. **name**: o nome do modelo atual. <br/>2. **modalityDisplay**: um valor booliano que indica que a modalidade está associada a uma avaliação de exibição. <br/>3. **modalitySpeak**: um valor booliano que indica que a modalidade está associada a uma avaliação de fala. |

## <a name="context-methods"></a>Métodos de contexto
O objeto `context` expõe os métodos a seguir.

| NOME | Tipo de retorno | Código | DESCRIÇÃO |
| ---- | ---- | ---- | ---- |
| `getCurrentTurn` | **number** | `context.getCurrentTurn();` | Obtenha o turno do Quadro no topo da pilha se você estiver executando uma nova solicitação. |

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Criar um bot](conversation-designer-create-bot.md)
