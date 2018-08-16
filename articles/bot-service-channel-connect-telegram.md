---
title: Criar um bot para o Telegram | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Telegram.
keywords: configurar bot, Telegram, canal de bot, bot do Telegram, token de acesso
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: ccd6bbbf30a9af83d6c687ee68f94d2f31c1c9cd
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296823"
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

Cole o token que você copiou anteriormente no campo **Token de Acesso** e clique em **Enviar**.

## <a name="enable-the-bot"></a>Habilitar o bot
Marque **Habilitar este bot no Telegram**. Em seguida, clique em **Já concluí a configuração do Telegram**.

Depois que você concluir essas etapas, o bot será configurado com êxito para se comunicar com os usuários no Telegram.
