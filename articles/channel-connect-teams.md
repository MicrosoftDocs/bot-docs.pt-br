---
title: Conectar um bot ao Teams | Microsoft Docs
description: Saiba como configurar um bot para acesso por meio do Team.
keywords: Teams, canal de bot, configurar Teams
author: kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 07/05/2019
ms.openlocfilehash: d2609e4294416691e156ba3dbd09eabc8e0d3423
ms.sourcegitcommit: b498649da0b44f073dc5b23c9011ea2831edb31e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592225"
---
# <a name="connect-a-bot-to-teams"></a>Conectar um bot ao Teams

Para adicionar o canal do Microsoft Teams, abra o bot no [portal do Azure](https://portal.azure.com), clique na folha **Canais** e, em seguida, clique em **Teams**.

![Adicionar canal do Teams](media/teams/connect-teams-channel.png)

Em seguida, clique em **Salvar**.

![Salvar canal do Teams](media/teams/save-teams-channel.png)

Após adicionar o canal do Teams, acesse a página **Canais** e clique em **Obter código de inserção do bot**.

![Obter código de inserção](media/teams/get-embed-code.png)

- Copie a parte _https_ do código mostrada na caixa de diálogo **Obter código de inserção do bot**. Por exemplo, `https://teams.microsoft.com/l/chat/0/0?users=28:b8a22302e-9303-4e54-b348-343232`. 

- No navegador, cole este endereço e escolha o aplicativo Microsoft Teams (cliente ou Web) usado para adicionar o bot ao Teams. Você deve poder ver o bot listado como um contato para o qual você pode enviar mensagens e do qual pode recebê-las no Microsoft Teams. 

## <a name="additional-information"></a>Informações adicionais
Para obter informações específicas do Microsoft Teams, confira a [documentação](https://docs.microsoft.com/en-us/microsoftteams/platform/overview) do Teams. 
