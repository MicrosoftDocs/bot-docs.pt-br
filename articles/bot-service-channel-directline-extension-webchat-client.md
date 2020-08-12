---
title: Usar o Webchat com a extensão de serviço de aplicativo do Direct Line
titleSuffix: Bot Service
description: Usar o Webchat com a extensão de serviço de aplicativo do Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: e4b5c14a25493ad3933fa0e716678b3c773f73e9
ms.sourcegitcommit: 7bf72623d9abf15e1444e8946535724f500643c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143825"
---
# <a name="use-web-chat-with-the-direct-line-app-service-extension"></a>Usar o Webchat com a extensão de serviço de aplicativo do Direct Line

Este artigo descreve como usar o Webchat com a Extensão do Serviço de Aplicativo do Direct Line. O Webchat versão 4.9.1 ou superior é necessário para suporte nativo da Extensão do Serviço de Aplicativo do Direct Line.

## <a name="integrate-web-chat-client"></a>Integrar cliente do Webchat

> [!NOTE]
> Os Cartões Adaptáveis enviados por meio da Extensão do Serviço de Aplicativo do Direct Line não passam pelo mesmo processamento que aqueles enviados por meio de outras versões do canal do Direct Line. Por causa disso, a declaração JSON do Cartão Adaptável enviado ao Webchat da Extensão do Serviço de Aplicativo do Direct Line não terá valores padrão adicionados pelo canal se os campos forem omitidos pelo bot quando o cartão for criado.

De modo geral, a abordagem é a mesma que a de antes. Com exceção da versão 4.9.1 ou superior do **Webchat** há suporte interno para estabelecer um **WebSocket** bidirecional, que, em vez de conectar-se ao [https://directline.botframework.com/](https://directline.botframework.com/), se conecta diretamente ao bot hospedado.
A URL do Direct Line para o bot será `https://<your_app_service>.azurewebsites.net/.bot/`, em que a extensão `/.bot/` é o **ponto de extremidade** do Direct Line em seu Serviço de Aplicativo.
Se você configurar o seu próprio nome de domínio, ainda precisará acrescentar o caminho `/.bot/` para acessar as APIs REST do Direct Line.

1. Troque o segredo por um token seguindo as instruções no artigo [Autenticação](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0). Em vez de obter um token em `https://directline.botframework.com/v3/directline/tokens/generate` , você gera o token diretamente de sua extensão de serviço de aplicativo de linha direta em `https://<your_app_service>.azurewebsites.net/.bot/v3/directline/tokens/generate` .

1. Para obter um exemplo que mostra como buscar um token do serviço de host, consulte [exemplos de chat Web](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/01.getting-started/i.protocol-direct-line-app-service-extension). Modifique a página da Web de exemplo com as alterações mostradas abaixo.

```html
<!DOCTYPE html>
<html lang="en-US">
  <head>
    <title>Web Chat</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script
      crossorigin="anonymous"
      src="https://cdn.botframework.com/botframework-webchat/latest/webchat-minimal.js"
    ></script>
    <style>
      html,
      body {
        background-color: #f7f7f7;
        height: 100%;
      }

      body {
        margin: 0;
      }

      #webchat {
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.05);
        height: 100%;
        margin: auto;
        max-width: 480px;
        min-width: 360px;
      }
    </style>
  </head>
  <body>
    <div id="webchat" role="main"></div>
    <script>
      (async function() {
        const res = await fetch('<URL of your Token Service as described in Steps 1 and 2 above.>', { method: 'POST' });
        const { token } = await res.json();

        window.WebChat.renderWebChat(
          {
            directLine: await window.WebChat.createDirectLineAppServiceExtension({
              domain: 'https://<your_app_service>.azurewebsites.net/.bot/v3/directline',
              token
            })
          },
          document.getElementById('webchat')
        );

        document.querySelector('#webchat > *').focus();
      })().catch(err => console.error(err));
    </script>
  </body>
</html>

```
