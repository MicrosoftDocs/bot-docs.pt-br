---
title: Enviar uma mensagem o bot – Serviço de Bot
description: Descubra como usar a versão 1,1 da API de linha direta para enviar mensagens para bots. Saiba como criar e enviar mensagens e anexos. Consulte as respostas esperadas.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 09b22007fb590f0dc628bb729beaf8acaf32bb52
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89361819"
---
# <a name="send-a-message-to-the-bot"></a>Enviar uma mensagem para o bot

> [!IMPORTANT]
> Este artigo descreve como enviar uma mensagem ao bot usando a API de linha direta 1.1. Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use a [API de Linha Direta 3.0](bot-framework-rest-direct-line-3-0-send-activity.md).

Usando o protocolo 1.1 de linha direta, os clientes podem trocar mensagens com bots. Essas mensagens são convertidas no esquema que o bot suporta (Bot Framework v1 ou Bot Framework v3). Um cliente pode enviar uma mensagem única por solicitação. 

## <a name="send-a-message"></a>Enviar uma mensagem

Para enviar uma mensagem ao bot, o cliente deve criar um objeto [Message](bot-framework-rest-direct-line-1-1-api-reference.md#message-object) para definir a mensagem e, em seguida, emitir uma solicitação `POST` para `https://directline.botframework.com/api/conversations/{conversationId}/messages`, especificando o objeto Message no corpo da solicitação.

Os snippets de código a seguir fornecem um exemplo de enviar mensagem solicitação e resposta.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/api/conversations/abc123/messages
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
[other headers]
```

```json
{
  "text": "hello",
  "from": "user1"
}
```

### <a name="response"></a>Resposta

Quando a mensagem é entregue ao bot, o serviço responde com um código de status HTTP que reflete o código de status do bot. Se o bot gerar um erro, uma resposta HTTP 500 ("Internal Server Error") é retornada ao cliente em resposta à solicitação Send Message. Se o POST for bem-sucedido, o serviço retornará um código de status HTTP 204. Nenhum dado é retornado no corpo da resposta. A mensagem do cliente e quaisquer mensagens do bot podem ser obtidas via [polling](bot-framework-rest-direct-line-1-1-receive-messages.md). 

```http
HTTP/1.1 204 No Content
[other headers]
```

### <a name="total-time-for-the-send-message-requestresponse"></a>Tempo total para o pedido / resposta Enviar mensagem

O tempo total para postar uma mensagem a uma conversa de linha direta é a soma das seguintes opções:

- Tempo em trânsito para a solicitação HTTP para viajar do cliente para o serviço de linha direta
- Tempo de processamento interno no linha direta (normalmente menos de 120 ms)
- Tempo em trânsito do serviço de linha direta para o bot
- Tempo de processamento no bot
- Tempo em trânsito para a resposta HTTP para a viagem de volta ao cliente

## <a name="send-attachments-to-the-bot"></a>Enviar o anexo (s) para o bot

Em algumas situações, um cliente talvez precise enviar anexos para o bot, como imagens ou documentos. Um cliente pode enviar anexos para o bot ou pelo [especificando as URLs](#send-by-url) dos anexos dentro a [mensagem](bot-framework-rest-direct-line-1-1-api-reference.md#message-object) do objeto que ele envia usando `POST /api/conversations/{conversationId}/messages` ou pelo [carregando anexos](#upload-attachments) usando `POST /api/conversations/{conversationId}/upload`.

## <a name="send-attachments-by-url"></a><a id="send-by-url"></a> Enviar anexos por URL

Para enviar um ou mais anexos como parte do objeto [Message](bot-framework-rest-direct-line-1-1-api-reference.md#message-object) usando `POST /api/conversations/{conversationId}/messages`, especifique o (s) URL (s) do (s) anexo (s) no array `images` e / ou `attachments` da mensagem.

## <a name="send-attachments-by-upload"></a><a id="upload-attachments"></a> Enviar anexos por upload

Geralmente, um cliente pode ter imagens ou documento (s) em um dispositivo que deseja enviar para o bot, mas não há URLs correspondem a esses arquivos. Nessa situação, um cliente pode emitir um `POST /api/conversations/{conversationId}/upload` solicitação para enviar anexos para o bot pelo carregamento. O formato e conteúdo da solicitação dependerão se o cliente é [enviar um único anexo](#upload-one-attachment) ou [enviando vários anexos](#upload-multiple-attachments).

### <a name="send-a-single-attachment-by-upload"></a><a id="upload-one-attachment"></a> Enviar um anexo por upload

Para enviar um único anexo por upload, envie esta solicitação: 

```http
POST https://directline.botframework.com/api/conversations/{conversationId}/upload?userId={userId}
Authorization: Bearer SECRET_OR_TOKEN
Content-Type: TYPE_OF_ATTACHMENT
Content-Disposition: ATTACHMENT_INFO
[other headers]

[file content]
```

Nesse URI de solicitação, substitua **{conversationId}** pelo ID da conversa e **{userId}** pelo ID do usuário que está enviando a mensagem. Nos cabeçalhos de solicitação, defina `Content-Type` para especificar o tipo do anexo e defina `Content-Disposition` para especificar o nome do arquivo do anexo.

Os snippets de código a seguir fornecem um exemplo da solicitação de envio (única) de anexo e a resposta.

#### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/api/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: image/jpeg
Content-Disposition: name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]
```

#### <a name="response"></a>Resposta

Se a solicitação for bem-sucedida, uma mensagem é enviada para o bot quando o carregamento for concluído e o serviço retorna um código de status HTTP 204.

```http
HTTP/1.1 204 No Content
[other headers]
```

### <a name="send-multiple-attachments-by-upload"></a><a id="upload-multiple-attachments"></a> Enviar vários anexos por upload

Para enviar vários anexos por upload, `POST`uma solicitação multipartes para o endpoint`/api/conversations/{conversationId}/upload`. Defina as `Content-Type` cabeçalho da solicitação para `multipart/form-data` e incluem o `Content-Type` cabeçalho e `Content-Disposition` cabeçalho para cada parte especificar o tipo e nome do arquivo de cada anexo. No URI de solicitação, defina o `userId` parâmetro para a ID do usuário que está enviando a mensagem. 

Você pode incluir um [mensagem](bot-framework-rest-direct-line-1-1-api-reference.md#message-object) objeto dentro da solicitação com a adição de uma parte que especifica se o `Content-Type` valor de cabeçalho `application/vnd.microsoft.bot.message`. Isso permite que o cliente personalizar a mensagem que contém o anexo (s). Se a solicitação incluir uma mensagem, os anexos especificados por outras partes da carga útil serão adicionados como anexos a essa mensagem antes de serem enviados. 

Os snippets a seguir fornecem um exemplo da solicitação e resposta Enviar (vários) Anexos. Neste exemplo, a solicitação envia uma mensagem que contém algum texto e um único anexo de imagem. Partes adicionais pôde ser adicionados à solicitação para incluir vários anexos nesta mensagem.

#### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/api/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: multipart/form-data; boundary=----DD4E5147-E865-4652-B662-F223701A8A89
[other headers]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: image/jpeg
Content-Disposition: form-data; name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: application/vnd.microsoft.bot.message
[other headers]

{
  "text": "Hey I just IM'd you\n\nand this is crazy\n\nbut here's my webhook\n\nso POST me maybe",
  "from": "user1"
}

----DD4E5147-E865-4652-B662-F223701A8A89
```

#### <a name="response"></a>Resposta

Se a solicitação for bem-sucedida, uma mensagem é enviada para o bot quando o carregamento for concluído e o serviço retorna um código de status HTTP 204.

```http
HTTP/1.1 204 No Content
[other headers]
```

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-1-1-concepts.md)
- [Autenticação](bot-framework-rest-direct-line-1-1-authentication.md)
- [Iniciar uma conversa](bot-framework-rest-direct-line-1-1-start-conversation.md)
- [Receber mensagens do bot](bot-framework-rest-direct-line-1-1-receive-messages.md)