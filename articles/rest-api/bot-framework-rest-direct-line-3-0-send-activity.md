---
title: Enviar uma atividade o bot | Microsoft Docs
description: Saiba como enviar uma atividade para o bot usando a API de linha direta v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 0b9e2c406ee20535dfb065639beda8b66d0a2996
ms.sourcegitcommit: c200cc2db62dbb46c2a089fb76017cc55bdf26b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70037352"
---
# <a name="send-an-activity-to-the-bot"></a>Enviar uma atividade para o bot

Usando o protocolo 3.0 de linha direta, bots e os clientes podem trocar vários tipos diferentes de [atividades](bot-framework-rest-connector-activities.md), incluindo **mensagem** atividades, **digitando** atividades, e atividades personalizados que dá suporte ao bot. Um cliente pode enviar uma única atividade por solicitação. 

## <a name="send-an-activity"></a>Enviar uma atividade

Para enviar uma atividade para o bot, o cliente deve criar uma [atividade][] objeto para definir a atividade e, em seguida, emitir um `POST` solicitar `https://directline.botframework.com/v3/directline/conversations/{conversationId}/activities`, especificando o objeto de atividade no corpo da solicitação.

Os snippets de código a seguir fornecem um exemplo de como a atividade enviar solicitação e resposta.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: application/json
[other headers]
```

```json
{
    "type": "message",
    "from": {
        "id": "user1"
    },
    "text": "hello"
}
```

### <a name="response"></a>Response

Quando a atividade é entregue para o bot, o serviço responde com um código de status HTTP que reflete o código de status do bot. Se o bot gera um erro, uma resposta de HTTP 502 ("Gateway incorreto") é retornada ao cliente em resposta à sua solicitação de atividade de enviar.

> [!NOTE]
> Isso pode ser causado pelo fato de que um token correto não foi usado. Somente o token recebido em *iniciar conversa* pode ser usado para enviar uma atividade.

Se o POST for bem-sucedido, a resposta contém uma carga JSON que especifica a ID da atividade que foi enviada para o bot.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
    "id": "0001"
}
```

### <a name="total-time-for-the-send-activity-requestresponse"></a>Tempo total para a atividade enviar solicitação/resposta

O tempo total para postar uma mensagem a uma conversa de linha direta é a soma das seguintes opções:

- Tempo em trânsito para a solicitação HTTP para viajar do cliente para o serviço de linha direta
- Tempo de processamento interno no linha direta (normalmente menos de 120 ms)
- Tempo em trânsito do serviço de linha direta para o bot
- Tempo de processamento no bot
- Tempo em trânsito para a resposta HTTP para a viagem de volta ao cliente

## <a name="send-attachments-to-the-bot"></a>Enviar o anexo (s) para o bot

