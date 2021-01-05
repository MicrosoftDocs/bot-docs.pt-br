---
title: Direct Line App Service Extension
titleSuffix: Bot Service
description: Familiarize-se com a extensão de serviço de aplicativo de linha direta. Consulte como usar extensões de streaming para se conectar diretamente a bots hospedados. Exibir recursos adicionais.
services: bot-service
author: mmiele
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: v-mimiel
ms.date: 05/19/2020
ms.openlocfilehash: 647601de8fa9445b6d5f2afdc1e7a3f946633043
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97758864"
---
# <a name="direct-line-app-service-extension"></a>Direct Line App Service Extension

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

A extensão do Serviço de Aplicativo do Direct Line permite que os clientes se conectem diretamente com o host em que o bot está localizado. Ele é executado dentro da mesma assinatura, serviço de aplicativo e rede do Azure que o bot e fornece isolamento de rede e, em alguns casos, desempenho aprimorado. O aplicativo cliente usa o [protocolo WebSocket](https://tools.ietf.org/html/rfc6455) para se comunicar com o bot. A figura a seguir mostra a arquitetura geral:

![Arquitetura de extensão do serviço de aplicativo de linha direta](./media/channels/direct-line-extension-architecture.png "Direct Line App Service Extension")

> Observação Se você não precisar de isolamento de rede e quiser usar o canal Standard por meio do protocolo HTTPS, consulte [conectar um bot à linha direta](bot-service-channel-connect-directline.md).

A extensão do serviço de aplicativo de linha direta adiciona um novo conjunto de extensões de streaming ao protocolo bot Framework, que substitui HTTP para troca de mensagens com um transporte que permite que solicitações bidirecionais sejam enviadas por um **WebSocket persistente**.

Antes das extensões de streaming, a Direct Line API oferecia uma forma de um cliente enviar Atividades ao Direct Line e duas formas de o cliente recuperar Atividades do Direct Line. As mensagens eram enviadas por um HTTP POST e recebidas por um HTTP GET (sondagem) ou abrindo um WebSocket para receber ActivitySets.
As extensões de streaming expandem o uso do WebSocket e permitem que **todas as comunicações de mensagens** sejam enviadas nesse WebSocket. As extensões de streaming também podem ser usadas entre os serviços de canal e o bot.

A extensão do serviço de aplicativo de linha direta é pré-instalada em todas as instâncias de serviços de Azure App em cada data center em todo o mundo. Ela é mantida e gerenciada pela Microsoft sem que haja trabalho de implantação adicional para o cliente. Ela fica desabilitada nos Serviços de Aplicativos do Azure por padrão, mas pode ser ativada facilmente para que possa se conectar ao bot hospedado.

## <a name="see-also"></a>Consulte Também

|Nome|Descrição|
|---|---|
|[Configurar o bot do .NET para a extensão](bot-service-channel-directline-extension-net-bot.md)|Atualize um bot do .NET para trabalhar com **pipes nomeados** e habilite a Extensão do Serviço de Aplicativo do Direct Line no recurso de **Serviço de Aplicativo do Azure** em que o bot está hospedado.  |
|[Configurar o bot do Node.js para a extensão](bot-service-channel-directline-extension-node-bot.md)|Atualize um bot do Node.js para trabalhar com **pipes nomeados** e habilite a Extensão do Serviço de Aplicativo do Direct Line no recurso de **Serviço de Aplicativo do Azure** em que o bot está hospedado.  |
|[Criar cliente .NET com a extensão](bot-service-channel-directline-extension-net-client.md)|Crie um cliente .NET em C# que se conecta à extensão do serviço de aplicativo de linha direta.|
|[Usar extensão com o chat da Web](bot-service-channel-directline-extension-webchat-client.md)|Use o chat da Web com a extensão do serviço de aplicativo de linha direta.|
|[Usar a extensão dentro do VNET](bot-service-channel-directline-extension-vnet.md)|Use a extensão de serviço de aplicativo de linha direta com uma VNET (rede virtual) do Azure.|

## <a name="additional-resources"></a>Recursos adicionais

- [Conectar um bot à Linha Direta](bot-service-channel-connect-directline.md)
- [Conectar um bot ao Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
