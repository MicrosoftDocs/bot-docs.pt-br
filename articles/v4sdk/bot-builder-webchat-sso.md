---
title: Adicionar logon único ao Web Chat-serviço de bot
description: Saiba mais sobre como adicionar logon único ao Web Chat na estrutura do bot.
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/10/2020
ms.openlocfilehash: 064760f815be423e499a985da78700e8f48b8d0a
ms.sourcegitcommit: 757ddf4f3fd3557ad9746dfa83ca742d54ac4763
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86876544"
---
# <a name="add-single-sign-on-to-web-chat"></a>Adicionar logon único ao Web Chat

O SSO (logon único) permite que um cliente, como um controle de chat da Web, se comunique com um bot em nome do usuário. No momento, há suporte apenas para o provedor de identidade do [Azure AD v2](~/v4sdk/bot-builder-concept-identity-providers.md#azure-active-directory-identity-provider).

Normalmente, um bate-papo da Web é inserido em uma página do site. Quando o usuário entra no site, o bate-papo da Web invoca um bot em nome do usuário. O token do cliente do site, com base nas credenciais do usuário, é trocado por um diferente para acessar o bot. Dessa forma, o usuário não precisa fazer logon duas vezes; na primeira vez no site e na segunda vez no bot, portanto, o termo SSO.

O diagrama a seguir mostra o fluxo de SSO ao usar um cliente de chat da Web.

![bot sso webchat](~/v4sdk/media/concept-bot-authentication/bot-auth-webchat-sso.PNG)

No caso de falha, o SSO volta para o comportamento existente de mostrar o cartão **OAuth** . A falha poderá ser causada, por exemplo, se o consentimento do usuário for necessário ou se a troca do token falhar.

Vamos analisar o fluxo.

1. O usuário entra no site.
1. Uma atividade de gatilho OAuth é recebida pelo bate-papo da Web.
1. O bate-papo da Web inicia uma conversa com o bot por meio de uma atividade de gatilho OAuth.
1. O bot envia de volta um cartão OAuth para o chat da Web.
1. O bate-papo da Web intercepta o cartão OAuth antes de exibi-lo para o usuário e verifica se ele contém uma `TokenExchangeResource` propriedade.
1. Se a propriedade existir, o chat da Web deverá obter um token intercambiável para o usuário, que deve ser um token do Azure AD v2.
1. O chat da Web envia uma atividade Invoke para o bot com o corpo mostrado abaixo.
    ```json
    {
        "type": "Invoke",
        "name": "signin/tokenExchange",
        "value": {
            "id": "<any unique Id>",
            "connectionName": "<connection name on the bot (from the OAuth Card)>",
            "token": "<exchangeable token>"
        }
    }
    ```

1. O bot processa o `TokenExchangeInvokeRequest` emitindo uma solicitação para o serviço de bot do Azure para obter um token intercambiável.

1. O serviço de bot do Azure envia o token para o bot.

1. O bot retorna de `TokenExchangeInvokeResponse` volta para o bate-papo da Web. O bate-papo da Web aguarda até receber o `TokenExchangeInvokeResponse` .

    ```json
    {
        "status": "<response code>",
        "body": {
            "id":"<unique Id>",
            "connectionName": "<connection Name on the bot (from the OAuth Card)>",
            "failureDetail": "<failure reason if status code is not 200, null otherwise>"
        }
    }
    ```

1. Se o `TokenExchangeInvokeResponse` tiver um `status` de `200` , o bate-papo da Web não mostrará a placa OAuth. Para qualquer outro `status` ou se o `TokenExchangeInvokeResponse` não for recebido, o bate-papo da Web mostrará o cartão OAuth ao usuário. Isso verifica se o fluxo de SSO volta ao fluxo de OAuthCard normal em caso de erros ou dependências não atendidas, como o consentimento do usuário.

Para obter um exemplo de implementação, consulte este [exemplo de SSO](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/07.advanced-web-chat-apps/e.sso-on-behalf-of-authentication).
