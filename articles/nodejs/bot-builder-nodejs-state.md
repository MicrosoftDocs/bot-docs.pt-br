---
title: Gerenciar dados de estado (JS v3) – Serviço de Bot
description: Saiba como salvar e recuperar dados de estado com o SDK do Bot Framework para Node.js.
author: DucVo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 40293ff3756687f270847dd9045a04a19363ad1b
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790321"
---
# <a name="manage-state-data"></a>Gerenciar dados de estado

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-state.md)
> - [Node.js](../nodejs/bot-builder-nodejs-state.md)

[!INCLUDE [State concept overview](../includes/snippet-dotnet-concept-state.md)]

## <a name="in-memory-data-storage"></a>Armazenamento de dados na memória

Armazenamento de dados na memória destina-se somente para teste. Esse armazenamento é volátil e temporário. Os dados serão limpos sempre que o bot for reiniciado. Para usar o armazenamento na memória para fins de teste, você precisará fazer duas coisas. Primeiro crie uma nova instância do armazenamento na memória:

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();
```

Então, configure-o para o bot quando você criar o **UniversalBot**:

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();
var bot = new builder.UniversalBot(connector, [..waterfall steps..])
                    .set('storage', inMemoryStorage); // Register in-memory storage 
```

Você pode usar esse método para definir seu próprio armazenamento de dados personalizado ou usar qualquer uma das *Extensões do Azure*.

## <a name="manage-custom-data-storage"></a>Gerenciar o armazenamento de dados personalizados

Para desempenho e razões de segurança no ambiente de produção, você pode implementar seu próprio armazenamento de dados ou considere a implementação de uma das seguintes opções de armazenamento de dados:

1. [Gerenciar dados de estado com o Cosmos DB](bot-builder-nodejs-state-azure-cosmosdb.md)

2. [Gerenciar dados de estado com o armazenamento de tabela](bot-builder-nodejs-state-azure-table-storage.md)