Em algumas situações, um cliente talvez precise enviar anexos para o bot, como imagens ou documentos. Um cliente pode enviar anexos para o bot ou pelo [especificando as URLs](#send-by-url) dos anexos dentro a [atividade][] do objeto que ele envia usando `POST /v3/directline/conversations/{conversationId}/activities` ou pelo [carregando anexos](#upload-attachments) usando `POST /v3/directline/conversations/{conversationId}/upload`.

## <a id="send-by-url"></a> Enviar anexos por URL

Para enviar um ou mais anexos como parte do [atividade][] objeto usando `POST /v3/directline/conversations/{conversationId}/activities`, basta incluir uma ou mais [anexo][] objetos dentro do objeto de atividade e conjunto o `contentUrl` propriedade de cada objeto de anexo para especificar HTTP, HTTPS, ou `data` URI do anexo.

## <a id="upload-attachments"></a> Enviar anexos por upload

Geralmente, um cliente pode ter imagens ou documento (s) em um dispositivo que deseja enviar para o bot, mas não há URLs correspondem a esses arquivos. Nessa situação, um cliente pode emitir um `POST /v3/directline/conversations/{conversationId}/upload` solicitação para enviar anexos para o bot pelo carregamento. O formato e conteúdo da solicitação dependerão se o cliente é [enviar um único anexo](#upload-one-attachment) ou [enviando vários anexos](#upload-multiple-attachments).

### <a id="upload-one-attachment"></a> Enviar um anexo por upload

Para enviar um único anexo por upload, envie esta solicitação: 

```http
POST https://directline.botframework.com/v3/directline/conversations/{conversationId}/upload?userId={userId}
Authorization: Bearer SECRET_OR_TOKEN
Content-Type: TYPE_OF_ATTACHMENT
Content-Disposition: ATTACHMENT_INFO
[other headers]

[file content]
```

Nesse URI de solicitação, substitua **{conversationId}** pelo ID da conversa e **{userId}** pelo ID do usuário que está enviando a mensagem. O `userId` parâmetro é obrigatório. Nos cabeçalhos de solicitação, defina `Content-Type` para especificar o tipo do anexo e defina `Content-Disposition` para especificar o nome do arquivo do anexo.

Os snippets de código a seguir fornecem um exemplo da solicitação de envio (única) de anexo e a resposta.

#### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: image/jpeg
Content-Disposition: name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]
```

#### <a name="response"></a>Response

Se a solicitação for bem-sucedida, uma **mensagem** Activity será enviada ao bot quando o upload for concluído e a resposta recebida pelo cliente conterá o ID da Activity que foi enviada.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "id": "0003"
}
```

### <a id="upload-multiple-attachments"></a> Enviar vários anexos por upload

Para enviar vários anexos por upload, `POST`uma solicitação multipartes para o endpoint`/v3/directline/conversations/{conversationId}/upload`. Defina as `Content-Type` cabeçalho da solicitação para `multipart/form-data` e incluem o `Content-Type` cabeçalho e `Content-Disposition` cabeçalho para cada parte especificar o tipo e nome do arquivo de cada anexo. No URI de solicitação, defina o `userId` parâmetro para a ID do usuário que está enviando a mensagem. 

Você pode incluir um objeto `Activity` dentro da solicitação adicionando um bloco que especifica o valor `application/vnd.microsoft.activity` do cabeçalho `Content-Type`. Se a solicitação inclui uma atividade, os anexos que são especificados por outras partes da carga são adicionados como anexos de atividade antes de serem enviado. Se a solicitação não incluir uma Atividade, uma Atividade vazia será criada para servir como o contêiner no qual os anexos especificados são enviados.

Os snippets a seguir fornecem um exemplo da solicitação e resposta Enviar (vários) Anexos. Neste exemplo, a solicitação envia uma mensagem que contém algum texto e um único anexo de imagem. Partes adicionais pôde ser adicionados à solicitação para incluir vários anexos nesta mensagem.

#### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: multipart/form-data; boundary=----DD4E5147-E865-4652-B662-F223701A8A89
[other headers]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: image/jpeg
Content-Disposition: form-data; name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: application/vnd.microsoft.activity
[other headers]

{
  "type": "message",
  "from": {
    "id": "user1"
  },
  "text": "Hey I just IM'd you\n\nand this is crazy\n\nbut here's my webhook\n\nso POST me maybe"
}

----DD4E5147-E865-4652-B662-F223701A8A89
```

#### <a name="response"></a>Response

Se a solicitação for bem-sucedida, uma mensagem Activity será enviada ao bot quando o upload for concluído e a resposta recebida pelo cliente conterá o ID da Activity que foi enviada.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
    "id": "0004"
}
```

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md)
- [Iniciar uma conversa](bot-framework-rest-direct-line-3-0-start-conversation.md)
- [Reconectar-se a uma conversa](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)
- [Atividades de recebimento do bot](bot-framework-rest-direct-line-3-0-receive-activities.md)
- [Terminar uma conversa](bot-framework-rest-direct-line-3-0-end-conversation.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
[Anexo]: bot-framework-rest-connector-api-reference.md#attachment-object
