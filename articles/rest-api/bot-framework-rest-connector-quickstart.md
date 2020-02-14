---
title: Crie um bot com o serviço Bot Connector – Serviço de Bot
description: Crie um bot com o serviço Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: f5ac5397d2ef13eb08d92d9cf560fddf582165c4
ms.sourcegitcommit: d24fe2178832261ac83477219e42606f839dc64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77071874"
---
# <a name="create-a-bot-with-the-bot-connector-service"></a>Crie um bot com o serviço Bot Connector
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-quickstart.md)
> - [Node.js](../nodejs/bot-builder-nodejs-quickstart.md)
> - [Serviço de bot](../bot-service-quickstart.md)
> - [REST](../rest-api/bot-framework-rest-connector-quickstart.md)

O serviço Bot Connector permite que seu bot troque mensagens com canais que são configurados no [Portal do Azure](https://portal.azure.com) usando REST padrão do setor e JSON sobre HTTPS. Este tutorial orienta você pelo processo de obter um token de acesso do Bot Framework e usar o serviço Bot Connector para trocar mensagens com o usuário.

## <a id="get-token"></a> Obter um token de acesso

> [!IMPORTANT]
> Se você ainda não fez isso, será necessário [registrar seu bot](../bot-service-quickstart-registration.md) com o Bot Framework para obter sua ID do Aplicativo e a senha. Você precisará da AppID e da senha do bot para solicitar um token de acesso.

Para comunicar-se com o serviço do Bot Connector, é necessário especificar um token de acesso no cabeçalho `Authorization` de cada solicitação de API, usando este formato: 

```http
Authorization: Bearer ACCESS_TOKEN
```

Você pode obter o token de acesso para o bot emitindo uma solicitação de API.

### <a name="request"></a>Solicitação

Para solicitar um token de acesso que pode ser usado para autenticar solicitações para o serviço Bot Connector, emita a seguinte solicitação, substituindo **MICROSOFT-APP-ID** e **MICROSOFT-APP-PASSWORD** pela ID do Aplicativo e a senha que você obteve ao [registrar](../bot-service-quickstart-registration.md) o bot com o Bot Framework.

```http
POST https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=MICROSOFT-APP-ID&client_secret=MICROSOFT-APP-PASSWORD&scope=https%3A%2F%2Fapi.botframework.com%2F.default
```

### <a name="response"></a>Resposta

Se a solicitação for bem-sucedida, você receberá uma resposta HTTP 200 que especifica o token de acesso e informações sobre sua expiração. 

```json
{
    "token_type":"Bearer",
    "expires_in":3600,
    "ext_expires_in":3600,
    "access_token":"eyJhbGciOiJIUzI1Ni..."
}
```

> [!TIP]
> Para obter mais detalhes sobre a autenticação no serviço Bot Connector, veja [Autenticação](bot-framework-rest-connector-authentication.md).

## <a name="exchange-messages-with-the-user"></a>Trocar mensagens com o usuário

Uma conversa é uma série de mensagens trocadas entre o usuário e o seu bot. 

### <a name="receive-a-message-from-the-user"></a>Receber uma mensagem do usuário

Quando o usuário envia uma mensagem, o Bot Framework Connector posta uma solicitação para o ponto de extremidade que foi especificado quando você [registrou](../bot-service-quickstart-registration.md) seu bot. O corpo da solicitação é um objeto [Atividade][]. O exemplo a seguir mostra o corpo da solicitação que um bot recebe quando o usuário envia uma mensagem simples para o bot. 

```json
{
    "type": "message",
    "id": "bf3cc9a2f5de...",
    "timestamp": "2016-10-19T20:17:52.2891902Z",
    "serviceUrl": "https://smba.trafficmanager.net/apis",
    "channelId": "channel's name/id",
    "from": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "12345678",
        "name": "bot's name"
    },
    "text": "Haircut on Saturday"
}
```

### <a name="reply-to-the-users-message"></a>Responder à mensagem do usuário

Quando o ponto de extremidade do seu bot recebe uma solicitação `POST` que representa uma mensagem do usuário (ou seja, `type` = **mensagem**), use as informações nessa solicitação para criar o objeto [Atividade][] da resposta.

1. Defina a propriedade **conversation** para o conteúdo da propriedade **conversation** na mensagem do usuário.
2. Defina a propriedade **from** para o conteúdo da propriedade **recipient** na mensagem do usuário.
3. Defina a propriedade **recipient** para o conteúdo da propriedade **from** na mensagem do usuário.
4. Defina as propriedades **text** e **attachments** conforme for apropriado.

Use a propriedade `serviceUrl` na solicitação de entrada para [identificar o URI base](bot-framework-rest-connector-api-reference.md#base-uri) que o bot deve usar para emitir sua resposta. 

Para enviar a resposta, `POST` seu objeto `Activity` para `/v3/conversations/{conversationId}/activities/{activityId}`, conforme mostrado no exemplo a seguir. O corpo dessa solicitação é um objeto `Activity` que solicita que o usuário selecione uma hora de compromisso disponível.

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/bf3cc9a2f5de... 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "bot's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "text": "I have these times available:",
    "replyToId": "bf3cc9a2f5de..."
}
```

Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri). 

> [!IMPORTANT]
> Conforme mostrado neste exemplo, o cabeçalho `Authorization` de cada solicitação de API que você enviar deverá conter a palavra **Bearer** seguido pelo token de acesso que você [obteve do Bot Framework](#get-token).

Para enviar outra mensagem que permite que um usuário selecione uma hora de compromisso disponível clicando em um botão, `POST` outra solicitação para o mesmo ponto de extremidade:

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/bf3cc9a2f5de... 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "bot's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "attachmentLayout": "list",
    "attachments": [
      {
        "contentType": "application/vnd.microsoft.card.thumbnail",
        "content": {
          "buttons": [
            {
              "type": "imBack",
              "title": "10:30",
              "value": "10:30"
            },
            {
              "type": "imBack",
              "title": "11:30",
              "value": "11:30"
            },
            {
              "type": "openUrl",
              "title": "See more",
              "value": "http://www.contososalon.com/scheduling"
            }
          ]
        }
      }
    ],
    "replyToId": "bf3cc9a2f5de..."
}
```   

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você obteve um token de acesso do Bot Framework e usou o serviço Bot Connector para trocar mensagens com o usuário. Você pode usar o [Bot Framework Emulator](../bot-service-debug-emulator.md) para testar e depurar seu bot. Se desejar compartilhar seu bot com outras pessoas, você precisará [configurá-lo](../bot-service-manage-channels.md) para que ele seja executado em um ou mais canais.

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
