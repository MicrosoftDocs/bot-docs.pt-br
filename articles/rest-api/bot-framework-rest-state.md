---
title: Gerenciar dados de estado – Serviço de Bot
description: Saiba mais sobre as alternativas para o serviço de Estado do Bot preterido. Veja como controlar as informações sobre usuários e conversas sem usar esse serviço.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
ms.openlocfilehash: dbe9a9ccb82169a0d7e2f3eaad8ef616d6b48ca9
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576688"
---
# <a name="manage-state-data-with-the-rest-api"></a>Gerenciar dados de estado com a API REST

Os bots normalmente usam o armazenamento para controlar o local de um usuário na conversa ou detalhes relevantes para o relacionamento deles com o bot. O SDK do Bot Framework gerencia automaticamente o usuário e o estado de conversa para os desenvolvedores de bot.

Originalmente, o Bot Framework era fornecido com um serviço de estado para armazenar esses dados, mas os bots mais modernos (e todas as versões recentes do SDK do Bot Framework) usam o armazenamento controlado diretamente pelo desenvolvedor de bot, em vez desse serviço gerenciado centralmente.

O serviço de Estado do Bot Framework central foi desativado em 30 de março de 2018. Para obter mais informações, confira [Lembrete: o serviço de Estado do Bot Framework foi desativado – o que você precisa saber](https://blog.botframework.com/2018/04/02/reminder-the-bot-framework-state-service-has-been-retired-what-you-need-to-know/).
