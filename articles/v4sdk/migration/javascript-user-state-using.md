---
title: Usar o estado de usuário v3 do JavaScript em um bot v4– Serviço de Bot
description: Como usar o estado de usuário v3 em um exemplo de bot v4
keywords: JavaScript, migração de bot, bot v3
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/14/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: cb3c83d0b0a8dd5e5b2be2ae6888566bf59e4839
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416835"
---
<!-- This article is on hold -->

# <a name="using-javascript-v3-user-state-in-a-v4-bot"></a>Usar o estado de usuário v3 do JavaScript em um bot v4

[!INCLUDE [applies-to-v4](../../includes/applies-to-v4-current.md)]

Este artigo mostra um exemplo de como um bot v4 pode executar operações de leitura, gravação e exclusão em informações de estado de usuário v3.

O exemplo de código pode ser encontrado [aqui](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot).

> [!NOTE]
> Um bot mantém o **estado da conversa** para acompanhar e direcionar a conversa e fazer perguntas ao usuário. Ele mantém o **estado do usuário** para acompanhar as respostas dele.

## <a name="prerequisites"></a>Pré-requisitos

- npm versão 6.9.0 ou superior (necessário para dar suporte a alias de pacote).

- Node.js versão 10.14.1 ou superior.

    Para verificar a versão instalada em seu computador, em um terminal, execute o seguinte comando:

    ```bash
    # determine node version
    node --version
    ```

## <a name="setup"></a>Instalação

1. Clonar o repositório

    ```bash
    git clone https://github.com/microsoft/botbuilder-samples.git
    ```

1. Em um terminal, navegue até `BotBuilder-Samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot`

    ```bash
    cd BotBuilder-Samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot
    ```

1. Execute `npm install` nos seguintes locais:

    ```bash
    root
    /V4V3StorageMapper
    /V4V3UserState
    ```

1. Execute ``npm run build`` ou ``tsc`` para criar os módulos `StorageMapper` e `UserState` nos seguintes locais:

    ```bash
    /V4V3StorageMapper
    /V4V3UserState
    ```

1. Configurar a base de dados

    1. Copie o conteúdo do arquivo `.env.example`.
    1. Crie um arquivo chamado `.env` e cole o conteúdo anterior nele. 
    1. Preencha os valores para seus provedores de armazenamento.
        Observe que *Nome de usuário*, *senha* e *informações de host* podem ser encontrados no portal do Azure na seção do seu provedor de armazenamento específico, como *Cosmos DB*, *Armazenamento de tabela* ou *Banco de dados SQL*. Os nomes de tabela e coleção são definidos pelo usuário.
  
1. Definir o provedor de armazenamento do bot

    1. Abra o arquivo `index.js` na raiz do projeto. Até o início do arquivo (linhas 38-98), você verá configurações para cada provedor de armazenamento, conforme observado nos comentários. Eles leem os valores de configuração do arquivo `.env` por meio do Nó `process.env`. O snippet de código a seguir mostra como configurar o Banco de Dados SQL.

        [!code-javascript[Storage configuration](~/../botbuilder-samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot/index.js?range=77-92)]

    1. Especifique qual provedor de armazenamento você deseja que seu bot use passando a instância de cliente de armazenamento de sua escolha para o adaptador `StorageMapper` (~linha 107).  

        [!code-javascript[StorageMapper](~/../botbuilder-samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot/index.js?range=105-107)]

        A configuração padrão é *Cosmos DB*. Os valores possíveis são:

        ```bash
            cosmosStorageClient
            tableStorage
            sqlStorage
        ```

1. Inicie o aplicativo. Na raiz do projeto, execute o seguinte comando:

    ```bash
    npm run start
    ```

## <a name="adapter-classes"></a>Classes de adaptador

### <a name="v4v3storagemapper"></a>V4V3StorageMapper

A classe `StorageMapper` contém a funcionalidade principal do adaptador. Ela implementa a interface de armazenamento v4 e mapeia os métodos de provedor de armazenamento (leitura, gravação e exclusão) de volta para as classes de provedor de armazenamento v3 para que o estado de usuário formatado por v3 possa ser usado em um bot v4.

### <a name="v4v3userstate"></a>V4V3UserState

Essa classe estende a classe `BotState` v4 (`botbuilder-core`) para que ela use uma chave de estilo v3, permitindo a leitura, gravação e exclusão no armazenamento v3.

## <a name="testing-the-bot-using-bot-framework-emulator"></a>Testar o bot usando o Bot Framework Emulator

O [Bot Framework Emulator][5] é um aplicativo de área de trabalho que permite testar e depurar um bot em localhost ou executando remotamente por meio de um túnel.

- Instale o Bot Framework Emulator versão 4.3.0 ou superior acessando [aqui][6]

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>Conectar-se ao bot usando o Bot Framework Emulator

1. Inicie o Bot Framework Emulator.
1. Insira a URL a seguir (ponto de extremidade): `http://localhost:3978/api/messages`.

### <a name="testing-steps"></a>Etapas de teste

1. Abra o bot no emulador e envie uma mensagem. Forneça seu nome quando solicitado.
1. Depois que o turno terminar, envie outra mensagem ao bot.
1. Garanta que você não será notificado novamente a inserir seu nome. O bot deve estar lendo isso no armazenamento e reconhecer que ele já solicitou a você.
1. O bot deve retornar sua mensagem.
1. Acesse o provedor de armazenamento no Azure e verifique se seu nome é armazenado como dados de usuário no banco de dados.

## <a name="deploy-the-bot-to-azure"></a>Implantar o bot no Azure

Para saber mais sobre como implantar um bot no Azure, confira [Implantar o bot no Azure][40] para obter uma lista completa de instruções de implantação.

## <a name="further-reading"></a>Leitura adicional

- [Introdução ao Serviço de Bot do Azure][21]
- [Estado do Bot][7]
- [Gravar diretamente no armazenamento][8]
- [Gerenciar conversa e estado do usuário][9]
- [Restify][30]
- [dotenv][31]

[3]: https://aka.ms/botframework-emulator-github
[5]: https://github.com/microsoft/botframework-emulator
[6]: https://github.com/Microsoft/BotFramework-Emulator/releases
[7]: https://docs.microsoft.com/azure/bot-service/bot-builder-storage-concept
[8]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-storage?tabs=javascript
[9]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-state?tabs=javascript
[21]: https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[30]: https://www.npmjs.com/package/restify
[31]: https://www.npmjs.com/package/dotenv
[40]: https://aka.ms/azuredeployment
