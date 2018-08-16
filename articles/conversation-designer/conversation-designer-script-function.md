---
title: Definir uma função de script como uma ação | Microsoft Docs
description: Aprenda a configurar a função de ação de script como ação Do.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 674781c2cb5a8700941feb59b2ce7ab902979d4f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297396"
---
# <a name="define-a-script-function-as-a-do-action"></a>Definir uma função de script como uma ação

[Funções de ação de script](conversation-designer-context-object.md#script-callback-functions) executam o script personalizado que você define para ajudar a concluir a tarefa. Por exemplo, chamar um serviço para definir o termostato em 74 graus quando o usuário disser "Ajustar meu termostato a 74 graus". 

Para usar a ação de script personalizado, selecione **ação de Script** como o **fazer** tipo de ação no editor de tarefas. Em seguida, insira o nome da função que implementa a ação. Clique em **Editar** para abrir o editor **Scripts** e escreva sua implementação para esta função. 

## <a name="script-action-function-parameter"></a>Parâmetro de função de ação de script

A função de retorno de chamada de ação especificado sempre será chamada com o [`context`](conversation-designer-context-object.md) objeto.

O objeto `context` inclui os itens `taskEntities` e `contextEntities`. Entidades de tarefas são entidades definidas ou geradas para essa tarefa e entidades de contexto são um pacote de propriedades em que as entidades podem ser preservadas em conversas com o usuário.

## <a name="return-value"></a>Valor de retorno
Espera-se que **funções de ação de script** retornem um booleano.

## <a name="sample-script-action-function"></a>Função de ação de script de exemplo
A função de amostra a seguir faz uma chamada HTTP para obter uma resposta antes de retornar um acionador correspondido.

```javascript
module.exports.NewTask_do_onRun = function(context) {
    var options =  {
        host: 'HOST',
        path: 'PATH',
        method: 'post',
        headers: {
            "HEADER1" : "VALUE"
        }, 
        body: {
            "BODY": VALUE
        }
    };

    return http.request(options).then(function(response) {
      // parse response payload
      // Send a message to user
      context.responses.push({text: "Done", type: "message"});
    });
} 
```

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Ação de caixa de diálogo](conversation-designer-dialogues.md)
