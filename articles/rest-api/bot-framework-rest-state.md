---
title: Gerenciar os dados de estado | Microsoft Docs
description: Saiba como armazenar e recuperar dados de estado usando o serviço de Estado do Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: b0d5ca6893d70a73bc005a949ef6cc2518d3862f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000019"
---
# <a name="manage-state-data"></a>Gerenciar os dados de estado

O serviço de Estado do Bot permite que o bot armazene e recupere dados de estado associados a um usuário, uma conversa ou um usuário específico no contexto de uma conversa específica. Você pode armazenar até 32 quilobytes de dados para cada usuário em um canal, cada conversa em um canal e cada usuário dentro do contexto de uma conversa em um canal. Os dados de estado podem ser usados para muitas finalidades, como determinar o ponto em que uma conversa parou ou apenas saudar um usuário já registrado pelo nome. Se você armazenar as preferências de um usuário, poderá usar essas informações para personalizar a conversa no próximo chat. Por exemplo, é possível alertar o usuário sobre um artigo de notícias relacionado a um tópico de interesse ou alertar um usuário quando um compromisso estiver disponível. 

> [!IMPORTANT]
> A API de Serviço de Estado do Bot Framework não é recomendada para ambientes de produção e pode ser preterida em uma versão futura. É recomendável que você atualize o código do bot para que ele use o armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção. Para obter mais informações, confira o tópico **Gerenciar dados de estado** para implementação do [.NET](~/dotnet/bot-builder-dotnet-state.md) ou do [Node](~/nodejs/bot-builder-nodejs-state.md).

## <a id="concurrency"></a> Simultaneidade de dados

Para controlar a simultaneidade de dados que são armazenados usando o serviço de Estado do Bot, a estrutura usa a Etag (marca da entidade) para solicitações `POST`. A estrutura não usa os cabeçalhos padrão para as Etags. Em vez disso, o corpo da solicitação e da resposta especifica a Etag usando a propriedade `eTag`. 

Por exemplo, se você emitir uma solicitação `GET` para recuperar dados do usuário do repositório, a resposta conterá a propriedade `eTag`. Se você alterar os dados e emitir uma solicitação `POST` para salvar os dados atualizados no repositório, a solicitação poderá incluir a propriedade `eTag` que especifica o mesmo valor recebido anteriormente na resposta `GET`. Se a Etag especificada na solicitação `POST` corresponder ao valor atual no repositório, o servidor salvará os dados do usuário, responderá com **HTTP 200 – Êxito** e especificará um novo valor `eTag` no corpo da resposta. Se a Etag especificada na solicitação `POST` não corresponder ao valor atual no repositório, o servidor responderá com **HTTP 412 – Falha de pré-condição** para indicar que os dados do usuário no repositório foram alterados desde a última gravação ou recuperação.

> [!TIP]
> Uma resposta `GET` sempre incluirá uma propriedade `eTag`, mas você não precisa especificar a propriedade `eTag` em solicitações `GET`. O valor da propriedade `eTag` de asterisco (`*`) indica que você não salvou anteriormente os dados para a combinação especificada de canal, usuário e conversa.
>
> A especificação da propriedade `eTag` em solicitações `POST` é opcional. 
> Se a simultaneidade não for um problema para o bot, não inclua a propriedade `eTag` em solicitações `POST`. 

## <a id="save-user-data"></a> Salvar dados do usuário

Para salvar os dados de estado de um usuário em um canal específico, emita esta solicitação:

```http
POST /v3/botstate/{channelId}/users/{userId}
```

Nesse URI de solicitação, substitua **{channelId}** pela ID do canal e **{userId}** pela ID do usuário nesse canal. As propriedades `channelId` e `from` em qualquer mensagem que o bot recebeu anteriormente do usuário conterão essas IDs. Você também pode optar por armazenar em cache esses valores em um local seguro, para acessar os dados do usuário no futuro sem precisar extrai-los de uma mensagem.

