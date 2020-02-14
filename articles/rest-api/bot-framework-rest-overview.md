---
title: APIs REST do Bot Framework – Serviço de Bot
description: Comece pelas APIs REST do Bot Framework que podem ser usadas para criar bots e clientes que se conectam aos bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 2c42b39f838177dcc0e07b53357ab1395500e010
ms.sourcegitcommit: d24fe2178832261ac83477219e42606f839dc64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77071794"
---
# <a name="bot-framework-rest-apis"></a>APIs REST do Bot Framework
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

As APIs REST do Bot Framework permitem que você crie bots que trocam mensagens com canais configurados no [Portal do Azure](https://portal.azure.com), armazenam e recuperam dados de estado e conectam seus próprios aplicativos de cliente aos seus bots. Todos os serviços de Bot Framework usam REST e JSON padrão do setor sobre HTTPS.

## <a name="build-a-bot"></a>Criar um bot

Você pode criar um bot com qualquer linguagem de programação usando o serviço Bot Connector para trocar mensagens com canais configurados no Portal do Bot Framework. 

> [!TIP]
> O Bot Framework fornece bibliotecas de cliente que podem ser usadas para criar bots em C# ou Node.js. Para criar um bot usando C#, use o [SDK do Bot Framework para C#](../dotnet/bot-builder-dotnet-overview.md). Para criar um bot usando Node.js, use o [SDK do Bot Framework para Node.js](../nodejs/index.md). 

Para saber mais sobre como criar bots usando o serviço Bot Connector, veja [Conceitos principais](bot-framework-rest-connector-concepts.md).

## <a name="build-a-client"></a>Criar um cliente

Você pode habilitar seu próprio aplicativo cliente para se comunicar com seu bot usando a API de Linha Direta. A API de Linha Direta implementa um mecanismo de autenticação que usa os padrões de segredo/token padrão e fornece um esquema estável, mesmo que seu bot mude sua versão de protocolo. Para saber mais sobre como usar a API de Linha Direta para habilitar a comunicação entre um cliente e seu bot, veja [Conceitos principais](bot-framework-rest-direct-line-3-0-concepts.md). 