---
title: Visão geral de como converter um bot v3 em uma habilidade – Serviço de Bot
description: Fornece uma visão geral de como converter o bot v3 em uma habilidade e consumi-lo em um bot v4.
keywords: migração do bot
author: clearab
ms.author: anclear
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/28/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 26ac21b4f0245b028605eeedff58200f857c44a5
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416864"
---
# <a name="convert-a-v3-bot-to-a-skill"></a>Converter um bot v3 em uma habilidade

[!INCLUDE [applies-to-v4](../../includes/applies-to-v4-current.md)]

Em alguns cenários, talvez não faça sentido migrar de um bot v3 para um bot v4 imediatamente, mas você talvez ainda seja interessante aproveitar a funcionalidade adicional disponível no SDK v4. Nesses casos, pode fazer sentido converter o bot v3 em uma habilidade e criar um bot de consumidor de habilidades com base no SDK v4 para passar mensagens para o bot v3. Confira [o artigo de visão geral das habilidades](../skills-conceptual.md) para obter informações adicionais sobre habilidades e consumidores de habilidades.

Para bots mais complexos, essa abordagem também pode permitir uma migração gradual. Você poderá controlar quais mensagens são manipuladas em seu bot de consumidor de habilidades e quais são passadas para sua habilidade. Isso permite migrar a funcionalidade para o novo bot em fases, e você poderá eventualmente desativar a habilidade depois que toda a funcionalidade tiver sido movida.

## <a name="whats-required"></a>O que é necessário

### <a name="upgrade-your-v3-bot-sdk-to-the-most-current-3x-version"></a>Atualizar o SDK do bot v3 para a versão 3.x mais atual

Os ganchos necessários para converter o bot em uma habilidade foram adicionados à versão 3.30.2 do SDK do .NET e à versão 3.30.0 do SDK do JavaScript.

### <a name="convert-your-v3-bot-to-a-skill"></a>Converter seu bot v3 em uma habilidade

Depois de atualizar sua versão do SDK, você precisará adicionar alguma lógica ao bot para manipular o envio e o recebimento de mensagens para o bot do consumidor de habilidades.

### <a name="create-a-v4-skill-consumer-bot"></a>Criar um bot de consumidor de habilidades v4

Em seguida, você precisará criar um bot para funcionar como seu consumidor de habilidades e adicionar lógica para determinar quais mensagens são roteadas para sua habilidade. Esse novo bot será aquele com o qual seus clientes interagem e será o local em que você adicionará funcionalidade extra com base nas funcionalidades expandidas do SDK v4.

### <a name="connect-your-users-to-the-new-skill-consumer-bot"></a>Conectar seus usuários ao novo bot do consumidor de habilidades

Por fim, você precisará substituir o bot v3 pelo novo bot v4 criado.

## <a name="get-started"></a>Introdução

Pronto para começar?

- [Converter um bot v3 do .NET em uma habilidade](net-v3-as-skill.md)
- [Converter um bot v3 do JavaScript em uma habilidade](javascript-v3-as-skill.md)
