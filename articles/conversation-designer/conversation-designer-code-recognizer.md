---
title: Definir o reconhecedor de código como o gatilho de tarefa | Microsoft Docs
description: Saiba como usar um reconhecedor de código personalizado como um gatilho de tarefa.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 6d44cd299e46971b2218bb78d16e454d5df3c1d9
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296621"
---
# <a name="define-code-recognizer-as-task-trigger"></a>Definir o reconhecedor de código como o gatilho de tarefa
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Surgirão mais detalhes sobre a disponibilidade do Designer de Conversa posteriormente este ano.

Os identificadores de código permitem que você escreva scripts personalizados para ajudar a executar uma tarefa. Uma avaliação de expressão baseada no regex ou uma chamada a outros serviços pode ser usada para ajudar a determinar a intenção do usuário. O script personalizado instruirá o tempo de execução da conversa a disparar uma tarefa. 

## <a name="create-a-script-function"></a>Criar uma função de script
Para usar um script como um reconhecedor, no editor de tarefas, escolha "Função de script" como o reconhecedor, especifique o nome da função e clique em **Criar/exibir função** para começar a editar um script. Clique também em **Criar/exibir função** sem especificar um nome de script e uma função vazia será criada para você com o nome padrão. 

## <a name="script-trigger-function-parameter"></a>Parâmetro de função de gatilho de script

A função de retorno de chamada de script especificada sempre será chamada com o objeto [`context`](conversation-designer-context-object.md).

O objeto `context` inclui `taskEntities` e `contextEntities`. As entidades da tarefa são as entidades definidas ou geradas para essa tarefa e as entidades do contexto são os recipientes de propriedades em que as entidades podem ser preservadas nas conversas com o usuário.

## <a name="return-value"></a>Valor de retorno

As funções de **gatilho de script** devem retornar um booliano.

## <a name="sample-regex-based-recognizer"></a>Reconhecedor baseado no regex de exemplo
O exemplo de código a seguir usa o regex para processar a solicitação e retorna um booliano, de modo que o tempo de execução da conversa possa determinar qual gatilho de script executar.

```javascript
module.exports.fnFindPhoneTrigger = function(context) {
    if (context.request.text.includes("call") || context.request.text.includes("ring")) {
        return true;
    } else {
        return false;
    }
} 
```

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Ação de resposta](conversation-designer-reply.md)
