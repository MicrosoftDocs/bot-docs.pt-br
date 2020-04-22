---
title: Habilitar o WebChat com a extensão de serviço de aplicativo do Direct Line
titleSuffix: Bot Service
description: Habilitar o WebChat com a extensão de serviço de aplicativo do Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: a3cb2e438d2b042f66d97ef90b162cf172aeb3f3
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81395639"
---
# <a name="use-webchat-with-the-direct-line-app-service-extension"></a>Habilitar o WebChat com a extensão de serviço de aplicativo do Direct Line

Este artigo descreve como habilitar o WebChat com a extensão de serviço de aplicativo do Direct Line.

## <a name="get-your-direct-line-secret"></a>Obter o segredo do Direct Line

A primeira etapa é encontrar seu segredo do Direct Line. Você pode fazer isso seguindo as instruções descritas no artigo [Conectar um bot ao Direct Line](bot-service-channel-connect-directline.md).

## <a name="get-the-preview-version-of-directlinejs"></a>Obter a versão prévia do DirectLineJS
A versão prévia do DirectLineJS pode ser encontrada aqui: https://github.com/Jeffders/DirectLineAppServiceExtensionPreview/tree/master/libraries

## <a name="integrate-webchat-client"></a>Integrar o cliente do WebChat

> [!NOTE]
> Os cartões adaptáveis enviados por meio da Extensão de Serviço de Aplicativo do Direct Line não passam pelo mesmo processamento que aqueles enviados por meio de outras versões do canal do Direct Line. Por causa disso, a representação JSON do cartão adaptável enviado ao WebChat da Extensão de Serviço de Aplicativo do Direct Line não terá valores padrão adicionados pelo canal se os campos forem omitidos pelo bot quando o cartão for criado.

De modo geral, a abordagem é a mesma que a de antes. Com a exceção da nova versão que foi criada do **WebChat** que dá suporte ao tráfego bidirecional do **WebSocket** e que, em vez de conectar-se a [https://directline.botframework.com/](https://directline.botframework.com/), conecta-se ao seu Bot hospedado.
A URL do Direct Line para o bot será `https://<your_app_service>.azurewebsites.net/.bot/`, em que a extensão `/.bot/` é o **ponto de extremidade** do Direct Line em seu Serviço de Aplicativo.
Se puder configurar seu próprio nome de domínio, você ainda precisará acrescentar o caminho `/.bot/` para acessar as APIs REST do Direct Line.

1. Troque o segredo por um token seguindo as instruções no artigo [Autenticação](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0). Mas, em vez de obter um token neste local: `https://directline.botframework.com/v3/directline/tokens/generate`, você gera o token diretamente de sua Extensão de Serviço de Aplicativo do Direct Line neste local: `https://<your_app_service>.azurewebsites.net/.bot/v3/directline/tokens/generate`.  

1. Quando tiver um token, você poderá atualizar a página da Web que usa o WebChat com estas alterações:

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
    <title>Direct Line Streaming Sample</title>
    <script src="~/directLine.js"></script>
    <script src="https://cdn.botframework.com/botframework-webchat/master/webchat.js"></script>
    <style>
        html, body {
            height: 100%
        }

        body {
            margin: 0
        }

        #webchat,

        #webchat > * {
            height: 100%;
            width: 100%;
        }
    </style>
</head>

<body>
    <div id="webchat" role="main"></div>
    <script>
        const activityMiddleware = () => next => card => {
            if (card.activity.type === 'trace') {
                // Return false means, don't render the trace activities
                return () => false;
            } else {
                return children => next(card)(children);
            }
        };


        var dl = new DirectLine.DirectLine({
            secret: '<your token>',
            domain: 'https://<your_site>.azurewebsites.net/.bot/v3/directline',
            webSocket: true,
            conversationId: '<your conversation id>'
        });
        window.WebChat.renderWebChat({
            activityMiddleware,
            directLine: dl,
            userID: '<your generated user id>'
        }, document.getElementById('webchat'));
    </script>
</body>
</html>

```
