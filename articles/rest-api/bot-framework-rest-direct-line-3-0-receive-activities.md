---
title: Receber atividades do bot | Microsoft Docs
description: Saiba como receber atividades do bot usando a API da Linha Direta v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: dd5e81ba3feaba09e60011c138dcbe1537144b5a
ms.sourcegitcommit: 721bb09f10524b0cb3961d7131966f57501734b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59541002"
---
# <a name="receive-activities-from-the-bot"></a>Receber atividades do bot

Usando o protocolo da Linha Direta 3.0, os clientes podem receber atividades pelo stream `WebSocket` ou recuperar as atividades emitindo solicitações `HTTP GET`. 

## <a name="websocket-vs-http-get"></a>WebSocket vs HTTP GET

Um WebSocket de streaming envia mensagens aos clientes com eficiência enquanto a interface GET permite que os clientes solicitem mensagens explicitamente. Embora o mecanismo do WebSocket seja geralmente preferido devido à sua eficiência, o mecanismo de GET pode ser útil para clientes que não podem usar WebSockets. 

Nem todos os [tipos de atividade](bot-framework-rest-connector-activities.md) estão disponíveis através do WebSocket e HTTP GET. A tabela a seguir descreve a disponibilidade de vários tipos de atividades para clientes que usam o protocolo da Linha Direta.

| Tipo de atividade | Disponibilidade | 
|----|----|
| Message | HTTP GET e WebSocket |
| digitação | Apenas WebSocket |
| conversationUpdate | Não enviado/recebido pelo cliente |
| contactRelationUpdate | Sem suporte na Linha Direta |
| endOfConversation | HTTP GET e WebSocket |
| todos os demais tipos de atividade | HTTP GET e WebSocket |

## <a id="connect-via-websocket"></a> Receber atividades por meio do fluxo do WebSocket

Quando um cliente envia uma solicitação [Iniciar Conversa](bot-framework-rest-direct-line-3-0-start-conversation.md) para abrir uma conversa com um bot, a resposta do serviço inclui uma propriedade `streamUrl` que o cliente pode subsequentemente usar para se conectar pelo WebSocket. A URL de fluxo vem pré-autorizada e, portanto, a solicitação do cliente para se conectar pelo WebSocket não requer um cabeçalho `Authorization`.

O exemplo a seguir mostra uma solicitação que usa um `streamUrl` para se conectar via WebSocket.

```http
-- connect to wss://directline.botframework.com --
GET /v3/directline/conversations/abc123/stream?t=RCurR_XV9ZA.cwA..."
Upgrade: websocket
Connection: upgrade
[other headers]
```

O serviço responde com o código de status HTTP 101 ("Protocolos de troca").

```http
HTTP/1.1 101 Switching Protocols
[other headers]
```

### <a name="receive-messages"></a>Receber mensagens

Depois da conexão pelo WebSocket, os clientes podem receber esses tipos de mensagens a partir do serviço da Linha Direta:

