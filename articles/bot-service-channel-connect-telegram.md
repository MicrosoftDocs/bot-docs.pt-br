---
title: Criar um bot para o Telegram – Serviço de Bot
description: Saiba como configurar os bots para usar o aplicativo de mensagens Telegram para se comunicar com pessoas. Consulte Como conectar bots ao Telegram.
keywords: configurar bot, Telegram, canal de bot, bot do Telegram, token de acesso
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 69db6bb582200acc5b33c917868d3f2f1bc63531
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92415569"
---
# <a name="connect-a-bot-to-telegram"></a>Conectar um bot ao Telegram

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Configure seu bot para se comunicar com as pessoas que usam o aplicativo de mensagens Telegram.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="visit-the-bot-father-to-create-a-new-telegram-bot"></a>Visite o Bot Father para criar um novo bot do Telegram

<a href="https://telegram.me/botfather" target="_blank">Crie um novo bot do Telegram</a> usando o Bot Father.

![Visite o Bot Father](~/media/channels/tg-StepVisitBotFather.png)

## <a name="create-a-new-telegram-bot"></a>Criar um novo bot do Telegram
Para criar um novo bot do Telegram, envie o comando `/newbot`.

![Criar novo bot](~/media/channels/tg-StepNewBot.png)

### <a name="specify-a-friendly-name"></a>Especificar um nome amigável

Dê ao bot do Telegram um nome amigável.

![Dar ao bot um nome amigável](~/media/channels/tg-StepNameBot.png)

### <a name="specify-a-username"></a>Especificar um nome de usuário

Dê o bot do Telegram um nome de usuário exclusivo.

![Dê ao bot um nome exclusivo](~/media/channels/tg-StepUsername.png)

### <a name="copy-the-access-token"></a>Copiar o token de acesso

Copie o token de acesso do bot do Telegram.

![Copiar token de acesso](~/media/channels/tg-StepBotCreated.png)

## <a name="enter-the-telegram-bots-access-token"></a>Inserir o token de acesso do bot do Telegram

Acesse a seção **Canais** do bot no portal do Azure e clique no botão do **Telegram**. 

> [!NOTE]
>  A interface do usuário do portal do Azure será ligeiramente diferente se você já tiver conectado o bot ao Telegram. 

![Selecionar o Telegram nos canais](~/media/channels/tg-connectBot-Azure.png)

Cole o token copiado anteriormente no campo **Token de Acesso** e clique em **Salvar**.

![Token de acesso do Telegram](~/media/channels/tg-accessToken-Azure.png)

O bot agora foi configurado com êxito para se comunicar com os usuários no Telegram. 

![Bot do Telegram habilitado](~/media/channels/tg-botEnabled-Azure.png)
