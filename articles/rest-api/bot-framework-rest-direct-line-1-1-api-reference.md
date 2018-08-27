---
title: Referência de API - API de Linha Direta 1.1 | Microsoft Docs
description: Saiba mais sobre cabeçalhos, códigos de status HTTP, esquema, operações e objetos na API de Linha Direta 1.1.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 2f688b9c80e762b93c2eba8f4671ff1760f624f9
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297272"
---
# <a name="api-reference---direct-line-api-11"></a>Referência de API – API de Linha Direta 1.1

> [!IMPORTANT]
> Este artigo contém informações de referência para a API de Linha Direta 1.1. Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use a [API de Linha Direta 3.0](bot-framework-rest-direct-line-3-0-api-reference.md) em vez disso.

Você pode habilitar seu aplicativo cliente para se comunicar com seu bot usando a API de Linha Direta 1.1. A API de Linha Direta 1.1 usa o padrão do setor REST e JSON sobre HTTPS.

## <a name="base-uri"></a>URI de base

Para acessar a API de Linha Direta 1.1, use esta URI de base para todas as solicitações de API:

`https://directline.botframework.com`

## <a name="headers"></a>Cabeçalhos

Além dos cabeçalhos de solicitação HTTP padrão, uma solicitação de API de Linha Direta deve incluir um cabeçalho `Authorization`, que especifica um segredo ou token para autenticação do cliente que está emitindo a solicitação. Você pode especificar o cabeçalho `Authorization` usando o esquema de “Bearer” ou o esquema “BotConnector”. 

**Esquema Bearer**:
```http
Authorization: Bearer SECRET_OR_TOKEN
```

**Esquema de BotConnector**:
```http
Authorization: BotConnector SECRET_OR_TOKEN
```

Para conferir mais detalhes sobre como obter um segredo ou token que pode ser usado por seu cliente para autenticar suas solicitações de API de Linha Direta, consulte [Autenticação](bot-framework-rest-direct-line-1-1-authentication.md).

## <a name="http-status-codes"></a>Códigos de status HTTP

O <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">código de status HTTP</a> retornado com cada resposta indica o resultado da solicitação correspondente. 

| Código de status HTTP | Significado |
|----|----|
| 200 | Solicitação bem-sucedida. |
| 204 | A solicitação foi bem-sucedida, mas não retornou conteúdo. |
| 400 | A solicitação foi malformada ou está incorreta. |
| 401 | O cliente não tem autorização para fazer a solicitação. Geralmente, esse código de status ocorre porque o cabeçalho `Authorization` está ausente ou malformado. |
| 403 | O cliente não tem autorização para executar a operação solicitada. Geralmente esse código de status ocorre porque o cabeçalho `Authorization` especifica um token inválido ou secreto. |
| 404 | O recurso solicitado não foi encontrado. Normalmente, esse código de status indica um URI de solicitação inválido. |
| 500 | Ocorreu um erro de servidor interno dentro do serviço de Linha Direta ou uma falha ocorreu dentro do bot. Se você receber um erro 500 ao postar uma mensagem para um bot, é possível que o erro tenha sido acionado por uma falha no bot. **Esse é um código de erro comum.** |

## <a name="token-operations"></a>Operações de token 
Use essas operações para criar ou atualizar um token que um cliente pode usar para acessar uma conversa individual.

