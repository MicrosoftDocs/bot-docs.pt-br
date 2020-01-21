---
title: Principais conceitos da Direct Line API do Bot Framework 3.0 – Serviço de Bot
description: Entenda os principais conceitos da API de linha direta do Bot Framework 3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/06/2019
ms.openlocfilehash: 83273ade9aa79266c20287682ea3c151b1b0f8a5
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75789438"
---
# <a name="key-concepts-in-direct-line-api-30"></a>Principais conceitos em linha API 3.0 direto

Você pode ativar a comunicação entre seu bot e seu próprio aplicativo cliente usando a API de linha direta. Este artigo apresenta os conceitos principais 3.0 de API de linha direta e fornece informações sobre recursos para desenvolvedores relevantes.

## <a name="authentication"></a>Autenticação

As solicitações da API de Linha Direta 3.0 podem ser autenticadas usando um **segredo** obtido da página de configuração do canal de Linha Direta no <a href="https://dev.botframework.com/" target="_blank">Bot Framework Portal</a> ou usando um **token** que você obtém em runtime. Para obter mais informações, consulte [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md).

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

::: moniker range="azure-bot-service-3.0"

### <a name="sample-code"></a>Código de exemplo

O <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples" target="_blank">BotBuilder-Samples</a> repositório do GitHub contém vários exemplos que mostram como usar a API de linha direta 3.0 com C# e Node. js.

| Amostra | Linguagem | Descrição |
|----|----|----|
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/core-DirectLine" target="_blank">Exemplo de Bot de linha direta</a> | C# | Um bot de exemplo e um se comunicar entre si usando a API de linha direta de cliente personalizado. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/core-DirectLineWebSockets" target="_blank">Direcionar o exemplo de Bot de linha (usando o cliente WebSocket)</a> | C# | Um robô de amostra e um cliente personalizado se comunicando entre si usando a API de linha direta e WebSockets. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/core-DirectLine" target="_blank">Exemplo de Bot de linha direta</a> | JavaScript | Um bot de exemplo e um se comunicar entre si usando a API de linha direta de cliente personalizado. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/core-DirectLineWebSockets" target="_blank">Direcionar o exemplo de Bot de linha (usando o cliente WebSocket)</a> | JavaScript | Um robô de amostra e um cliente personalizado se comunicando entre si usando a API de linha direta e WebSockets. |

::: moniker-end

### <a name="web-chat-control"></a>Controle de webchat 

O Bot Framework fornece um controle que permite incorporar um bot de linha direta em seu aplicativo cliente. Para obter mais informações, consulte o <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle WebChat do Microsoft Bot Framework</a>.
