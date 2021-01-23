---
title: Canais adicionais – Serviço de Bot
description: Saiba como conectar bots a equipes WebEx, WebSocket, WebHooks, Google hangouts, assistente do Google e Amazon Alexa. Compare o uso de adaptadores e canais.
keywords: canais de bot, hangouts, Twilio, facebook, portal do azure
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/08/2019
ms.openlocfilehash: 4e588914fea9f37815b328a52347db48446f9839
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717182"
---
# <a name="additional-channels"></a>Canais adicionais

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Além dos canais listados nestes documentos, há alguns canais adicionais disponíveis como adaptadores por meio das nossas [plataformas fornecidas](https://botkit.ai/docs/v4/platforms/) pelo Botkit ou de [repositórios da comunidade](https://github.com/BotBuilderCommunity/). Abaixo estão os canais adicionais fornecidos:

- [Webex Teams](https://botkit.ai/docs/v4/platforms/webex.html)
- [Websocket e Webhooks](https://botkit.ai/docs/v4/platforms/web.html)
- [Google Hangouts e Google Assistente](https://github.com/BotBuilderCommunity/) (disponível por meio da comunidade)
- [Amazon Alexa](https://github.com/BotBuilderCommunity/) (disponível por meio da comunidade)

## <a name="currently-available-adapters"></a>Adaptadores disponíveis no momento

Consulte [adaptadores de plataforma](https://botkit.ai/docs/v4/platforms/) para obter uma lista completa dos adaptadores disponíveis. Você observará que alguns canais estão também disponíveis como um adaptador. Cabe a você decidir quando usar um canal ou um adaptador.

### <a name="when-to-use-an-adapter"></a>Quando usar um adaptador

1. O serviço não oferece suporte ao canal desejado.
2. Os requisitos de segurança e conformidade de sua implantação ditam que você não pode contar com um serviço externo.
3. A profundidade dos recursos necessários em um canal específico pode não ter suporte.

### <a name="when-to-use-a-channel"></a>Quando usar um canal

1. Você precisa de compatibilidade entre canais: seu bot deve funcionar em mais de um dos canais disponíveis.
2. Suporte interno: a Microsoft mantém, corrige e serviços de forma direta cada canal para você cada vez que um terceiro faz atualizações.
3. Permite o acesso a canais adicionais exclusivos da Microsoft, como o crescimento rápido de equipes da Microsoft.
4. Se você quiser contar com uma interface GUI para habilitar canais adicionais para o bot.