| Operação | DESCRIÇÃO |
|----|----|
| [Gerar Token](#generate-token) | Gere um token para uma nova conversa. | 
| [Atualizar Token](#refresh-token) | Atualize um token. | 

### <a name="generate-token"></a>Gerar Token
Gera um token válido para uma conversa. 
```http 
POST /api/tokens/conversation
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma cadeia de caracteres que representa o token | 

### <a name="refresh-token"></a>Atualizar Token
Atualiza o token. 
```http 
GET /api/tokens/{conversationId}/renew
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma cadeia de caracteres que representa o novo token | 

## <a name="conversation-operations"></a>Operações de conversa 
Use essas operações para abrir uma conversa com seu bot e trocar mensagens entre o cliente e o bot.

| Operação | DESCRIÇÃO |
|----|----|
| [Iniciar Conversa](#start-conversation) | Abre uma nova conversa com o bot. | 
| [Receber mensagens](#get-messages) | Recuperar mensagens do bot. |
| [Enviar uma mensagem](#send-a-message) | Envia uma mensagem para o bot. | 
| [Carregar e enviar arquivo(s)](#upload-send-files) | Carrega e envia arquivo(s) como anexo(s). |

### <a name="start-conversation"></a>Iniciar Conversa
Abre uma nova conversa com o bot. 
```http 
POST /api/conversations
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [Conversation](#conversation-object) | 

### <a name="get-messages"></a>Receber mensagens
Recupera mensagens do bot da conversa especificada. Defina o parâmetro `watermark` no URI da solicitação para indicar a mensagem mais recente vista pelo cliente. 

```http
GET /api/conversations/{conversationId}/messages?watermark={watermark_value}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [MessageSet](#messageset-object). A resposta contém `watermark` como uma propriedade do objeto `MessageSet`. Os clientes devem percorrer as mensagens disponíveis aprimorando o valor `watermark`, até que nenhuma mensagem retorne. | 

### <a name="send-a-message"></a>Enviar uma mensagem
Envia uma mensagem para o bot. 
```http 
POST /api/conversations/{conversationId}/messages
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Message](#message-object) |
| **Retorna** | Nenhum dado é retornado no corpo da resposta. O serviço responde com um código de status HTTP 204, se a mensagem foi enviada com êxito. O cliente pode obter sua mensagem enviada (juntamente com quaisquer mensagens que o bot enviou ao cliente) usando a operação [Obter mensagens](#get-messages). |

### <a id="upload-send-files"></a>Carregar e Enviar Arquivo(s)
Carrega e envia arquivo(s) como anexo(s). Defina o parâmetro `userId` no URI da solicitação para especificar a ID do usuário que está enviando os anexos.
```http 
POST /api/conversations/{conversationId}/upload?userId={userId}
```

| | |
|----|----|
| **Corpo da solicitação** | Para um único anexo, preencha o corpo da solicitação com o conteúdo do arquivo. Para vários anexos, crie um corpo de solicitação com várias partes, com uma parte para cada anexo, e também (opcionalmente) uma parte para o objeto [Message](#message-object) que deve servir como contêiner para os anexos especificados. Confira mais informações em [Enviar uma mensagem para o bot](bot-framework-rest-direct-line-1-1-send-message.md). |
| **Retorna** | Nenhum dado é retornado no corpo da resposta. O serviço responde com um código de status HTTP 204, se a mensagem foi enviada com êxito. O cliente pode obter sua mensagem enviada (juntamente com quaisquer mensagens que o bot enviou ao cliente) usando a operação [Obter mensagens](#get-messages). | 

> [!NOTE]
> Os arquivos carregados são excluídos após 24 horas.

## <a name="schema"></a>Esquema

O esquema de Linha Direta 1.1 é uma cópia simplificada do esquema do Bot Framework v1 que inclui os seguintes objetos.

### <a name="message-object"></a>Objeto Message

Define uma mensagem que um cliente envia para um bot ou recebe de um bot.

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **ID** | string | ID que identifica exclusivamente a mensagem (atribuída por Linha Direta). | 
| **conversationId** | string | ID que identifica a conversa.  | 
| **created** | string | Data e hora em que a mensagem foi criada, expressa no formato <a href="https://en.wikipedia.org/wiki/ISO_8601" target="_blank">ISO-8601</a>. | 
| **from** | string | ID que identifica o usuário que é o remetente da mensagem. Ao criar uma mensagem, os clientes devem definir essa propriedade como uma ID de usuário estável. Embora a Linha Direta atribua uma ID de usuário, se esta não for fornecida, isso normalmente resulta em um comportamento inesperado. | 
| **text** | string | Texto da mensagem que é enviado do usuário para o bot ou do bot para o usuário. | 
| **channelData** | objeto | Um objeto que contém o conteúdo específico do canal. Alguns canais fornecem recursos que exigem informações adicionais que não podem ser representadas usando o esquema de anexo. Para esses casos, defina essa propriedade para o conteúdo específico do canal, conforme definido na documentação do canal. Esses dados são enviados sem modificações entre o cliente e o bot. Esta propriedade deve ser definida como um objeto complexo ou deixada em branco. Não defina-a como uma cadeia de caracteres, número ou outro tipo simples. | 
| **images** | string[] | Matriz de cadeias de caracteres que contém a(s) URL(s) da(s) imagen(s) contida(s) na mensagem. Em alguns casos, as cadeias de caracteres nesta matriz podem ser URLs relativas. Se qualquer cadeia de caracteres nesta matriz não começar com “http” ou “https”, acrescente `https://directline.botframework.com` no início da cadeia de caracteres para formar a URL completa. | 
| **attachments** | [Attachment](#attachment-object)[] | Matriz de objetos **Attachment** que representam os anexos sem imagem contidos na mensagem. Cada objeto na matriz contém uma propriedade `url` e uma propriedade `contentType`. Nas mensagens que um cliente recebe de um bot, a propriedade `url`, às vezes, pode especificar uma URL relativa. Para qualquer valor de propriedade `https://directline.botframework.com` que não comece com “http” ou “https”, acrescente `url` no início da cadeia de caracteres para formar a URL completa. | 

O exemplo a seguir mostra um objeto Message que contém todas as propriedades possíveis. Na maioria dos casos, ao criar uma mensagem, o cliente só precisa fornecer a propriedade `from` e pelo menos uma propriedade de conteúdo (por exemplo, `text`, `images`, `attachments` ou `channelData`).

```json
{
    "id": "CuvLPID4kDb|000000000000000004",
    "conversationId": "CuvLPID4kDb",
    "created": "2016-10-28T21:19:51.0357965Z",
    "from": "examplebot",
    "text": "Hello!",
    "channelData": {
        "examplefield": "abc123"
    },
    "images": [
        "/attachments/CuvLPID4kDb/0.jpg?..."
    ],
    "attachments": [
        {
            "url": "https://example.com/example.docx",
            "contentType": "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
        }, 
        {
            "url": "https://example.com/example.doc",
            "contentType": "application/msword"
        }
    ]
}
```

### <a name="messageset-object"></a>Um objeto MessageSet 
Define um conjunto de mensagens.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **messages** | [Message](#message-object)[] | Matriz de objetos **Message**. |
| **watermark** | string | Marca d'água máxima das mensagens dentro do conjunto. Um cliente pode usar o valor `watermark` para indicar a mensagem mais recente visualizada ao [recuperar mensagens do bot](bot-framework-rest-direct-line-1-1-receive-messages.md). |

### <a name="attachment-object"></a>Objeto Attachment
Define um anexo sem imagem.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **contentType** | string | O tipo de mídia do conteúdo no anexo. |
| **url** | string | URL do conteúdo do anexo. |

### <a name="conversation-object"></a>Objeto Conversation
Define uma conversa de Linha Direta.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **conversationId** | string | ID que identifica exclusivamente a conversa para o qual o token especificado é válido. |
| **token** | string | Token válido para a conversa especificada. |
| **expires_in** | número | Número de segundos até a expiração do token. |

### <a name="error-object"></a>Objeto Error
Define um erro.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **code** | string | Código do erro. Um destes valores: **MissingProperty**, **MalformedData**, **NotFound**, **ServiceError**, **interno**, **InvalidRange**, **NotSupported**, **NotAllowed**, **BadCertificate**. |
| **message** | string | Uma descrição do erro. |
| **statusCode** | número | Código de status. |

### <a name="errormessage-object"></a>Objeto ErrorMessage
Um conteúdo de erro de mensagem padronizada.<br/><br/> 


|        Propriedade        |          Tipo          |                                 DESCRIÇÃO                                 |
|------------------------|------------------------|-----------------------------------------------------------------------------|
| <strong>error</strong> | [Erro](#error-object) | Um objeto <strong>Error</strong> que contém informações sobre o erro. |
