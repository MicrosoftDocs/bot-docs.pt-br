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
ms.openlocfilehash: ee99fa79acc415589fa4bc8719d6fd7913a5ffa6
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92415669"
---
# <a name="connect-a-bot-to-groupme"></a>Conectar um bot ao GroupMe

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

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
