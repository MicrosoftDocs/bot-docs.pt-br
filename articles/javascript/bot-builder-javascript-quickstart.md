---
title: Crie um bot usando o Bot Builder SDK para JavaScript | Microsoft Docs
description: Crie rapidamente um bot usando o Bot Builder SDK para JavaScript.
keywords: início rápido, bot builder sdk, começando
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/23/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3e721c9142ffb1511ef926f5b1caca782e0919ed
ms.sourcegitcommit: 54ed5000c67a5b59e23b667547565dd96c7302f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49315082"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-javascript"></a>Criar um bot com o SDK do Bot Builder para JavaScript

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Este início rápido orienta você na criação de um bot usando o gerador Yeoman Bot Builder e o Bot Builder SDK para JavaScript e, em seguida, testando-o com o Bot Framework Emulator. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://www.visualstudio.com/downloads)
- [Node.js](https://nodejs.org/)
- [ Yeoman](http://yeoman.io/), que pode usar um gerador para criar um bot para você
- [Emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator)
- Conhecimento de [restify](http://restify.com/) e programação assíncrona em JavaScript

> [!NOTE]
> Para algumas instalações, a etapa de instalação para restify está dando um erro relacionado ao node-gyp.
> Se este for o caso, tente executar `npm install -g windows-build-tools`.

## <a name="create-a-bot"></a>Criar um bot

Abra um prompt de comando elevado, crie um diretório e inicialize o pacote para seu bot.

```bash
md myJsBots
cd myJsBots
```

Certifique-se de que sua versão do npm esteja atualizada.
```bash
npm i npm
```

Em seguida, instale Yeoman e o gerador para JavaScript.

```bash
npm install -g yo
npm install -g generator-botbuilder
```

Em seguida, use o gerador para criar um bot de eco.

```bash
yo botbuilder
```

Yeoman pede-lhe alguma informação para criar o seu bot.

- Digite um nome para o seu bot.
- Digite uma descrição.
- Escolha o idioma do seu bot, `JavaScript`ou`TypeScript`.
- Escolha o modelo `Echo`.

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Na verdade, você não precisa escrever nenhum código adicional.

> [!NOTE]
> Para um bot Básico, você precisa de um modelo de linguagem do LUIS. É possível criar um em [luis.ai](https://www.luis.ai). Após a criação do modelo, atualize o arquivo .bot. O arquivo do bot deverá ser semelhante a [este](../v4sdk/bot-builder-service-file.md). 

## <a name="start-your-bot"></a>Inicie seu bot

No Powershell/Bash, altere os diretórios para aquele criado para o seu bot, e inicie-o com `npm start`. Neste ponto, seu bot está em execução localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
1. Inicie o emulador.
2. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador.
3. Selecione o arquivo .bot localizado no diretório em que você criou o projeto.

Envie uma mensagem para seu bot e o bot responderá com uma mensagem.
![Emulador em execução](../media/emulator-v4/emulator-running.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como funcionam os bots](../v4sdk/bot-builder-basics.md) 
