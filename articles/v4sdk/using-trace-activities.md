---
title: Adicionar atividades de rastreamento ao bot – Serviço de Bot
description: Descreve qual é a atividade de rastreamento no SDK do Bot Framework e como usá-la.
keywords: rastreamento, atividade, bot, SDK do Bot Framework
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/18/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e6e3941b5fb65f4fe23da4afea03738defdb08cf
ms.sourcegitcommit: 42f3472bd6ecfa4b1541e5375a6044f6b0bf40c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86542372"
---
# <a name="add-trace-activities-to-your-bot"></a>Adicionar atividades de rastreamento ao bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

<!-- What is it and why use it -->

Uma _atividade de rastreamento_ é uma atividade que seu bot pode enviar para o Bot Framework Emulator.
Você pode usar atividades de rastreamento para depurar interativamente um bot, pois elas permitem que você veja informações sobre o bot enquanto ele é executado localmente.

<!-- Details -->

As atividades de rastreamento são enviadas somente para o Emulator e não para nenhum outro cliente nem canal.
O Emulator exibe-as no log, mas não no painel de chat principal.

- As atividades de rastreamento enviadas por meio do contexto de ativação são enviadas por meio dos _manipuladores de envio de atividade_ registrados no contexto do turno.
- As atividades de rastreamento enviadas por meio do contexto de turnos são associadas à atividade de entrada aplicando a referência de conversa, caso haja uma.
  Para uma mensagem proativa, a _responder à ID_ será um novo GUID.
- Independentemente de como ela é enviada, uma atividade de rastreamento nunca define o sinalizador _respondido_.

## <a name="to-use-a-trace-activity"></a>Para usar uma atividade de rastreamento

Para ver uma atividade de rastreamento no Emulator, você precisa de um cenário no qual seu bot envie uma atividade de rastreamento, tal como lançar uma exceção e enviar uma atividade de rastreamento do manipulador "on turn error" do adaptador.

Para enviar uma atividade de rastreamento do bot:

1. Crie uma atividade.
   - Defina a propriedade _tipo_ dela para "rastreamento". Isso é necessário.
   - Opcionalmente, defina as propriedades _nome_, _rótulo_, _valor_ e _tipo de valor_, conforme apropriado para o rastreamento.
1. Use o método _enviar atividade_ do objeto de _contexto do turno_ para enviar a atividade de rastreamento.
   - Esse método adiciona valores para as propriedades necessárias restantes da atividade, com base na atividade de entrada.
     Isso inclui as propriedades _ID do canal_, _URL do serviço_, _de_ e _destinatário_.

Para exibir uma atividade de rastreamento no Emulator:

1. Execute o bot localmente em seu computador.
1. Teste-o usando o Emulator.
   - Interaja com o bot e use as etapas em seu cenário para gerar a atividade de rastreamento.
   - Quando o bot emite a atividade de rastreamento, a atividade de rastreamento é exibida no log do Emulator.

Aqui está uma atividade de rastreamento que você talvez veja caso tenha executado o bot principal sem primeiro configurar a base de dados de conhecimento do QnA Maker da qual o bot depende.

![Captura de tela do Emulator](./media/using-trace-activities.png)

## <a name="add-a-trace-activity-to-the-adapters-on-error-handler"></a>Adicionar uma atividade de rastreamento ao manipulador "on error" do adaptador

O manipulador _on turn error_ do adaptador captura qualquer exceção não capturada, caso contrário, emitida do bot durante uma rodada.
Esse é um bom lugar para uma atividade de rastreamento, pois você pode enviar uma mensagem amigável para o usuário e enviar informações de depuração sobre a exceção para o Emulator.

Este código de exemplo é do exemplo **Bot Principal**. Confira o exemplo completo em [**C#** ](https://aka.ms/cs-core-sample), [**JavaScript**](https://aka.ms/js-core-sample) ou [**Python**](https://aka.ms/py-core-sample).

# <a name="c"></a>[C#](#tab/csharp)

O manipulador **OnTurnError** do adaptador cria a atividade de rastreamento para incluir as informações de exceção e as envia para o Emulator.

**AdapterWithErrorHandler.cs**

[!code-csharp[OnTurnError](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/AdapterWithErrorHandler.cs?range=16-54&highlight=36-37)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O manipulador **onTurnError** do adaptador cria a atividade de rastreamento para incluir as informações de exceção e as envia para o Emulator.

**index.js**

[!code-javascript[onTurnError](~/../BotBuilder-Samples/samples/javascript_nodejs/13.core-bot/index.js?range=36-59&highlight=9-15)]

# <a name="python"></a>[Python](#tab/python)

O manipulador **on_error** do adaptador cria a atividade de rastreamento para incluir as informações de exceção e as envia para o Emulator.

**adapter_with_error_handler.py**

[!code-python[on_error](~/../BotBuilder-Samples/samples/python/13.core-bot/adapter_with_error_handler.py?range=26-50&highlight=24-25)]

---

## <a name="additional-resources"></a>Recursos adicionais

- Como [Depurar um bot com middleware de inspeção](../bot-service-debug-inspection-middleware.md) descreve como adicionar o middleware que emite atividades de rastreamento.
- Para depurar um bot implantado, você pode usar o Application Insights. Para obter mais informações, confira [Adicionar telemetria ao bot](bot-builder-telemetry.md).
- Para obter informações detalhadas sobre cada tipo de atividade, confira o [Esquema de atividade do Bot Framework](https://aka.ms/botSpecs-activitySchema).
