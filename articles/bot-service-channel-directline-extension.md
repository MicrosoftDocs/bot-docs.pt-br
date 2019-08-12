---
title: Extensão de Serviço de Aplicativo do Direct Line
titleSuffix: Bot Service
description: Recursos da extensão de serviço de aplicativo do Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/09/2019
ms.openlocfilehash: 27067cf2582de63cf67785cfaaa70b9a33685894
ms.sourcegitcommit: a1eaa44f182a7210197bd793250907df00e9edab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68757758"
---
## <a name="direct-line-app-service-extension"></a>Direct Line App Service Extension

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

A extensão de serviço de aplicativo do Direct Line estabelece um conjunto de **pipes nomeados persistentes** para se conectar a um bot por meio de um **BotAdapter** que o cliente pode adicionar.

Ela adiciona um novo conjunto de extensões de streaming ao protocolo Bot Framework. Essas extensões substituem o HTTP como transporte para troca de mensagens com um transporte que permite que solicitações bidirecionais sejam enviadas por um **WebSocket persistente**. Isso melhora o desempenho e permite mais isolamento durante a troca de informações.

Antes das extensões de streaming, a Direct Line API oferecia uma forma de um cliente enviar Atividades ao Direct Line e duas formas de o cliente recuperar Atividades do Direct Line. As mensagens eram enviadas por um HTTP POST e recebidas por um HTTP GET (sondagem) ou abrindo um WebSocket para receber ActivitySets.
As extensões de streaming expandem o uso do WebSocket e permitem que **todas as comunicações de mensagens** sejam enviadas nesse WebSocket. As extensões de streaming também podem ser usadas entre os serviços de canal e o bot.


A extensão de serviço de aplicativo do Direct Line é pré-instalada em todas as instâncias dos Serviços de Aplicativos do Azure em cada data center em todo o mundo. Ela é mantida e gerenciada pela Microsoft sem que haja trabalho de implantação adicional para o cliente.
Ela fica desabilitada nos Serviços de Aplicativos do Azure por padrão, mas pode ser ativada facilmente para que possa se conectar ao bot hospedado.

A figura a seguir mostra a arquitetura geral:

![Arquitetura da extensão do Direct Line](./media/channels/direct-line-extension-architecture.png)

## <a name="see-also"></a>Veja também

|NOME|DESCRIÇÃO|
|---|---|
|[Bot do .NET com extensão](bot-service-channel-directline-extension-net-bot.md)|Atualize um bot para trabalhar com **pipes nomeados** e habilite a extensão de serviço de aplicativo do Direct Line no recurso de **Serviço de Aplicativo do Azure** em que o bot está hospedado.  |
|[Cliente .NET com extensão](bot-service-channel-directline-extension-net-client.md)|Crie um cliente .NET em C# que se conecta à Extensão do Serviço de Aplicativo do Direct Line|
|[WebChat com extensão](bot-service-channel-directline-extension-webchat-client.md)|Habilitar o WebChat com a extensão de serviço de aplicativo do Direct Line|
|[Usar a extensão dentro do VNET](bot-service-channel-directline-extension-vnet.md)|Use a Extensão de Serviço de Aplicativo do Direct Line com uma VNET (Rede Virtual do Azure)|

## <a name="addtional-resources"></a>Recursos adicionais

- [Conectar um bot à Linha Direta](bot-service-channel-connect-directline.md)
- [Conectar um bot ao Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
