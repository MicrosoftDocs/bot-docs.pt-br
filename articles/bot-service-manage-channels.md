---
title: Configurar o bot para execução em um ou mais canais | Microsoft Docs
description: Saiba como configurar um bot para execução em um ou mais canais usando o Portal do Bot Framework.
keywords: canais de bot, configurar, cortana, facebook messenger, kik, slack, skype, portal do azure
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: cb682bf77f801c98d00deffa0fc63249962248cd
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352915"
---
# <a name="connect-a-bot-to-channels"></a>Conectar um bot aos canais

Um canal é uma conexão entre o Bot Framework e aplicativos de comunicação. Você configura um bot para se conectar aos canais nos quais você deseja ele fique disponível. Por exemplo, um bot conectado para o canal do Skype pode ser adicionado a uma lista de contatos e as pessoas podem interagir com ele no Skype. 

Os canais incluem muitos serviços populares, como [Cortana](bot-service-channel-connect-cortana.md), [Facebook Messenger](bot-service-channel-connect-facebook.md), [Kik](bot-service-channel-connect-kik.md), e [Slack](bot-service-channel-connect-slack.md), assim como vários outros. O [Skype](https://dev.skype.com/bots) e Web Chat são pré-configurados para você. 

Conectar-se a canais é rápido e fácil no [Portal do Azure](https://portal.azure.com).

## <a name="get-started"></a>Introdução

Para a maioria dos canais, você deve fornecer informações de configuração de canal para executar seu bot no canal. A maioria dos canais requerem que seu bot tenha uma conta no canal, e outros, como o Facebook Messenger, exigem que seu bot tenha um aplicativo registrado no canal também.

Para configurar seu bot para se conectar a um canal, conclua as seguintes etapas:

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.
1. Selecione o bot que você deseja configurar.
3. Na folha do serviço de Bot, clique em **Canais** em **Gerenciamento de Bot**.
4. Clique no ícone do canal que você deseja adicionar ao seu bot.

![Conectar-se aos canais](~/media/channels/connect-to-channels.png)

Depois de configurar o canal, os usuários naquele canal podem começar a usar o seu bot.

## <a name="publish-a-bot"></a>Publicar um bot

O processo de publicação é diferente para cada canal.

[!INCLUDE [publishing](~/includes/snippet-publish-to-channel.md)]

