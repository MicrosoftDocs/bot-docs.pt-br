---
title: Testar um bot | Microsoft Docs
description: Testar e depurar o bot de Designer de Conversa.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: b08bd96bc7c413ff7cb6db4899c8c0d3ad613ab8
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297428"
---
# <a name="test-your-conversation-designer-bot"></a>Testar o bot de Designer de Conversa
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Mais detalhes sobre a disponibilidade do Designer de Conversa serão lançados ainda este ano.

O Designer de Conversa oferece ferramentas de depuração úteis por meio de várias janelas de Saída diferentes (localizadas na parte inferior da tela). É possível iniciar o teste e a depuração, clicando no botão **Testar** localizado no canto superior direito da janela. 

## <a name="validation-errors"></a>Erros de validação
Há várias condições sob as quais ocorrem erros de validação. Por exemplo:  
- você está perdendo uma resposta ao gatilho 
- informação parcialmente completa sobre fluxo
- ausência de gatilhos e code-behind para modelo de resposta condicional, estados de processo e decisão
- um loop infinito e recursivo é detectado nas referências de modelo 
- o diálogo tem um estado órfão que não está conectado ao fluxo.
- você adicionou uma tarefa, mas o gatilho ou ação está ausente 


## <a name="javascript-output"></a>Saída JavaScript
É possível anexar um script à execução de uma resposta para fornecer funcionalidade adicional. Se houver um erro no script, ele será exibido aqui. É possível adicionar `console.log()`, `error.log()` ou `debug.log()` à lógica de negócios do bot que listará as mensagens na janela de saída. Por exemplo: 

``` javascript
module.exports.Respond_beforeResponse = function(context) {
    console.log(JSON.stringify(context));
}
```

## <a name="runtime-output"></a>Saída de tempo de execução
Erros ou exceções gerados pelo tempo de execução são exibidos aqui. Por exemplo, se o bot não estiver respondendo, examine a saída para investigar o que causou o erro ou exceção. Se houver muitas mensagens na janela de saída, clique em **Limpar tudo** e, em seguida, teste o bot novamente, enviando uma mensagem ao bot para ver os detalhes do erro. 

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Importar e exportar bot](conversation-designer-export-import-bot.md)
