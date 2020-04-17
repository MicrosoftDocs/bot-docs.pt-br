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
ms.openlocfilehash: 02b5e318699a31ffcf6b42befda2a4bb88c8352c
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80250175"
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

<!--
This section belongs to a how to (sample) article (TBD).

## Create Azure AD applications

Currently SSO in botframework is only supported for aadV2 apps.
We need to create 2 applications - one for the client and one for the Bot.
Depending on the scenario, the client may be webchat or a virtual assistant.
The general case for a Bot would be a skill Bot.

## Client Azure AD app

The client AAD application will be used to create an exchangeable token that will be passed onto the bot.
For an example of how to create an AAD app, look at the [bot builder authentication docs](https://docs.microsoft.com/azure/bot-service/bot-builder-authentication?view=azure-bot-service-4.0&tabs=csharp#create-your-azure-ad-application).

## Service Azure AD app

1) Follow the steps on [Create your Azure AD application](https://docs.microsoft.com/azure/bot-service/bot-builder-authentication?view=azure-bot-service-4.0&tabs=csharp#create-your-azure-ad-application).
2) In the **Expose an api** panel, click **Add a scope**. Fill in the fields
    - Click the **Add scope button**.
    - Click the **Add a client application** button, and enter the app Id for the client AAD app. Select the Scope that you created in the previous step. This ensures that the user will not be asked to consent when the client tries to get an exchangeable token for this app's scope
3) In the **Manifest** panel, set the `accessTokenAcceptedVersion` key to be `2`.

## Service Auth Connection

Remove these links and add back to how to page after sample is posted to botbuilder-samples experimental folder

1) Follow the directions in the [bot builder authentication doc](https://docs.microsoft.com/azure/bot-service/bot-builder-authentication?view=azure-bot-service-4.0&tabs=csharp#azure-ad-v2)
2) In the **Expose an api** panel, copy the scope that you added earlier. Fill it in the **Token Exchange Uri** field.
3) Save the connection setting. -->