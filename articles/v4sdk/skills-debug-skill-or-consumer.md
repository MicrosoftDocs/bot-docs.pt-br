---
title: Como depurar um consumidor de habilidades ou habilidades | Microsoft Docs
description: Descreve como depurar um consumidor de habilidades ou habilidades com ngrok, quando apenas um bot é local.
keywords: habilidade de bot, bot de habilidades, consumidor de habilidades, depuração
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/15/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c8bab4861f8f8072418e4184efd40cdf4a7703cc
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417335"
---
# <a name="debug-a-skill-or-skill-consumer"></a>Depurar uma habilidade ou um consumidor de habilidades

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Talvez seja necessário depurar uma habilidade ou um bot do consumidor de habilidades, onde o que você deseja depurar está em execução localmente e o outro está em execução na nuvem. Nessa situação, você pode usar a ferramenta ngrok para expor o ponto de extremidade de bot local como uma URL pública.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do [Microsoft Azure](https://azure.microsoft.com/).
- [ngrok](https://ngrok.com/) instalado.
- O [emulador do bot Framework](https://aka.ms/Emulator-wiki-getting-started) instalado.
- Conhecimento de [habilidades](skills-conceptual.md), como [depurar um bot](../bot-service-debug-bot.md), como [depurar um bot de qualquer canal usando ngrok](../bot-service-debug-channel-ngrok.md)e como [depurar com o emulador](../bot-service-debug-emulator.md).
- Habilidades registradas e bots do consumidor de habilidades.

## <a name="to-debug-a-local-skill-consumer"></a>Para depurar um consumidor de habilidades local

Nesse cenário, você não precisa modificar a configuração da habilidade implantada e pode usar o emulador do bot Framework para testar o consumidor diretamente.

1. Configure um ponto de extremidade de túnel para o bot local e configure seu registro no portal do Azure conforme descrito em como [depurar um bot de qualquer canal usando ngrok](../bot-service-debug-channel-ngrok.md).
1. Atualize a configuração do aplicativo do consumidor e defina o ponto de extremidade do host de habilidade para a URL gerada pelo ngrok.
1. Por fim, execute o consumidor localmente e conecte-o conforme descrito em como [depurar com o emulador](../bot-service-debug-emulator.md).

## <a name="to-debug-a-local-skill-bot"></a>Para depurar um bot de habilidades local

Enquanto você estiver testando as alterações em uma habilidade, talvez você queira executá-lo e depurá-lo localmente enquanto ele é acessado de um bot de consumidor hospedado em um servidor de preparo.

Nesse cenário, você precisa modificar a configuração do consumidor de habilidades implantado. Como você usará o consumidor para testar o bot.

1. Configure um ponto de extremidade de túnel para o bot local e configure seu registro no portal do Azure conforme descrito em como [depurar um bot de qualquer canal usando ngrok](../bot-service-debug-channel-ngrok.md).
1. Atualize a configuração do aplicativo do consumidor e defina o ponto de extremidade de habilidade para a URL gerada pelo ngrok.
   Você pode editar a configuração diretamente no servidor ou pode ser necessário reimplantar o consumidor de habilidades.
1. Execute a habilidade localmente e invoque-a do consumidor de habilidades.
