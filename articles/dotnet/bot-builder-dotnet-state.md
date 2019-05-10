---
title: Gerenciar dados de estado | Microsoft Docs
description: Saiba como salvar e recuperar dados de estado com o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 3ee3af72d1c03faf485a64adb8d9fa2548f5d99d
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563596"
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
| `DeleteStateForUser` | Usuário | Excluir dados de estado para o usuário que foi armazenado anteriormente usando o método `SetUserData` ou o método `SetPrivateConversationData`. <br/><br/>**Observação**: seu bot deverá chamar esse método quando receber uma atividade do tipo [deleteUserData](bot-builder-dotnet-activities.md#deleteuserdata) ou do tipo [contactRelationUpdate](bot-builder-dotnet-activities.md#contactrelationupdate), que indica que o bot foi removido da lista de contatos do usuário. |

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

## <a name="handle-concurrency-issues"></a>Lidar com problemas de simultaneidade

Seu bot pode receber uma resposta de erro com o código de status HTTP **412 Falha na Pré-condição** quando ele tenta salvar os dados de estado, se outra instância do bot tiver alterado os dados. Você pode projetar seu bot para cuidar deste cenário, conforme mostrado no exemplo de código a seguir.

[!code-csharp[Handle exception saving state](../includes/code/dotnet-state.cs#handleException)]

## <a name="additional-resources"></a>Recursos adicionais

- [Guia de Solução de Problemas do Bot Framework](../bot-service-troubleshoot-general-problems.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>

[Activity]: https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html
