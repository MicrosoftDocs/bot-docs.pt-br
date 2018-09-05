---
title: Referência de API | Microsoft Docs
description: Saiba mais sobre cabeçalhos, operações, objetos e erros no serviço do Bot Connector e serviço de Estado do Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: d76daffcfc4661a87d1efaf85e6bb08e3e999988
ms.sourcegitcommit: e8c513d3af5f0c514cadcbcd0a737a7393405afa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42756740"
---
# <a name="api-reference"></a>Referência de API

> [!NOTE]
> A API REST não é equivalente ao SDK. A API REST é fornecida para permitir a comunicação REST padrão, no entanto, o método preferido de interação com o Framework Bot é o SDK. 

No Bot Framework, o serviço do Bot Connector permite que o bot troque mensagens com usuários em canais que são configurados no portal do Bot Framework e o serviço de Estado do Bot permite que o bot armazene e recupere dados de estado que são relacionados às conversas que o bot conduz usando o serviço do Bot Connector. Ambos os serviços usam REST padrão do setor e JSON por HTTPS.

> [!IMPORTANT]
> A API de Serviço de Estado do Bot Framework não é recomendada para ambientes de produção e poderá ser preterida em uma versão futura. É recomendável que você atualize o código do bot para que ele use o armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção. Para obter mais informações, confira o tópico **Gerenciar dados de estado** para implementação do [.NET](~/dotnet/bot-builder-dotnet-state.md) ou do [Node](~/nodejs/bot-builder-nodejs-state.md).

## <a name="base-uri"></a>URI de base

