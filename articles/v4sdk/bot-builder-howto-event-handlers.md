---
title: Manipuladores de evento | Microsoft Docs
description: Entenda como usar manipuladores de eventos.
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 06/14/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0c054b8f4209004806e4564be45e83bdf3a8ec25
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297284"
---
# <a name="event-handlers"></a>Manipuladores de eventos

Manipuladores de eventos são funções que podemos adicionar a eventos de atividades futuras dentro de um [turno](bot-builder-basics.md#defining-a-turn). Essas atividades são `SendActivity`, `UpdateActivity` e `DeleteActivity`, e cada uma tem seu próprio manipulador. Esses manipuladores são úteis quando você precisa fazer algo em todas as atividades futuras desse tipo para o objeto de contexto atual.

Você pode adicionar vários manipuladores de eventos a cada evento de atividade, e eles serão processados para todos os eventos **após** a adição. Portanto, o local onde você os adiciona no código é importante.

> [!NOTE]
> Os *manipuladores de eventos* neste artigo não estão aderindo às definições específicas de linguagem tradicional de *eventos* e *manipuladores*. Em vez disso, eles se referem à ideia de programação mais conceitual de *eventos* e *manipuladores*.

## <a name="using-the-next-delegate"></a>Usar o delegado *next*

Ao chamar o delegado *next*, cada manipulador passa a execução para o próximo manipulador na ordem na qual os manipuladores foram adicionados. O último delegado *next* é uma chamada para o adaptador realmente enviar, atualizar ou excluir a atividade.

A menos que você queira o oposto, é importante chamar *next()* dentro do seu manipulador, caso contrário, você causará um [curto-circuito](bot-builder-create-middleware.md#short-circuit-routing) da atividade. A diferença entre o curto-circuito de uma atividade e middleware é que o curto-circuito cancela completamente a atividade, enquanto o middleware altera qual código pode ser executado, mas continua a execução da atividade.

Para uma atividade de envio, se for bem-sucedida, o delegado *next* retorna as IDs atribuídas às mensagens enviadas.

## <a name="expected-return-value"></a>Valor de retorno esperado

Para o valor de retorno, o manipulador de eventos espera que seja o resultado do próximo delegado. Se for necessário, esse resultado pode ser exibido e armazenado antes de retorná-lo ou ele pode ser retornado diretamente, conforme mostrado abaixo.

O valor de retorno do manipulador `SendActivity` é o valor de retorno passado pela cadeia como o valor de retorno do próximo delegado correspondente.

# <a name="ctabcseventhandler"></a>[C#](#tab/cseventhandler)

Os três tipos de manipuladores de evento fornecidos são `OnSendActivities()`, `OnUpdateActivity()` e `OnDeleteActivity()`. Aqui, adicionamos um manipulador `OnSendActivities()` dentro do código do nosso bot. No entanto, os manipuladores também podem ser adicionados no código de middleware.

Geralmente, os manipuladores são adicionados como expressões lambda, as quais você verá neste exemplo. Aqui, escutaremos a atividade de entrada do usuário quando **help** for escrito.

```cs
public Task OnTurn(ITurnContext context)
{
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context
        
        context.OnSendActivities(async (handlerContext, activities, handlerNext) =>
        {
            if (handlerContext.Activity.Text == "help")
            {
                Console.WriteLine("help!");
                // Do whatever logging you want to do for this help message
            }

            return await handlerNext();
        });
        // ...
    }
}
```

# <a name="javascripttabjseventhandler"></a>[JavaScript](#tab/jseventhandler)

Os três tipos de manipuladores de evento fornecidos são `onSendActivities()`, `onUpdateActivity()` e `onDeleteActivity()`. Aqui, adicionamos um manipulador `onSendActivities()` dentro do código do nosso bot. No entanto, os manipuladores também podem ser adicionados no código de middleware.

Este exemplo escutará a atividade de entrada do usuário quando **help** for escrito.

```js
adapter.processActivity(req, res, async (context) => {

    if (context.activity.type === 'message') {

        context.onSendActivities(async (handlerContext, activities, handlerNext) => { 
            
            if(handlerContext.activity.text === 'help'){
                console.log('help!')
                // Do whatever logging you want to do for this help message
            }
            // Add handler logic here
        
            await handlerNext(); 
        });
        await context.sendActivity(`you said ${context.activity.text}`);
    }
});
```

---

É importante diferenciar entre eventos de *atividade de envio* e *atividade de atualização ou exclusão*, onde o primeiro cria um evento de atividade totalmente novo e o segundo age em uma atividade anterior. Além disso, nem todos os canais dão suporte à atividade de *atualização* ou de *exclusão*. Recomendamos adicionar o tratamento de exceção apropriado em chamadas dessas atividades e seus manipuladores, a fim de levar em conta essa possibilidade.