Defina o corpo da solicitação como um objeto [BotData][BotData], em que a propriedade `data` especifica os dados que você deseja salvar. Se você usar marcas da entidade para o [controle de simultaneidade](#concurrency), defina a propriedade `eTag` como a Etag recebida na resposta na última vez que você salvou ou recuperou os dados do usuário (o que for mais recente). Se você não usar marcas da entidade para a simultaneidade, não inclua a propriedade `eTag` na solicitação.

> [!NOTE]
> Essa solicitação `POST` substituirá os dados do usuário no repositório somente se a Etag especificada corresponder à Etag do servidor ou se nenhuma Etag for especificada na solicitação.

### <a name="request"></a>Solicitação 

O exemplo a seguir mostra uma solicitação que salva os dados de um usuário em um canal específico. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/botstate/abcd1234/users/12345678
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "data": [
        {
            "trail": "Lake Serene",
            "miles": 8.2,
            "difficulty": "Difficult",
        },
        {
            "trail": "Rainbow Falls",
            "miles": 6.3,
            "difficulty": "Moderate",
        }
    ],
    "eTag": "a1b2c3d4"
}
```

### <a name="response"></a>Response 

A resposta conterá um objeto [BotData][BotData] com um novo valor `eTag`.

## <a name="get-user-data"></a>Obter dados do usuário

Para obter os dados de estado salvos anteriormente do usuário em um canal específico, emita esta solicitação:

```http
GET /v3/botstate/{channelId}/users/{userId}
```

Nesse URI de solicitação, substitua **{channelId}** pela ID do canal e **{userId}** pela ID do usuário nesse canal. As propriedades `channelId` e `from` em qualquer mensagem que o bot recebeu anteriormente do usuário conterão essas IDs. Você também pode optar por armazenar em cache esses valores em um local seguro, para acessar os dados do usuário no futuro sem precisar extrai-los de uma mensagem.

### <a name="request"></a>Solicitação

O exemplo a seguir mostra uma solicitação que obtém os dados salvos anteriormente do usuário. Nessa solicitação de exemplo, `https://smba.trafficmanager.net/apis` representa o URI base; o URI base das solicitações emitidas pelo bot pode ser diferente. Para obter detalhes sobre como definir o URI base, confira [Referência de API](bot-framework-rest-connector-api-reference.md#base-uri).

