---
title: Crie um bot usando o Bot Builder SDK para JavaScript | Microsoft Docs
description: Crie rapidamente um bot usando o Bot Builder SDK para JavaScript.
keywords: início rápido, bot builder sdk, começando
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 07/12/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 337b6a0b8739b5e5de4d2d1b2b87dcad55f2c854
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352925"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-v4-preview-for-javascript"></a>Crie um bot com o Bot Builder SDK v4 (preview) para JavaScript
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Este início rápido orienta você na criação de um bot usando o gerador Yeoman Bot Builder e o Bot Builder SDK para JavaScript e, em seguida, testando-o com o Bot Framework Emulator. Isso é baseado no [SDK v4](https://github.com/Microsoft/botbuilder-js) do Microsoft Bot Builder.

## <a name="pre-requisites"></a>Pré-requisitos
- [Visual Studio Code](https://www.visualstudio.com/downloads)
- [Node.js](https://nodejs.org/en/)
- [ Yeoman](http://yeoman.io/), que pode usar um gerador para criar um bot para você
- [Emulador de bot](https://github.com/Microsoft/BotFramework-Emulator)
- Conhecimento de [restify](http://restify.com/) e programação assíncrona em Java

> [!NOTE]
> Para algumas instalações, a etapa de instalação para restify está dando um erro relacionado ao node-gyp.
> Se este for o caso, tente executar `npm install -g windows-build-tools`.


O Bot Builder SDK para JavaScript consiste em uma série de [pacotes](https://github.com/Microsoft/botbuilder-js/tree/master/libraries) que podem ser instalados a partir do NPM usando uma tag `@preview` especial.

# <a name="create-a-bot"></a>Crie um bot

Abra um prompt de comando elevado, crie um diretório e inicialize o pacote para seu bot.

```bash
md myJsBots
cd myJsBots
```

Em seguida, instale Yeoman e o gerador para JavaScript.

```bash
npm install -g yo
npm install -g generator-botbuilder@preview
```

Em seguida, use o gerador para criar um bot de eco.

```bash
yo botbuilder
```

Yeoman pede-lhe alguma informação para criar o seu bot.
-   Digite um nome para o seu bot.
-   Digite uma descrição.
-   Escolha o idioma do seu bot, `JavaScript`ou`TypeScript`.
-   Escolha o modelo a ser usado. Atualmente, `Echo` é o único modelo, mas outros serão adicionados em breve.

Yeoman cria seu bot em uma nova pasta.

## <a name="explore-code"></a>Explore o código

Quando você abrir sua pasta bot recém-criada, você verá um arquivo `app.js`. Este arquivo `app.js` conterá todo o código necessário para executar um aplicativo bot. Este arquivo contém um bot de eco que fará o eco de qualquer entrada e também incrementará um contador. 

No código a seguir, o middleware de estado de conversação usa armazenamento na memória. Ele lê e grava o objeto de estado no armazenamento. A variável de contagem controla o número de mensagens enviadas ao bot. Você pode usar uma técnica semelhante para manter o estado entre as curvas. 

**app. js**
```javascript
// Packages are installed for you
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({ 
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);
```

O código a seguir escuta a solicitação de entrada e verifica o tipo de atividade de entrada antes de enviar uma resposta ao usuário.

```javascript
// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        // This bot is only handling Messages
        if (context.activity.type === 'message') {
        
            // Get the conversation state
            const state = conversationState.get(context);
            
            // If state.count is undefined set it to 0, otherwise increment it by 1
            const count = state.count === undefined ? state.count = 0 : ++state.count;
            
            // Echo back to the user whatever they typed.
            return context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            // Echo back the type of activity the bot detected if not of type message
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

## <a name="start-your-bot"></a>Inicie seu bot

Altere os diretórios para o criado para o seu bot e inicie-o.

```bash
cd <bot directory>
node app.js
```

## <a name="start-the-emulator-and-connect-your-bot"></a>Inicie o emulador e conecte seu bot
Neste ponto, seu bot está sendo executado localmente. Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:
1. Clique no link **criar uma nova configuração de bot** na guia "Boas-vindas" do emulador. 

2. Digite um **nome do bot** e digite o caminho do diretório para o seu código bot. O arquivo de configuração do bot será salvo neste caminho.

3. Digite `http://localhost:port-number/api/messages` no campo **URL do terminal**, onde *número da porta* corresponde ao número da porta mostrado no navegador em que o aplicativo está sendo executado.

4. Clique em **Connect** para se conectar ao seu bot. Você não precisará especificar **ID do aplicativo Microsoft** e **Microsoft App Password**. Você pode deixar esses campos em branco por enquanto. Você receberá essas informações mais tarde quando registrar seu bot.

Envie "Hi" para o seu bot, e o bot irá responder com "0: Você disse" Hi "'para a mensagem.

## <a name="next-steps"></a>Próximas etapas

Em seguida, salte para os conceitos que explicam um bot e como ele funciona.

> [!div class="nextstepaction"]
> [Conceitos básicos do Bot](../v4sdk/bot-builder-basics.md)