---
title: Usar o Webchat com a extensão de serviço de aplicativo do Direct Line
titleSuffix: Bot Service
description: Saiba como usar o chat Web com uma extensão de serviço de aplicativo de linha direta. Exiba o código que mostra como configurar uma URL de linha direta para um bot e obter um token.
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: 127b36151771dafffbd956a8a2ef96bb2e888aa6
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92415403"
---
# <a name="use-web-chat-with-the-direct-line-app-service-extension"></a>Usar o Webchat com a extensão de serviço de aplicativo do Direct Line

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como usar o chat da Web com a extensão do serviço de aplicativo de linha direta. O Web Chat versão 4.9.1 ou superior é necessário para suporte nativo de extensão do serviço de aplicativo de linha direta.

## <a name="integrate-web-chat-client"></a>Integrar cliente do Webchat

> [!NOTE]
> Os Cartões Adaptáveis enviados por meio da Extensão do Serviço de Aplicativo do Direct Line não passam pelo mesmo processamento que aqueles enviados por meio de outras versões do canal do Direct Line. Por causa disso, a declaração JSON do Cartão Adaptável enviado ao Webchat da Extensão do Serviço de Aplicativo do Direct Line não terá valores padrão adicionados pelo canal se os campos forem omitidos pelo bot quando o cartão for criado.

De modo geral, a abordagem é a mesma que a de antes. Com exceção da versão 4.9.1 ou superior do **chat da Web** , há suporte interno para estabelecer um **WebSocket**bidirecional, que, em vez de conectar-se a conectar- [https://directline.botframework.com/](https://directline.botframework.com/) se diretamente à extensão do serviço de aplicativo de linha direta hospedada com o bot.
A URL de linha direta para o bot será `https://<your_app_service>.azurewebsites.net/.bot/` , o ponto de **extremidade** de linha direta em sua extensão de serviço de aplicativo.
Se você configurar seu próprio nome de domínio ou se o bot estiver hospedado em uma nuvem do Azure soberanas, substitua a URL apropriada e acrescente o `/.bot/` caminho para acessar as APIs REST da extensão do serviço de aplicativo de linha direta.

1. Troque o segredo por um token seguindo as instruções no artigo [Autenticação](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0). Em vez de obter um token em `https://directline.botframework.com/v3/directline/tokens/generate` , você irá gerar o token diretamente de sua extensão de serviço de aplicativo de linha direta em  `https://<your_app_service>.azurewebsites.net/.bot/v3/directline/tokens/generate` .

1. Para obter um exemplo que mostra como buscar um token, consulte [exemplos de chat Web](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/01.getting-started/i.protocol-direct-line-app-service-extension).

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
        <!-- NOTE: It is highly recommended to replace the below fetch with a call to your own token service as described in step 2 above, and to avoid exposing your channel secret in client side code. -->
        const res = await fetch('https://<your_app_service>.azurewebsites.net/.bot/v3/directline/tokens/generate', { method: 'POST', Headers:{'Authorization':'Bearer ' + '<Your Bot's Direct Line channel secret>'}});
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
