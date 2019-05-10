---
ms.openlocfilehash: cf0e23e349ace78958f861ea2e650a5ebcb78466
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563750"
---
O <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de webchat em código aberta</a> se comunica com os bots usando a [API de Linha Direta](https://docs.botframework.com/en-us/restapi/directline3/#navtitle), que permite que `activities` seja enviado e recebido entre cliente e o bot. O tipo mais comum de atividade é `message`, mas há também outros tipos. Por exemplo, o tipo de atividade `typing` indica que um usuário está digitando ou que o bot está trabalhando para compilar uma resposta. 

Você pode usar o mecanismo de backchannel para trocar informações entre cliente e o bot sem apresentá-lo para o usuário definindo o tipo de atividade para `event`. O controle de webchat ignorará automaticamente todas as atividades em que `type="event"`.