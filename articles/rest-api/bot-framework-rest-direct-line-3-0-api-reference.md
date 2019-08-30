---
title: Referência de API – API de Linha Direta 3.0 | Microsoft Docs
description: Saiba mais sobre cabeçalhos, códigos de status HTTP, esquema, operações e objetos na API de Linha Direta 3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 25f6e30898101b87289af775e8386f941aa747a9
ms.sourcegitcommit: c200cc2db62dbb46c2a089fb76017cc55bdf26b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70037377"
---
# <a name="api-reference---direct-line-api-30"></a>Referência de API – API de Linha Direta 3.0

Você pode habilitar seu aplicativo cliente para se comunicar com seu bot usando a API de Linha Direta 3.0. A API de Linha Direta 3.0 usa o padrão do setor REST e JSON sobre HTTPS.

## <a name="base-uri"></a>URI de base

Para acessar a API de Linha Direta 3.0, use esta URI de base para todas as solicitações de API:

`https://directline.botframework.com`

## <a name="headers"></a>Cabeçalhos

Além dos cabeçalhos de solicitação HTTP padrão, uma solicitação de API de Linha Direta deve incluir um cabeçalho `Authorization`, que especifica um segredo ou token para autenticação do cliente que está emitindo a solicitação. Especifique o cabeçalho `Authorization` usando este formato:

```http
Authorization: Bearer SECRET_OR_TOKEN
```

Saiba mais sobre como obter um segredo ou token que pode ser usado por seu cliente para autenticar suas solicitações de API de Linha Direta em [Autenticação](bot-framework-rest-direct-line-3-0-authentication.md).

## <a name="http-status-codes"></a>Códigos de status HTTP

O <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">Código de status HTTP</a> retornado com cada resposta indica o resultado da solicitação correspondente. 

| Código de status HTTP | Significado |
|----|----|
| 200 | Solicitação com êxito. |
| 201 | Solicitação com êxito. |
| 202 | A solicitação foi aceita para processamento. |
| 204 | A solicitação foi processada com êxito, mas nenhum conteúdo foi retornado. |
| 400 | A solicitação foi malformada ou está incorreta. |
| 401 | O cliente não tem autorização para fazer a solicitação. Geralmente, esse código de status ocorre porque o cabeçalho `Authorization` está ausente ou malformado. |
| 403 | O cliente não tem autorização para executar a operação solicitada. Se a solicitação especificou um token anteriormente válido, mas já expirado, a propriedade `code` do [Erro][] retornado dentro do objeto [ErrorResponse][] será definida como `TokenExpired`. |
| 404 | O recurso solicitado não foi localizado. Normalmente, esse código de status indica um URI de solicitação inválido. |
| 500 | Ocorreu um erro de servidor interno no serviço de Linha Direta. |
| 502 | O bot está indisponível ou retornou um erro. **Esse é um código de erro comum.** |

> [!NOTE]
> O código de status HTTP 101 é usado no caminho de conexão de WebSocket, embora isso provavelmente seja tratado pelo seu cliente de WebSocket.

### <a name="errors"></a>Errors

Qualquer resposta que especifique um código de status HTTP no intervalo 4xx ou 5xx incluirá um objeto [ErrorResponse][] no corpo da resposta que fornece informações sobre o erro. Se você receber uma resposta de erro no intervalo 4xx, inspecione o objeto **ErrorResponse** para identificar a causa do erro e resolver o problema antes de reenviar a solicitação.

> [!NOTE]
> Códigos de status HTTP e os valores especificados na propriedade `code` dentro do objeto **ErrorResponse** estão estáveis. Os valores especificados na propriedade `message` dentro do objeto **ErrorResponse** podem mudar ao longo do tempo.

Os snippets de código a seguir mostram um exemplo de solicitação e a resposta de erro resultante.

#### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
[detail omitted]
```

#### <a name="response"></a>Response
```http
HTTP/1.1 502 Bad Gateway
[other headers]
```
```json
{
    "error": {
        "code": "BotRejectedActivity",
        "message": "Failed to send activity: bot returned an error"
    }
}
```

## <a name="token-operations"></a>Operações de token 
Use essas operações para criar ou atualizar um token que um cliente pode usar para acessar uma conversa individual.

| Operação | DESCRIÇÃO |
|----|----|
| [Gerar Token](#generate-token) | Gere um token para uma nova conversa. | 
| [Atualizar Token](#refresh-token) | Atualize um token. | 

### <a name="generate-token"></a>Gerar Token
Gera um token válido para uma conversa. 
```http 
POST /v3/directline/tokens/generate
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [Conversation](#conversation-object) | 

### <a name="refresh-token"></a>Atualizar Token
Atualiza o token. 
```http 
POST /v3/directline/tokens/refresh
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [Conversation](#conversation-object) | 

## <a name="conversation-operations"></a>Operações de conversa 
Use essas operações para abrir uma conversa com seu bot e trocar atividades entre o cliente e o bot.

| Operação | DESCRIÇÃO |
|----|----|
| [Iniciar Conversa](#start-conversation) | Abre uma nova conversa com o bot. | 
| [Obter Informações da Conversa](#get-conversation-information) | Obtém informações sobre uma conversa existente. Essa operação gera uma nova URL de fluxo de WebSocket que um cliente pode usar para se [reconectar](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) a uma conversa. |
| [Obter Atividades](#get-activities) | Recupera atividades do bot. |
| [Enviar uma Atividade](#send-an-activity) | Envia uma atividade para o bot. | 
| [Carregar e Enviar Arquivo(s)](#upload-send-files) | Carrega e envia arquivo(s) como anexo(s). |

### <a name="start-conversation"></a>Iniciar Conversa
Abre uma nova conversa com o bot. 
```http 
POST /v3/directline/conversations
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [Conversation](#conversation-object) | 

### <a name="get-conversation-information"></a>Obter Informações da Conversa
Obtém informações sobre uma conversa existente e também gera uma nova URL de fluxo de WebSocket que um cliente pode usar para se [reconectar](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) a uma conversa. Como opção, você pode fornecer o parâmetro `watermark` no URI da solicitação para indicar a mensagem mais recente vista pelo cliente.
```http 
GET /v3/directline/conversations/{conversationId}?watermark={watermark_value}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [Conversation](#conversation-object) | 

### <a name="get-activities"></a>Obter Atividades
Recupera atividades do bot da conversa especificada. Como opção, você pode fornecer o parâmetro `watermark` no URI da solicitação para indicar a mensagem mais recente vista pelo cliente. 

```http 
GET /v3/directline/conversations/{conversationId}/activities?watermark={watermark_value}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [ActivitySet](#activityset-object). A resposta contém `watermark` como uma propriedade do objeto `ActivitySet`. Os clientes devem percorrer as atividades disponíveis aprimorando o valor `watermark`, até que nenhuma atividade retorne. | 

### <a name="send-an-activity"></a>Enviar uma Atividade
Envia uma atividade para o bot. 
```http 
POST /v3/directline/conversations/{conversationId}/activities
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade][] |
| **Retorna** | Um [ResourceResponse][] que contém uma propriedade `id` que especifica a ID da Atividade que foi enviada ao bot. | 

### <a id="upload-send-files"></a>Carregar e Enviar Arquivo(s)
Carrega e envia arquivo(s) como anexo(s). Defina o parâmetro `userId` no URI da solicitação para especificar a ID do usuário que está enviando os anexos.
```http 
POST /v3/directline/conversations/{conversationId}/upload?userId={userId}
```

| | |
|----|----|
| **Corpo da solicitação** | Para um único anexo, preencha o corpo da solicitação com o conteúdo do arquivo. Para vários anexos, crie um corpo de solicitação com várias partes, com uma parte para cada anexo, e também (opcionalmente) uma parte para o objeto [Atividade][] que deve servir como contêiner para os anexos especificados. Saiba mais em [Enviar uma atividade para o bot](bot-framework-rest-direct-line-3-0-send-activity.md). |
| **Retorna** | Um [ResourceResponse][] que contém uma propriedade `id` que especifica a ID da Atividade que foi enviada ao bot. | 

> [!NOTE]
> Os arquivos carregados são excluídos após 24 horas.

## <a name="schema"></a>Esquema

O esquema do Direct Line 3.0 inclui todos os objetos definidos pelo [Esquema de Atividade do Bot Framework](https://aka.ms/botSpecs-activitySchema), bem como o objeto `ActivitySet` e o objeto `Conversation`.

### <a name="activityset-object"></a>Objeto ActivitySet 
Define um conjunto de atividades.<br/><br/>

| Propriedade | Type | DESCRIÇÃO |
|----|----|----|
| **activities** | [Atividade][][] | Matriz de objetos **Atividade**. |
| **watermark** | string | Marca-d'água máxima das atividades dentro do conjunto. Um cliente pode usar o valor `watermark` para indicar a mensagem mais recente vista, ao [recuperar as atividades do bot](bot-framework-rest-direct-line-3-0-receive-activities.md#http-get) ou ao [gerar uma nova URL de fluxo de WebSocket](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md). |

### <a name="conversation-object"></a>Objeto Conversa
Define uma conversa de Linha Direta.<br/><br/>

| Propriedade | Type | DESCRIÇÃO |
|----|----|----|
| **conversationId** | string | ID que identifica exclusivamente a conversa para o qual o token especificado é válido. |
| **expires_in** | número | Número de segundos até a expiração do token. |
| **streamUrl** | string | URL para o fluxo de mensagens da conversa. |
| **token** | string | Token válido para a conversa especificada. |

### <a name="activities"></a>Atividades

Para cada [Atividade][] que um cliente recebe de um bot via Linha Direta:

- Cartões de anexo são preservados.
- URLs para anexos carregados ficam ocultos com um link privado.
- A propriedade `channelData` será preservada sem modificações. 

Os clientes podem [receber](bot-framework-rest-direct-line-3-0-receive-activities.md) várias atividades do bot como parte de um [ActivitySet](#activityset-object). 

Quando um cliente envia uma `Activity` para um bot via Direct Line:

- A propriedade `type` especifica o tipo de atividade que está enviando (normalmente **mensagem**).
- A propriedade `from` deve ser preenchida com uma ID de usuário escolhida pelo cliente.
- Os anexos podem conter URLs para recursos existentes ou URLs carregado por meio do ponto de extremidade de anexo da Linha Direta.
- A propriedade `channelData` será preservada sem modificações.
- O tamanho total da atividade, quando serializada para o JSON e criptografada, não deve exceder 256 mil caracteres. Portanto, é recomendável que as atividades sejam mantidas abaixo de 150 mil. Se mais dados forem necessários, divida a atividade em várias e/ou considere o uso de anexos.

Os clientes podem [enviar](bot-framework-rest-direct-line-3-0-send-activity.md) uma única atividade por solicitação. 

## <a name="additional-resources"></a>Recursos adicionais

- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
[Erro]: bot-framework-rest-connector-api-reference.md#error-object
[ErrorResponse]: bot-framework-rest-connector-api-reference.md#errorresponse-object
[ResourceResponse]: bot-framework-rest-connector-api-reference.md#resourceresponse-object
