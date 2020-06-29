---
title: Logon único no Serviço de Bot do Azure – Serviço de Bot
description: Saiba mais sobre o logon único no Serviço de Bot do Azure.
keywords: serviço de bot do azure, autenticação, serviço de token do bot framework
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/19/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 858c3498031821f6b2117e61f9387c7f97d2ba60
ms.sourcegitcommit: 5add21ad3daf0ce894612a22b951b98350961720
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420417"
---
# <a name="single-sign-on"></a>Logon único

O SSO (logon único) permite que um cliente, como o assistente virtual, WebChat e assim por diante, comunique-se com um bot ou uma habilidade em nome do usuário.
No momento, há suporte apenas para o provedor de identidade do [Azure AD v2](./bot-builder-concept-identity-providers.md#azure-active-directory-identity-provider).

O SSO se aplica aos seguintes cenários:

- Assistente virtual e um ou mais bots de habilidades. O usuário pode entrar uma vez no assistente virtual. O assistente então invoca várias habilidades em nome do usuário. Confira também [Assistente virtual](./bot-builder-virtual-assistant-introduction.md).
- Webchat inserido em um site. O usuário entra no site. O site então invoca um bot ou uma habilidade em nome do usuário.

O SSO oferece as seguintes vantagens:

- O usuário não precisará fazer logon novamente se já tiver entrado em um assistente virtual ou site.
- O assistente virtual ou site não tem conhecimento das permissões do usuário.

> [!NOTE]
> O SSO é um novo recurso do SDK do Bot Framework v4.8.

## <a name="sso-components-interaction"></a>Interação de componentes de SSO

Os diagramas de sequência de tempo a seguir mostram as interações entre os vários componentes do SSO.


- O diagrama a seguir mostra um fluxo normal ao usar um cliente do assistente virtual.

    ![bot sso va](media/concept-bot-authentication/bot-auth-sso-va-time-sequence.PNG)


- O diagrama abaixo mostra um fluxo normal e de fallback ao usar um cliente do WebChat.

    ![bot sso webchat](media/concept-bot-authentication/bot-auth-sso-webchat-time-sequence.PNG)

    No caso de falha, o SSO volta para o comportamento existente de mostrar o cartão OAuth.
    A falha poderá ser causada, por exemplo, se o consentimento do usuário for necessário ou se a troca do token falhar.

Vamos analisar o fluxo.

1. O cliente inicia uma conversa com o bot disparando um cenário de OAuth.
1. O bot envia de volta um Cartão OAuth para o cliente.
1. O cliente intercepta o cartão OAuth antes de exibi-lo ao usuário e verifica se ele contém uma propriedade `TokenExchangeResource`.
1. Se a propriedade existir, o cliente enviará um `TokenExchangeInvokeRequest` ao bot. O cliente deve ter um token intercambiável para o usuário, que deve ser um token do Azure AD v2 e cujo público-alvo deve ser o mesmo que o da propriedade `TokenExchangeResource.Uri`. <!-- For an example on how to get the user's exchangeable token, please refer to this [Webchat Sample (TBD)](https://linkrequired). --> O cliente envia uma atividade Invoke para o bot com o corpo mostrado abaixo.

    ```json
    {
        "type": "Invoke",
        "name": "signin/tokenExchange",
        "value": {
            "id": "<any unique Id>",
            "connectionName": "<connection Name on the skill bot (from the OAuth Card)>",
            "token": "<exchangeable token>"
        }
    }
    ```

1. O bot processa o `TokenExchangeInvokeRequest` e retorna um `TokenExchangeInvokeResponse` ao cliente. O cliente deve aguardar até receber `TokenExchangeInvokeResponse`.

    ```json
    {
        "status": "<response code>",
        "body": {
            "id":"<unique Id>",
            "connectionName": "<connection Name on the skill bot (from the OAuth Card)>",
            "failureDetail": "<failure reason if status code is not 200, null otherwise>"
        }
    }
    ```

1. Se o `TokenExchangeInvokeResponse` tiver um `status` de `200`, o cliente não mostrará o cartão OAuth. Confira o diagrama *fluxo normal*. Para qualquer outro `status` ou se o `TokenExchangeInvokeResponse` não for recebido, o cliente mostrará o cartão OAuth ao usuário. Confira o diagrama de *fluxo de fallback*. Isso verifica se o fluxo de SSO volta ao fluxo de OAuthCard normal em caso de erros ou dependências não atendidas, como o consentimento do usuário.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar logon único a um bot](bot-builder-authentication-sso.md)
