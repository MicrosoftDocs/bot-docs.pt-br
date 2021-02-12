---
ms.openlocfilehash: f1bd4585b34fdb3bfdff67e2c6d760a8777e1f0e
ms.sourcegitcommit: 22a92bc07c85f899b3b1dca4f19421bc302db23f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100271925"
---
O <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de webchat em código aberta</a> se comunica com os bots usando a [API de Linha Direta](../rest-api/bot-framework-rest-direct-line-3-0-concepts.md#client-libraries), que permite que `activities` seja enviado e recebido entre cliente e o bot.
O tipo mais comum de atividade é `message`, mas há também outros tipos.
Por exemplo, o tipo de atividade `typing` indica que um usuário está digitando ou que o bot está trabalhando para compilar uma resposta.

Você pode usar o mecanismo de backchannel para trocar informações entre cliente e o bot sem apresentá-lo para o usuário definindo o tipo de atividade para `event`. O controle de webchat ignorará automaticamente todas as atividades em que `type="event"`.