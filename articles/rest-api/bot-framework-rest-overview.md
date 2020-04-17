---
title: APIs REST do Bot Framework – Serviço de Bot
description: Comece pelas APIs REST do Bot Framework que podem ser usadas para criar bots e clientes que se conectam aos bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
ms.openlocfilehash: 60a5e08caf11977eed72bfba0dfeba3472194822
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80117451"
---
# <a name="bot-framework-rest-apis"></a>APIs REST do Bot Framework

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

A maioria dos bots do Bot Framework é criada usando o SDK do Bot Framework, que organiza o bot e processa toda a conversa para você. Uma alternativa ao uso do SDK é enviar mensagens diretamente para o bot usando a API REST.

## <a name="build-a-bot"></a>Criar um bot

Ao codificar as APIs REST do Bot Framework, você pode enviar e receber mensagens com usuários em qualquer canal configurado no registro do Serviço de Bot do Azure do bot.

> [!TIP]
> O Bot Framework fornece bibliotecas de cliente que podem ser usadas para criar bots em C# ou Node.js.
> Para criar um bot usando C#, use o [SDK do Bot Framework para C#](../dotnet/bot-builder-dotnet-overview.md).
> Para criar um bot usando Node.js, use o [SDK do Bot Framework para Node.js](../nodejs/index.md).

Consulte [Serviço de Bot do Azure](../bot-service-overview-introduction.md) para saber mais sobre a criação de bots usando o serviço.

## <a name="build-a-direct-line-or-web-chat-client"></a>Criar um cliente do Direct Line ou do Webchat

A maioria dos canais, como Facebook, Teams ou Slack, fornece clientes, mas, com o Direct Line e o Webchat, você pode habilitar seu próprio aplicativo cliente para comunicar-se com seu bot. A API de Linha Direta implementa um mecanismo de autenticação que usa os padrões de segredo/token padrão e fornece um esquema estável, mesmo que seu bot mude sua versão de protocolo. Para saber mais sobre como usar a API de Linha Direta para habilitar a comunicação entre um cliente e seu bot, veja [Conceitos principais](bot-framework-rest-direct-line-3-0-concepts.md).

Os clientes do Direct Line e do Webchat podem estar em diferentes linguagens e implantações (por exemplo, um aplicativo, em vez de um serviço). Para obter mais informações, confira [Sobre o Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-directline?view=azure-bot-service-4.0).
