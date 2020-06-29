---
title: Canais adicionais – Serviço de Bot
description: Saiba como configurar canais adicionais para o bot.
keywords: canais de bot, hangouts, Twilio, facebook, portal do azure
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/08/2019
ms.openlocfilehash: 7aff1376ffa7cc720548ca34eec92acc2d2ef057
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75789144"
---
# <a name="additional-channels"></a>Canais adicionais

Além dos canais listados nestes documentos, há alguns canais adicionais disponíveis como adaptadores por meio das nossas [plataformas fornecidas](https://botkit.ai/docs/v4/platforms/) pelo Botkit ou de [repositórios da comunidade](https://github.com/BotBuilderCommunity/). Abaixo estão os canais adicionais fornecidos:

- [Webex Teams](https://botkit.ai/docs/v4/platforms/webex.html)
- [Websocket e Webhooks](https://botkit.ai/docs/v4/platforms/web.html)
- [Google Hangouts e Google Assistente](https://github.com/BotBuilderCommunity/) (disponível por meio da comunidade)
- [Amazon Alexa](https://github.com/BotBuilderCommunity/) (disponível por meio da comunidade)

## <a name="currently-available-adapters"></a>Adaptadores disponíveis no momento

Uma lista completa de adaptadores disponíveis pode ser [encontrada aqui](https://botkit.ai/docs/v4/platforms/). Você observará que alguns canais estão também disponíveis como um adaptador. Cabe a você decidir quando usar um canal ou um adaptador.

### <a name="when-to-use-an-adapter"></a>Quando usar um adaptador

1. O serviço não dá suporte ao canal que você deseja
2. Os requisitos de segurança e conformidade da implantação determinam que você não pode contar com um serviço externo
3. Talvez não haja suporte para a profundidade dos recursos que você precisa em um canal específico

### <a name="when-to-use-a-channel"></a>Quando usar um canal

1. Você exige compatibilidade entre canais: seu bot deve funcionar em mais de um dos canais disponíveis
2. Suporte interno: A Microsoft mantém, atende e aplica patches em cada canal perfeitamente para você sempre que um terceiro faz atualizações
3. Permite o acesso a mais canais da Microsoft exclusivos, como o Microsoft Teams, que está crescendo rapidamente
4. Se você quiser contar com uma interface GUI para habilitar os canais adicionais para o bot
