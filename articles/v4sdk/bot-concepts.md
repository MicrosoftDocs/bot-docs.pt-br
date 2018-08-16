---
title: Canais e o Serviço do Bot Connector | Microsoft Docs
description: Conceitos fundamentais do SDK do Bot Builder.
keywords: atividades, conversa
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/28/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: abb2b18d45fb722d7de98c5cd02bd88350aad803
ms.sourcegitcommit: 9a38d76afb0e82fdccc1f36f9b1a65042671e538
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2018
ms.locfileid: "39515086"
---
# <a name="channels-and-the-bot-connector-service"></a>Canais e o Serviço do Bot Connector

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Canais são os pontos de extremidade que um usuário está conectando ao bot como Facebook, Skype, Email, Slack, etc. O Serviço do Bot Connector, configurado através do [portal do Azure](https://portal.azure.com), conecta o bot a esses canais e facilita a comunicação entre o bot e o usuário. 

Além dos canais padrão fornecidos com o Serviço do Bot Connector, também é possível conectar o bot ao seu próprio aplicativo cliente usando [Direct Line](bot-builder-howto-direct-line.md) como o canal.

O Serviço do Bot Connector permite que você desenvolva o bot de maneira independente de canal, normalizando as mensagens que o bot envia a um canal. Isso envolve convertê-lo do esquema do construtor de bots no esquema do canal. No entanto, se o canal não der suporte a todos os aspectos do esquema do construtor de bots, o serviço tentará converter a mensagem em um formato com suporte pelo canal. Por exemplo, se o bot enviar uma mensagem contendo um cartão com botões de ação ao canal SMS, o conector poderá enviar o cartão como uma imagem e incluir as ações como links no texto da mensagem.

## <a name="activities-and-conversations"></a>Atividades e conversas


O Serviço do Bot Connector usa JSON para trocar informações entre o bot e o usuário, e o SDK do Bot Builder encapsula essas informações em um objeto de atividade específico a um idioma. Os [tipos de atividade](../bot-service-activities-entities.md) foram mencionados ao discutir sobre a [interação com o bot](bot-builder-basics.md#interaction-with-your-bot), com o tipo mais comum de atividade que é uma mensagem, mas os outros tipos de atividades também são igualmente importantes. Isso inclui uma atualização de conversa, atualização de relação de contato, exclusão de dados de usuário, fim de conversa, digitação, reação à mensagem e algumas outras atividades específicas de bot que o usuário provavelmente nunca verá. Detalhes sobre cada um deles e quando acontecem podem ser encontrados no conteúdo de referência de atividades.

Cada turno e a atividade associada pertence a uma **conversa** lógica, que representa uma interação entre um ou mais bots e um usuário ou grupo de usuários específico. Uma conversa é específica de um canal e tem uma ID exclusiva desse canal.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com alguns conceitos fundamentais de um bot, veremos detalhadamente as diferentes formas de conversa que o bot pode usar.

> [!div class="nextstepaction"]
> [Formas de conversa](bot-builder-conversations.md)
