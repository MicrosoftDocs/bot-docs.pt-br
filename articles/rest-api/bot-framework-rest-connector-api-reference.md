---
title: Referência de API | Microsoft Docs
description: Saiba mais sobre cabeçalhos, operações, objetos e erros no serviço do Bot Connector e serviço de Estado do Bot.
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/02/2019
ms.openlocfilehash: 5a520c2fc5b9e94976e9a2618286aa184045bdeb
ms.sourcegitcommit: 6a83b2c8ab2902121e8ee9531a7aa2d85b827396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68866637"
---
# <a name="api-reference"></a>Referência de API

> [!NOTE]
> A API REST não é equivalente ao SDK. A API REST é fornecida para permitir a comunicação REST padrão, no entanto, o método preferido de interação com o Framework Bot é o SDK. 

Dentro do Bot Framework, o serviço Bot Connector permite que seu bot troque mensagens com usuários em canais que são configurados no Portal do Bot Framework. O serviço usa REST padrão do setor e JSON por HTTPS.

## <a name="base-uri"></a>URI de base

Quando um usuário envia uma mensagem para o bot, a solicitação recebida contém um objeto [Atividade](#bot-framework-activity-schema) com uma propriedade `serviceUrl` que especifica o ponto de extremidade para o qual o bot deve enviar a resposta. Para acessar o serviço do Bot Connector, use o valor `serviceUrl` como o URI de base para solicitações de API. 

Por exemplo, suponha que o bot receba a seguinte atividade quando o usuário envia uma mensagem ao bot.

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

A propriedade `serviceUrl` na mensagem do usuário indica que o bot deve enviar a resposta ao ponto de extremidade `https://smba.trafficmanager.net/apis`, que será o URI de base para quaisquer solicitações subsequentes que o bot emite no contexto dessa conversa. Se o bot precisar enviar uma mensagem proativa ao usuário, salve o valor de `serviceUrl`.

O exemplo a seguir mostra a solicitação que o bot emite para responder à mensagem do usuário. 

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
    "text": "I have several times available on Saturday!",
    "replyToId": "bf3cc9a2f5de..."
}
```

## <a name="headers"></a>Cabeçalhos

### <a name="request-headers"></a>Cabeçalhos da solicitação

Além dos cabeçalhos de solicitação HTTP padrão, cada solicitação de API que você emitir deverá incluir um cabeçalho `Authorization` que especifique um token de acesso para autenticar o bot. Especifique o cabeçalho `Authorization` usando este formato:

```http
Authorization: Bearer ACCESS_TOKEN
```

Para obter detalhes sobre como obter um token de acesso para o bot, consulte [Autenticar solicitações do bot para o serviço do Bot Connector](bot-framework-rest-connector-authentication.md#bot-to-connector).

### <a name="response-headers"></a>Cabeçalhos de resposta

Além dos cabeçalhos de resposta HTTP padrão, cada resposta conterá um cabeçalho `X-Correlating-OperationId`. O valor deste cabeçalho é uma ID que corresponde à entrada de log do Bot Framework que contém detalhes sobre a solicitação. Sempre que você receber uma resposta de erro, deverá capturar o valor desse cabeçalho. Se não for possível resolver o problema de maneira independente, inclua esse valor nas informações fornecidas à Equipe de suporte ao relatar o problema.

## <a name="http-status-codes"></a>Códigos de status HTTP

O <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">Código de status HTTP</a> retornado com cada resposta indica o resultado da solicitação correspondente. 

| Código de status HTTP | Significado |
|----|----|
| 200 | Solicitação com êxito. |
| 201 | Solicitação com êxito. |
| 202 | A solicitação foi aceita para processamento. |
| 204 | A solicitação foi processada com êxito, mas nenhum conteúdo foi retornado. |
| 400 | A solicitação foi malformada ou está incorreta. |
| 401 | O bot não está autorizado a fazer a solicitação. |
| 403 | O bot não tem permissão para executar a operação solicitada. |
| 404 | O recurso solicitado não foi localizado. |
| 500 | Ocorreu um erro interno no servidor. |
| 503 | O serviço está indisponível. |

### <a name="errors"></a>Errors

Qualquer resposta que especifique um código de status HTTP no intervalo 4xx ou 5xx incluirá um objeto [ErrorResponse](#bot-framework-activity-schema) no corpo da resposta que fornece informações sobre o erro. Se você receber uma resposta de erro no intervalo 4xx, inspecione o objeto **ErrorResponse** para identificar a causa do erro e resolver o problema antes de reenviar a solicitação.

## <a name="conversation-operations"></a>Operações de conversa 
Use essas operações para criar conversas, enviar mensagens (atividades) e gerenciar o conteúdo das conversas.

| Operação | DESCRIÇÃO |
|----|----|
| [Criar conversa](#create-conversation) | Cria uma nova conversa. |
| [Enviar para conversa](#send-to-conversation) | Envia uma atividade (mensagem) para o final da conversa especificada. |
| [Responder a atividade](#reply-to-activity) | Envia uma atividade (mensagem) para a conversa especificada, como uma resposta à atividade especificada. |
| [Obter conversas](#get-conversations) | Obtém uma lista das conversas de que o bot participou. |
| [Obter membros da conversa](#get-conversation-members) | Obtém os membros da conversa especificada. |
| [Obter membros paginados da conversa](#get-conversation-paged-members) | Obtém os membros da conversa especificada, uma página de cada vez. |
| [Obter membros da atividade](#get-activity-members) | Obtém os membros da atividade especificada na conversa especificada. |
| [Atualizar atividade](#update-activity) | Atualiza uma atividade existente. |
| [Excluir atividade](#delete-activity) | Exclui uma atividade existente. |
| [Excluir membro da conversa](#delete-conversation-member) | Remove um membro de uma conversa. |
| [Enviar histórico da conversa](#send-conversation-history) | Carrega uma transcrição de atividades anteriores para a conversa. |
| [Carregar anexo ao canal](#upload-attachment-to-channel) | Carrega um anexo diretamente no armazenamento de blobs de um canal. |

### <a name="create-conversation"></a>Criar conversa
Cria uma nova conversa.
```http 
POST /v3/conversations
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto `ConversationParameters` |
| **Retorna** | Um objeto `ConversationResourceResponse` |

