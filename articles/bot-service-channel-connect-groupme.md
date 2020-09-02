---
title: Conectar um bot ao GroupMe – Serviço de Bot
description: Saiba como configurar os bots para usar o aplicativo de mensagens do grupo GroupMe para se comunicar com pessoas. Consulte Como conectar bots ao GroupMe.
keywords: canal de bot, GroupMe, criar GroupMe, credenciais
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 426d5c9707c644c3efc9676eea682d21bd8a16e7
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89362099"
---
# <a name="connect-a-bot-to-groupme"></a>Conectar um bot ao GroupMe

Configure um bot para se comunicar com as pessoas que usam o aplicativo de mensagens GroupMe.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="sign-up-for-a-groupme-account"></a>Inscrever-se em uma conta do GroupMe

Caso você não tenha uma conta do GroupMe, [inscreva-se para obter uma nova conta](https://web.groupme.com/signup).

## <a name="create-a-groupme-application"></a>Criar um aplicativo GroupMe

[Crie um aplicativo GroupMe](https://dev.groupme.com/applications/new) para seu bot.

Use esta URL de retorno de chamada: `https://groupme.botframework.com/Home/Login`

![Criar aplicativo](~/media/channels/GM-StepApp.png)

## <a name="gather-credentials"></a>Obter credenciais

1. No campo **URL de Redirecionamento** campo, copie o valor após **client_id=** .
2. Copie o valor de **Token de Acesso**.

![Copiar a ID do cliente e o token de acesso](~/media/channels/GM-StepClientId.png)


## <a name="submit-credentials"></a>Enviar credenciais

1. Em dev.botframework.com, cole o valor de **client_id** que você acabou de copiar para o campo **ID do Cliente**.
2. Cole o valor de **Token de Acesso** no campo **Token de Acesso**.
2. Clique em **Save** (Salvar).

![Inserir as credenciais](~/media/channels/GM-StepClientIDToken.png)
