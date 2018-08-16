---
title: Gerenciar dados de estado personalizados com o armazenamento de Tabela do Azure | Microsoft Docs
description: Saiba como salvar e recuperar dados de estado usando o armazenamento de Tabela do Azure com o SDK do Construtor de Bot para Node.js.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: c77b07801b8eb0168ac3e09d7b271ddfb17a04ac
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296921"
---
# <a name="manage-custom-state-data-with-azure-table-storage-for-nodejs"></a>Gerenciar dados de estado personalizado com o armazenamento de Tabela do Azure para Node.js

Neste artigo, você implementará o armazenamento de Tabela do Azure para armazenar e gerenciar os dados de estado de seu bot. O Serviço de Estado do Conector padrão usado por bots não se destina ao ambiente de produção. Você deve usar as [Extensões do Azure](https://www.npmjs.com/package/botbuilder-azure) disponíveis no GitHub ou implementar um cliente de estado personalizado usando uma plataforma de armazenamento de dados de sua escolha. Estes são alguns dos motivos para usar o armazenamento de estado personalizado:

- taxa de transferência de API com estado maior (mais controle sobre o desempenho)
- latência menor para distribuição geográfica
- controle sobre o local de armazenamento dos dados (por exemplo, Oeste dos EUA versus Leste dos EUA)
- acesso aos dados de estado real
- bd de dados de estado não compartilhado com outros bots
- armazene mais de 32 kb

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/).
- [Bot Framework Emulator](~/bot-service-debug-emulator.md).
- Deve ter um bot em Node.js. Se você não tiver um, [crie um bot](bot-builder-nodejs-quickstart.md). 
- [Gerenciador de Armazenamento](http://storageexplorer.com/).

## <a name="create-azure-account"></a>Criar conta do Azure
Caso você não tenha uma conta do Azure, clique [aqui](https://azure.microsoft.com/en-us/free/) para se inscrever em uma conta de avaliação gratuita.

## <a name="set-up-the-azure-table-storage-service"></a>Configurar o serviço de armazenamento de Tabela do Azure
1. Após se conectar ao Portal do Azure, crie um novo serviço de armazenamento de Tabela do Azure clicando em **Novo**. 
2. Pesquise a **conta de armazenamento** que implementa a Tabela do Azure. Clique em **Criar** para começar a criar a conta de armazenamento. 
3. Preencha os campos, clique no botão **Criar** na parte inferior da tela para implantar o novo serviço de armazenamento. 
4. Após a implantação do novo serviço de armazenamento, navegue até a conta de armazenamento que você acabou de criar. Encontre-a na folha **Contas de Armazenamento**.
4. Selecione **Chaves de acesso** e copie a chave para uso posterior. Seu bot usará o **Nome da conta de armazenamento** e a **Chave** para chamar o serviço de armazenamento para salvar os dados de estado.

## <a name="install-botbuilder-azure-module"></a>Instalar o módulo botbuilder-azure

Para instalar o módulo `botbuilder-azure` de um prompt de comando, navegue até o diretório do bot e execute o seguinte comando npm:

```nodejs
npm install --save botbuilder-azure
```

## <a name="modify-your-bot-code"></a>Modifique o código do seu bot

Para usar seu armazenamento de **Tabela do Azure**, adicione as seguintes linhas de código ao arquivo **App.js** de seu bot.

1. Exija o módulo recém-instalado.

   ```javascript
   var azure = require('botbuilder-azure'); 
   ```

2. Defina as configurações de conexão para se conectar ao Azure.
   ```javascript
   // Table storage
   var tableName = "Table-Name"; // You define
   var storageName = "Table-Storage-Name"; // Obtain from Azure Portal
   var storageKey = "Azure-Table-Key"; // Obtain from Azure Portal
   ```
   Os valores `storageName` e `storageKay` podem ser encontrados no menu **Chaves de acesso** de sua Tabela do Azure. Se `tableName` não existir na Tabela do Azure, ele será criado para você.

3. Usando o módulo `botbuilder-azure`, crie dois objetos novos para se conectar à Tabela do Azure. Primeiro, crie uma instância de `AzureTableClient` passando as definições de configuração de conexão. Em seguida, crie uma instância de `AzureBotStorage` passando o objeto `AzureTableClient`. Por exemplo: 

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

## <a name="run-your-bot-app"></a>Executar o bot do seu aplicativo

Em um prompt de comando, navegue até o diretório do seu bot e execute seu bot com o comando a seguir:

```nodejs
node app.js
```

## <a name="connect-your-bot-to-the-emulator"></a>Conectar seu bot ao emulador

Neste ponto, seu bot está em execução localmente. Inicie o emulador e conecte-se ao seu bot no emulador:

1. Digite <strong>http://localhost:port-number/api/messages</strong> na barra de endereços do emulador, em que port-number corresponde ao número da porta no navegador onde seu aplicativo está sendo executado. Você pode deixar os campos <strong>ID de Aplicativo da Microsoft</strong> e <strong>Senha de Aplicativo da Microsoft</strong> em branco por enquanto. Você receberá essas informações posteriormente quando [registrar seu bot](~/bot-service-quickstart-registration.md).
2. Clique em **Conectar**.
3. Teste seu bot enviando uma mensagem a ele. Interaja normalmente com seu bot. Quando terminar, acesse o **Gerenciador de Armazenamento** e veja seus dados de estado salvos.

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

Agora que você tem controle total sobre os dados de estado de seu bot, vamos dar uma olhada em como você pode usá-lo para gerenciar melhor o fluxo da conversa.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-nodejs-dialog-manage-conversation-flow.md)