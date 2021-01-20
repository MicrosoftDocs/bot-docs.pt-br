---
title: Principais conceitos da Direct Line API do Bot Framework 3.0 – Serviço de Bot
description: Saiba mais sobre a versão 3,0 da API de linha do bot Framework Direct. Exibir informações sobre autenticação, iniciar conversas, mensagens e recursos de desenvolvedor.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/06/2019
ms.openlocfilehash: e066a7f17d15e5e8e294577d8ecb8a6f75e87c73
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576558"
---
# <a name="key-concepts-in-direct-line-api-30"></a>Principais conceitos em linha API 3.0 direto

Você pode ativar a comunicação entre seu bot e seu próprio aplicativo cliente usando a API de linha direta. Este artigo apresenta os conceitos principais 3.0 de API de linha direta e fornece informações sobre recursos para desenvolvedores relevantes. Você pode criar um cliente usando o SDK, a API REST ou o Webchat.

## <a name="authentication"></a>Autenticação

As solicitações da Direct Line API 3.0 podem ser autenticadas usando um **segredo** obtido da página de configuração do canal da Direct Line no [Portal do Azure](https://portal.azure.com) ou usando um **token** que você obtém em runtime. Para obter mais informações, consulte [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md).

## <a name="starting-a-conversation"></a>Iniciando uma conversa

As conversas de linha diretas explicitamente são abertas pelos clientes e podem ser executadas desde que o cliente e o bot participarem e tem credenciais válidas. Para saber mais, veja [Iniciar uma conversa](bot-framework-rest-direct-line-3-0-start-conversation.md).

## <a name="sending-messages"></a>Envio de mensagens

Usando o Direct Line API 3.0, um cliente pode enviar mensagens para o bot emitindo solicitações `HTTP POST`. Um cliente pode enviar uma mensagem única por solicitação. Saiba mais em [Enviar uma atividade para o bot](bot-framework-rest-direct-line-3-0-send-activity.md).

## <a name="receiving-messages"></a>Recebendo mensagens

Usando a linha API 3.0 direto, um cliente pode receber mensagens de seu bot via `WebSocket` transmitir ou emitindo `HTTP GET` solicitações. Usando qualquer uma dessas técnicas, um cliente pode receber várias mensagens do bot cada vez como parte de um `ActivitySet`. Para obter mais informações, consulte [atividades de recebimento do bot](bot-framework-rest-direct-line-3-0-receive-activities.md).

## <a name="developer-resources"></a>Recursos para desenvolvedores

### <a name="client-libraries"></a>Bibliotecas de cliente

A estrutura de Bot fornece bibliotecas de cliente que facilitam o acesso à API de linha direta 3.0 por meio do c# e Node. js. 

- Para usar a biblioteca de cliente .NET em um projeto do Visual Studio, instale o <a href="https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine" target="_blank">pacote NuGet</a> `Microsoft.Bot.Connector.DirectLine`. 

- Para usar a biblioteca de cliente do Node. js, instale o `botframework-directlinejs` biblioteca usando <a href="https://www.npmjs.com/package/botframework-directlinejs" target="_blank">NPM</a> (ou <a href="https://github.com/Microsoft/BotFramework-DirectLineJS" target="_blank">baixar</a> o código-fonte).

### <a name="web-chat-control"></a>Controle de webchat 

O Bot Framework fornece um controle que permite incorporar um bot de linha direta em seu aplicativo cliente. Para obter mais informações, consulte o <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle WebChat do Microsoft Bot Framework</a>.
