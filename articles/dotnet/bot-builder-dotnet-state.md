---
title: Gerenciar dados de estado | Microsoft Docs
description: Saiba como salvar e recuperar dados do estado com o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: deb8361a5cca2f37840abb1180c2de571ee08143
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999753"
---
# <a name="manage-state-data"></a>Gerenciar dados de estado

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-state.md)
> - [Node.js](../nodejs/bot-builder-nodejs-state.md)

[!INCLUDE [State concept overview](../includes/snippet-dotnet-concept-state.md)]

## <a name="in-memory-data-storage"></a>Armazenamento de dados na memória

Armazenamento de dados na memória destina-se somente para teste. Esse armazenamento é volátil e temporário. Os dados serão limpos sempre que o bot for reiniciado. Para usar o armazenamento na memória para fins de teste, você precisará: 

Instale os seguintes pacotes NuGet: 
- Microsoft.Bot.Builder.Azure
- Autofac.WebApi2

No método **Application_Start**, crie uma nova instância do armazenamento na memória e registre o novo armazenamento de dados:

```cs
// Global.asax file

var store = new InMemoryDataStore();

Conversation.UpdateContainer(
           builder =>
           {
               builder.Register(c => store)
                         .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
                         .AsSelf()
                         .SingleInstance();

               builder.Register(c => new CachingBotDataStore(store,
                          CachingBotDataStoreConsistencyPolicy
                          .ETagBasedConsistency))
                          .As<IBotDataStore<BotData>>()
                          .AsSelf()
                          .InstancePerLifetimeScope();


           });
GlobalConfiguration.Configure(WebApiConfig.Register);

```

Você pode usar esse método para definir seu próprio armazenamento de dados personalizado ou usar qualquer uma das *Extensões do Azure*.

## <a name="manage-custom-data-storage"></a>Gerenciar o armazenamento de dados personalizados

Para desempenho e razões de segurança no ambiente de produção, você pode implementar seu próprio armazenamento de dados ou considere a implementação de uma das seguintes opções de armazenamento de dados:

1. [Gerenciar dados de estado com o Cosmos DB](bot-builder-dotnet-state-azure-cosmosdb.md)

2. [Gerenciar dados de estado com o armazenamento de tabelas](bot-builder-dotnet-state-azure-table-storage.md)

Com uma dessas opções de [Extensões do Azure](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure/), o mecanismo para definir e manter dados por meio do SDK do Bot Framework para .NET permanece o mesmo que o armazenamento de dados na memória.

## <a name="bot-state-methods"></a>Métodos de estado do bot

Esta tabela lista os métodos que você pode usar para gerenciar os dados de estado.

