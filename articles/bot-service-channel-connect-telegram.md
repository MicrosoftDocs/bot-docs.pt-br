---
title: Criar um bot para o Telegram | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Telegram.
keywords: configurar bot, Telegram, canal de bot, bot do Telegram, token de acesso
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: cdf9a98d77f876fb582432ab9b4704d2ac98d45f
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70385972"
---
# <a name="connect-a-bot-to-telegram"></a>Conectar um bot ao Telegram

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