Com uma dessas opções [ do Azure Extensions ](https://www.npmjs.com/package/botbuilder-azure), o mecanismo para configuração e persistência de dados por meio do Bot Framework SDK para Node.js permanece o mesmo que o armazenamento de dados na memória.

## <a name="storage-containers"></a>Contêineres de armazenamento

No SDK do Bot Framework para Node.js, o objeto `session` expõe as propriedades a seguir para armazenar dados de estado.

| Propriedade | No escopo | Descrição |
| ---- | ---- | ---- |
| [`userData`][userDataURL] | Usuário | Contém dados que são salvos para o usuário no canal especificado. Esses dados persistirão em várias conversas. |
| [`privateConversationData`][privateConversationDataURL] | Conversação | Contém dados que são salvos para o usuário no contexto de uma conversa específica no canal especificado. Esses dados são privados para o usuário atual e persistirão apenas para a conversa atual. A propriedade é limpa quando a conversa termina ou quando `endConversation` é chamado explicitamente. |
| [`conversationData`][conversationDataURL] | Conversação | Contém dados que são salvos no contexto de uma conversa específica no canal especificado. Esses dados são compartilhados com todos os usuários que participam da conversa e persistirão apenas para a conversa atual. A propriedade é limpa quando a conversa termina ou quando `endConversation` é chamado explicitamente. |
| [`dialogData`][dialogDataURL] | caixa de diálogo | Contém dados que é salvo para a caixa de diálogo atual somente. Cada caixa de diálogo mantém sua própria cópia dessa propriedade. A propriedade é limpo quando a caixa de diálogo é removida da pilha de caixa de diálogo. |

Essas quatro propriedades correspondem aos contêineres de armazenamento de dados de quatro que podem ser usados para armazenar dados. Quais propriedades você pode usar para armazenar dados varia de acordo com o escopo apropriado para os dados você está armazenando, a natureza dos dados que você está armazenando, e quanto tempo deseja manter os dados. Por exemplo, se você precisar armazenar dados de usuário que estarão disponíveis em várias conversas, considere usar o `userData` propriedade. Se você precisar armazenar temporariamente os valores de variáveis locais dentro do escopo de uma caixa de diálogo, considere o uso de `dialogData` propriedade. Se você precisar armazenar temporariamente os dados que devem ser acessíveis em várias caixas de diálogo, considere o uso de `conversationData` propriedade.

## <a name="data-persistence"></a>Persistência de dados

Por padrão, os dados armazenados usando o `userData`, `privateConversationData`, e `conversationData` propriedades está configurada para persistir após o término de conversa. Se você não deseja que os dados persistam na `userData` contêiner, defina a `persistUserData` sinalizador como **falso**. Se você não deseja que os dados persistam na `conversationData` contêiner, defina a `persistConversationData` sinalizador como **falso**. 

```javascript
// Do not persist userData
bot.set(`persistUserData`, false);

// Do not persist conversationData
bot.set(`persistConversationData`, false);
```

> [!NOTE]
> É possível desabilitar a persistência de dados para o `privateConversationData` contêiner; sempre são persistentes.

## <a name="set-data"></a>Conjunto de dados

Você pode armazenar objetos de JavaScript simples, salvá-las diretamente em um contêiner de armazenamento. Para um objeto complexo, como `Date`, considere a possibilidade de convertê-la em `string`. Isso ocorre porque os dados de estado são serializados e armazenados como JSON. Os exemplos de código a seguir mostram como armazenar dados primitivos, uma matriz, um mapa de objeto e um complexo `Date` objeto. 

**Store dados primitivos**

```javascript
session.userData.userName = "Kumar Sarma";
session.userData.userAge = 37;
session.userData.hasChildren = true;
```

**Uma matriz de Store**

```javascript
session.userData.profile = ["Kumar Sarma", "37", "true"];
```

**Store um mapa de objeto**

```javascript
session.userData.about = {
    "Profile": {
        "Name": "Kumar Sarma",
        "Age": 37,
        "hasChildren": true
    },
    "Job": {
        "Company": "Contoso",
        "StartDate": "June 8th, 2010",
        "Title": "Developer"
    }
}
```
**Store data e hora** 

Para um objeto JavaScript complexo, converta-o em uma string antes de salvar no contêiner de armazenamento. 

```javascript 
var startDate = builder.EntityRecognizer.resolveTime([results.response]); 

// Date as string: "2017-08-23T05:00:00.000Z" 
session.userdata.start = startDate.toISOString(); 
``` 

### <a name="saving-data"></a>Salvando dados

Os dados criados em cada contêiner de armazenamento permanecerão na memória até que o contêiner seja salvo. O SDK do Bot Framework para Node.js envia dados para o serviço `ChatConnector` em lotes para serem salvos quando houver mensagens a serem enviadas. Para salvar os dados que existem nos contêineres de armazenamento sem enviar qualquer mensagem, você pode chamar manualmente os [`save`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#save) método. Se você não chamar o método `save`, os dados existentes nos contêineres de armazenamento serão mantidos como parte do processamento em lote.

```javascript
session.userData.favoriteColor = "Red";
session.userData.about.job.Title = "Senior Developer"; 
session.save();
```

## <a name="get-data"></a>Obter dados

Para acessar os dados salvos em um contêiner de armazenamento específico, simplesmente faça referência à propriedade correspondente. Os exemplos de código a seguir mostram como acessar dados que foram armazenados anteriormente como dados primitivos, uma matriz, um mapa de objetos e um objeto Date complexo.

**Acessar dados primitivos**

```javascript
var userName = session.userData.userName;
var userAge = session.userData.userAge;
var hasChildren = session.userData.hasChildren;
```

**Acessar uma matriz**

```javascript
var userProfile = session.userData.userProfile;

session.send("User Profile:");
for(int i = 0; i < userProfile.length, i++){
    session.send(userProfile[i]);
}
```

**Acesso um mapa de objeto**

```javascript
var about = session.userData.about;

session.send("User %s works at %s.", about.Profile.Name, about.Job.Company);
```

**Acessar um objeto de data** 

Recuperar dados de data como cadeia de caracteres e convertê-la em um objeto de data de um JavaScript. 

```javascript 
// startDate as a JavaScript Date object. 
var startDate = new Date(session.userdata.start); 
``` 

## <a name="delete-data"></a>Excluir dados

Por padrão, os dados são armazenados no `dialogData` contêiner é limpo quando uma caixa de diálogo é removida da pilha de caixa de diálogo. Da mesma forma, os dados armazenados no contêiner `conversationData` e no contêiner `privateConversationData` são limpos quando o método `endConversation` é chamado. No entanto, para excluir dados armazenados no contêiner `userData`, você deve explicitamente limpá-lo.

Para limpar explicitamente os dados armazenados em qualquer um dos contêineres de armazenamento, basta redefinir o contêiner conforme mostrado no exemplo de código a seguir. 

```javascript
// Clears data stored in container.
session.userData = {}; 
session.privateConversationData = {};
session.conversationData = {};
session.dialogData = {};
```

Nunca defina um contêiner de dados `null` ou remova-o do objeto `session`, pois isso causará erros na próxima vez que você tentar acessar o contêiner. Além disso, convém chamar `session.save();` manualmente depois de limpar manualmente um contêiner na memória, para limpar os dados correspondentes que foram persistidos anteriormente.

## <a name="next-steps"></a>Próximas etapas

Agora que você entende como gerenciar dados de estado do usuário, vamos dar uma olhada em como você pode usá-lo a gerenciar melhor o fluxo da conversa.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-nodejs-dialog-manage-conversation-flow.md)

## <a name="additional-resources"></a>Recursos adicionais
- [Solicitar entrada do usuário](bot-builder-nodejs-dialog-prompt.md)

[userDataURL]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata
[conversationDataURL]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#conversationdata
[privateConversationDataURL]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#privateconversationdata
[dialogDataURL]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#dialogdata

[ChatConnector]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
