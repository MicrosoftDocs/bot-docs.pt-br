---
title: Solicitações de agente do usuário do Bot Framework – Serviço de Bot
description: Noções básicas sobre solicitações do Bot Framework para o servidor Web.
author: JohnD-ms
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: f30cf7b8d86195595575260dfd2fbcd8c14c1a15
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80648043"
---
# <a name="bot-framework-user-agent-requests"></a>Solicitações de agente do usuário do Bot Framework

Se você está lendo esta mensagem, provavelmente recebeu uma solicitação de um Microsoft Bot Framework Service. Este guia irá ajudá-lo a compreender a natureza dessas solicitações e fornecerá as etapas para pará-las, se desejado.

Se você recebeu uma solicitação do serviço, provavelmente havia um cabeçalho do agente do usuário formatado de forma semelhante à cadeia de caracteres a seguir:

```User-Agent: BF-DirectLine/3.0 (Microsoft-BotFramework/3.0 +https://botframework.com/ua)```

A parte mais importante dessa cadeia de caracteres é o identificador **Microsoft-BotFramework**, que é usado pelo Microsoft Bot Framework, uma coleção de ferramentas e serviços que permite aos desenvolvedores de software independentes criar e operar seus próprios bots.

Se você é um desenvolvedor de bot, talvez já saiba por que essas solicitações estão sendo direcionadas ao serviço. Se não, continue lendo para saber mais.

## <a name="why-is-microsoft-contacting-my-service"></a>Por que a Microsoft está contatando meu serviço?

O Bot Framework conecta usuários em serviços de chat, como o Facebook Messenger, aos bots, que são servidores da Web com APIs REST em execução em pontos de extremidade acessíveis pela Internet. As chamadas HTTP para bots (também denominadas como chamadas webhook) são enviadas apenas para URLs especificadas por um desenvolvedor de bots que registrou-se no portal do desenvolvedor do Bot Framework.

Se você estiver recebendo solicitações não solicitadas dos serviços do Bot Framework para o serviço Web, é provável que isso ocorra porque um desenvolvedor digitou a URL acidentalmente ou conscientemente como o retorno de chamada webhook para o bot.

## <a name="to-stop-these-requests"></a>Para parar essas solicitações

Para obter assistência e impedir que solicitações indesejadas alcancem o serviço Web, contate [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).
