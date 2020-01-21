---
title: Conectar um bot ao GroupMe – Serviço de Bot
description: Saiba como configurar a conexão de um bot com o GroupMe.
keywords: canal de bot, GroupMe, criar GroupMe, credenciais
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: ae2f2e289d6c0b4e90041fc2ad60452b1996d53d
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791972"
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
