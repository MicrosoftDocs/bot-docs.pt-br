---
title: Enviar e receber mensagens | Microsoft Docs
description: Saiba como enviar e receber mensagens usando o serviço Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: d694f20985b6bf2875fe28f599ff603512b87757
ms.sourcegitcommit: c200cc2db62dbb46c2a089fb76017cc55bdf26b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70037342"
---
# <a name="send-and-receive-messages"></a>Enviar e receber mensagens

O serviço Bot Connector permite que um bot se comunique em vários canais como Skype, Email, Slack e muito mais. Ele facilita a comunicação entre o bot e o usuário por [atividades](bot-framework-rest-connector-activities.md) de retransmissão do bot para o canal e do canal para o bot. Cada atividade contém as informações usadas para encaminhar a mensagem para o destino apropriado, juntamente com informações sobre quem criou a mensagem, o contexto e o destinatário da mensagem. Este artigo descreve como usar o serviço Bot Connector para trocar atividades de **mensagem** entre o bot e o usuário em um canal. 

## <a id="create-reply"></a> Responder a uma mensagem

### <a name="create-a-reply"></a>Criar uma resposta 

Quando o usuário enviar uma mensagem ao bot, o bot receberá a mensagem como um objeto [Atividade][] do tipo **message**. Para criar uma resposta à mensagem de um usuário, crie um objeto `Activity` e comece a definir estas propriedades:

| Propriedade | Valor |
|----|----|
| conversation | Defina essa propriedade como o conteúdo da propriedade `conversation` na mensagem do usuário. |
| de | Defina essa propriedade como o conteúdo da propriedade `recipient` na mensagem do usuário. |
| localidade | Defina essa propriedade como o conteúdo da propriedade `locale` na mensagem do usuário, se for especificada. |
| recipient | Defina essa propriedade como o conteúdo da propriedade `from` na mensagem do usuário. |
| replyToId | Defina essa propriedade como o conteúdo da propriedade `id` na mensagem do usuário. |
| Tipo | Defina essa propriedade como **message**. |

Em seguida, defina as propriedades que especificam as informações que você deseja comunicar ao usuário. Por exemplo, defina a propriedade `text` para especificar o texto a ser exibido na mensagem, defina a propriedade `speak` para especificar o texto a ser falado pelo bot e defina a propriedade `attachments` para especificar os cartões avançados ou os anexos de mídia a serem incluídos na mensagem. Para obter informações detalhadas sobre as propriedades de mensagem geralmente usadas, confira [Criar mensagens](bot-framework-rest-connector-create-messages.md).

### <a name="send-the-reply"></a>Enviar a resposta

Use a propriedade `serviceUrl` na atividade de entrada para [identificar o URI base](bot-framework-rest-connector-api-reference.md#base-uri) que o bot deve usar para emitir sua resposta. 

Para enviar a resposta, emita esta solicitação: 

```http
POST /v3/conversations/{conversationId}/activities/{activityId}
```

Nesse URI de solicitação, substitua **{conversationId}** pelo valor da propriedade `id` do objeto `conversation` na Atividade (resposta) e substitua **{activityId}** pelo valor da propriedade `replyToId` na Atividade (resposta). Defina o corpo da solicitação como o objeto [Atividade][] que você criou para representar sua resposta.

O exemplo a seguir mostra uma solicitação que envia uma resposta simples somente texto a uma mensagem do usuário. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723 
Authorization: Bearer ACCESS_TOKEN 
Content-Type: application/json 
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "Pepper's News Feed"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "Convo1"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "SteveW"
    },
    "text": "My bot's reply",
    "replyToId": "5d5cdc723"
}
```

## <a id="send-message"></a> Enviar uma mensagem (não resposta)

A maioria das mensagens enviadas pelo bot será em resposta às mensagens que ele recebe do usuário. No entanto, pode haver ocasiões em que o bot precisa enviar uma mensagem para a conversa que não seja uma resposta direta a nenhuma mensagem do usuário. Por exemplo, talvez o bot precise iniciar um novo tópico de conversa ou enviar uma mensagem de despedida no final da conversa. 

Para enviar uma mensagem para uma conversa que não seja uma resposta direta a nenhuma mensagem do usuário, emita esta solicitação: 

```http
POST /v3/conversations/{conversationId}/activities
```

Nesse URI de solicitação, substitua **{conversationId}** pela ID da conversa. 
    
Defina o corpo da solicitação como um objeto [Atividade][] que você criou para representar sua resposta.

> [!NOTE]
> O Bot Framework não impõe nenhuma restrição quanto ao número de mensagens que podem ser enviadas por um bot. No entanto, a maioria dos canais impõe uma limitação para impedir que os bots enviem um grande número de mensagens em um período curto. Além disso, se o bot enviar rapidamente várias mensagens sucessivas, o canal nem sempre pode renderizar as mensagens na sequência correta.

## <a name="start-a-conversation"></a>Iniciar uma conversa

Pode haver ocasiões em que o bot precisa iniciar uma conversa com um ou mais usuários. Para iniciar uma conversa com um usuário em um canal, o bot precisa saber seus dados da conta e os dados da conta do usuário nesse canal. 

> [!TIP]
> Se o bot precisar iniciar conversas com seus usuários no futuro, armazene em cache os dados da conta do usuário, outras informações relevantes, como as preferências e a localidade do usuário, e a URL de serviço (para usar como o URI base na solicitação Iniciar Conversa). 

Para iniciar uma conversa, emita esta solicitação: 

```http
POST /v3/conversations
```

Defina o corpo da solicitação como um objeto [ConversationParameters][] que especifica os dados da conta do bot e os dados da conta dos usuários que você deseja incluir na conversa.

> [!NOTE]
> Nem todos os canais dão suporte a conversas em grupo. Consulte a documentação do canal para determinar se um canal dá suporte a conversas em grupo e identificar o número máximo de participantes permitidos em uma conversa por um canal.

O exemplo a seguir mostra uma solicitação que inicia uma conversa. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations 
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "bot": {
        "id": "12345678",
        "name": "bot's name"
    },
    "isGroup": false,
    "members": [
        {
            "id": "1234abcd",
            "name": "recipient's name"
        }
    ],
    "topicName": "News Alert"
}
```

Se a conversa for estabelecida com os usuários especificados, a resposta conterá uma ID que identifica a conversa. 

```json
{
    "id": "abcd1234"
}
```

O bot pode então usar essa ID da conversa para [enviar uma mensagem](#send-message) para os usuários na conversa.

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral das atividades](bot-framework-rest-connector-activities.md)
- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
[ConversationParameters]: bot-framework-rest-connector-api-reference.md#conversationparameters-object
