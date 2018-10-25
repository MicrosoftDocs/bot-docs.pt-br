---
title: APIs REST do Bot Builder | Microsoft Docs
description: Comece pelas APIs REST do Bot Framework que podem ser usadas para criar bots e clientes que se conectam aos bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 632d0b1e603445954eb4894fcfc256d5ac738539
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998433"
---
# <a name="bot-framework-rest-apis"></a>APIs REST do Bot Framework
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

As APIs REST do Bot Framework permitem que você crie bots que troquem mensagens com canais configurados no <a href="https://dev.botframework.com/" target="_blank">Portal do Bot Framework</a>, armazenem e recuperem dados de estado e conectem seus próprios aplicativos de cliente aos seus bots. Todos os serviços de Bot Framework usam REST e JSON padrão do setor sobre HTTPS.

## <a name="build-a-bot"></a>Criar um bot

Você pode criar um bot com qualquer linguagem de programação usando o serviço Bot Connector para trocar mensagens com canais configurados no Portal do Bot Framework. 

> [!TIP]
> O Bot Framework fornece bibliotecas de cliente que podem ser usadas para criar bots em C# ou Node.js. Para criar um bot usando C#, use o [SDK do Bot Builder para C#](../dotnet/bot-builder-dotnet-overview.md). Para criar um bot usando Node.js, use o [SDK do Bot Builder para Node.js](../nodejs/index.md). 

Para saber mais sobre como criar bots usando o serviço Bot Connector, veja [Conceitos principais](bot-framework-rest-connector-concepts.md).

## <a name="build-a-client"></a>Criar um cliente

Você pode habilitar seu próprio aplicativo cliente para se comunicar com seu bot usando a API de Linha Direta. A API de Linha Direta implementa um mecanismo de autenticação que usa os padrões de segredo/token padrão e fornece um esquema estável, mesmo que seu bot mude sua versão de protocolo. Para saber mais sobre como usar a API de Linha Direta para habilitar a comunicação entre um cliente e seu bot, veja [Conceitos principais](bot-framework-rest-direct-line-3-0-concepts.md). 