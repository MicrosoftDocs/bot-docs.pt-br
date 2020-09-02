---
title: APIs REST do Bot Framework – Serviço de Bot
description: Comece pelas APIs REST do Bot Framework que podem ser usadas para criar bots e clientes que se conectam aos bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
ms.openlocfilehash: 10a89ec8c696758a8c233e0170356865caf4130d
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89361709"
---
# <a name="bot-framework-rest-apis"></a>APIs REST do Bot Framework

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

A maioria dos bots da estrutura de bot são criados usando o kit de controle do bot Framework, que organiza o bot e manipula todas as conversas para você. Uma alternativa ao uso do SDK é enviar mensagens diretamente para o bot usando uma API REST.

## <a name="build-a-bot"></a>Criar um bot

Ao codificar com APIs REST do bot Framework, você pode enviar e receber mensagens com usuários em qualquer canal configurado no registro do serviço de bot do Azure do bot.

> [!TIP]
> O Bot Framework fornece bibliotecas de cliente que podem ser usadas para criar bots em C# ou Node.js.
> Para criar um bot usando C#, use o [SDK do Bot Framework para C#](../dotnet/bot-builder-dotnet-overview.md).
> Para criar um bot usando Node.js, use o [SDK do Bot Framework para Node.js](../nodejs/index.md).

Consulte os documentos do [serviço de bot do Azure](../bot-service-overview-introduction.md) para saber mais sobre a criação de bots usando o serviço.

## <a name="build-a-direct-line-client"></a>Criar um cliente de linha direta

A maioria dos canais, como o Facebook, as equipes ou a margem de atraso, fornece clientes, mas com linha direta, você pode habilitar seu próprio aplicativo cliente para se comunicar com o bot. O [chat da Web](https://github.com/microsoft/BotFramework-WebChat) é um exemplo de software livre de um cliente de linha direta e pode ser usado como está ou modificado ou aprendido do ao fazer seu próprio cliente. A API de Linha Direta implementa um mecanismo de autenticação que usa os padrões de segredo/token padrão e fornece um esquema estável, mesmo que seu bot mude sua versão de protocolo. Para saber mais sobre como usar a API de Linha Direta para habilitar a comunicação entre um cliente e seu bot, veja [Conceitos principais](bot-framework-rest-direct-line-3-0-concepts.md).

Os clientes de linha direta podem estar em diferentes idiomas e locais (por exemplo, um aplicativo de área de trabalho em vez de uma página da Web). Para obter mais informações, confira [Sobre o Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-directline?view=azure-bot-service-4.0).
