---
title: Gerenciar dados de estado – Serviço de Bot
description: Saiba como armazenar e recuperar dados de estado usando o serviço de Estado do Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
ms.openlocfilehash: 1dc6fa1a9223e61daaa52cf475095fc08e0356d7
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77519955"
---
# <a name="manage-state-data"></a>Gerenciar dados de estado

Os bots normalmente usam o armazenamento para controlar o local de um usuário na conversa ou detalhes relevantes para o relacionamento deles com o bot. O SDK do Bot Framework gerencia automaticamente o usuário e o estado de conversa para os desenvolvedores de bot. 

Originalmente, o Bot Framework era fornecido com um serviço de estado para armazenar esses dados, mas os bots mais modernos (e todas as versões recentes do SDK do Bot Framework) usam o armazenamento controlado diretamente pelo desenvolvedor de bot, em vez desse serviço gerenciado centralmente. 

O serviço de Estado do Bot Framework central foi desativado em 30 de março de 2018. Para obter mais informações, confira [Lembrete: o serviço de Estado do Bot Framework foi desativado – o que você precisa saber](https://blog.botframework.com/2018/04/02/reminder-the-bot-framework-state-service-has-been-retired-what-you-need-to-know/).
