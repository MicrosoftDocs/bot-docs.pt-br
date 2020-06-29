---
title: Gerenciar dados de estado personalizados com o Armazenamento de Tabela do Azure (JS v3) – Serviço de Bot
description: Saiba como salvar e recuperar dados de estado usando o armazenamento de Tabela do Azure com o SDK do Bot Framework para Node.js.
author: DucVo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6eb4645437210f98cf43270a50b1654faf30ef25
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790341"
---
# <a name="manage-custom-state-data-with-azure-table-storage-for-nodejs"></a>Gerenciar dados de estado personalizado com o armazenamento de Tabela do Azure para Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Neste artigo, você implementará o armazenamento do Azure Table para armazenar e gerenciar os dados de estado do seu bot. O Serviço de Estado do Conector padrão usado pelos bots não é destinado ao ambiente de produção. Você deve usar [Extensões do Azure](https://www.npmjs.com/package/botbuilder-azure) disponíveis no GitHub ou implementar um cliente de estado personalizado usando a plataforma de armazenamento de dados de sua escolha. Aqui estão alguns dos motivos para usar o armazenamento de estado personalizado:

- taxa de transferência da API de estado mais alto (mais controle sobre o desempenho)
- Latência inferior para a distribuição geográfica
- controle sobre onde os dados são armazenados (por exemplo: leste dos EUA, oeste dos EUA)
- acesso aos dados do estado real
- dados de estado db não compartilhados com outros bots
- armazenar mais de 32kb

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/).
- [Bot Framework Emulator](~/bot-service-debug-emulator.md).
- Deve ter um bot de Node. js. Se você não tiver uma, acesse [criar um bot](bot-builder-nodejs-quickstart.md). 
- [Gerenciador de Armazenamento](http://storageexplorer.com/).

## <a name="create-azure-account"></a>Criar conta do Azure
Se você não tiver uma conta do Azure, clique em [aqui](https://azure.microsoft.com/free/) para se inscrever para uma conta gratuita.

## <a name="set-up-the-azure-table-storage-service"></a>Configurar o serviço de armazenamento de tabelas do Azure
1. Depois de efetuar login no portal do Azure, crie um novo serviço de armazenamento do Azure Table clicando em **Novo**. 
2. Pesquise **conta de armazenamento** que implementa a tabela do Azure. Clique em **Criar** para começar a criar a conta de armazenamento. 
3. Preencha os campos e clique no botão **Criar** na parte inferior da tela para implantar o novo serviço de armazenamento. 
4. Após a implantação do novo serviço de armazenamento, navegue até a conta de armazenamento que você acabou de criar. Encontre-a na folha **Contas de Armazenamento**.
4. Selecione **Chaves de acesso** e copie a chave para uso posterior. Seu bot usará o **Nome da conta de armazenamento** e a **Chave** para chamar o serviço de armazenamento para salvar os dados de estado.

## <a name="install-botbuilder-azure-module"></a>Instalar o módulo azure botbuilder

Para instalar o módulo `botbuilder-azure` a partir de um prompt de comando, navegue até o diretório do bot e execute o seguinte comando npm:

```nodejs
npm install --save botbuilder-azure
```

## <a name="modify-your-bot-code"></a>Modifique o código do bot

Para usar seu armazenamento de **Tabela do Azure**, adicione as seguintes linhas de código ao arquivo **App.js** de seu bot.

1. Exija o módulo recém-instalado.

   ```javascript
   var azure = require('botbuilder-azure'); 
   ```

2. Configure as configurações de conexão para se conectar ao Azure.
   ```javascript
   // Table storage
   var tableName = "Table-Name"; // You define
   var storageName = "Table-Storage-Name"; // Obtain from Azure Portal
   var storageKey = "Azure-Table-Key"; // Obtain from Azure Portal
   ```
   Os valores `storageName` e `storageKay` podem ser encontrados no menu **Chaves de acesso** de sua Tabela do Azure. Se `tableName` não existir na Tabela do Azure, ele será criado para você.

3. Usando o módulo `botbuilder-azure`, crie dois objetos novos para se conectar à Tabela do Azure. Primeiro, crie uma instância de `AzureTableClient` passando as definições de configuração de conexão. Em seguida, crie uma instância de `AzureBotStorage` passando o `AzureTableClient` objeto. Por exemplo:

   ```javascript
   var azureTableClient = new azure.AzureTableClient(tableName, storageName, storageKey);

   var tableStorage = new azure.AzureBotStorage({gzipData: false}, azureTableClient);
   ```

4. Especifique que você deseja usar seu banco de dados personalizado em vez do armazenamento na memória, e adicione informações de sessão ao banco de dados. Por exemplo:

   ```javascript
   var bot = new builder.UniversalBot(connector, function (session) {
        // ... Bot code ...

        // capture session user information
        session.userData = {"userId": session.message.user.id, "jobTitle": "Senior Developer"};

        // capture conversation information  
        session.conversationData[timestamp.toISOString().replace(/:/g,"-")] = session.message.text;

        // save data
        session.save();
   })
   .set('storage', tableStorage);
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

## <a name="view-data-in-storage-explorer"></a>Ver dados no Gerenciador de Armazenamento

Para exibir os dados de estado, abra o **Gerenciador de Armazenamento** e conecte-se ao Azure usando suas credenciais do Portal do Azure, ou conecte-se diretamente à Tabela usando `storageName` e `storageKey` e navegue até seu `tableName`. 

![Captura de tela do Gerenciador de Armazenamento com linhas da tabela botdata](~/media/bot-builder-nodejs-state-azure-table-storage/bot-builder-nodejs-state-azure-table-storage-query.png)

Um registro da conversa na coluna **dados** se parece com:

```JSON
{
    "2018-05-15T18-23-48.780Z": "I'm the second user",
    "2018-05-15T18-23-55.120Z": "Do you know what time it is?",
    "2018-05-15T18-24-12.214Z": "I'm looking for information about the new process."
}
```

## <a name="next-step"></a>Próxima etapa

Agora que você tem controle total sobre os dados de estado do seu bot, vamos dar uma olhada em como você pode usá-lo para gerenciar melhor o fluxo de conversas.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-nodejs-dialog-manage-conversation-flow.md)
