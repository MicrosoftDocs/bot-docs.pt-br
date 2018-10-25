---
title: Configurar o bot para execução em um ou mais canais | Microsoft Docs
description: Saiba como configurar um bot para execução em um ou mais canais usando o Portal do Bot Framework.
keywords: canais de bot, configurar, cortana, facebook messenger, kik, slack, skype, portal do azure
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 09/22/2018
ms.openlocfilehash: f5ca01b592266d005c8f000e2351ddcf812acb6d
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997374"
---
# <a name="connect-a-bot-to-channels"></a>Conectar um bot aos canais

Um canal é uma conexão entre o bot e os aplicativos de comunicação. Você configura um bot para se conectar aos canais nos quais você deseja ele fique disponível. O Serviço Bot Framework, configurado no portal do Azure, conecta o bot a esses canais e facilita a comunicação entre o bot e o usuário. Você pode se conectar a muitos serviços populares, como [Cortana](bot-service-channel-connect-cortana.md), [Facebook Messenger](bot-service-channel-connect-facebook.md), [Kik](bot-service-channel-connect-kik.md) e [Slack](bot-service-channel-connect-slack.md), assim como vários outros. O [Skype](https://dev.skype.com/bots) e Web Chat são pré-configurados para você. Além dos canais padrão fornecidos com o Serviço do Bot Connector, também é possível conectar seu bot ao seu próprio aplicativo cliente usando Direct Line como o canal.

O Serviço Bot Framework permite que você desenvolva o bot de maneira independente de canal, normalizando as mensagens que o bot envia a um canal. Isso envolve convertê-lo do esquema do construtor de bots no esquema do canal. No entanto, se o canal não der suporte a todos os aspectos do esquema do construtor de bots, o serviço tentará converter a mensagem em um formato com suporte pelo canal. Por exemplo, se o bot enviar uma mensagem contendo um cartão com botões de ação ao canal SMS, o conector poderá enviar o cartão como uma imagem e incluir as ações como links no texto da mensagem.



Para a maioria dos canais, você deve fornecer informações de configuração de canal para executar seu bot no canal. A maioria dos canais requerem que seu bot tenha uma conta no canal, e outros, como o Facebook Messenger, exigem que seu bot tenha um aplicativo registrado no canal também.

Para configurar seu bot para se conectar a um canal, conclua as seguintes etapas:

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.
1. Selecione o bot que você deseja configurar.
3. Na folha do serviço de Bot, clique em **Canais** em **Gerenciamento de Bot**.
4. Clique no ícone do canal que você deseja adicionar ao seu bot.

![Conectar-se aos canais](./media/channels/connect-to-channels.png)

Depois de configurar o canal, os usuários naquele canal podem começar a usar o seu bot.

## <a name="publish-a-bot"></a>Publicar um bot

O processo de publicação é diferente para cada canal.

[!INCLUDE [publishing](./includes/snippet-publish-to-channel.md)]

## <a name="additional-resources"></a>Recursos adicionais
O SDK inclui exemplos que podem ser usados para criar bots. Visite o repositório [GitHub](https://github.com/Microsoft/BotBuilder-samples) para ver a lista de exemplos.
