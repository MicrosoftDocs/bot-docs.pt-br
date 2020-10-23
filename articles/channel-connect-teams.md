---
title: Conectar um bot ao Microsoft Teams – Serviço de Bot
description: Saiba como conectar um bot ao Microsoft Teams. Consulte como configurar o bot como um contato com o qual você pode trocar mensagens no Teams.
keywords: Teams, canal de bot, configurar Teams
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/26/2019
ms.openlocfilehash: 26beb5652dd1f623067a0eaceb6d20b40d52bcdd
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416497"
---
# <a name="connect-a-bot-to-microsoft-teams"></a>Conectar um bot ao Microsoft Teams

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Os bots em produção devem ser adicionados ao Microsoft Teams como parte de um aplicativo. Para obter mais informações, leia [Criar um bot](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-create) e [Testar e depurar o bot do Microsoft Teams](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-test) nos documentos do Microsoft Teams.

## <a name="test-a-bot-in-microsoft-teams"></a>Testar um bot no Microsoft Teams

> [!IMPORTANT]
> A adição de um bot por GUID, para qualquer outra coisa que não seja para fins de teste, não é recomendável. Fazer isso limita severamente a funcionalidade de um bot. Os bots em produção devem ser adicionados ao Teams como parte de um aplicativo. Para obter mais informações, leia [Criar um bot](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-create) e [Testar e depurar o bot do Microsoft Teams](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-test) nos documentos do Microsoft Teams.

Para adicionar o canal do Microsoft Teams, abra o bot no [portal do Azure](https://portal.azure.com), clique na folha **Canais** e, em seguida, clique em **Teams**.

![Adicionar canal do Teams](media/teams/connect-teams-channel.png)

Em seguida, clique em **Salvar**.

![Salvar canal do Teams](media/teams/save-teams-channel.png)

Após adicionar o canal do Teams, acesse a página **Canais** e clique em **Obter código de inserção do bot**.

![Obter código de inserção](media/teams/get-embed-code.png)

- Copie a parte _https_ do código mostrada na caixa de diálogo **Obter código de inserção do bot**. Por exemplo, `https://teams.microsoft.com/l/chat/0/0?users=28:b8a22302e-9303-4e54-b348-343232`.

- No navegador, cole este endereço e escolha o aplicativo Microsoft Teams (cliente ou Web) usado para adicionar o bot ao Teams. Você deve poder ver o bot listado como um contato para o qual você pode enviar mensagens e do qual pode recebê-las no Microsoft Teams.

## <a name="additional-information"></a>Informações adicionais

Para obter informações específicas do Microsoft Teams, confira a [documentação](https://docs.microsoft.com/microsoftteams/platform/overview) do Teams.
