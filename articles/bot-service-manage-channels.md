---
title: Configurar um bot para execução em um ou mais canais – Serviço de Bot
description: Saiba como configurar um bot para execução em um ou mais canais usando o Portal do Bot Framework.
keywords: canais de bot, configurar, Facebook Messenger, Kik, margem de atraso, portal do Azure
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/31/2019
ms.openlocfilehash: d1d2bd27dde5f45852cfa318032b168491755104
ms.sourcegitcommit: 22a92bc07c85f899b3b1dca4f19421bc302db23f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100271915"
---
# <a name="connect-a-bot-to-channels"></a>Conectar um bot aos canais

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Um canal é uma conexão entre aplicativos de comunicação e um bot. Um bot, registrado com o Azure, usa canais para facilitar a comunicação com os usuários.

Você pode configurar um bot para se conectar a qualquer um dos canais padrão, como Alexa, Facebook Messenger e margem de atraso. Para obter mais informações, consulte o [registro de canais de bot](bot-service-quickstart-registration.md).

Além dos canais fornecidos, você também pode conectar um bot ao seu aplicativo de comunicação usando a **linha direta** como canal.

A estrutura de bot permite desenvolver um bot de forma independente de canal, normalizando as mensagens que o bot envia para um canal. Isso envolve o seguinte:

- Converta as mensagens do esquema do bot Framework no esquema do canal.
- Se o canal não oferecer suporte a todos os aspectos do esquema do bot Framework, o serviço do conector de bot tentará converter a mensagem em um formato que o canal dá suporte. Por exemplo, se o bot enviar uma mensagem contendo um cartão com botões de ação ao canal de email, o conector poderá enviar o cartão como uma imagem e incluir as ações como links no texto da mensagem.
- Para a maioria dos canais, você deve fornecer informações de configuração de canal para executar um bot no canal. A maioria dos canais exige que um bot tenha uma conta no canal. Outros, como o Facebook Messenger, exigem um bot para ter um aplicativo registrado com o canal também.

Para configurar um bot para se conectar a um canal, conclua as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione o bot que você deseja configurar.
3. Na folha do serviço de Bot, clique em **Canais** em **Gerenciamento de Bot**.
4. Clique no ícone do canal que você deseja adicionar ao seu bot.

    ![Conectar-se aos canais](./media/channels/connect-to-channels.png)

Depois de configurar o canal, os usuários naquele canal podem começar a usar o seu bot.

## <a name="connect-a-bot-to-a-channel"></a>Conectar um bot a um canal

As etapas de conexão são diferentes para cada canal. Consulte o artigo relacionado na tabela abaixo mais informações.

> [!div class="mx-tdBreakAll"]
> |Canal|Descrição|
> |:-|:-|
> |[Alexa](bot-service-channel-connect-alexa.md) <img width="150px"/>|Comunique-se com usuários por meio de dispositivos Alexa que dão suporte a habilidades personalizadas.|
> |[Linha direta](bot-service-channel-directline.md)| Integre um bot em um aplicativo móvel, em uma página da Web ou em outros aplicativos.|
> |[Email do Office 365](bot-service-channel-connect-email.md)|Habilite um bot para se comunicar com usuários por email do Office 365.|
> |[Facebook](bot-service-channel-connect-facebook.md)|Conecte um bot ao Facebook Messenger e ao Facebook workplace, para que ele possa se comunicar com os usuários em ambas as plataformas.|
> |[Kik](bot-service-channel-connect-groupMe.md)|Configure um bot para se comunicar com usuários por meio do aplicativo de mensagens kik.|
> |[LINE](bot-service-channel-connect-line.md)|Configure um bot para se comunicar com usuários por meio do aplicativo de linha.|
> |[Microsoft Teams](channel-connect-teams.md)|Configure um bot para se comunicar com usuários por meio do Microsoft Teams.|
> |[Skype](bot-service-channel-connect-skype.md)|Configure um bot para se comunicar com usuários por meio do Skype.|
> |[Skype for Business](bot-service-channel-connect-skypeforbusiness.md)|Configure um bot para se comunicar com usuários por meio do Skype para Glossário.|
> |[Margem de atraso](bot-service-channel-connect-slack.md)|Configure um bot para se comunicar com os usuários por meio da margem de atraso.|
> |[Telegram](bot-service-channel-connect-telegram.md)|Configure um bot para se comunicar com usuários por meio do Telegram.|
> |[Telefonia](bot-service-channel-connect-telephony.md)|Configure um bot para se comunicar com os usuários por meio do canal de telefonia do bot Framework.|
> |[Twilio](bot-service-channel-connect-twilio.md)|Configure um bot para se comunicar com os usuários por meio da plataforma de comunicação de nuvem do twilio.|
> |[WeChat](bot-service-channel-connect-wechat.md)|Configure um bot para se comunicar com usuários usando a plataforma WeChat.|
> |[Web Chat](bot-service-channel-connect-webchat.md)| Configurado automaticamente para você quando você cria um bot com o serviço bot Framework.|
> |[Webex](bot-service-adapter-connect-webex.md)|Configure um bot para se comunicar com usuários usando o WebEx.|
> |[Canais adicionais](bot-service-channel-additional-channels.md)|Canais adicionais disponíveis como um adaptador por meio de [plataformas fornecidas](https://botkit.ai/docs/v4/platforms/) por meio de [repositórios](https://botkit.ai/docs/v4/platforms/)de Botkit e comunidade.|

## <a name="publish-a-bot"></a>Publicar um bot

O processo de publicação é diferente para cada canal.

[!INCLUDE [publishing](./includes/snippet-publish-to-channel.md)]

## <a name="additional-resources"></a>Recursos adicionais

O SDK inclui exemplos que podem ser usados para criar bots. Visite o [repositório de exemplos no GitHub](https://github.com/Microsoft/BotBuilder-samples) para ver uma lista de exemplos.
