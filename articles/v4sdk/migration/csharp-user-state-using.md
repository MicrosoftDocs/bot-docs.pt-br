---
title: Usar o estado do usuário v3 do .NET em um bot v4 – Serviço de Bot
description: Veja um exemplo de como usar o estado do usuário do C# v3 em um bot do C# v4.
keywords: Csharp, migração de bot, bot v3
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/21/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d1a237851790d9aeee22660a5f245c2f63cf4154
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95452250"
---
# <a name="using-net-v3-user-state-in-a-v4-bot"></a>Usar o estado de usuário v3 do .NET em um bot v4

[!INCLUDE [applies-to-v4](../../includes/applies-to-v4-current.md)]

Este artigo mostra um exemplo de como um bot v4 pode executar operações de leitura, gravação e exclusão em informações de estado de usuário v3.
O bot mantém o estado de conversa usando o `MemoryStorage` para acompanhar e direcionar a conversa ao fazer perguntas ao usuário.  Ele mantém o **estado do usuário** no formato v3 para acompanhar as respostas do usuário utilizando uma classe `IStorage` personalizada chamada `V3V4Storage`.  Um dos argumentos para essa classe é um `IBotDataStore`. A base de código do SDK v3 foi copiada para `Bot.Builder.Azure.V3V4` e contém todos os três provedores de armazenamento do SDK v3 (Azure Sql, Tabela do Azure e Cosmos DB).  A intenção é permitir que o **estado do usuário v3** existente seja colocado em um bot v4 migrado.

O exemplo de código pode ser encontrado [aqui](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/CSharp/V4StateBotFromV3Providers).

## <a name="prerequisites"></a>Pré-requisitos

- [SDK do .NET Core](https://dotnet.microsoft.com/download) versão 2.1

    ```bash
    # determine dotnet version
    dotnet --version
    ```

## <a name="setup"></a>Instalação

1. Clonar o repositório

    ```bash
    git clone https://github.com/microsoft/botbuilder-samples.git
    ```

1. Em um terminal, navegue até `MigrationV3V4/CSharp/V4StateBotFromV3Providers`

    ```bash
    cd BotBuilder-Samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers
    ```

1. Execute o bot em um terminal ou no Visual Studio, escolha a opção A ou B.

    - Em um terminal

        ```bash
        # run the bot
        dotnet run
        ```

    - Ou no Visual Studio

        - Inicie o Arquivo do Visual Studio -> Abrir -> Projeto/Solução
        - Navegue até a pasta `BotBuilder-Samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers`
        - Selecione o arquivo `V4StateBot.sln`
        - Pressione F5 para executar o projeto


## <a name="storage-provider-setup"></a>Configuração do Provedor de Armazenamento

Presume-se que você tem um armazenamento de estado v3 existente configurado e em uso. Nesse caso, configurar esse exemplo para usar o armazenamento de estado existente simplesmente envolve adicionar as informações de conexão do provedor de armazenamento ao arquivo `web.config`, conforme mostrado a seguir.

- Cosmos DB

```json
  "v3CosmosEndpoint": "https://yourcosmosdb.documents.azure.com:443/",
  "v3CosmosKey": "YourCosmosDbKey",
  "v3CosmosDatataseName": "v3botdb",
  "v3CosmosCollectionName": "v3botcollection",
```

- Azure Sql

```json
 "ConnectionStrings": {
    "SqlBotData": "Server=YourServer;Initial Catalog=BotData;Persist Security Info=False;User ID=YourUserName;Password=YourUserPassword;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=True;Connection Timeout=30;"
  },
```

- tabela do Azure

```json
 "ConnectionStrings": {
    "AzureTable": "DefaultEndpointsProtocol=https;AccountName=YourAccountName;AccountKey=YourAccountKey;EndpointSuffix=core.windows.net"
  },
```

- Definir o provedor de armazenamento do bot

    Abra o arquivo `Startup.cs` na raiz do projeto `V4V3StateBot`. Até o meio do arquivo (linhas 52-76), você verá configurações para cada provedor de armazenamento. Elas leem os valores de configuração de web.config. 

    [!code-csharp[Storage configuration](~/../botbuilder-samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers/V4V3StateBot/Startup.cs?range=52-76)]

    Especifique qual provedor de armazenamento você deseja que seu bot use cancelando o comentário das linhas correspondentes da instância de sua escolha. Quando o provedor estiver devidamente configurado, verifique se a classe do provedor foi passada para `V3V4Storage` (linhas ~72-75). 

    [!code-csharp[Storage provider](~/../botbuilder-samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers/V4V3StateBot/Startup.cs?range=72-75)]

    O Cosmos DB (antigo Document DB) está configurado por padrão. Os valores possíveis são:

    ```bash
    documentDbBotDataStore
    tableBotDataStore
    tableBotDataStore2
    sqlBotDataStore
    ```

- Inicie o aplicativo. 

## <a name="v3v4-storage-and-state-classes"></a>Classes de estado e armazenamento V3V4

### <a name="v3v4storage"></a>V3V4Storage

A classe `V3V4Storage` contém a funcionalidade principal do mapeamento de armazenamento. Ela implementa a interface `IStorage` v4 e mapeia os métodos de provedor de armazenamento (leitura, gravação e exclusão) de volta para as classes de provedor de armazenamento v3 para que o estado de usuário formatado por v3 possa ser usado em um bot v4.

### <a name="v3v4state"></a>V3V4State

Essa classe é herdada da classe `BotState` v4 e usa uma chave de estilo v3 (`IAddress`). Isso permite leituras, gravações e exclusões no armazenamento v3 da mesma maneira que o armazenamento de estado v3 sempre funcionou.


## <a name="testing-the-bot-using-bot-framework-emulator"></a>Testar o bot usando o Bot Framework Emulator

O [Bot Framework Emulator][5] é um aplicativo de área de trabalho que permite que os desenvolvedores de bot testem e depurem seus bots em localhost ou executem remotamente por meio de um túnel.

- Instale o Bot Framework Emulator versão 4.3.0 ou superior acessando [aqui][6]


### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>Conectar-se ao bot usando o Bot Framework Emulator

- Inicie o Bot Framework Emulator
- Arquivo -> Abrir Bot
- Insira uma URL de bot de `http://localhost:3978/api/messages`


## <a name="further-reading"></a>Leitura adicional

- [Introdução ao Serviço de Bot do Azure][21]
- [Estado do Bot][7]
- [Gravar diretamente no armazenamento][8]
- [Gerenciar conversa e estado do usuário][9]

[3]: https://aka.ms/botframework-emulator-github
[5]: https://github.com/microsoft/botframework-emulator
[6]: https://github.com/Microsoft/BotFramework-Emulator/releases
[7]: https://docs.microsoft.com/azure/bot-service/bot-builder-storage-concept
[8]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-storage?tabs=csharp
[9]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-state?tabs=csharp
[21]: ../../bot-service-overview-introduction.md
[40]: https://aka.ms/azuredeployment
