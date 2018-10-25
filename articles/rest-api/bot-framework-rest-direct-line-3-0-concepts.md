---
title: Principais conceitos na API de Linha Direta do Bot Framework 3.0  | Microsoft Docs
description: Entenda os principais conceitos da API de linha direta do Bot Framework 3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 09/28/2018
ms.openlocfilehash: 8c63893704f513a420220a47528bada5c4f845d3
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999703"
---
# <a name="key-concepts-in-direct-line-api-30"></a>Principais conceitos em linha API 3.0 direto

Você pode ativar a comunicação entre seu bot e seu próprio aplicativo cliente usando a API de linha direta. Este artigo apresenta os conceitos principais 3.0 de API de linha direta e fornece informações sobre recursos para desenvolvedores relevantes.

## <a name="authentication"></a>Autenticação

As solicitações da API de Linha Direta 3.0 podem ser autenticadas usando um **segredo** obtido da página de configuração do canal de Linha Direta no <a href="https://dev.botframework.com/" target="_blank">Bot Framework Portal</a> ou usando um **token** que você obtém em tempo de execução. Para obter mais informações, consulte [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md).

## <a name="starting-a-conversation"></a>Iniciando uma conversa

As conversas de Linha Direta são explicitamente abertas por clientes e podem ser executadas desde que o bot e o cliente participem e tenham credenciais válidas. Para saber mais, veja [Iniciar uma conversa](bot-framework-rest-direct-line-3-0-start-conversation.md).

## <a name="sending-messages"></a>Enviar mensagens

Usando o Direct Line API 3.0, um cliente pode enviar mensagens para o bot emitindo solicitações `HTTP POST`. Um cliente pode enviar uma mensagem única por solicitação. Saiba mais em [Enviar uma atividade para o bot](bot-framework-rest-direct-line-3-0-send-activity.md).

## <a name="receiving-messages"></a>Recebendo mensagens

Usando a linha API 3.0 direto, um cliente pode receber mensagens de seu bot via `WebSocket` transmitir ou emitindo `HTTP GET` solicitações. Usando qualquer uma dessas técnicas, um cliente pode receber várias mensagens do bot cada vez como parte de um `ActivitySet`. Para obter mais informações, consulte [atividades de recebimento do bot](bot-framework-rest-direct-line-3-0-receive-activities.md).

## <a name="developer-resources"></a>Recursos para desenvolvedores

### <a name="client-libraries"></a>Bibliotecas de cliente

A estrutura de Bot fornece bibliotecas de cliente que facilitam o acesso à API de linha direta 3.0 por meio do c# e Node. js. 

- Para usar a biblioteca de cliente .NET dentro de um projeto do Visual Studio, instale o `Microsoft.Bot.Connector.DirectLine` <a href="https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine" target="_blank">pacote do NuGet</a>. 

- Para usar a biblioteca de cliente do Node. js, instale o `botframework-directlinejs` biblioteca usando <a href="https://www.npmjs.com/package/botframework-directlinejs" target="_blank">NPM</a> (ou <a href="https://github.com/Microsoft/BotFramework-DirectLineJS" target="_blank">baixar</a> o código-fonte).

Como uma alternativa ao uso de bibliotecas de cliente C# ou Node. js, você pode gerar sua própria biblioteca de cliente na linguagem de sua escolha usando o <a href="https://docs.botframework.com/en-us/restapi/directline3/swagger.json" target="_blank">direto linha 3.0 arquivo Swagger da API</a>.

::: moniker range="azure-bot-service-3.0"

### <a name="sample-code"></a>Exemplo de código

O <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples" target="_blank">BotBuilder-Samples</a> repositório do GitHub contém vários exemplos que mostram como usar a API de linha direta 3.0 com C# e Node. js.

| Amostra | Linguagem | DESCRIÇÃO |
|----|----|----|
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/core-DirectLine" target="_blank">Exemplo de Bot de linha direta</a> | C# | Um bot de exemplo e um se comunicar entre si usando a API de linha direta de cliente personalizado. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/core-DirectLineWebSockets" target="_blank">Direcionar o exemplo de Bot de linha (usando o cliente WebSocket)</a> | C# | Um robô de amostra e um cliente personalizado se comunicando entre si usando a API de linha direta e WebSockets. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/core-DirectLine" target="_blank">Exemplo de Bot de linha direta</a> | JavaScript | Um bot de exemplo e um se comunicar entre si usando a API de linha direta de cliente personalizado. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/core-DirectLineWebSockets" target="_blank">Direcionar o exemplo de Bot de linha (usando o cliente WebSocket)</a> | JavaScript | Um robô de amostra e um cliente personalizado se comunicando entre si usando a API de linha direta e WebSockets. |

::: moniker-end

### <a name="web-chat-control"></a>Controle de webchat 

O Bot Framework fornece um controle que permite incorporar um bot de linha direta em seu aplicativo cliente. Para obter mais informações, consulte o <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle WebChat do Microsoft Bot Framework</a>.