| Método | No escopo | Objetivo |                                                
|----|----|----|
| `GetUserData` | Usuário | Obter os dados de estado salvos anteriormente do usuário no canal especificado |
| `GetConversationData` | Conversação | Obter os dados de estado salvos anteriormente da conversa no canal especificado |
| `GetPrivateConversationData` | Usuário e conversa | Obter os dados de estado salvos anteriormente para o usuário dentro da conversa no canal especificado |
| `SetUserData` | Usuário | Salva dados de estado para o usuário no canal especificado |
| `SetConversationData` | Conversação | Salva dados de estado para a conversa no canal especificado. <br/><br/>**Observação**: como o método `DeleteStateForUser` não exclui os dados que foram armazenados usando o método `SetConversationData`, você NÃO deve usar esse método para armazenar as informações de identificação pessoal (PII) de um usuário. |
| `SetPrivateConversationData` | Usuário e conversa | Salva dados de estado para o usuário dentro da conversa no canal especificado |
| `DeleteStateForUser` | Usuário | Excluir dados de estado para o usuário que foi armazenado anteriormente usando o método `SetUserData` ou o método `SetPrivateConversationData`. <br/><br/>**Observação**: o seu bot deverá chamar esse método quando receber uma atividade do tipo [deleteUserData](bot-builder-dotnet-activities.md#deleteuserdata) ou do tipo [contactRelationUpdate](bot-builder-dotnet-activities.md#contactrelationupdate) que indica o bot foi removido da lista de contatos do usuário. |

Se seu bot salvar os dados de estado usando um dos métodos "**Set...Data**", as mensagens futuras que seu bot receberá no mesmo contexto conterão esses dados, que seu bot pode acessar por meio do método "**Get...Data**" correspondente.

## <a name="useful-properties-for-managing-state-data"></a>Propriedades úteis para o gerenciamento de dados de estado

Cada objeto [Activity][Activity] contém propriedades que você usará para gerenciar dados de estado.

| Propriedade | DESCRIÇÃO | Caso de uso |
|----|----|----|
| `From` | Identifica exclusivamente um usuário em um canal | Armazenar e recuperar dados de estado que estão associados a um usuário |
| `Conversation` | Identifica exclusivamente uma conversa | Armazenar e recuperar dados de estado que estão associados a uma conversa |
| `From` e `Conversation` | Identifica exclusivamente um usuário e uma conversa | Armazenar e recuperar dados de estado que estão associados a um usuário específico dentro do contexto de uma conversa específica |

> [!NOTE]
> Você pode usar esses valores de propriedade como chaves, mesmo se optar por armazenar os dados de estado em seu próprio banco de dados, em vez de usar o armazenamento de dados de estado do Bot Framework.

## <a id="state-client"></a> Criar um cliente de estado

O objeto `StateClient` permite que você gerencie os dados de estado usando o SDK do Construtor de Bot para .NET. Se você tiver acesso a uma mensagem que pertença ao mesmo contexto no qual você deseja gerenciar dados de estado, poderá criar um cliente de estado chamando o método `GetStateClient` no objeto `Activity`.

[!code-csharp[Get State client](../includes/code/dotnet-state.cs#getStateClient1)]

Se você não tiver acesso a uma mensagem que pertença ao mesmo contexto no qual você deseja gerenciar dados de estado, poderá criar um cliente de estado apenas criando uma nova instância da classe `StateClient`. Neste exemplo, `microsoftAppId` e `microsoftAppPassword` são as credenciais de autenticação do Bot Framework que você adquire para o bot durante o processo de [criação do bot](../bot-service-quickstart.md).

> [!NOTE]
> Para encontrar a **AppID** e a **AppPassword** do bot, confira [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

[!code-csharp[Get State client](../includes/code/dotnet-state.cs#getStateClient2)]

> [!NOTE]
> O cliente do estado padrão é armazenado em um serviço central. Para alguns canais, você talvez queira usar uma API de estado que é hospedada dentro do canal em si, para que os dados de estado possam ser armazenados em um repositório compatível que o canal fornece.

## <a name="get-state-data"></a>Obter dados de estado

Cada um dos métodos "**Get...Data**" retorna um objeto `BotData` que contém os dados de estado para o usuário e/ou a conversa especificada. Para obter um valor de propriedade específica de um objeto `BotData`, chame o método `GetProperty`. 

O exemplo de código a seguir mostra como obter uma propriedade de tipo de dados do usuário. 

[!code-csharp[Get state property](../includes/code/dotnet-state.cs#getProperty1)]

O exemplo de código a seguir mostra como obter uma propriedade de um tipo complexo dentro dos dados do usuário.

[!code-csharp[Get state property](../includes/code/dotnet-state.cs#getProperty2)]

Se não houver dado de estado para o usuário e/ou a conversa que esteja especificada para uma chamada de método "**Obter... Dados**", o objeto `BotData` que é retornado conterá esses valores de propriedade: 
- `BotData.Data` = null
- `BotData.ETag` = "*"

## <a name="save-state-data"></a>Salvar dados de estado

Para salvar dados de estado, primeiro obtenha o objeto `BotData` chamando o método apropriado "**Get...Data**", em seguida, atualizá-lo chamando o método `SetProperty` para cada propriedade que você deseja atualizar e salvá-lo chamando o método apropriado "**Set...Data**". 

> [!NOTE]
> Você pode armazenar até 32 quilobytes de dados para cada usuário em um canal, cada conversa em um canal e cada usuário dentro do contexto de uma conversa em um canal. 

O exemplo de código a seguir mostra como salvar uma propriedade de tipo nos dados do usuário.

[!code-csharp[Set state property](../includes/code/dotnet-state.cs#setProperty1)]

O exemplo de código a seguir mostra como salvar uma propriedade em um tipo complexo dentro dos dados do usuário. 

[!code-csharp[Set state property](../includes/code/dotnet-state.cs#setProperty2)]

## <a name="handle-concurrency-issues"></a>Lidar com problemas de simultaneidade

Seu bot pode receber uma resposta de erro com o código de status HTTP **412 Falha na Pré-condição** quando ele tenta salvar os dados de estado, se outra instância do bot tiver alterado os dados. Você pode projetar seu bot para cuidar deste cenário, conforme mostrado no exemplo de código a seguir.

[!code-csharp[Handle exception saving state](../includes/code/dotnet-state.cs#handleException)]

## <a name="additional-resources"></a>Recursos adicionais

- [Guia de Solução de Problemas do Bot Framework](../bot-service-troubleshoot-general-problems.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Construtor de Bot para .NET</a>

[Activity]: https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html
