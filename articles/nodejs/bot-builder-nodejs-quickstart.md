---
title: Criar um bot com o Construtor de bot do SDK para Node.js | Microsoft Docs
description: Criar um bot com o Construtor de bot do SDK para Node.js, uma estrutura poderosa de construção de bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 7259e1336965733b860a6129d51b3c2eb10362c7
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296887"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-nodejs"></a>Criar um bot com o Construtor de bot do SDK para Node.js
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-quickstart.md)
> - [Node.js](../nodejs/bot-builder-nodejs-quickstart.md)
> - [Serviço de Bot](../bot-service-quickstart.md)
> - [REST](../rest-api/bot-framework-rest-connector-quickstart.md)

O Construtor de bot do SDK para Node.js é uma estrutura para desenvolvimento de bots. É fácil de usar e modela estruturas como Express & Restify para oferecer aos desenvolvedores de JavaScript uma forma conhecida de gravar bots.

Este tutorial orienta você pela criação de um bot usando o Construtor de bot do SDK para Node.js. Você pode testar o bot em uma janela do console e com o Bot Framework Emulator.

## <a name="prerequisites"></a>Pré-requisitos
Comece a usar após concluir as seguintes tarefas de pré-requisito:

1. Instale o [Node.js](https://nodejs.org).
2. Crie uma pasta para o bot.
3. Em um prompt de comando ou terminal, navegue até a pasta que você acabou de criar.
4. Execute o seguinte comando do **npm**:

```nodejs
npm init
```

Siga os prompts na tela para inserir informações sobre seu bot e o npm criará um arquivo **package.json** que contém as informações fornecidas. 

## <a name="install-the-sdk"></a>Instalar o SDK
Em seguida, instale o Construtor de bot do SDK para Node.js executando o seguinte comando do **npm**:

```nodejs
npm install --save botbuilder
```

Após instalar o SDK, você está pronto para gravar seu primeiro bot.

Para seu primeiro bot, você criará um bot que simplesmente retorna todas as entradas do usuário. Para criar seu bot, execute estas etapas:

1. Na pasta que você criou anteriormente para o bot, crie um novo arquivo chamado **app.js**.
2. Abra o **app.js** em um editor de texto ou um IDE de sua escolha. Adicione o seguinte código ao arquivo: 

   [!code-javascript[consolebot code sample Node.js](../includes/code/node-getstarted.js#consolebot)]

3. Salve o arquivo. Agora você está pronto para executar e testar seu bot.

### <a name="start-your-bot"></a>Inicie seu bot

Navegue até o diretório do bot em uma janela do console e inicie seu bot:

```nodejs
node app.js
```

Agora o bot está em execução localmente. Teste seu bot digitando algumas mensagens na janela do console.
Você deverá ver que o bot responde todas as mensagens enviadas retornando sua mensagem prefixada com o texto *"Você disse:"*.

## <a name="install-restify"></a>Instalar Restify

Os bots de console são bons clientes baseados em texto, mas, para usar qualquer um dos canais do Bot Framework (ou executar seu bot no emulador), seu bot precisa executar em um ponto de extremidade de API. Instale <a href="http://restify.com/" target="_blank">restify</a> executando o seguinte comando do **npm**:

```nodejs
npm install --save restify
```

Após o Restify estar instalado, você estará pronto para fazer algumas alterações em seu bot.

## <a name="edit-your-bot"></a>Edite seu bot

Você precisará fazer algumas alterações no arquivo **app.js**. 

1. Adicione uma linha para exigir o módulo `restify`.
2. Altere o `ConsoleConnector` para um `ChatConnector`.
3. Inclua sua ID do Aplicativo e Senha do Aplicativo da Microsoft.
4. Faça com que o conector detecte um ponto de extremidade da API.

   [!code-javascript[echobot code sample Node.js](../includes/code/node-getstarted.js#echobot)]

5. Salve o arquivo. Agora você está pronto para executar e testar seu bot no emulador.

> [!NOTE] 
> Você não precisa de uma **ID do Aplicativo da Microsoft** ou de uma **Senha do Aplicativo da Microsoft** para executar seu bot no Bot Framework Emulator.

## <a name="test-your-bot"></a>Teste seu bot
Em seguida, teste o bot usando o [Bot Framework Emulator](../bot-service-debug-emulator.md) para vê-lo em ação. O emulador é um aplicativo da área de trabalho que permite testar e depurar o bot no localhost ou executar remotamente por meio de um túnel.

Primeiro, será necessário [fazer o download](https://emulator.botframework.com) e instalar o emulador. Após a conclusão do download, inicie o executável e conclua o processo de instalação.

### <a name="start-your-bot"></a>Inicie seu bot

Após instalar o emulador, navegue até o diretório do bot em uma janela do console e inicie seu bot:

```nodejs
node app.js
```
   
Agora o bot está em execução localmente.

### <a name="start-the-emulator-and-connect-your-bot"></a>Inicie o emulador e conecte seu bot
Depois de iniciar seu bot, inicie o emulador e, em seguida, conecte seu bot:

1. Clique no link **criar uma nova configuração de bot** na janela do emulador. 

2. Digite `http://localhost:port-number/api/messages` na barra de endereços, em que *port-number* corresponde ao número da porta no navegador onde seu aplicativo está sendo executado.

3. Clique em Salvar e conectar. Você não precisará especificar a ID do Aplicativo da Microsoft e a Senha do Aplicativo da Microsoft. Você pode deixar esses campos em branco por enquanto. Você receberá essas informações posteriormente quando registrar seu bot.

### <a name="try-out-your-bot"></a>Experimente seu bot

Agora que seu bot é executado localmente e está conectado ao emulador, teste seu bot digitando algumas mensagens no emulador.
Você deverá ver que o bot responde todas as mensagens enviadas retornando sua mensagem prefixada com o texto *"Você disse:"*.

Você criou eficazmente seu primeiro bot usando o Construtor de bot do SDK para Node.js!

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Construtor de bot do SDK para Node. js](bot-builder-nodejs-overview.md)
