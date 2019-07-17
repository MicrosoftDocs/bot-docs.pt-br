---
ms.openlocfilehash: b320aadc876074a76fe209ad55a81cb70b1ddcac
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230556"
---
O <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de webchat em código aberta</a> se comunica com os bots usando a [API de Linha Direta](https://docs.botframework.com/restapi/directline3/#navtitle), que permite que `activities` seja enviado e recebido entre cliente e o bot. O tipo mais comum de atividade é `message`, mas há também outros tipos. Por exemplo, o tipo de atividade `typing` indica que um usuário está digitando ou que o bot está trabalhando para compilar uma resposta. 

Você pode usar o mecanismo de backchannel para trocar informações entre cliente e o bot sem apresentá-lo para o usuário definindo o tipo de atividade para `event`. O controle de webchat ignorará automaticamente todas as atividades em que `type="event"`.