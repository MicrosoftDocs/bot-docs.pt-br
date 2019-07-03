---
title: Gerenciar dados de estado personalizados com o Azure Cosmos DB | Microsoft Docs
description: Saiba como salvar e recuperar dados de estado usando o Azure Cosmos DB com o SDK do Bot Framework para Node.js.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 67e86455aefd000c8a6956a71adcfdb821266196
ms.sourcegitcommit: a295a90eac461f8b96770dd902ba44919acf33fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67404330"
---
# <a name="manage-custom-state-data-with-azure-cosmos-db-for-nodejs"></a>Gerenciar dados de estado personalizados com o Azure Cosmos DB for Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Neste artigo, você implementará o armazenamento do Cosmos DB para armazenar e gerenciar os dados de estado do seu bot. O Serviço de Estado do Conector padrão usado pelos bots não é destinado ao ambiente de produção. Você deve usar [Extensões do Azure](https://www.npmjs.com/package/botbuilder-azure) disponíveis no GitHub ou implementar um cliente de estado personalizado usando a plataforma de armazenamento de dados de sua escolha. Aqui estão alguns dos motivos para usar o armazenamento de estado personalizado:

- taxa de transferência da API de estado mais alto (mais controle sobre o desempenho)
- Latência inferior para a distribuição geográfica
- controle sobre onde os dados são armazenados (por exemplo: leste dos EUA, oeste dos EUA)
- acesso aos dados do estado real
- dados de estado db não compartilhados com outros bots
- armazenar mais de 32kb

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/).
- [Emulador do bot Framework](~/bot-service-debug-emulator.md)
- Deve ter um bot de Node. js. Se você não tiver uma, acesse [criar um bot](bot-builder-nodejs-quickstart.md). 

## <a name="create-azure-account"></a>Criar conta do Azure
Se você não tiver uma conta do Azure, clique em [aqui](https://azure.microsoft.com/free/) para se inscrever para uma conta gratuita.

## <a name="set-up-the-azure-cosmos-db-database"></a>Configurar o banco de dados do Azure Cosmos DB
1. Depois de efetuar login no portal do Azure, crie um novo banco de dados do *Azure Cosmos DB* clicando em **Novo**. 
2. Clique em **Bancos de Dados**. 
3. Encontre **do Azure Cosmos DB** e clique em **criar**.
4. Preencha os campos. Para o **API** campo, selecione **SQL (DocumentDB)** . Quando terminar de preencher todos os campos, clique no botão **Criar** na parte inferior da tela para implantar o novo banco de dados. 
5. Depois que o novo banco de dados é implantado, navegue até seu novo banco de dados. Clique em **chaves de acesso** para encontrar chaves e cadeias de caracteres de conexão. Seu bot usará essas informações para chamar o serviço de armazenamento para salvar os dados do estado.

## <a name="install-botbuilder-azure-module"></a>Instalar o módulo azure botbuilder

Para instalar o módulo `botbuilder-azure` a partir de um prompt de comando, navegue até o diretório do bot e execute o seguinte comando npm:

```nodejs
npm install --save botbuilder-azure
```

## <a name="modify-your-bot-code"></a>Modifique o código do bot

Para usar sua **do Azure Cosmos DB** banco de dados, adicione as seguintes linhas de código ao seu bot **App. js** arquivo.

1. Exija o módulo recém-instalado.

   ```javascript
   var azure = require('botbuilder-azure'); 
   ```

2. Configure as configurações de conexão para se conectar ao Azure.
   ```javascript
   var documentDbOptions = {
       host: 'Your-Azure-DocumentDB-URI', 
       masterKey: 'Your-Azure-DocumentDB-Key', 
       database: 'botdocs',   
       collection: 'botdata'
   };
   ```
   Os valores `host` e `masterKey` podem ser encontrados no menu **Chaves** do seu banco de dados. Se o `database` e `collection` não existem entradas no banco de dados do Azure, serão criados para você.

3. Usando o módulo `botbuilder-azure`, crie dois novos objetos para se conectar ao banco de dados do Azure. Primeiro, crie uma instância do `DocumentDBClient` passando os parâmetros de configuração de conexão (definido como `documentDbOptions` acima). Em seguida, crie uma instância de `AzureBotStorage` passando o `DocumentDBClient` objeto. Por exemplo:
   ```javascript
   var docDbClient = new azure.DocumentDbClient(documentDbOptions);

   var cosmosStorage = new azure.AzureBotStorage({ gzipData: false }, docDbClient);
   ```

4. Especifique que você deseja usar seu banco de dados personalizado em vez do armazenamento na memória. Por exemplo:

   ```javascript
   var bot = new builder.UniversalBot(connector, function (session) {
        // ... Bot code ...
   })
   .set('storage', cosmosStorage);
   ```

Agora você está pronto para testar o bot com o emulador.

## <a name="run-your-bot-app"></a>Execute seu bot de aplicativo

Em um prompt de comando, navegue até o diretório do seu bot e execute seu bot com o seguinte comando:

```nodejs
node app.js
```

## <a name="connect-your-bot-to-the-emulator"></a>Conectar seu bot no emulador

Neste ponto, seu bot está em execução localmente. Iniciar o emulador e, em seguida, conecte-se ao seu bot do emulador:

1. Tipo de <strong>http://localhost:port-number/api/messages</strong> na barra de endereços do emulador, em que o número da porta corresponde ao número da porta no navegador onde seu aplicativo está sendo executado. Você pode deixar os campos <strong>Microsoft App ID</strong> e <strong>Microsoft App Password</strong> em branco por enquanto. Você receberá essas informações posteriormente, quando você [registrar seu bot](~/bot-service-quickstart-registration.md).
2. Clique em **Conectar**.
3. Teste o seu bot enviando uma mensagem ao seu bot. Interaja com seu bot como faria normalmente. Quando você terminar, vá para **Gerenciador de armazenamento** e exibir seus dados de estado salvo.

## <a name="view-state-data-on-azure-portal"></a>Dados de estado de exibição no Portal do Azure

Para exibir os dados de estado, entre no seu portal do Azure e navegue até seu banco de dados. Clique em **Data Explorer (visualização)** para verificar o que está sendo salva as informações de estado do seu bot.

## <a name="next-step"></a>Próxima etapa

Agora que você tem controle total sobre os dados de estado do seu bot, vamos dar uma olhada em como você pode usá-lo para gerenciar melhor o fluxo de conversas.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-nodejs-dialog-manage-conversation-flow.md)