```http
GET https://smba.trafficmanager.net/apis/v3/botstate/abcd1234/users/12345678
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

### <a name="response"></a>Response

A resposta conterá um objeto [BotData][BotData], em que a propriedade `data` contém os dados salvos anteriormente do usuário e a propriedade `eTag` contém a Etag que você pode usar em uma solicitação seguinte para salvar os dados do usuário. Se você não salvou anteriormente os dados do usuário, a propriedade `data` será `null` e a propriedade `eTag` conterá um asterisco (`*`).

Este exemplo mostra a resposta para a solicitação `GET`:

```json
{
    "data": [
        {
            "trail": "Lake Serene",
            "miles": 8.2,
            "difficulty": "Difficult",
        },
        {
            "trail": "Rainbow Falls",
            "miles": 6.3,
            "difficulty": "Moderate",
        }
    ],
    "eTag": "xyz12345"
}
```

## <a name="save-conversation-data"></a>Salvar dados da conversa

Para salvar os dados de estado de uma conversa em um canal específico, emita esta solicitação:

```http
POST /v3/botstate/{channelId}/conversations/{conversationId}
```

Nesse URI de solicitação, substitua **{channelId}** pela ID do canal e **{conversationId}** pela ID da conversa. As propriedades `channelId` e `conversation` em qualquer mensagem que o bot anteriormente enviou ou recebeu no contexto da conversa conterão essas IDs. Você também pode optar por armazenar em cache esses valores em um local seguro, para acessar os dados da conversa no futuro sem precisar extrai-los de uma mensagem.

Defina o corpo da solicitação como um objeto [BotData][BotData], conforme descrito em [Salvar dados do usuário](#save-user-data).

> [!IMPORTANT]
> Como a operação [Excluir dados do usuário](#delete-user-data) não exclui os dados que foram armazenados usando a operação **Salvar dados da conversa**, você NÃO deve usar essa operação para armazenar PII (informações de identificação pessoal) de um usuário.

### <a name="response"></a>Response

A resposta conterá um objeto [BotData][BotData] com um novo valor `eTag`.

## <a name="get-conversation-data"></a>Obter dados da conversa

Para obter os dados de estado salvos anteriormente de uma conversa em um canal específico, emita esta solicitação:

```http
GET /v3/botstate/{channelId}/conversations/{conversationId}
```

Nesse URI de solicitação, substitua **{channelId}** pela ID do canal e **{conversationId}** pela ID da conversa. As propriedades `channelId` e `conversation` em qualquer mensagem que o bot anteriormente enviou ou recebeu no contexto da conversa conterão essas IDs. Você também pode optar por armazenar em cache esses valores em um local seguro, para acessar os dados da conversa no futuro sem precisar extrai-los de uma mensagem.

### <a name="response"></a>Response

A resposta conterá um objeto [BotData][BotData] com um novo valor `eTag`.

## <a name="save-private-conversation-data"></a>Salvar dados particulares da conversa

Para salvar os dados de estado de um usuário dentro do contexto de uma conversa específica, emita esta solicitação:

```http
POST /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId}
```

Nesse URI de solicitação, substitua **{channelId}** pela ID do canal, substitua **{conversationId}** pela ID da conversa e substitua **{userId}** pela ID do usuário nesse canal. As propriedades `channelId`, `conversation` e `from` em qualquer mensagem que o bot anteriormente recebeu do usuário no contexto da conversa conterão essas IDs. Você também pode optar por armazenar em cache esses valores em um local seguro, para acessar os dados da conversa no futuro sem precisar extrai-los de uma mensagem.

Defina o corpo da solicitação como um objeto [BotData][BotData], conforme descrito em [Salvar dados do usuário](#save-user-data).

### <a name="response"></a>Response

A resposta conterá um objeto [BotData][BotData] com um novo valor `eTag`.

## <a name="get-private-conversation-data"></a>Obter dados particulares da conversa

Para obter os dados de estado salvos anteriormente de um usuário dentro do contexto de uma conversa específica, emita esta solicitação: 

```http
GET /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId}
```

Nesse URI de solicitação, substitua **{channelId}** pela ID do canal, substitua **{conversationId}** pela ID da conversa e substitua **{userId}** pela ID do usuário nesse canal. As propriedades `channelId`, `conversation` e `from` em qualquer mensagem que o bot anteriormente recebeu do usuário no contexto da conversa conterão essas IDs. Você também pode optar por armazenar em cache esses valores em um local seguro, para acessar os dados da conversa no futuro sem precisar extrai-los de uma mensagem.

### <a name="response"></a>Response

A resposta conterá um objeto [BotData][BotData] com um novo valor `eTag`.

## <a name="delete-user-data"></a>Excluir dados de usuário

Para excluir os dados de estado de um usuário em um canal específico, emita esta solicitação:

```http
DELETE /v3/botstate/{channelId}/users/{userId}
```
Nesse URI de solicitação, substitua **{channelId}** pela ID do canal e **{userId}** pela ID do usuário nesse canal. As propriedades `channelId` e `from` em qualquer mensagem que o bot recebeu anteriormente do usuário conterão essas IDs. Você também pode optar por armazenar em cache esses valores em um local seguro, para acessar os dados do usuário no futuro sem precisar extrai-los de uma mensagem.

> [!IMPORTANT]
> Essa operação exclui os dados que foram armazenados anteriormente do usuário usando a operação [Salvar dados do usuário](#save-user-data) ou [Salvar dados particulares da conversa](#save-private-conversation-data). Ela NÃO exclui os dados que foram armazenados anteriormente usando a operação [Salvar dados da conversa](#save-conversation-data). Portanto, você NÃO deve usar a operação **Salvar dados da conversa** para armazenar PII (informações de identificação pessoal) do usuário.

O bot deverá executar a operação **Excluir dados do usuário** quando receber uma [Activity][Activity] do tipo [deleteUserData](bot-framework-rest-connector-activities.md#deleteuserdata) ou uma atividade do tipo [contactRelationUpdate](bot-framework-rest-connector-activities.md#contactrelationupdate) que indica o bot foi removido da lista de contatos do usuário.

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-connector-concepts.md)
- [Visão geral das atividades](bot-framework-rest-connector-activities.md)

[BotData]: bot-framework-rest-connector-api-reference.md#botdata-object
[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
