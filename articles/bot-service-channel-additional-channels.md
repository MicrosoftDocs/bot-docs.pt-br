---
title: Canais adicionais | Microsoft Docs
description: Saiba como configurar canais adicionais para o bot.
keywords: canais de bot, hangouts, Twilio, facebook, portal do azure
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 02/08/2019
ms.openlocfilehash: d6cf068f3cd4d57ff9cde2be6d41e084cee6524d
ms.sourcegitcommit: e276008fb5dd7a37554e202ba5c37948954301f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693736"
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
