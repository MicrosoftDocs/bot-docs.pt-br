---
title: Referência de API – Serviço de Bot
description: Saiba mais sobre cabeçalhos, operações, objetos e erros no serviço do Bot Connector e serviço de Estado do Bot.
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/02/2019
ms.openlocfilehash: edfb73febe4b250d52b46065e3318a32a7258922
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81379394"
---
# <a name="api-reference"></a>Referência de API

> [!NOTE]
> A API REST não é equivalente ao SDK. A API REST é fornecida para permitir a comunicação REST padrão, no entanto, o método preferido de interação com o Framework Bot é o SDK.

Dentro do Bot Framework, o serviço Bot Connector permite que seu bot troque mensagens com usuários em canais que são configurados no Portal do Bot Framework. O serviço usa REST padrão do setor e JSON por HTTPS.

## <a name="base-uri"></a>URI de base

Quando um usuário envia uma mensagem para o bot, a solicitação recebida contém um objeto [Atividade](#activity-object) com uma propriedade `serviceUrl` que especifica o ponto de extremidade para o qual o bot deve enviar a resposta. Para acessar o serviço do Bot Connector, use o valor `serviceUrl` como o URI de base para solicitações de API.

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

## <a name="headers"></a>headers

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
| 405 | O canal não é compatível com a operação solicitada. |
| 500 | Ocorreu um erro interno no servidor. |
| 503 | O serviço está indisponível. |

### <a name="errors"></a>Errors

Qualquer resposta que especifique um código de status HTTP no intervalo 4xx ou 5xx incluirá um objeto [ErrorResponse](#errorresponse-object) no corpo da resposta que fornece informações sobre o erro. Se você receber uma resposta de erro no intervalo 4xx, inspecione o objeto **ErrorResponse** para identificar a causa do erro e resolver o problema antes de reenviar a solicitação.

## <a name="conversation-operations"></a>Operações de conversa

Use essas operações para criar conversas, enviar mensagens (atividades) e gerenciar o conteúdo das conversas.

| Operação | Descrição |
|----|----|
| [Criar conversa](#create-conversation) | Cria uma nova conversa. |
| [Excluir atividade](#delete-activity) | Exclui uma atividade existente. |
| [Excluir membro da conversa](#delete-conversation-member) | Remove um membro de uma conversa. |
| [Obter membros da atividade](#get-activity-members) | Obtém os membros da atividade especificada na conversa especificada. |
| [Obter membro da conversa](#get-conversation-member) | Obtém detalhes sobre um membro de uma conversa. |
| [Obter membros da conversa](#get-conversation-members) | Obtém os membros da conversa especificada. |
| [Obter membros paginados da conversa](#get-conversation-paged-members) | Obtém os membros da conversa especificada, uma página de cada vez. |
| [Obter conversas](#get-conversations) | Obtém uma lista das conversas de que o bot participou. |
| [Responder a atividade](#reply-to-activity) | Envia uma atividade (mensagem) para a conversa especificada, como uma resposta à atividade especificada. |
| [Enviar histórico da conversa](#send-conversation-history) | Carrega uma transcrição de atividades anteriores para a conversa. |
| [Enviar para conversa](#send-to-conversation) | Envia uma atividade (mensagem) para o final da conversa especificada. |
| [Atualizar atividade](#update-activity) | Atualiza uma atividade existente. |
| [Carregar anexo ao canal](#upload-attachment-to-channel) | Carrega um anexo diretamente no armazenamento de blobs de um canal. |

### <a name="create-conversation"></a>Criar conversa

Cria uma nova conversa.

```http
POST /v3/conversations
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [ConversationParameters](#conversationparameters-object) |
| **Retorna** | Um objeto [ConversationResourceResponse](#conversationresourceresponse-object) |

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

### <a name="get-activity-members"></a>Obter membros da atividade

Obtém os membros da atividade especificada na conversa especificada.

```http
GET /v3/conversations/{conversationId}/activities/{activityId}/members
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma matriz de objetos [ChannelAccount](#channelaccount-object) |

### <a name="get-conversations"></a>Obter conversas

Obtém uma lista das conversas de que o bot participou.

```http
GET /v3/conversations?continuationToken={continuationToken}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [ConversationsResult](#conversationsresult-object) |

### <a name="get-conversation-member"></a>Obter membro da conversa

Obtém detalhes sobre um membro específico de uma conversa específica.

```http
GET /v3/conversations/{conversationId}/members/{memberId}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [ChannelAccount](#channelaccount-object) para o membro. |

### <a name="get-conversation-members"></a>Obter membros da conversa

Obtém os membros da conversa especificada.

```http
GET /v3/conversations/{conversationId}/members
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Uma matriz de objetos [ChannelAccount](#channelaccount-object) para os membros da conversa. |

### <a name="get-conversation-paged-members"></a>Obter membros paginados da conversa

Obtém os membros da conversa especificada, uma página de cada vez.

```http
GET /v3/conversations/{conversationId}/pagedmembers?pageSize={pageSize}&continuationToken={continuationToken}
```

| | |
|----|----|
| **Corpo da solicitação** | n/d |
| **Retorna** | Um objeto [PagedMembersResult](#pagedmembersresult-object) |

### <a name="reply-to-activity"></a>Responder a atividade

Envia uma atividade (mensagem) para a conversa especificada, como uma resposta à atividade especificada. A atividade será adicionada como resposta a outra atividade, se houver suporte para isso no canal. Se o canal não der suporte a respostas aninhadas, essa operação se comportará como [Enviar para conversa](#send-to-conversation).

```http
POST /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#activity-object) |
| **Retorna** | Um objeto [ResourceResponse](#resourceresponse-object) |

### <a name="send-conversation-history"></a>Enviar histórico da conversa

Carrega uma transcrição de atividades anteriores para a conversa para que o cliente possa renderizá-las.

```http
POST /v3/conversations/{conversationId}/activities/history
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Transcript](#transcript-object). |
| **Retorna** | Um objeto [ResourceResponse](#resourceresponse-object). |

### <a name="send-to-conversation"></a>Enviar para conversa

Envia uma atividade (mensagem) para a conversa especificada. A atividade será anexada ao final da conversa, de acordo com o carimbo de data/hora ou semântica do canal. Para responder a uma mensagem específica na conversa, use [Responder a atividade](#reply-to-activity).

```http
POST /v3/conversations/{conversationId}/activities
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#activity-object) |
| **Retorna** | Um objeto [ResourceResponse](#resourceresponse-object) |

### <a name="update-activity"></a>Atualizar atividade

Alguns canais permitem editar uma atividade existente para refletir o novo estado de uma conversa de bot. Por exemplo, será possível remover botões de uma mensagem na conversa depois que o usuário clicar em um dos botões. Se tiver êxito, essa operação atualizará a atividade especificada dentro da conversa especificada.

```http
PUT /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [Atividade](#activity-object) |
| **Retorna** | Um objeto [ResourceResponse](#resourceresponse-object) |

### <a name="upload-attachment-to-channel"></a>Carregar anexo ao canal

Carrega um anexo para a conversa especificada diretamente no armazenamento de blobs de um canal. Isso permite que você armazene dados em um armazenamento compatível.

```http
POST /v3/conversations/{conversationId}/attachments
```

| | |
|----|----|
| **Corpo da solicitação** | Um objeto [AttachmentData](#attachmentdata-object). |
| **Retorna** | Um objeto [ResourceResponse](#resourceresponse-object). A propriedade **id** especifica a ID do anexo que pode ser usado com a operação [Obter Informações do Anexo](#get-attachment-info) e a operação [Obter Anexo](#get-attachment). |

## <a name="attachment-operations"></a>Operações de Anexo

Use essas operações para recuperar informações sobre um anexo, bem como os dados binários para o próprio arquivo.

| Operação | Descrição |
|----|----|
| [Obter Informações do Anexo](#get-attachment-info) | Obtém informações sobre o anexo especificado, incluindo o nome e o tipo de arquivo e os modos de exibição disponíveis (por exemplo, original ou miniatura). |
| [Obter anexo](#get-attachment) | Obtém a exibição especificada do anexo especificado como conteúdo binário. |

### <a name="get-attachment-info"></a>Obter informações do anexo

Obtém informações sobre o anexo especificado, incluindo o nome do arquivo, o tipo e os modos de exibição disponíveis (por exemplo, original ou miniatura).

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

## <a name="state-operations-deprecated"></a>Operações de estado (preterido)

O serviço de Estado do Microsoft Bot Framework foi desativado em 30 de março de 2018. Anteriormente, bots criados no Serviço de Bot do Azure ou no SDK do Bot Builder tinham uma conexão padrão com esse serviço hospedado pela Microsoft para armazenar dados de estado do bot. Os bots deverão ser atualizados para usar seu próprio armazenamento de estado.

| Operação | Descrição |
|----|----|
| `Set User Data` | Armazena dados de estado para um usuário específico em um canal. |
| `Set Conversation Data` | Armazena dados de estado para uma conversa específica em um canal. |
| `Set Private Conversation Data` | Armazena dados de estado para um usuário específico no contexto de uma conversa específica em um canal. |
| `Get User Data` | Recupera dados de estado que foram armazenados anteriormente para um usuário específico em todas as conversas em um canal. |
| `Get Conversation Data` | Recupera dados de estado que foram armazenados anteriormente para uma conversa específica em um canal. |
| `Get Private Conversation Data` | Recupera dados de estado que foram armazenados anteriormente para um usuário específico no contexto de uma conversa específica em um canal. |
| `Delete State For User` | Exclui os dados de estado que foram armazenados para um usuário. |

## <a name="schema"></a>Esquema

O esquema do Bot Framework define os objetos e suas propriedades que seu bot pode usar para se comunicar com um usuário.

| Objeto | Descrição |
| ---- | ---- |
| [Objeto Activity](#activity-object) | Define uma mensagem trocada entre o bot e o usuário. |
| [Objeto AnimationCard](#animationcard-object) | Define um cartão que pode reproduzir GIFs animados ou vídeos curtos. |
| [Objeto Attachment](#attachment-object) | Define informações adicionais para incluir na mensagem. Um anexo pode ser um arquivo de mídia (por exemplo, áudio, vídeo, imagem, arquivo) ou um cartão avançado. |
| [Objeto AttachmentData](#attachmentdata-object) | Descreve um anexo de dados. |
| [Objeto AttachmentInfo](#attachmentinfo-object) | Descreve um anexo. |
| [Objeto AttachmentView](#attachmentview-object) | Define um modo de exibição de anexo. |
| [Objeto AudioCard](#audiocard-object) | Define um cartão que pode reproduzir um arquivo de áudio. |
| [Objeto CardAction](#cardaction-object) | Define uma ação para executar. |
| [Objeto CardImage](#cardimage-object) | Define uma imagem para exibir em um cartão. |
| [Objeto ChannelAccount](#channelaccount-object) | Define um bot ou uma conta de usuário no canal. |
| [Objeto ConversationAccount](#conversationaccount-object) | Define uma conversa em um canal. |
| [Objeto ConversationMembers](#conversationmembers-object) | Define os membros de uma conversa. |
| [Objeto ConversationParameters](#conversationparameters-object) | Definir parâmetros para criar uma nova conversa |
| [Objeto ConversationReference](#conversationreference-object) | Define um ponto específico em uma conversa. |
| [Objeto ConversationResourceResponse](#conversationresourceresponse-object) | Define uma resposta a [Criar conversa](#create-conversation). |
| [Objeto ConversationsResult](#conversationsresult-object) | Define o resultado de uma chamada para [Obter conversas](#get-conversations). |
| [Objeto Entity](#entity-object) | Define um objeto de entidade. |
| [Objeto Error](#error-object) | Define um erro. |
| [Objeto ErrorResponse](#errorresponse-object) | Define uma resposta de API HTTP. |
| [Objeto Fact](#fact-object) | Define um par chave-valor que contém um fato. |
| [Objeto GeoCoordinates](#geocoordinates-object) | Define uma localização geográfica usando as coordenadas WSG84 (World Geodetic System). |
| [Objeto HeroCard](#herocard-object) | Define um cartão com uma imagem grande, título, texto e botões de ação. |
| [Objeto InnerHttpError](#innerhttperror-object) | Objeto representando um erro HTTP interno. |
| [Objeto MediaEventValue](#mediaeventvalue-object) | Parâmetro suplementar para eventos de mídia. |
| [Objeto MediaUrl](#mediaurl-object) | Define a URL para a fonte de um arquivo de mídia. |
| [Objeto Mention](#mention-object) | Define um usuário ou bot que foi mencionado na conversa. |
| [Objeto MessageReaction](#messagereaction-object) | Define uma reação a uma mensagem. |
| [Objeto PagedMembersResult](#pagedmembersresult-object) | Página de membros retornada por [Obter Membros Paginados da Conversa](#get-conversation-paged-members). |
| [Objeto Place](#place-object) | Define um local que foi mencionado na conversa. |
| [Objeto ReceiptCard](#receiptcard-object) | Define um cartão que contém um recibo para uma compra. |
| [Objeto ReceiptItem](#receiptitem-object) | Define um item de linha dentro de um recibo. |
| [Objeto ResourceResponse](#resourceresponse-object) | Define um recurso. |
| [Objeto SemanticAction](#semanticaction-object) | Define uma referência a uma ação através programática. |
| [Objeto SignInCard](#signincard-object) | Define um cartão que permite ao usuário entrar em um serviço. |
| [Objeto SuggestedActions](#suggestedactions-object) | Define as opções que um usuário pode escolher. |
| [Objeto TextHighlight](#texthighlight-object) | Refere-se a uma substring de conteúdo dentro de outro campo. |
| [Objeto ThumbnailCard](#thumbnailcard-object) | Define um cartão com uma imagem em miniatura, título, texto e botões de ação. |
| [Objeto ThumbnailUrl](#thumbnailurl-object) | Define a URL para a fonte de uma imagem. |
| [Objeto Transcript](#transcript-object) | Uma coleção de atividades a ser carregada usando [Enviar histórico da conversa](#send-conversation-history). |
| [Objeto VideoCard](#videocard-object) | Define um cartão que pode reproduzir vídeos. |

### <a name="activity-object"></a>Objeto de Atividade

Define uma mensagem trocada entre o bot e o usuário.

| Propriedade | Type | Descrição |
|----|----|----|
| **action** | string | A ação a aplicar ou que foi aplicada. Use a propriedade **tipo** para determinar o contexto da ação. Por exemplo, se **tipo** for **contactRelationUpdate**, o valor da propriedade **ação** será **adicionar** se o usuário adicionou o bot à lista de contatos ou **remover** se o usuário removeu o bot da lista de contatos. |
| **attachmentLayout** | string | Layout dos **anexos** de cartão avançado que a mensagem inclui. Um desses valores: **carrossel**, **lista**. Para obter mais informações sobre anexos de cartão avançado, consulte [Adicionar anexos de cartão avançado às mensagens](bot-framework-rest-connector-add-rich-cards.md). |
| **attachments** | [Attachment](#attachment-object)[] | Matriz de objetos **Anexo** que define informações adicionais para incluir na mensagem. Cada anexo pode ser um arquivo (por exemplo, áudio, vídeo, imagem) ou um cartão avançado. |
| **callerId** | string | Uma cadeia de caracteres que contém um IRI que identifica o chamador de um bot. Este campo não se destina a ser transmitido eletronicamente; em vez disso, ele é preenchido por bots e clientes com base em dados verificáveis criptograficamente que declara a identidade dos chamadores (por exemplo, tokens). |
| **channelData** | objeto | Um objeto que contém conteúdo específico do canal. Alguns canais fornecem recursos que exigem informações adicionais que não podem ser representadas usando o esquema de anexo. Para esses casos, defina essa propriedade para o conteúdo específico do canal, conforme definido na documentação do canal. Para obter mais informações, consulte [Implementar funcionalidade específica do canal](bot-framework-rest-connector-channeldata.md). |
| **channelId** | string | Uma ID que identifica exclusivamente o canal. Definida pelo canal. |
| **code** | string | Código indicando por que a conversa encerrou. |
| **conversa** | [ConversationAccount](#conversationaccount-object) | Um objeto **ConversationAccount** que define a conversa à qual a atividade pertence. |
| **deliveryMode** | string | Uma dica de entrega para sinalizar os caminhos de entrega alternativos do destinatário para a atividade. Um desses valores: **normal**, **notification**. |
| **entidades** | object[] | Matriz de objetos que representa as entidades mencionadas na mensagem. Objetos nessa matriz podem ser qualquer objeto [Schema.org](http://schema.org/). Por exemplo, a matriz pode incluir objetos [Menção](#mention-object) que identificam alguém que foi mencionado na conversa e objetos [Local](#place-object) que identificam um local mencionado na conversa. |
| **expiration** | string | A hora na qual a atividade deve ser considerada "expirada" e não deve ser apresentada ao destinatário. |
| **from** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que especifica o remetente da mensagem. |
| **historyDisclosed** | booleano | Sinalizador que indica se o histórico é ou não divulgado. O valor padrão é **false**. |
| **id** | string | ID que identifica exclusivamente a atividade no canal. |
| **importance** | string | Define a importância de uma Atividade. Um destes valores: **low**, **normal**, **high**. |
| **inputHint** | string | Valor que indica se o bot está aceitando, esperando ou ignorando a entrada do usuário após a entrega da mensagem ao cliente. Um desses valores: **acceptingInput**, **expectingInput**, **ignoringInput**. |
| **label** | string | Um rótulo descritivo da atividade. |
| **listenFor** | string[] | Lista de frases e referências que os sistemas de preparação da fala e de linguagem devem escutar. |
| **locale** | string | Localidade do idioma que deve ser usado para exibir texto dentro da mensagem, no formato `<language>-<country>`. O canal usa essa propriedade para indicar o idioma do usuário, para que o bot possa especificar cadeia de caracteres de exibição nesse idioma. O valor padrão é **en-US**. |
| **localTimestamp** | string | Data e hora em que a mensagem foi enviada no fuso horário local, expressa no formato [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601). |
| **localTimezone** | string | Contém o nome do fuso horário local da mensagem, expresso no formato de banco de dados de fuso horário IANA. Por exemplo, America/Los_Angeles. |
| **membersAdded** | [ChannelAccount](#channelaccount-object)[] | Matriz de objetos **ChannelAccount** que representam a lista de usuários que participaram da conversa. Presente apenas se a atividade **tipo** for "conversationUpdate" e os usuários ingressarem na conversa. |
| **membersRemoved** | [ChannelAccount](#channelaccount-object)[] | Matriz de objetos **ChannelAccount** que representam a lista de usuários que saíram da conversa. Presente apenas se a atividade **tipo** for "conversationUpdate" e os usuários saírem da conversa. |
| **name** | string | Nome da operação a invocar ou o nome do evento. |
| **reactionsAdded** | [MessageReaction](#messagereaction-object)[] | A coleção de reações adicionadas à conversa. |
| **reactionsRemoved** | [MessageReaction](#messagereaction-object)[] | A coleção de reações removidas da conversa. |
| **recipient** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que especifica o destinatário da mensagem. |
| **relatesTo** | [ConversationReference](#conversationreference-object) | Um objeto **ConversationReference** que define um ponto específico em uma conversa. |
| **replyToId** | string | A ID da mensagem à qual essa mensagem responde. Para responder a uma mensagem que o usuário enviou, defina essa propriedade como a ID da mensagem do usuário. Nem todos os canais dão suporte a respostas encadeadas. Nesses casos, o canal ignorará essa propriedade e usará a semântica ordenada por tempo (carimbo de data/hora) para acrescentar a mensagem à conversa. |
| **semanticAction** |[SemanticAction](#semanticaction-object) | Um objeto **SemanticAction** que representa uma referência a uma ação programática. |
| **serviceUrl** | string | URL que especifica o ponto de extremidade de serviço do canal. Definida pelo canal. |
| **speak** | string | Texto a ser falado pelo bot em um canal habilitado para fala. Para controlar várias características de fala do bot como voz, velocidade, volume, pronúncia e tom, especifique essa propriedade no formato [SSML (Linguagem de Marcação de Sintetização de Voz)](https://msdn.microsoft.com/library/hh378377(v=office.14).aspx). |
| **suggestedActions** | [SuggestedActions](#suggestedactions-object) | Um objeto **SuggestedActions** que define as opções a partir das quais o usuário poderá escolher. |
| **summary** | string | Resumo das informações que a mensagem contém. Por exemplo, para uma mensagem enviada em um canal de email, essa propriedade pode especificar os primeiros 50 caracteres da mensagem de email. |
| **text** | string | Texto da mensagem que é enviada do usuário para o bot ou do bot para o usuário. Consulte a documentação do canal para os limites impostos sobre o conteúdo dessa propriedade. |
| **textFormat** | string | Formato do **texto** da mensagem. Um desses valores: **markdown**, **plain**, **xml**. Para detalhes sobre o formato de texto, consulte [Criar mensagens](bot-framework-rest-connector-create-messages.md). |
| **textHighlights** | [TextHighlight](#texthighlight-object)[] | A coleção de fragmentos de texto a serem realçados quando a atividade contém um valor **replyToId**. |
| **timestamp** | string | Data e hora em que a mensagem foi enviada no fuso horário UTC, expressa no formato [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601). |
| **topicName** | string | Tópico da conversa à qual a atividade pertence. |
| **tipo** | string | Tipo de atividade. Um destes valores: **message**, **contactRelationUpdate**, **conversationUpdate**, **typing**, **endOfConversation**, **event**, **invoke**, **deleteUserData**, **messageUpdate**, **messageDelete**, **installationUpdate**, **messageReaction**, **suggestion**, **trace**, **handoff**. Para detalhes sobre tipos de atividade, consulte [Visão geral das atividades](https://aka.ms/botSpecs-activitySchema). |
| **value** | objeto | Valor em aberto. |
| **valueType** | string | O tipo do objeto de valor da atividade. |

[Retornar à tabela de esquemas](#schema)

### <a name="animationcard-object"></a>Objeto AnimationCard

Define um cartão que pode reproduzir GIFs animados ou vídeos curtos.

| Propriedade | Type | Descrição |
|----|----|----|
| **aspect** | booleano | Taxa de proporção do espaço reservado de miniatura/mídia. Os valores permitidos são “16:9” e “4:3”. |
| **autoloop** | booleano | Sinalizador que indica se deve repetir a lista de GIFs animados quando o último encerrar. Configure essa propriedade como **true** para reproduzir automaticamente a animação, caso contrário, como **false**. O valor padrão é **true**. |
| **autostart** | booleano | Sinalizador que indica se deverá reproduzir automaticamente a animação quando o cartão for exibido. Defina essa propriedade como **true** para reproduzir automaticamente a animação, caso contrário, como **false**. O valor padrão é **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **duration** | string | O comprimento do conteúdo de mídia, no [formato de duração ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). |
| **imagem** | [ThumbnailUrl](#thumbnailurl-object) | Um objeto **ThumbnailUrl** que especifica a imagem a ser exibida no cartão. |
| **media** | [MediaUrl](#mediaurl-object)[] | Matriz de objetos **MediaUrl**. Quando esse campo contém mais de uma URL, cada URL é um formato alternativo do mesmo conteúdo.|
| **shareable** | booleano | Sinalizador que indica se a animação pode ser compartilhada com outras pessoas. Configure esta propriedade para **true** se a animação puder ser compartilhada, caso contrário, para **false**. O valor padrão é **true**. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |
| **value** | objeto | Parâmetro suplementar para esse cartão. |

[Retornar à tabela de esquemas](#schema)

### <a name="attachment-object"></a>Objeto Anexo

Define informações adicionais para incluir na mensagem. Um anexo pode ser um arquivo (como uma imagem, áudio ou vídeo) ou um cartão avançado.

| Propriedade | Type | Descrição |
|----|----|----|
| **content** | objeto | O conteúdo do anexo. Se o anexo for um cartão avançado, defina essa propriedade para o objeto cartão avançado. Essa propriedade e a propriedade **contentUrl** são mutuamente exclusivas. |
| **contentType** | string | O tipo de mídia do conteúdo no anexo. Para arquivos de mídia, defina essa propriedade para tipos de mídia conhecidos, como **image/png**, **audio/wav** e **video/mp4**. Para cartões avançados, defina essa propriedade para um desses tipos específicos do fornecedor:<ul><li>**application/vnd.microsoft.card.adaptive**: Um cartão sofisticado que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. Defina a propriedade de **conteúdo** para um objeto [AdaptiveCard](https://adaptivecards.io/explorer/AdaptiveCard.html).</li><li>**application/vnd.microsoft.card.animation**: Um cartão sofisticado que reproduz animação. Defina a propriedade de **conteúdo** para um objeto [AnimationCard](#animationcard-object).</li><li>**application/vnd.microsoft.card.audio**: Um cartão sofisticado que reproduz arquivos de áudio. Defina a propriedade de **conteúdo** como um objeto [AudioCard](#audiocard-object).</li><li>**application/vnd.microsoft.card.hero**: Um cartão em destaque. Defina e propriedade de **conteúdo** para um objeto [HeroCard](#herocard-object).</li><li>**application/vnd.microsoft.card.receipt**: Um cartão de recibo. Defina e propriedade de **conteúdo** para um objeto [ReceiptCard](#receiptcard-object).</li><li>**application/vnd.microsoft.card.signin**: Um cartão de entrada do usuário. Defina e propriedade de **conteúdo** para um objeto [SignInCard](#signincard-object).</li><li>**application/vnd.microsoft.card.thumbnail**: Um cartão em miniatura. Defina a propriedade de **conteúdo** para um objeto [ThumbnailCard](#thumbnailcard-object).</li><li>**application/vnd.microsoft.card.video**: Um cartão sofisticado que reproduz vídeos. Defina a propriedade de **conteúdo** para um objeto [VideoCard](#videocard-object).</li></ul> |
| **contentUrl** | string | URL para o conteúdo do anexo. Por exemplo, se o anexo for uma imagem, será possível definir **contentUrl** como a URL que representa o local da imagem. Os protocolos com suporte são: HTTP, HTTPS, Arquivo e Dados. |
| **name** | string | Nome do anexo. |
| **thumbnailUrl** | string | URL para uma imagem em miniatura que o canal pode usar se for compatível com o uso de uma forma alternativa menor de **conteúdo** ou **contentUrl**. Por exemplo, se você definir **contentType** como **application/word** e definir **contentUrl** como o local do documento do Word, talvez inclua uma imagem em miniatura que represente o documento. O canal pode exibir a imagem em miniatura em vez do documento. Quando o usuário clicar na imagem, o canal abrirá o documento. |

[Retornar à tabela de esquemas](#schema)

### <a name="attachmentdata-object"></a>Objeto AttachmentData

Descreve os dados de um anexo.

| Propriedade | Type | Descrição |
|----|----|----|
| **name** | string | Nome do anexo. |
| **originalBase64** | string | Conteúdo do anexo. |
| **thumbnailBase64** | string | Conteúdo em miniatura do anexo. |
| **tipo** | string | Tipo de conteúdo do anexo. |

[Retornar à tabela de esquemas](#schema)

### <a name="attachmentinfo-object"></a>Objeto AttachmentInfo

Metadados de um anexo.

| Propriedade | Type | Descrição |
|----|----|----|
| **name** | string | Nome do anexo. |
| **tipo** | string | Tipo de conteúdo do anexo. |
| **modos de exibição** | [AttachmentView](#attachmentview-object)[] | Matriz de objetos **AttachmentView** que representam as exibições disponíveis para o anexo. |

[Retornar à tabela de esquemas](#schema)

### <a name="attachmentview-object"></a>Objeto AttachmentView

Define um modo de exibição de anexo.

| Propriedade | Type | Descrição |
|----|----|----|
| **size** | número | Tamanho do arquivo. |
| **viewId** | string | ID da exibição. |

[Retornar à tabela de esquemas](#schema)

### <a name="audiocard-object"></a>Objeto AudioCard

Define um cartão que pode reproduzir um arquivo de áudio.

| Propriedade | Type | Descrição |
|----|----|----|
| **aspect** | string | Proporção da miniatura especificada na propriedade da **imagem**. Os valores válidos são **16:9** e **4:3**. |
| **autoloop** | booleano | Sinalizador que indica se deverá reproduzir a lista de arquivos de áudio quando o último encerrar. Defina essa propriedade como **true** para reproduzir automaticamente os arquivos de áudio, caso contrário, como **false**. O valor padrão é **true**. |
| **autostart** | booleano | Sinalizador que indica se deverá reproduzir automaticamente o áudio quando o cartão for exibido. Defina essa propriedade como **true** para reproduzir automaticamente o áudio, caso contrário, como **false**. O valor padrão é **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **duration** | string | O comprimento do conteúdo de mídia, no [formato de duração ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). |
| **imagem** | [ThumbnailUrl](#thumbnailurl-object) | Um objeto **ThumbnailUrl** que especifica a imagem a ser exibida no cartão. |
| **media** | [MediaUrl](#mediaurl-object)[] | Matriz de objetos **MediaUrl**.  Quando esse campo contém mais de uma URL, cada URL é um formato alternativo do mesmo conteúdo. |
| **shareable** | booleano | Sinalizador que indica se os arquivos de áudio podem ser compartilhados com outras pessoas. Defina essa propriedade como **true** se o áudio puder ser compartilhado, caso contrário, **false**. O valor padrão é **true**. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |
| **value** | objeto | Parâmetro suplementar para esse cartão. |

[Retornar à tabela de esquemas](#schema)

### <a name="cardaction-object"></a>Objeto CardAction

Define uma ação que pode ser clicada com um botão.

| Propriedade | Type | Descrição |
|----|----|----|
| **channelData** | string | Dados específicos do canal associados a esta ação. |
| **displayText** | string | Texto a ser exibido no feed do chat se o botão receber cliques. |
| **imagem** | string | URL da imagem que será exibida no botão, ao lado do rótulo de texto. |
| **text** | string | Texto da ação. |
| **title** | string | Descrição de texto exibida no botão. |
| **tipo** | string | Tipo de ação a ser executada. Para obter uma lista de valores válidos, consulte [Adicionar anexos de cartão avançado às mensagens](bot-framework-rest-connector-add-rich-cards.md). |
| **value** | objeto | Parâmetro suplementar para a ação. O comportamento dessa propriedade variará de acordo com a ação **tipo**. Para obter mais informações, consulte [Adicionar anexos de cartão avançado às mensagens](bot-framework-rest-connector-add-rich-cards.md). |

[Retornar à tabela de esquemas](#schema)

### <a name="cardimage-object"></a>Objeto CardImage

Define uma imagem para exibir em um cartão.

| Propriedade | Type | Descrição |
|----|----|----|
| **alt** | string | Descrição da imagem. É necessário incluir a descrição para dar suporte à acessibilidade. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especificará a ação a ser executada se o usuário tocar ou clicar na imagem. |
| **url** | string | URL para a origem da imagem ou o binário base64 da imagem (por exemplo, `data:image/png;base64,iVBORw0KGgo...`). |

[Retornar à tabela de esquemas](#schema)

### <a name="channelaccount-object"></a>Objeto ChannelAccount

Define um bot ou uma conta de usuário no canal.

| Propriedade | Type | Descrição |
|----|----|----|
| **aadObjectId** | string | A ID de objeto desta conta no Azure Active Directory. |
| **id** | string | ID exclusiva para o usuário ou bot neste canal. |
| **name** | string | Exibir nome amigável do bot ou do usuário. |
| **role** | string | Função da entidade por trás da conta. **Usuário** ou **bot**. |

[Retornar à tabela de esquemas](#schema)

### <a name="conversationaccount-object"></a>Objeto ConversationAccount

Define uma conversa em um canal.

| Propriedade | Type | Descrição |
|----|----|----|
| **aadObjectId** | string | A ID de objeto da conta dentro do AAD (Azure Active Directory). |
| **conversationType** | string | Indica o tipo de conversa nos canais que diferenciam os tipos de conversas (por exemplo, grupo, pessoal). |
| **id** | string | A ID que identifica a conversa. A ID é exclusiva por canal. Se o canal iniciar a conversa, ele definirá essa ID, caso contrário, o bot definirá essa propriedade como a ID que será retornada na resposta quando iniciar a conversa (confira [Criar Conversa](#create-conversation)). |
| **isGroup** | booleano | Sinalizar para indicar se a conversa contém mais de dois participantes no momento em que a atividade foi gerada. Defina como **true** se esta for uma conversa em grupo, caso contrário, como **false**. O padrão é **false**. |
| **name** | string | Um nome de exibição que pode ser usado para identificar a conversa. |
| **role** | string | Função da entidade por trás da conta. **Usuário** ou **bot**. |
| **tenantId** | string | A ID de locatário da conversa. |

[Retornar à tabela de esquemas](#schema)

### <a name="conversationmembers-object"></a>Objeto ConversationMembers

Define os membros de uma conversa.

| Propriedade | Type | Descrição |
|----|----|----|
| **id** | string | A ID da conversa. |
| **members** | [ChannelAccount](#channelaccount-object)[] | Lista de membros nesta conversa. |

[Retornar à tabela de esquemas](#schema)

### <a name="conversationparameters-object"></a>Objeto ConversationParameters

Define parâmetros para criar uma conversa.

| Propriedade | Type | Descrição |
|----|----|----|
| **activity** | [Atividade](#activity-object) | A mensagem inicial a ser enviada para a conversa quando ela for criada. |
| **bot** | [ChannelAccount](#channelaccount-object) | Informações de conta do canal necessárias para rotear uma mensagem para o bot. |
| **channelData** | objeto | Conteúdo específico do canal para criar a conversa. |
| **isGroup** | booleano | Indica se essa é uma conversa em grupo. |
| **members** | [ChannelAccount](#channelaccount-object)[] | Informações de conta do canal necessárias para rotear uma mensagem para cada usuário. |
| **tenantId** | string | A ID de locatário na qual a conversa deve ser criada. |
| **topicName** | string | Tópico da conversa. Essa propriedade será usada apenas se um canal der suporte. |

[Retornar à tabela de esquemas](#schema)

### <a name="conversationreference-object"></a>Objeto ConversationReference

Define um ponto específico em uma conversa.

| Propriedade | Type | Descrição |
|----|----|----|
| **activityId** | string | ID que identifica exclusivamente a atividade à qual esse objeto faz referência. |
| **bot** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que identifica o bot na conversa à qual esse objeto faz referência. |
| **channelId** | string | Uma ID que identifica exclusivamente o canal na conversa à qual esse objeto faz referência. |
| **conversa** | [ConversationAccount](#conversationaccount-object) | Um objeto **ConversationAccount** que define a conversação à qual esse objeto faz referência. |
| **serviceUrl** | string | URL que especifica o ponto de extremidade de serviço do canal na conversa à qual esse objeto faz referência. |
| **user** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que identifica o usuário na conversa à qual esse objeto faz referência. |

[Retornar à tabela de esquemas](#schema)

### <a name="conversationresourceresponse-object"></a>Objeto ConversationResourceResponse

Define uma resposta a [Criar conversa](#create-conversation).

| Propriedade | Type | Descrição |
|----|----|----|
| **activityId** | string | ID da atividade, se enviada. |
| **id** | string | ID do recurso. |
| **serviceUrl** | string | Ponto de extremidade de serviço em que as operações relacionadas à conversa podem ser executadas. |

[Retornar à tabela de esquemas](#schema)

### <a name="conversationsresult-object"></a>Objeto ConversationsResult

Define o resultado de [Obter conversas](#get-conversations).

| Propriedade | Type | Descrição |
|----|----|----|
| **conversations** | [ConversationMembers](#conversationmembers-object)[] | Os membros em cada uma das conversas. |
| **continuationToken** | string | O token de continuação que pode ser usado em chamadas posteriores a [Obter conversas](#get-conversations). |

[Retornar à tabela de esquemas](#schema)

### <a name="entity-object"></a>Objeto de entidade

Objeto de metadados pertencente a uma atividade.

| Propriedade | Type | Descrição |
|----|----|----|
| **tipo** | string | Tipo desta entidade (RFC 3987 IRI). |

[Retornar à tabela de esquemas](#schema)

### <a name="error-object"></a>Objeto de erro

Objeto representando informações de erro.

| Propriedade | Type | Descrição |
|----|----|----|
| **code** | string | Código do erro. |
| **innerHttpError** | [InnerHttpError](#innerhttperror-object) | Objeto representando o erro HTTP interno. |
| **message** | string | Uma descrição do erro. |

[Retornar à tabela de esquemas](#schema)

### <a name="errorresponse-object"></a>Objeto ErrorResponse

Define uma resposta de API HTTP.

| Propriedade | Type | Descrição |
|----|----|----|
| **error** | [Erro](#error-object) | Um objeto **Erro** que contém informações sobre o erro. |

[Retornar à tabela de esquemas](#schema)

### <a name="fact-object"></a>Objeto Fato

Define um par chave-valor que contém um fato.

| Propriedade | Type | Descrição |
|----|----|----|
| **chave** | string | Nome do fato. Por exemplo, **Check-in**. A chave é usada como um rótulo ao exibir o valor do fato. |
| **value** | string | Valor do fato. Por exemplo, **10 de outubro de 2016**. |

[Retornar à tabela de esquemas](#schema)

### <a name="geocoordinates-object"></a>Objeto GeoCoordinates

Define uma localização geográfica usando as coordenadas WSG84 (World Geodetic System).

| Propriedade | Type | Descrição |
|----|----|----|
| **elevation** | número | Elevação da localização. |
| **latitude** | número | Latitude da localização. |
| **longitude** | número | Longitude da localização. |
| **name** | string | Nome da localização. |
| **tipo** | string | O tipo desse objeto. Sempre definido para **GeoCoordinates**. |

[Retornar à tabela de esquemas](#schema)

### <a name="herocard-object"></a>Objeto HeroCard

Define um cartão com uma imagem grande, título, texto e botões de ação.

| Propriedade | Type | Descrição |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **images** | [CardImage](#cardimage-object)[] | Matriz de objetos **CardImage** que especifica a imagem a ser exibida no cartão. Um cartão Hero contém apenas uma imagem. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no cartão. Essa pode ser a mesma ação que um dos botões ou uma ação diferente. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |

[Retornar à tabela de esquemas](#schema)

### <a name="innerhttperror-object"></a>Objeto InnerHttpError

Objeto representando um erro HTTP interno.

| Propriedade | Type | Descrição |
|----|----|----|
| **statusCode** | número | Código de status HTTP da solicitação com falha. |
| **body** | objeto | Corpo da solicitação com falha. |

[Retornar à tabela de esquemas](#schema)

### <a name="mediaeventvalue-object"></a>Objeto MediaEventValue

Parâmetro suplementar para eventos de mídia.

| Propriedade | Type | Descrição |
|----|----|----|
| **cardValue** | objeto | Parâmetro de retorno de chamada especificado no campo **value** do cartão de mídia que originou esse evento. |

[Retornar à tabela de esquemas](#schema)

### <a name="mediaurl-object"></a>Objeto MediaUrl

Define a URL para a fonte de um arquivo de mídia.

| Propriedade | Type | Descrição |
|----|----|----|
| **profile** | string | Dica que descreve o conteúdo da mídia. |
| **url** | string | URL para a origem do arquivo de mídia. |

[Retornar à tabela de esquemas](#schema)

### <a name="mention-object"></a>Objeto Mention

Define um usuário ou bot que foi mencionado na conversa.

| Propriedade | Type | Descrição |
|----|----|----|
| **mentioned** | [ChannelAccount](#channelaccount-object) | Um objeto **ChannelAccount** que especifica o usuário ou o bot que foi mencionado. Observe que alguns canais, como Slack, atribuem nomes por conversa, portanto, é possível que o nome mencionado do bot (na propriedade do **destinatário** ) seja diferente do identificador especificado quando você [registrou](../bot-service-quickstart-registration.md) o bot. No entanto, as IDs da conta para ambos seriam as mesmas. |
| **text** | string | O usuário ou bot como mencionado na conversa. Por exemplo, se a mensagem for "@ColorBot escolha uma nova cor," essa propriedade será definida como **@ColorBot** . Nem todos os canais definem essa propriedade. |
| **tipo** | string | Tipo desse objeto. Sempre defina para **Menção**. |

[Retornar à tabela de esquemas](#schema)

### <a name="messagereaction-object"></a>Objeto MessageReaction

Define uma reação a uma mensagem.

| Propriedade | Type | Descrição |
|----|----|----|
| **tipo** | string | Tipo de reação. **like** ou **plusOne**. |

[Retornar à tabela de esquemas](#schema)

### <a name="pagedmembersresult-object"></a>Objeto PagedMembersResult

Página de membros retornada por [Obter Membros Paginados da Conversa](#get-conversation-paged-members).

| Propriedade | Type | Descrição |
|----|----|----|
| **continuationToken** | string | O token de continuação que pode ser usado em chamadas posteriores a [Obter Membros Paginados da Conversa](#get-conversation-paged-members). |
| **members** | [ChannelAccount](#channelaccount-object)[] | Uma matriz de membros de conversa. |

[Retornar à tabela de esquemas](#schema)

### <a name="place-object"></a>Objeto Local

Define um local que foi mencionado na conversa.

| Propriedade | Type | Descrição |
|----|----|----|
| **address** | objeto |  Endereço de um local. Essa propriedade pode ser uma **cadeia de caracteres** ou um objeto complexo do tipo **PostalAddress**. |
| **geo** | [GeoCoordinates](#geocoordinates-object) | Um objeto **GeoCoordinates** que especifica as coordenadas geográficas do lugar. |
| **hasMap** | objeto | Mapa para o local. Essa propriedade pode ser uma **cadeia de caracteres** (URL) ou um objeto complexo do tipo **Map**. |
| **name** | string | Nome do local. |
| **tipo** | string | Tipo desse objeto. Sempre defina para **Local**. |

[Retornar à tabela de esquemas](#schema)

### <a name="receiptcard-object"></a>Objeto ReceiptCard

Define um cartão que contém um recibo para uma compra.

| Propriedade | Type | Descrição |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **facts** | [Fact](#fact-object)[] | Matriz de objetos **Fato** que especificam informações sobre a compra. Por exemplo, a lista de fatos para um recibo de hospedagem pode incluir a data de check-in e a data de check-out. O canal determina o número de fatos que podem ser especificados. |
| **items** | [ReceiptItem](#receiptitem-object)[] | Matriz de objetos **ReceiptItem** que especificam os itens comprados |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no cartão. Essa pode ser a mesma ação que um dos botões ou uma ação diferente. |
| **tax** | string | Uma cadeia de caracteres formatada em moeda que especifica o valor do imposto aplicado à compra. |
| **title** | string | Título exibido na parte superior do recebimento. |
| **total** | string | Uma cadeia de caracteres formatada em moeda que especifica o preço total de compra, incluindo todos os impostos aplicáveis. |
| **vat** | string | Uma cadeia de caracteres formatada como moeda que especifica o IVA (imposto sobre valor agregado) aplicado ao preço de compra. |

[Retornar à tabela de esquemas](#schema)

### <a name="receiptitem-object"></a>Objeto ReceiptItem

Define um item de linha dentro de um recibo.

| Propriedade | Type | Descrição |
|----|----|----|
| **imagem** | [CardImage](#cardimage-object) | Um objeto **CardImage** que especifica a imagem em miniatura para exibir ao lado do item de linha.  |
| **price** | string | Uma cadeia de caracteres formatada como moeda que especifica o preço total de todas as unidades compradas. |
| **quantity** | string | Uma cadeia numérica que especifica o número de unidades compradas. |
| **subtitle** | string | Subtítulo a ser exibido abaixo do título do item de linha. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no item de linha. |
| **text** | string | Descrição do item de linha. |
| **title** | string | Título do item de linha. |

[Retornar à tabela de esquemas](#schema)

### <a name="resourceresponse-object"></a>Objeto ResourceResponse

Define uma resposta que contém uma ID de recurso.

| Propriedade | Type | Descrição |
|----|----|----|
| **id** | string | ID que identifica exclusivamente o recurso. |

[Retornar à tabela de esquemas](#schema)

### <a name="semanticaction-object"></a>Objeto SemanticAction

Define uma referência a uma ação através programática.

| Propriedade | Type | Descrição |
|----|----|----|
| **entidades** | objeto | Um objeto em que o valor de cada propriedade é um objeto [Entity](#entity-object). |
| **id** | string | ID desta ação. |
| **state** | string | Estado desta ação. Valores permitidos: **start**, **continue**, **done**. |

[Retornar à tabela de esquemas](#schema)

### <a name="signincard-object"></a>Objeto SignInCard

Define um cartão que permite ao usuário entrar em um serviço.

| Propriedade | Type | Descrição |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário entrar em um serviço. O canal determina o número de botões que você pode especificar. |
| **text** | string | Descrição ou solicitação para incluir no cartão de entrada. |

[Retornar à tabela de esquemas](#schema)

### <a name="suggestedactions-object"></a>Objeto SuggestedActions

Define as opções que um usuário pode escolher.

| Propriedade | Type | Descrição |
|----|----|----|
| **actions** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que definem as ações sugeridas. |
| **to** | string[] | Matriz de cadeias de caracteres que contém as IDs dos destinatários para os quais as ações sugeridas devem ser exibidas. |

[Retornar à tabela de esquemas](#schema)

### <a name="texthighlight-object"></a>Objeto TextHighlight

Refere-se a uma substring de conteúdo dentro de outro campo.

| Propriedade | Type | Descrição |
|----|----|----|
| **occurrence** | número | Ocorrência do campo de texto dentro do texto referenciado, se houver vários. |
| **text** | string | Define o snippet de texto a ser realçado. |

[Retornar à tabela de esquemas](#schema)

### <a name="thumbnailcard-object"></a>Objeto ThumbnailCard

Define um cartão com uma imagem em miniatura, título, texto e botões de ação.

| Propriedade | Type | Descrição |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **images** | [CardImage](#cardimage-object)[] | Matriz de objetos **CardImage** que especificam imagens em miniatura para exibir no cartão. O canal determina o número de imagens em miniatura que podem ser especificados. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **tap** | [CardAction](#cardaction-object) | Um objeto **CardAction** que especifica a ação a ser executada se o usuário tocar ou clicar no cartão. Essa pode ser a mesma ação que um dos botões ou uma ação diferente. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |

[Retornar à tabela de esquemas](#schema)

### <a name="thumbnailurl-object"></a>Objeto ThumbnailUrl

Define a URL para a fonte de uma imagem.

| Propriedade | Type | Descrição |
|----|----|----|
| **alt** | string | Descrição da imagem. É necessário incluir a descrição para dar suporte à acessibilidade. |
| **url** | string | URL para a origem da imagem ou o binário base64 da imagem (por exemplo, `data:image/png;base64,iVBORw0KGgo...`). |

[Retornar à tabela de esquemas](#schema)

### <a name="transcript-object"></a>Objeto Transcript

Uma coleção de atividades a ser carregada usando [Enviar histórico da conversa](#send-conversation-history).

| Propriedade | Type | Descrição |
|----|----|----|
| **activities** | matriz | Uma matriz de objetos [Atividade](#activity-object). Eles devem ter uma ID exclusiva e um carimbo de data/hora. |

[Retornar à tabela de esquemas](#schema)

### <a name="videocard-object"></a>Objeto VideoCard

Define um cartão que pode reproduzir vídeos.

| Propriedade | Type | Descrição |
|----|----|----|
| **aspect** | string | Taxa de proporção do vídeo. **16:9** ou **4:3**. |
| **autoloop** | booleano | Sinalizador que indica se deverá reproduzir a lista de vídeos quando o último encerrar. Defina esta propriedade como **true** para reproduzir automaticamente os vídeos, caso contrário, como **false**. O valor padrão é **true**. |
| **autostart** | booleano | Sinalizador que indica se deverá reproduzir automaticamente os vídeos quando o cartão for exibido. Defina essa propriedade para **true** para reproduzir automaticamente os vídeos, caso contrário, como **false**. O valor padrão é **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Matriz de objetos **CardAction** que permitem ao usuário executar uma ou mais ações. O canal determina o número de botões que você pode especificar. |
| **duration** | string | O comprimento do conteúdo de mídia, no [formato de duração ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). |
| **imagem** | [ThumbnailUrl](#thumbnailurl-object) | Um objeto **ThumbnailUrl** que especifica a imagem a ser exibida no cartão. |
| **media** | [MediaUrl](#mediaurl-object)[] | Matriz de **MediaUrl**.  Quando esse campo contém mais de uma URL, cada URL é um formato alternativo do mesmo conteúdo. |
| **shareable** | booleano | Sinalizador que indica se os vídeos podem ser compartilhados com outras pessoas. Defina esta propriedade para **true** se os vídeos puderem ser compartilhados, caso contrário, como **false**. O valor padrão é **true**. |
| **subtitle** | string | Subtítulo a ser exibido sob o título do cartão. |
| **text** | string | Descrição ou solicitação para exibir sob o título ou subtítulo do cartão. |
| **title** | string | Título do cartão. |
| **value** | objeto | Parâmetro suplementar para esse cartão |

[Retornar à tabela de esquemas](#schema)