Quando um usuário envia uma mensagem para o bot, a solicitação recebida contém um objeto [Atividade](#activity-object) com uma propriedade `serviceUrl` que especifica o ponto de extremidade para o qual o bot deve enviar a resposta. Para acessar o serviço do Bot Connector ou o serviço de Estado do Bot, use o valor `serviceUrl` como o URI de base para solicitações de API. 

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

Qualquer resposta que especifique um código de status HTTP no intervalo 4xx ou 5xx incluirá um objeto [ErrorResponse](#errorresponse-object) no corpo da resposta que fornece informações sobre o erro. Se você receber uma resposta de erro no intervalo 4xx, inspecione o objeto **ErrorResponse** para identificar a causa do erro e resolver o problema antes de reenviar a solicitação.

## <a name="conversation-operations"></a>Operações de conversa 
Use essas operações para criar conversas, enviar mensagens (atividades) e gerenciar o conteúdo das conversas.

| Operação | DESCRIÇÃO |
|----|----|
| [Criar conversa](#create-conversation) | Cria uma nova conversa. | 
| [Enviar para conversa](#send-to-conversation) | Envia uma atividade (mensagem) para o final da conversa especificada. | 
| [Responder a atividade](#reply-to-activity) | Envia uma atividade (mensagem) para a conversa especificada, como uma resposta à atividade especificada. | 
| [Obter membros da conversa](#get-conversation-members) | Obtém os membros da conversa especificada. |
| [Obter membros da atividade](#get-activity-members) | Obtém os membros da atividade especificada na conversa especificada. | 
| [Atualizar atividade](#update-activity) | Atualiza uma atividade existente. | 
| [Excluir atividade](#delete-activity) | Exclui uma atividade existente. | 
| [Carregar anexo ao canal](#upload-attachment-to-channel) | Carrega um anexo diretamente no armazenamento de blobs de um canal. |

### <a name="create-conversation"></a>Criar conversa
Cria uma nova conversa. 
```http 
POST /v3/conversations
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Conversation](#conversation-object) |
| **Retorna** | Um objeto [ResourceResponse](#resourceresponse-object) | 

### <a name="send-to-conversation"></a>Enviar para conversa
Envia uma atividade (mensagem) para a conversa especificada. A atividade será anexada ao final da conversa, de acordo com o carimbo de data/hora ou semântica do canal. Para responder a uma mensagem específica na conversa, use [Responder a atividade](#reply-to-activity).
```http
POST /v3/conversations/{conversationId}/activities
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#activity-object) |
| **Retorna** | Um objeto [Identificação](#identification-object) | 

### <a name="reply-to-activity"></a>Responder a atividade
Envia uma atividade (mensagem) para a conversa especificada, como uma resposta à atividade especificada. A atividade será adicionada como resposta a outra atividade, se houver suporte para isso no canal. Se o canal não der suporte a respostas aninhadas, essa operação se comportará como [Enviar para conversa](#send-to-conversation).
```http
POST /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#activity-object) |
| **Retorna** | Um objeto [Identificação](#identification-object) | 

### <a name="get-conversation-members"></a>Obter membros da conversa
Obtém os membros da conversa especificada.
```http
GET /v3/conversations/{conversationId}/members
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma matriz de objetos [ChannelAccount](#channelaccount-object) | 

### <a name="get-activity-members"></a>Obter membros da atividade
Obtém os membros da atividade especificada na conversa especificada.
```http
GET /v3/conversations/{conversationId}/activities/{activityId}/members
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma matriz de objetos [ChannelAccount](#channelaccount-object) | 

### <a name="update-activity"></a>Atualizar atividade
Alguns canais permitem editar uma atividade existente para refletir o novo estado de uma conversa de bot. Por exemplo, será possível remover botões de uma mensagem na conversa depois que o usuário clicar em um dos botões. Se tiver êxito, essa operação atualizará a atividade especificada dentro da conversa especificada. 
```http
PUT /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#activity-object) |
| **Retorna** | Um objeto [Identificação](#identification-object) | 

### <a name="delete-activity"></a>Excluir atividade
Alguns canais permitem excluir uma atividade existente. Se tiver êxito, essa operação removerá a atividade especificada da conversa especificada.
```http
DELETE /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um código de status HTTP que indica o resultado da operação. Nada é especificado no corpo da resposta. | 

### <a name="upload-attachment-to-channel"></a>Carregar anexo ao canal
Carrega um anexo para a conversa especificada diretamente no armazenamento de blobs de um canal. Isso permite que você armazene dados em um armazenamento compatível. 
```http 
POST /v3/conversations/{conversationId}/attachments
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [AttachmentUpload](#attachmentupload-object). |
| **Retorna** | Um objeto [ResourceResponse](#resourceresponse-object). A propriedade **id** especifica a ID do anexo que pode ser usado com a operação [Obter Informações dos Anexos](#get-attachment-info) e a operação [Obter Anexo](#get-attachment). | 

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
| **Retorna** | Um objeto [AttachmentInfo](#attachmentinfo-object) | 

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
Use essas operações para armazenar e recuperar dados de estado.

| Operação | DESCRIÇÃO |
|----|----|
| [Definir dados de usuário](#set-user-data) | Armazena dados de estado para um usuário específico em um canal. | 
| [Definir dados de conversa](#set-conversation-data) | Armazena dados de estado para uma conversa específica em um canal. | 
| [Definir dados de conversa privada](#set-private-conversation-data) | Armazena dados de estado para um usuário específico no contexto de uma conversa específica em um canal. | 
| [Obter dados de usuário](#get-user-data) | Recupera dados de estado que foram armazenados anteriormente para um usuário específico em todas as conversas em um canal. | 
| [Obter dados de conversa](#get-conversation-data) | Recupera dados de estado que foram armazenados anteriormente para uma conversa específica em um canal. | 
| [Obter dados de conversa privada](#get-private-conversation-data) | Recupera dados de estado que foram armazenados anteriormente para um usuário específico no contexto de uma conversa específica em um canal. | 
| [Excluir estado para usuário](#delete-state-for-user) | Exclui dados de estado que foram armazenados anteriormente para um usuário usando a operação [Definir Dados de Usuário](#set-user-data) ou a operação [Definir Dados de Conversa Privada](#set-private-conversation-data). | 

### <a name="set-user-data"></a>Definir dados de usuário
Armazena dados de estado para o usuário especificado no canal especificado.
```http
POST /v3/botstate/{channelId}/users/{userId} 
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [BotData](#botdata-object) |
| **Retorna** | Um objeto [BotData](#botdata-object) | 

### <a name="set-conversation-data"></a>Definir dados de conversa
Armazena dados de estado para a conversa especificada no canal especificado.
```http
POST /v3/botstate/{channelId}/conversations/{conversationId}
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [BotData](#botdata-object) |
| **Retorna** | Um objeto [BotData](#botdata-object) | 

### <a name="set-private-conversation-data"></a>Definir dados de conversa privada
Armazena dados de estado para o usuário especificado no contexto da conversa especificada no canal especificado.
```http
POST /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId} 
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [BotData](#botdata-object) |
| **Retorna** | Um objeto [BotData](#botdata-object) | 

### <a name="get-user-data"></a>Obter dados de usuário
Recupera dados de estado que foram armazenados anteriormente para o usuário especificado em todas as conversas no canal especificado.
```http
GET /v3/botstate/{channelId}/users/{userId} 
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [BotData](#botdata-object) | 

### <a name="get-conversation-data"></a>Obter dados de conversa
Recupera dados de estado que foram armazenados anteriormente para a conversa especificada no canal especificado.
```http
GET /v3/botstate/{channelId}/conversations/{conversationId} 
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [BotData](#botdata-object) | 

### <a name="get-private-conversation-data"></a>Obter dados de conversa privada
Recupera dados de estado que foram armazenados anteriormente para o usuário especificado no contexto da conversa especificada no canal especificado.
```http
GET /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId} 
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [BotData](#botdata-object) | 

### <a name="delete-state-for-user"></a>Excluir estado para usuário
Exclui os dados de estado que foram armazenados anteriormente para o usuário especificado no canal especificado usando a operação [Definir dados de usuário](#set-user-data) ou a operação [Definir dados de conversa privada](#set-private-conversation-data).
```http
DELETE /v3/botstate/{channelId}/users/{userId} 
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um matriz de cadeia de caracteres (IDs) | 

## <a id="objects"></a> Esquema

O esquema define o objeto e a propriedades que o bot pode usar para comunicar-se com um usuário. 

| Objeto | DESCRIÇÃO |
| ---- | ---- |
| [Objeto Activity](#activity-object) | Define uma mensagem trocada entre o bot e o usuário. |
| [Objeto AnimationCard](#animationcard-object) | Define um cartão que pode reproduzir GIFs animados ou vídeos curtos. |
| [Objeto Attachment](#attachment-object) | Define informações adicionais para incluir na mensagem. Um anexo pode ser um arquivo de mídia (por exemplo, áudio, vídeo, imagem, arquivo) ou um cartão avançado. |
| [Objeto AttachmentData](#attachmentdata-object) |Descreve um anexo de dados. |
| [Objeto AttachmentInfo](#attachmentinfo-object) | Descreve um anexo. |
| [Objeto AttachmentView](#attachmentview-object) | Define um modo de exibição de anexo. |
| [Objeto AttachmentUpload](#attachmentupload-object) | Define um anexo para ser carregado. |
| [Objeto AudioCard](#audiocard-object) | Define um cartão que pode reproduzir um arquivo de áudio. |
| [Objeto BotData](#botdata-object) | Define dados de estado para um usuário, uma conversa ou um usuário no contexto de uma conversa específica que é armazenada usando o serviço de Estado do Bot. |
| [Objeto CardAction](#cardaction-object) | Define uma ação para executar. |
| [Objeto CardImage](#cardimage-object) | Define uma imagem para exibir em um cartão. |
| [Objeto ChannelAccount](#channelaccount-object) | Define um bot ou uma conta de usuário no canal. |
| [Objeto Conversation](#conversation-object) | Define uma conversa, incluindo o bot e os usuários incluídos na conversa. |
| [Objeto ConversationAccount](#conversationaccount-object) | Define uma conversa em um canal. |
| [Objeto ConversationParameters](#conversationparameters-object) | Definir parâmetros para criar uma nova conversa |
| [Objeto ConversationReference](#conversationreference-object) | Define um ponto específico em uma conversa. |
| [Objeto ConversationResourceResponse](#conversationresourceresponse-object) | "Uma resposta contendo um recurso |
| [Objeto Entity](#entity-object) | Define um objeto de entidade. |
| [Objeto Error](#error-object) | Define um erro. |
| [Objeto ErrorResponse](#errorresponse-object) | Define uma resposta de API HTTP. |
| [Objeto Fact](#fact-object) | Define um par chave-valor que contém um fato. |
| [Objeto Geocoordinates](#geocoordinates-object) | Define uma localização geográfica usando as coordenadas WSG84 (World Geodetic System). |
| [Objeto HeroCard](#herocard-object) | Define um cartão com uma imagem grande, título, texto e botões de ação. |
| [Objeto Identification](#identification-object) | Identifica um recurso. |
| [Objeto MediaEventValue](#mediaeventvalue-object) |Parâmetro suplementar para eventos de mídia.|
| [Objeto MediaUrl](#mediaurl-object) | Define a URL para a fonte de um arquivo de mídia. |
| [Objeto Mention](#mention-object) | Define um usuário ou bot que foi mencionado na conversa. |
| [Objeto MessageReaction](#messagereaction-object) | Define uma reação a uma mensagem. |
| [Objeto Place](#place-object) | Define um local que foi mencionado na conversa. |
| [Objeto ReceiptCard](#receiptcard-object) | Define um cartão que contém um recibo para uma compra. |
| [Objeto ReceiptItem](#receiptitem-object) | Define um item de linha dentro de um recibo. |
| [Objeto ResourceResponse](#resourceresponse-object) | Define um recurso. |
| [Objeto SignInCard](#signincard-object) | Define um cartão que permite ao usuário entrar em um serviço. |
| [Objeto SuggestedActions](#suggestedactions-object) | Define as opções que um usuário pode escolher. |
| [Objeto ThumbnailCard](#thumbnailcard-object) | Define um cartão com uma imagem em miniatura, título, texto e botões de ação. |
| [Objeto ThumbnailUrl](#thumbnailurl-object) | Define a URL para a fonte de uma imagem. |
| [Objeto VideoCard](#videocard-object) | Define um cartão que pode reproduzir vídeos. |


### <a name="activity-object"></a>Objeto de Atividade
Define uma mensagem trocada entre o bot e o usuário.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **action** | string | A ação a aplicar ou que foi aplicada. Use a propriedade **tipo** para determinar o contexto da ação. Por exemplo, se **tipo** for **contactRelationUpdate**, o valor da propriedade **ação** será **adicionar** se o usuário adicionou o bot à lista de contatos ou **remover** se o usuário removeu o bot da lista de contatos. |
| **attachments** | [Attachment](#attachment-object)[] | Matriz de objetos **Anexo** que define informações adicionais para incluir na mensagem. Cada anexo pode ser um arquivo de mídia (por exemplo, áudio, vídeo, imagem, arquivo) ou um cartão avançado. |
| **attachmentLayout** | string | Layout dos **anexos** de cartão avançado que a mensagem inclui. Um desses valores: **carrossel**, **lista**. Para obter mais informações sobre anexos de cartão avançado, consulte [Adicionar anexos de cartão avançado às mensagens](bot-framework-rest-connector-add-rich-cards.md). |
| **channelData** | objeto | Um objeto que contém conteúdo específico do canal. Alguns canais fornecem recursos que exigem informações adicionais que não podem ser representadas usando o esquema de anexo. Para esses casos, defina essa propriedade para o conteúdo específico do canal, conforme definido na documentação do canal. Para obter mais informações, consulte [Implementar funcionalidade específica do canal](bot-framework-rest-connector-channeldata.md). |
| **channelId** | string | Uma ID que identifica exclusivamente o canal. Definida pelo canal. | 
| **conversa** | [ConversationAccount](#conversationaccount-object) | Um objeto **ConversationAccount** que define a conversa à qual a atividade pertence. |
| **code** | string | Código indicando por que a conversa encerrou. |
| **entidades** | object[] | Matriz de objetos que representa as entidades mencionadas na mensagem. Objetos nessa matriz podem ser qualquer objeto <a href="http://schema.org/" target="_blank">Schema.org</a>. Por exemplo, a matriz pode incluir objetos [Menção](#mention-object) que identificam alguém que foi mencionado na conversa e objetos [Local](#place-object) que identificam um local mencionado na conversa. |
| **from** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que especifica o remetente da mensagem. |
| **historyDisclosed** | booleano | Sinalizador que indica se o histórico é ou não divulgado. O valor padrão é **false**. |
| **ID** | string | ID que identifica exclusivamente a atividade no canal. | 
| **inputHint** | string | Valor que indica se o bot está aceitando, esperando ou ignorando a entrada do usuário após a entrega da mensagem ao cliente. Um desses valores: **acceptingInput**, **expectingInput**, **ignoringInput**. |
| **locale** | string | Localidade do idioma que deve ser usado para exibir texto dentro da mensagem, no formato `<language>-<country>`. O canal usa essa propriedade para indicar o idioma do usuário, para que o bot possa especificar cadeia de caracteres de exibição nesse idioma. O valor padrão é **en-US**. |
| **localTimestamp** | string | Data e hora em que a mensagem foi enviada no fuso horário local, expressa no formato <a href="https://en.wikipedia.org/wiki/ISO_8601" target="_blank">ISO-8601</a>. |
| **membersAdded** | [ChannelAccount](#channelaccount-object)[] | Matriz de objetos **ChannelAccount** que representam a lista de usuários que participaram da conversa. Presente apenas se a atividade **tipo** for "conversationUpdate" e os usuários ingressarem na conversa. | 
| **membersRemoved** | [ChannelAccount](#channelaccount-object)[] | Matriz de objetos **ChannelAccount** que representam a lista de usuários que saíram da conversa. Presente apenas se a atividade **tipo** for "conversationUpdate" e os usuários saírem da conversa. | 
| **name** | string | Nome da operação a invocar ou o nome do evento. |
| **recipient** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que especifica o destinatário da mensagem. |
| **relatesTo** | [ConversationReference](#conversationreference-object) | Um objeto **ConversationReference** que define um ponto específico em uma conversa. |
| **replyToId** | string | A ID da mensagem à qual essa mensagem responde. Para responder a uma mensagem que o usuário enviou, defina essa propriedade como a ID da mensagem do usuário. Nem todos os canais dão suporte a respostas encadeadas. Nesses casos, o canal ignorará essa propriedade e usará a semântica ordenada por tempo (carimbo de data/hora) para anexar a mensagem à conversa. | 
| **serviceUrl** | string | URL que especifica o ponto de extremidade de serviço do canal. Definida pelo canal. | 
| **speak** | string | Texto a ser falado pelo bot em um canal habilitado para fala. Para controlar várias características de fala do bot como voz, velocidade, volume, pronúncia e tom, especifique essa propriedade no formato <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">SSML (Linguagem de Marcação de Sintetização de Voz)</a>. |
| **suggestedActions** | [SuggestedActions](#suggestedactions-object) | Um objeto **SuggestedActions** que define as opções a partir das quais o usuário poderá escolher. |
| **summary** | string | Resumo das informações que a mensagem contém. Por exemplo, para uma mensagem enviada em um canal de email, essa propriedade pode especificar os primeiros 50 caracteres da mensagem de email. |
| **text** | string | Texto da mensagem que é enviada do usuário para o bot ou do bot para o usuário. Consulte a documentação do canal para os limites impostos sobre o conteúdo dessa propriedade. |
| **textFormat** | string | Formato do **texto** da mensagem. Um desses valores: **markdown**, **plain**, **xml**. Para detalhes sobre o formato de texto, consulte [Criar mensagens](bot-framework-rest-connector-create-messages.md). |
| **timestamp** | string | Data e hora em que a mensagem foi enviada no fuso horário UTC, expressa no formato <a href="https://en.wikipedia.org/wiki/ISO_8601" target="_blank">ISO-8601</a>. |
| **topicName** | string | Tópico da conversa à qual a atividade pertence. |
| **tipo** | string | Tipo de atividade. Um destes valores: **contactRelationUpdate**, **conversationUpdate**, **deleteUserData**, **message**, **typing**, **endOfConversation**. Para detalhes sobre tipos de atividade, consulte [Visão geral das atividades](bot-framework-rest-connector-activities.md). |
| **valor** | objeto | Valor em aberto. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="animationcard-object"></a>Objeto AnimationCard
Define um cartão que pode reproduzir GIFs animados ou vídeos curtos.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **autoloop** | booleano | Sinalizador que indica se deve repetir a lista de GIFs animados quando o último encerrar. Configure essa propriedade como **true** para reproduzir automaticamente a animação, caso contrário, como **false**. O valor padrão é **true**. |
| **autostart** | booleano | Sinalizador que indica se deverá reproduzir automaticamente a animação quando o cartão for exibido. Defina essa propriedade como **true** para reproduzir automaticamente a animação, caso contrário, como **false**. O valor padrão é **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **image** | [ThumbnailUrl](#thumbnailurl-object) | Um objeto **ThumbnailUrl** que especifica a imagem a ser exibida no cartão. |
| **media** | [MediaUrl](#mediaurl-object)[] | Matriz de objetos **MediaUrl** que especifica a lista de GIFs animados a serem reproduzidos. |
| **shareable** | booleano | Sinalizador que indica se a animação pode ser compartilhada com outras pessoas. Configure esta propriedade para **true** se a animação puder ser compartilhada, caso contrário, para **false**. O valor padrão é **true**. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |
| **valor** | objeto | Parâmetro suplementar para esse cartão |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="attachment-object"></a>Objeto Anexo
Define informações adicionais para incluir na mensagem. Um anexo pode ser um arquivo de mídia (por exemplo, áudio, vídeo, imagem, arquivo) ou um cartão avançado.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **contentType** | string | O tipo de mídia do conteúdo no anexo. Para arquivos de mídia, defina essa propriedade para tipos de mídia conhecidos, como **image/png**, **audio/wav** e **video/mp4**. Para cartões avançados, defina essa propriedade para um desses tipos específicos do fornecedor:<ul><li>**application/vnd.microsoft.card.adaptive**: um cartão avançado que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. Defina a propriedade de **conteúdo** para um objeto <a href="http://adaptivecards.io/documentation/#create-cardschema" target="_blank">AdaptiveCard</a>.</li><li>**application/vnd.microsoft.card.animation**: um cartão avançado que reproduz animação. Defina a propriedade de **conteúdo** para um objeto [AnimationCard](#animationcard-object).</li><li>**application/vnd.microsoft.card.audio**: um cartão avançado que reproduz arquivos de áudio. Defina a propriedade de **conteúdo** como um objeto [AudioCard](#audiocard-object).</li><li>**application/vnd.microsoft.card.video**: um cartão avançado que reproduz vídeos. Defina a propriedade de **conteúdo** para um objeto [VideoCard](#videocard-object).</li><li>**application/vnd.microsoft.card.hero**: um cartão Hero. Defina e propriedade de **conteúdo** para um objeto [HeroCard](#herocard-object).</li><li>**application/vnd.microsoft.card.thumbnail**: um cartão de Miniatura. Defina a propriedade de **conteúdo** para um objeto [ThumbnailCard](#thumbnailcard-object).</li><li>**application/vnd.microsoft.com.card.receipt**: um cartão de recebimento. Defina e propriedade de **conteúdo** para um objeto [ReceiptCard](#receiptcard-object).</li><li>**application/vnd.microsoft.com.card.signin**: um cartão de entrada do usuário. Defina e propriedade de **conteúdo** para um objeto [SignInCard](#signincard-object).</li></ul> |
| **contentUrl** | string | URL para o conteúdo do anexo. Por exemplo, se o anexo for uma imagem, defina **contentUrl** como a URL que representa o local da imagem. Os protocolos com suporte são: HTTP, HTTPS, arquivo e dados. |
| **content** | objeto | O conteúdo do anexo. Se o anexo for um cartão avançado, defina essa propriedade para o objeto cartão avançado. Essa propriedade e a propriedade **contentUrl** são mutuamente exclusivas. |
| **name** | string | Nome do anexo. |
| **thumbnailUrl** | string | URL para uma imagem em miniatura que o canal pode usar se for compatível com o uso de uma forma alternativa menor de **conteúdo** ou **contentUrl**. Por exemplo, se você definir **contentType** como **application/word** e definir **contentUrl** como o local do documento do Word, talvez inclua uma imagem em miniatura que represente o documento. O canal pode exibir a imagem em miniatura em vez do documento. Quando o usuário clicar na imagem, o canal abrirá o documento. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="attachmentdata-object"></a>Objeto AttachmentData 
Descreve um anexo de dados.

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **name** | string | Nome do anexo. |
| **originalBase64** | string | Conteúdo do anexo. |
| **thumbnailBase64** | string | Conteúdo em miniatura do anexo. |
| **tipo** | string | Tipo de conteúdo do anexo. |

### <a name="attachmentinfo-object"></a>Objeto AttachmentInfo
Descreve um anexo.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **name** | string | Nome do anexo. |
| **tipo** | string | ContentType do anexo. |
| **modos de exibição** | [AttachmentView](#attachmentview-object)[] | Matriz de objetos **AttachmentView** que representam as exibições disponíveis para o anexo. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="attachmentview-object"></a>Objeto AttachmentView
Define um modo de exibição de anexo.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **viewId** | string | ID da exibição. |
| **tamanho** | número | Tamanho do arquivo. |

<a href="#objects">Retornar à tabela de esquemas</a>

<!-- TODO - can't find in swagger file -->
### <a name="attachmentupload-object"></a>Objeto AttachmentUpload
Define um anexo para ser carregado.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **tipo** | string | ContentType do anexo. | 
| **name** | string | Nome do anexo. | 
| **originalBase64** | string | Dados binários que representam o conteúdo da versão original do arquivo. |
| **thumbnailBase64** | string | Dados binários que representam o conteúdo da versão em miniatura do arquivo. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="audiocard-object"></a>Objeto AudioCard
Define um cartão que pode reproduzir um arquivo de áudio.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **aspect** | string | Proporção da miniatura especificada na propriedade da **imagem**. Os valores válidos são **16:9** e **9:16**. |
| **autoloop** | booleano | Sinalizador que indica se deverá reproduzir a lista de arquivos de áudio quando o último encerrar. Defina essa propriedade como **true** para reproduzir automaticamente os arquivos de áudio, caso contrário, como **false**. O valor padrão é **true**. |
| **autostart** | booleano | Sinalizador que indica se deverá reproduzir automaticamente o áudio quando o cartão for exibido. Defina essa propriedade como **true** para reproduzir automaticamente o áudio, caso contrário, como **false**. O valor padrão é **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **image** | [ThumbnailUrl](#thumbnailurl-object) | Um objeto **ThumbnailUrl** que especifica a imagem a ser exibida no cartão. |
| **media** | [MediaUrl](#mediaurl-object)[] | Matriz de objetos **MediaUrl** que especifica a lista de arquivos de áudio a serem reproduzidos. |
| **shareable** | booleano | Sinalizador que indica se os arquivos de áudio podem ser compartilhados com outras pessoas. Defina essa propriedade como **true** se o áudio puder ser compartilhado, caso contrário, **false**. O valor padrão é **true**. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |
| **valor** | objeto | Parâmetro suplementar para esse cartão |

<a href="#objects">Retornar à tabela de esquemas</a>

<!-- TODO - can't find in swagger file -->
### <a name="botdata-object"></a>Objeto BotData
Define dados de estado para um usuário, uma conversa ou um usuário no contexto de uma conversa específica que é armazenada usando o serviço de Estado do Bot.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **dados** | objeto | Em uma solicitação, um objeto JSON que especifica as propriedades e os valores a serem armazenados usando o serviço do Estado do Bot. Em uma resposta, um objeto JSON que especifica as propriedades e valores que foram armazenados usando o serviço de Estado do Bot. | 
| **eTag** | string | O valor da marca da entidade que pode ser usado para controlar simultaneidade de dados dos dados que você armazena usando o serviço de Estado do Bot. Para obter mais informações, consulte [Gerenciar dados de estado](bot-framework-rest-state.md). | 

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="cardaction-object"></a>Objeto CardAction
Define uma ação para executar.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **image** | string | URL de uma imagem para exibir no | 
| **text** | string | Texto para a ação |
| **title** | string | Texto do botão. Aplicável apenas para a ação de um botão. |
. Aplicável apenas para a ação de um botão. |
| **tipo** | string | Tipo de ação a ser executada. Para obter uma lista de valores válidos, consulte [Adicionar anexos de cartão avançado às mensagens](bot-framework-rest-connector-add-rich-cards.md). |
| **valor** | objeto | Parâmetro suplementar para a ação. O valor dessa propriedade irá variar de acordo com a ação **tipo**. Para obter mais informações, consulte [Adicionar anexos de cartão avançado às mensagens](bot-framework-rest-connector-add-rich-cards.md). |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="cardimage-object"></a>Objeto CardImage
Define uma imagem para exibir em um cartão.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **alt** | string | Descrição da imagem. É necessário incluir a descrição para dar suporte à acessibilidade. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especificará a ação a ser executada se o usuário tocar ou clicar na imagem. |
| **url** | string | URL para a origem da imagem ou o binário base64 da imagem (por exemplo, `data:image/png;base64,iVBORw0KGgo...`). |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="channelaccount-object"></a>Objeto ChannelAccount
Define um bot ou uma conta de usuário no canal.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **ID** | string | ID que identifica exclusivamente o bot ou usuário no canal. |
| **name** | string | Nome do bot ou usuário. |

<a href="#objects">Retornar à tabela de esquemas</a>

<!--TODO can't find-->
### <a name="conversation-object"></a>Objeto Conversa
Define uma conversa, incluindo o bot e os usuários incluídos na conversa.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **bot** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que identifica o bot. |
| **isGroup** | booleano | Sinalizar para indicar se esta é ou não uma conversa em grupo. Defina como **true** se esta for uma conversa em grupo, caso contrário, como **false**. O padrão é **false**. Para iniciar uma conversa em grupo, o canal deve dar suporte para conversas em grupo. |
| **members** | [ChannelAccount](#channelaccount-object)[] | Matriz de objetos **ChannelAccount** que identificam os membros da conversa. Essa lista deve conter um único usuário, a menos que **isGroup** esteja definido como **true**. Essa lista pode incluir outros bots. |
| **topicName** | string | Título da conversa. |
| **activity** | [Atividade](#activity-object) | Em uma solicitação [Criar Conversa](#create-conversation), um objeto **Atividade** que define a primeira mensagem a ser postada na nova conversa. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="conversationaccount-object"></a>Objeto ConversationAccount
Define uma conversa em um canal.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **ID** | string | A ID que identifica a conversa. A ID é exclusiva por canal. Se o canal iniciar a conversa, ele definirá essa ID, caso contrário, o bot definirá essa propriedade para a ID que retorna na resposta quando iniciar a conversa (consulte Iniciar uma conversa). |
| **isGroup** | booleano | Sinalizar para indicar se a conversa contém mais de dois participantes no momento em que a atividade foi gerada. Defina como **true** se esta for uma conversa em grupo, caso contrário, como **false**. O padrão é **false**. |
| **name** | string | Um nome de exibição que pode ser usado para identificar a conversa. |
| **conversationType** | string | Indica o tipo de conversa nos canais que diferenciam os tipos de conversas (por exemplo, grupo, pessoal). |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="conversationparameters-object"></a>Objeto ConversationParameters
Definir parâmetros para criar uma nova conversa

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **isGroup** | booleano | Indica se essa é uma conversa em grupo. |
| **bot** | [ChannelAccount](#channelaccount-object) | Endereço do bot na conversa. |
| **members** | matriz | Lista de membros para adicionar à conversa. |
| **topicName** | string | Título do tópico de uma conversa. Essa propriedade será usada apenas se um canal der suporte. |
| **activity** | [Atividade](#activity-object) | (opcional) Use essa atividade como a mensagem inicial para a conversa ao criar uma nova conversa. |
| **channelData** | objeto | Carga específica do canal para criar a conversa. |

### <a name="conversationreference-object"></a>Objeto ConversationReference
Define um ponto específico em uma conversa.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **activityId** | string | ID que identifica exclusivamente a atividade à qual esse objeto faz referência. | 
| **bot** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que identifica o bot na conversa à qual esse objeto faz referência. |
| **channelId** | string | Uma ID que identifica exclusivamente o canal na conversa à qual esse objeto faz referência. | 
| **conversa** | [ConversationAccount](#conversationaccount-object) | Um objeto **ConversationAccount** que define a conversação à qual esse objeto faz referência. |
| **serviceUrl** | string | URL que especifica o ponto de extremidade de serviço do canal na conversa à qual esse objeto faz referência. | 
| **user** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que identifica o usuário na conversa à qual esse objeto faz referência. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="conversationresourceresponse-object"></a>Objeto ConversationResourceResponse
Define uma resposta que contém um recurso.

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **activityId** | string | ID da atividade. |
| **ID** | string | ID do recurso. |
| **serviceUrl** | string | Ponto de extremidade de serviço. |

### <a name="error-object"></a>Objeto Erro
Define um erro.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **code** | string | Código do erro. |
| **message** | string | Uma descrição do erro. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="entity-object"></a>Objeto de entidade
Define um objeto de entidade.

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **tipo** | string | Tipo de entidade. Geralmente contêm tipos do schema.org. |


### <a name="errorresponse-object"></a>Objeto ErrorResponse
Define uma resposta de API HTTP.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **error** | [Erro](#error-object) | Um objeto **Erro** que contém informações sobre o erro. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="fact-object"></a>Objeto Fato
Define um par chave-valor que contém um fato.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **chave** | string | Nome do fato. Por exemplo, **Check-in**. A chave é usada como um rótulo ao exibir o valor do fato. |
| **valor** | string | Valor do fato. Por exemplo, **10 de outubro de 2016**. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="geocoordinates-object"></a>Objeto Geocoordinates
Define uma localização geográfica usando as coordenadas WSG84 (World Geodetic System).<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **elevation** | número | Elevação da localização. |
| **name** | string | Nome da localização. |
| **latitude** | número | Latitude da localização. |
| **longitude** | número | Longitude da localização. |
| **tipo** | string | O tipo desse objeto. Sempre definido para **GeoCoordinates**. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="herocard-object"></a>Objeto HeroCard
Define um cartão com uma imagem grande, título, texto e botões de ação.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **images** | [CardImage](#cardimage-object)[] | Matriz de objetos **CardImage** que especifica a imagem a ser exibida no cartão. Um cartão Hero contém apenas uma imagem. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no cartão. Essa pode ser a mesma ação que um dos botões ou uma ação diferente. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |

<a href="#objects">Retornar à tabela de esquemas</a>

<!--TODO can't find-->
### <a name="identification-object"></a>Objeto Identification
Identifica um recurso.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **ID** | string | ID que identifica exclusivamente o recurso. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="mediaeventvalue-object"></a>Objeto MediaEventValue 
Parâmetro suplementar para eventos de mídia.

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **cardValue** | objeto | Parâmetro de retorno de chamada especificado no campo **Valor** do cartão de memória que originou esse evento. |

### <a name="mediaurl-object"></a>Objeto MediaUrl
Define a URL para a fonte de um arquivo de mídia.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **profile** | string | Dica que descreve o conteúdo da mídia. |
| **url** | string | URL para a origem do arquivo de mídia. |

<a href="#objects">Retornar à tabela de esquemas</a>

<!--TODO can't find-->
### <a name="mention-object"></a>Objeto Mention
Define um usuário ou bot que foi mencionado na conversa.<br/><br/> 


|          Propriedade          |                   Tipo                   |                                                                                                                                                                                                                           DESCRIÇÃO                                                                                                                                                                                                                            |
|----------------------------|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <strong>mentioned</strong> | [ChannelAccount](#channelaccount-object) | Um objeto <strong>ChannelAccount</strong> que especifica o usuário ou o bot que foi mencionado. Observe que alguns canais, como Slack, atribuem nomes por conversa, portanto, é possível que o nome mencionado do bot (na propriedade do <strong>destinatário</strong> ) seja diferente do identificador especificado quando você [registrou](../bot-service-quickstart-registration.md) o bot. No entanto, as IDs da conta para ambos seriam as mesmas. |
|   <strong>text</strong>    |                  string                  |                                                                                                                         O usuário ou bot como mencionado na conversa. Por exemplo, se a mensagem for "@ColorBot escolha uma nova cor," essa propriedade será definida como <strong>@ColorBot</strong>. Nem todos os canais definem essa propriedade.                                                                                                                          |
|   <strong>tipo</strong>    |                  string                  |                                                                                                                                                                                                   Tipo desse objeto. Sempre defina para <strong>Menção</strong>.                                                                                                                                                                                                    |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="messagereaction-object"></a>Objeto MessageReaction
Define uma reação a uma mensagem.

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **tipo** | string | Tipo de reação. |

### <a name="place-object"></a>Objeto Local
Define um local que foi mencionado na conversa.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **address** | objeto |  Endereço de um local. Essa propriedade pode ser um `string` ou um objeto complexo do tipo `PostalAddress`. |
| **geo** | [GeoCoordinates](#geocoordinates-object) | Um objeto **GeoCoordinates** que especifica as coordenadas geográficas do lugar. |
| **hasMap** | objeto | Mapa para o local. Essa propriedade pode ser um `string` (URL) ou um objeto complexo do tipo `Map`. |
| **name** | string | Nome do local. |
| **tipo** | string | Tipo desse objeto. Sempre defina para **Local**. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="receiptcard-object"></a>Objeto ReceiptCard
Define um cartão que contém um recibo para uma compra.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **facts** | [Fact](#fact-object)[] | Matriz de objetos **Fato** que especificam informações sobre a compra. Por exemplo, a lista de fatos para um recibo de hospedagem pode incluir a data de check-in e a data de check-out. O canal determina o número de fatos que podem ser especificados. |
| **items** | [ReceiptItem](#receiptitem-object)[] | Matriz de objetos **ReceiptItem** que especificam os itens comprados |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no cartão. Essa pode ser a mesma ação que um dos botões ou uma ação diferente. |
| **tax** | string | Uma cadeia de caracteres formatada em moeda que especifica o valor do imposto aplicado à compra. |
| **title** | string | Título exibido na parte superior do recebimento. |
| **total** | string | Uma cadeia de caracteres formatada em moeda que especifica o preço total de compra, incluindo todos os impostos aplicáveis. |
| **vat** | string | Uma cadeia de caracteres formatada como moeda que especifica o IVA (imposto sobre valor agregado) aplicado ao preço de compra. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="receiptitem-object"></a>Objeto ReceiptItem
Define um item de linha dentro de um recibo.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **image** | [CardImage](#cardimage-object) | Um objeto **CardImage** que especifica a imagem em miniatura para exibir ao lado do item de linha.  |
| **price** | string | Uma cadeia de caracteres formatada como moeda que especifica o preço total de todas as unidades compradas. |
| **quantity** | string | Uma cadeia numérica que especifica o número de unidades compradas. |
| **subtitle** | string | Subtítulo a ser exibido abaixo do título do item de linha. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no item de linha. |
| **text** | string | Descrição do item de linha. |
| **title** | string | Título do item de linha. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="resourceresponse-object"></a>Objeto ResourceResponse
Define uma resposta que contém uma ID de recurso.<br/><br/>


|      Propriedade       |  Tipo  |                DESCRIÇÃO                |
|---------------------|--------|-------------------------------------------|
| <strong>ID</strong> | string | ID que identifica exclusivamente o recurso. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="signincard-object"></a>Objeto SignInCard
Define um cartão que permite ao usuário entrar em um serviço.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário entrar em um serviço. O canal determina o número de botões que você pode especificar. |
| **text** | string | Descrição ou solicitação para incluir no cartão de entrada. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="suggestedactions-object"></a>Objeto SuggestedActions
Define as opções que um usuário pode escolher.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **actions** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que definem as ações sugeridas. |
| **to** | string[] | Matriz de cadeias de caracteres que contém as IDs dos destinatários para os quais as ações sugeridas devem ser exibidas. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="thumbnailcard-object"></a>Objeto ThumbnailCard
Define um cartão com uma imagem em miniatura, título, texto e botões de ação.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **images** | [CardImage](#cardimage-object)[] | Matriz de objetos **CardImage** que especificam imagens em miniatura para exibir no cartão. O canal determina o número de imagens em miniatura que podem ser especificados. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no cartão. Essa pode ser a mesma ação que um dos botões ou uma ação diferente. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="thumbnailurl-object"></a>Objeto ThumbnailUrl
Define a URL para a fonte de uma imagem.<br/><br/> 

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **alt** | string | Descrição da imagem. É necessário incluir a descrição para dar suporte à acessibilidade. |
| **url** | string | URL para a origem da imagem ou o binário base64 da imagem (por exemplo, `data:image/png;base64,iVBORw0KGgo...`). |

<a href="#objects">Retornar à tabela de esquemas</a>

### <a name="videocard-object"></a>Objeto VideoCard
Define um cartão que pode reproduzir vídeos.<br/><br/>

| Propriedade | Tipo | DESCRIÇÃO |
|----|----|----|
| **aspect** | string | Proporção de um vídeo (por exemplo, 16: 9, 4: 3).|
| **autoloop** | booleano | Sinalizador que indica se deverá reproduzir a lista de vídeos quando o último encerrar. Defina esta propriedade como **true** para reproduzir automaticamente os vídeos, caso contrário, como **false**. O valor padrão é **true**. |
| **autostart** | booleano | Sinalizador que indica se deverá reproduzir automaticamente os vídeos quando o cartão for exibido. Defina essa propriedade para **true** para reproduzir automaticamente os vídeos, caso contrário, como **false**. O valor padrão é **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **image** | [ThumbnailUrl](#thumbnailurl-object) | Um objeto **ThumbnailUrl** que especifica a imagem a ser exibida no cartão. |
| **media** | [MediaUrl](#mediaurl-object)[] | Matriz de objetos **MediaUrl** que especifica a lista de vídeos a serem reproduzidos. |
| **shareable** | booleano | Sinalizador que indica se os vídeos podem ser compartilhados com outras pessoas. Defina esta propriedade para **true** se os vídeos puderem ser compartilhados, caso contrário, como **false**. O valor padrão é **true**. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |
| **valor** | objeto | Parâmetro suplementar para esse cartão|

<a href="#objects">Retornar à tabela de esquemas</a>
