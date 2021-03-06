---
title: Principais conceitos da Direct Line API do Bot Framework 1.1 – Serviço de Bot
description: Saiba mais sobre a versão 1,1 da API de linha do bot Framework Direct. Exibir informações sobre autenticação, iniciar conversas, mensagens e recursos de desenvolvedor.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 1a97da1740e19df0190303035bfb2e4d28b5f66f
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89361809"
---
# <a name="key-concepts-in-direct-line-api-11"></a>Principais conceitos da API de Linha Direta 1.1

Você pode ativar a comunicação entre seu bot e seu próprio aplicativo cliente usando a API de linha direta. 

> [!IMPORTANT]
> Este artigo apresenta os principais conceitos da API de Linha Direta 1.1 e fornece informações sobre recursos relevantes para desenvolvedores. Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use a [API de Linha Direta 3.0](bot-framework-rest-direct-line-3-0-concepts.md).

## <a name="authentication"></a>Autenticação

As solicitações da Direct Line API 1.1 podem ser autenticadas usando um **segredo** obtido da página de configuração do canal da Direct Line no [Portal do Azure](https://portal.azure.com) ou usando um **token** que você obtém em runtime.  Para obter mais informações, consulte [Autenticação](bot-framework-rest-direct-line-1-1-authentication.md).

## <a name="starting-a-conversation"></a>Iniciando uma conversa

As conversas de linha diretas explicitamente são abertas pelos clientes e podem ser executadas desde que o cliente e o bot participarem e tem credenciais válidas. Para saber mais, veja [Iniciar uma conversa](bot-framework-rest-direct-line-1-1-start-conversation.md).

## <a name="sending-messages"></a>Envio de mensagens

Usando a API de Linha Direta 1.1, um cliente pode enviar mensagens ao seu bot emitindo solicitações `HTTP POST`. Um cliente pode enviar uma mensagem única por solicitação. Para saber mais, veja [Enviar uma mensagem para o bot](bot-framework-rest-direct-line-1-1-send-message.md).

## <a name="receiving-messages"></a>Recebendo mensagens

Usando a API de Linha Direta 1.1, um cliente pode receber mensagens por meio da sondagem com solicitações `HTTP GET`. Em resposta a cada solicitação, um cliente poderá receber várias mensagens do bot como parte de um `MessageSet`. Para saber mais, veja [Receber mensagens do bot](bot-framework-rest-direct-line-1-1-receive-messages.md).

## <a name="developer-resources"></a>Recursos para desenvolvedores

### <a name="client-library"></a>Biblioteca do cliente

A Estrutura de Bot fornece uma biblioteca de clientes que facilita o acesso à API de Linha Direta 1.1 por meio do C#. Para usar a biblioteca de clientes em um projeto do Visual Studio, instale o <a href="https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine/1.1.1" target="_blank">pacote NuGet v1.x</a> do `Microsoft.Bot.Connector.DirectLine`. 

Como alternativa ao uso da biblioteca de clientes do C#, você pode gerar sua própria biblioteca de clientes na linguagem da sua preferência usando o <a href="https://docs.botframework.com/restapi/directline/swagger.json" target="_blank">arquivo Swagger da API de Linha Direta 1.1</a>.