### <a name="send-to-conversation"></a>Enviar para conversa
Envia uma atividade (mensagem) para a conversa especificada. A atividade será anexada ao final da conversa, de acordo com o carimbo de data/hora ou semântica do canal. Para responder a uma mensagem específica na conversa, use [Responder a atividade](#reply-to-activity).
```http
POST /v3/conversations/{conversationId}/activities
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#bot-framework-activity-schema) |
| **Retorna** | Um objeto [Identificação](#bot-framework-activity-schema) | 

### <a name="reply-to-activity"></a>Responder a atividade
Envia uma atividade (mensagem) para a conversa especificada, como uma resposta à atividade especificada. A atividade será adicionada como resposta a outra atividade, se houver suporte para isso no canal. Se o canal não der suporte a respostas aninhadas, essa operação se comportará como [Enviar para conversa](#send-to-conversation).
```http
POST /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#bot-framework-activity-schema) |
| **Retorna** | Um objeto [Identificação](#bot-framework-activity-schema) | 

### <a name="get-conversations"></a>Obter conversas
Obtém uma lista das conversas de que o bot participou.
```http
GET /v3/conversations?continuationToken={continuationToken}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [ConversationsResult](#bot-framework-activity-schema) | 

### <a name="get-conversation-members"></a>Obter membros da conversa
Obtém os membros da conversa especificada.
```http
GET /v3/conversations/{conversationId}/members
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma matriz de objetos [ChannelAccount](#bot-framework-activity-schema) | 

### <a name="get-conversation-paged-members"></a>Obter membros paginados da conversa
Obtém os membros da conversa especificada, uma página de cada vez.
```http
GET /v3/conversations/{conversationId}/pagedmembers?pageSize={pageSize}&continuationToken={continuationToken}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma matriz de objetos [ChannelAccount](#bot-framework-activity-schema) e um token de continuação que pode ser usado para obter mais valores |

### <a name="get-activity-members"></a>Obter membros da atividade
Obtém os membros da atividade especificada na conversa especificada.
```http
GET /v3/conversations/{conversationId}/activities/{activityId}/members
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma matriz de objetos [ChannelAccount](#bot-framework-activity-schema) | 

### <a name="update-activity"></a>Atualizar atividade
Alguns canais permitem editar uma atividade existente para refletir o novo estado de uma conversa de bot. Por exemplo, será possível remover botões de uma mensagem na conversa depois que o usuário clicar em um dos botões. Se tiver êxito, essa operação atualizará a atividade especificada dentro da conversa especificada. 
```http
PUT /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#bot-framework-activity-schema) |
| **Retorna** | Um objeto [Identificação](#bot-framework-activity-schema) | 

### <a name="delete-activity"></a>Excluir atividade
Alguns canais permitem excluir uma atividade existente. Se tiver êxito, essa operação removerá a atividade especificada da conversa especificada.
```http
DELETE /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um código de status HTTP que indica o resultado da operação. Nada é especificado no corpo da resposta. | 

### <a name="delete-conversation-member"></a>Excluir membro da conversa
Remove um membro de uma conversa. Se esse membro for o último da conversa, a conversa também será excluída.
```http
DELETE /v3/conversations/{conversationId}/members/{memberId}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um código de status HTTP que indica o resultado da operação. Nada é especificado no corpo da resposta. | 

### <a name="send-conversation-history"></a>Enviar histórico da conversa
Carrega uma transcrição de atividades anteriores para a conversa para que o cliente possa renderizá-las.
```http
POST /v3/conversations/{conversationId}/activities/history
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Transcript](#bot-framework-activity-schema). |
| **Retorna** | Um objeto [ResourceResponse](#bot-framework-activity-schema). | 

### <a name="upload-attachment-to-channel"></a>Carregar anexo ao canal
Carrega um anexo para a conversa especificada diretamente no armazenamento de blobs de um canal. Isso permite que você armazene dados em um armazenamento compatível.
```http 
POST /v3/conversations/{conversationId}/attachments
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [AttachmentUpload](#bot-framework-activity-schema). |
| **Retorna** | Um objeto [ResourceResponse](#bot-framework-activity-schema). A propriedade **id** especifica a ID do anexo que pode ser usado com a operação [Obter Informações dos Anexos](#get-attachment-info) e a operação [Obter Anexo](#get-attachment). | 

## <a name="attachment-operations"></a>Operações de Anexo 
Use essas operações para recuperar informações sobre um anexo, bem como os dados binários para o próprio arquivo.

| Operação | DESCRIÇÃO |
|----|----|
| [Obter Informações do Anexo](#get-attachment-info) | Obtém informações sobre o anexo especificado, incluindo o nome do arquivo, tipo de arquivo e os modos de exibição disponíveis (por exemplo, original ou miniatura). |
| [Obter anexo](#get-attachment) | Obtém a exibição especificada do anexo especificado como conteúdo binário. | 

### <a name="get-attachment-info"></a>Obter informações do anexo 
Obtém informações sobre o anexo especificado, incluindo o nome do arquivo, tipo e os modos de exibição disponíveis (por exemplo, original ou miniatura).
```http
GET /v3/attachments/{attachmentId}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [AttachmentInfo](#bot-framework-activity-schema) | 

### <a name="get-attachment"></a>Obter anexo
Obtém a exibição especificada do anexo especificado como conteúdo binário.
```http
GET /v3/attachments/{attachmentId}/views/{viewId}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Conteúdo binário que representa a exibição especificada do anexo especificado | 

## <a name="state-operations"></a>Operações de estado
O serviço de Estado do Microsoft Bot Framework foi desativado em 30 de março de 2018. Anteriormente, bots criados no Serviço de Bot do Azure ou no SDK do Bot Builder tinham uma conexão padrão com esse serviço hospedado pela Microsoft para armazenar dados de estado do bot. Os bots deverão ser atualizados para usar seu próprio armazenamento de estado.

| Operação | DESCRIÇÃO |
|----|----|
| `Set User Data` | Armazena dados de estado para um usuário específico em um canal. |
| `Set Conversation Data` | Armazena dados de estado para uma conversa específica em um canal. |
| `Set Private Conversation Data` | Armazena dados de estado para um usuário específico no contexto de uma conversa específica em um canal. |
| `Get User Data` | Recupera dados de estado que foram armazenados anteriormente para um usuário específico em todas as conversas em um canal. |
| `Get Conversation Data` | Recupera dados de estado que foram armazenados anteriormente para uma conversa específica em um canal. |
| `Get Private Conversation Data` | Recupera dados de estado que foram armazenados anteriormente para um usuário específico no contexto de uma conversa específica em um canal. |
| `Delete State For User` | Exclui os dados de estado que foram armazenados para um usuário. |

## <a name="bot-framework-activity-schema"></a>Esquema de Atividade do Bot Framework

Consulte o [Esquema de Atividade do Bot Framework](https://aka.ms/botSpecs-activitySchema) para ver os objetos e as propriedades que o bot pode usar para se comunicar com um usuário.
