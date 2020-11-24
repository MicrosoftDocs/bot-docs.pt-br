---
title: Solicitações de agente do usuário do Bot Framework – Serviço de Bot
description: Saiba mais sobre as solicitações que o serviço bot Framework envia aos servidores Web. Entenda por que o serviço envia essas chamadas de webhook. Veja como pará-los.
author: JohnD-ms
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: ee30161256539a12d5efd6bcf634c7493745bd6b
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456200"
---
# <a name="bot-framework-user-agent-requests"></a>Solicitações de agente do usuário do Bot Framework

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Se você estiver lendo esta mensagem, talvez tenha recebido uma solicitação de um serviço do Microsoft bot Framework. Este guia irá ajudá-lo a compreender a natureza dessas solicitações e fornecerá as etapas para pará-las, se desejado.

Se você recebeu uma solicitação do serviço, provavelmente havia um cabeçalho do agente do usuário formatado de forma semelhante à cadeia de caracteres a seguir:

```User-Agent: BF-DirectLine/3.0 (Microsoft-BotFramework/3.0 +https://botframework.com/ua)```

A parte mais importante dessa cadeia de caracteres é o identificador **Microsoft-BotFramework**, que é usado pelo Microsoft Bot Framework, uma coleção de ferramentas e serviços que permite aos desenvolvedores de software independentes criar e operar seus próprios bots.

Se você for um desenvolvedor de bot, talvez já saiba por que essas solicitações estão sendo direcionadas para seu serviço. Se não, continue lendo para saber mais.

## <a name="why-is-microsoft-contacting-my-service"></a>Por que a Microsoft está contatando meu serviço?

O Bot Framework conecta usuários em serviços de chat, como o Facebook Messenger, aos bots, que são servidores da Web com APIs REST em execução em pontos de extremidade acessíveis pela Internet. As chamadas HTTP para bots (também denominadas como chamadas webhook) são enviadas apenas para URLs especificadas por um desenvolvedor de bots que registrou-se no portal do desenvolvedor do Bot Framework.

Se você estiver recebendo solicitações não solicitadas dos serviços do bot Framework para seu serviço Web, é provável que um desenvolvedor tenha inserido acidentalmente ou sabendo sua URL como o retorno de chamada de webhook para seu bot.

## <a name="to-stop-these-requests"></a>Para parar essas solicitações

Para obter assistência e impedir que solicitações indesejadas alcancem o serviço Web, contate [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).