- Uma mensagem que contém um [ActivitySet](bot-framework-rest-direct-line-3-0-api-reference.md#activityset-object) que inclui uma ou mais atividades e uma marca d'água (descritos abaixo).
- Uma mensagem vazia, que o serviço da Linha Direta usa para garantir que a conexão ainda é válida.
- Outros tipos serão definidos posteriormente. Esses tipos são identificados pelas propriedades na raiz JSON.

Um `ActivitySet` contém as mensagens enviadas pelo bot e por todos os usuários na conversa. O exemplo a seguir mostra um `ActivitySet` que contém uma única mensagem.

```json
{
    "activities": [
        {
            "type": "message",
            "channelId": "directline",
            "conversation": {
                "id": "abc123"
            },
            "id": "abc123|0000",
            "from": {
                "id": "user1"
            },
            "text": "hello"
        }
    ],
    "watermark": "0000a-42"
}
```

### <a name="process-messages"></a>Processar mensagens

Um cliente deve controlar o valor `watermark` que recebe em cada [ActivitySet](bot-framework-rest-direct-line-3-0-api-reference.md#activityset-object) para poder usar a marca d'água e garantir que nenhuma mensagem seja esquecida em caso de perda da conexão e seja preciso [reconectar-se à conversa](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md). Se o cliente receber um `ActivitySet` em que a propriedade `watermark` seja `null` ou estiver ausente, ele deverá ignorar esse valor e não substituir a marca d'água anteriormente recebida.

Um cliente deve ignorar mensagens vazias recebidas do serviço da Linha Direta.

Um cliente pode enviar mensagens vazias para o serviço da Linha Direta para verificar a conectividade. O serviço da Linha Direta vai ignorar mensagens vazias recebidas do cliente.

O serviço da Linha Direta pode forçosamente fechar a conexão do WebSocket em determinadas condições. Se o cliente não tiver recebido uma atividade `endOfConversation`, ele poderá [gerar uma nova URL de fluxo do WebSocket](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) que poderá ser usada para reconectar-se à conversa. 

O fluxo do WebSocket contém atualizações dinâmicas e mensagens muito recentes (uma vez que a chamada para se conectar por meio do WebSocket foi emitida), mas não inclui mensagens que foram enviadas antes da mais recente `POST` para `/v3/directline/conversations/{id}`. Para recuperar as mensagens que foram enviadas anteriormente na conversa, use `HTTP GET` conforme descrito abaixo.

## <a id="http-get"></a> Recuperar atividades com o HTTP GET

Os clientes que não podem usar WebSockets podem recuperar as atividades usando `HTTP GET`.

Para recuperar mensagens de uma conversa específica, emita uma solicitação `GET` para o ponto de extremidade `/v3/directline/conversations/{conversationId}/activities`, especificando opcionalmente o parâmetro `watermark` para indicar a mensagem mais recente vista pelo cliente. 

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Obter Atividades de Conversa. A resposta de Obter Atividades de Conversa contém `watermark` como uma propriedade de [ActivitySet](bot-framework-rest-direct-line-3-0-api-reference.md#activityset-object). Os clientes devem percorrer as atividades disponíveis aprimorando o valor `watermark` até que nenhuma atividade retorne.

### <a name="request"></a>Solicitação

```http
GET https://directline.botframework.com/v3/directline/conversations/abc123/activities?watermark=0001a-94
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>Response

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
    "activities": [
        {
            "type": "message",
            "channelId": "directline",
            "conversation": {
                "id": "abc123"
            },
            "id": "abc123|0000",
            "from": {
                "id": "user1"
            },
            "text": "hello"
        }, 
        {
            "type": "message",
            "channelId": "directline",
            "conversation": {
                "id": "abc123"
            },
            "id": "abc123|0001",
            "from": {
                "id": "bot1"
            },
            "text": "Nice to see you, user1!"
        }
    ],
    "watermark": "0001a-95"
}
```

## <a name="timing-considerations"></a>Considerações sobre timing

A maioria dos clientes deseja manter um histórico de mensagens completo. Mesmo que a Linha Direta seja um protocolo de várias partes com potenciais intervalos de medição de tempo, o protocolo e o serviço foram projetados para facilitar a criação de um cliente confiável.

- A propriedade `watermark` que é enviada no fluxo do WebSocket e na resposta de Obter Atividades de Conversa é confiável. Um cliente não perderá qualquer mensagem, desde que repita a marca d'água textual.

- Quando um cliente inicia uma conversa e conecta-se ao fluxo do WebSocket, todas as atividades que são enviadas após o POST, mas antes do soquete ser aberto são reproduzidas antes das novas atividades.

- Quando um cliente emite uma solicitação Obter Atividades de Conversa (para atualizar o histórico) enquanto está conectado ao fluxo do WebSocket, as atividades acabar sendo duplicadas em ambos canais. Os clientes devem manter o controle de todas as IDs de atividade conhecidas para conseguirem rejeitar atividades duplicadas, caso elas ocorram.

Os clientes que votam usando `HTTP GET` devem escolher um intervalo de sondagem que corresponda ao seu uso pretendido.

- Geralmente, os aplicativos de serviço a serviço usam um intervalo de sondagem de 5 ou 10 segundos.

- Os aplicativos voltados para o cliente geralmente usam um intervalo de sondagem de 1 s e emitem uma única solicitação adicional logo após cada mensagem que o cliente envia (para recuperar rapidamente a resposta de um bot). Esse atraso pode ser de até 300 ms, mas deve ser ajustado com base na velocidade e tempo de trânsito do bot. A sondagem não deve ter uma frequência maior do que uma vez por segundo para qualquer período de tempo prolongado.

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md)
- [Iniciar uma conversa](bot-framework-rest-direct-line-3-0-start-conversation.md)
- [Reconectar-se a uma conversa](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)
- [Enviar uma atividade para o bot](bot-framework-rest-direct-line-3-0-send-activity.md)
- [Terminar uma conversa](bot-framework-rest-direct-line-3-0-end-conversation.md)
