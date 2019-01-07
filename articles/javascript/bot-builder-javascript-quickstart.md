---
title: Crie um bot usando o Bot Builder SDK para JavaScript | Microsoft Docs
description: Crie rapidamente um bot usando o Bot Builder SDK para JavaScript.
keywords: início rápido, bot builder sdk, começando
author: jonathanfingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/30/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1804f7bb6a19fbe0cfced49cffa4e9d6d77005eb
ms.sourcegitcommit: f7a8f05fc05ff4a7212a437d540485bf68831604
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735926"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-javascript"></a>Criar um bot com o SDK do Bot Builder para JavaScript

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Este início rápido orienta você na criação de um único bot usando o gerador Yeoman Bot Builder e o Bot Builder SDK para JavaScript e, em seguida, testando-o com o Bot Framework Emulator.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://www.visualstudio.com/downloads)
- [Node.js](https://nodejs.org/)
- [Yeoman](http://yeoman.io/), que usa um gerador para criar um bot para você
- [git](https://git-scm.com/)
- [Emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator)
- Conhecimento de [restify](http://restify.com/) e programação assíncrona em JavaScript

> [!NOTE]
> Para algumas instalações, a etapa de instalação para restify está dando um erro relacionado ao node-gyp.
> Se esse for o caso, tente executar esse comando com permissões elevadas:
> ```bash
> npm install -g windows-build-tools
> ```

## <a name="create-a-bot"></a>Criar um bot

Para criar seu bot e inicializar seus pacotes

1. Abra um prompt de comandos com privilégios elevados ou terminais.
1. Se você ainda não tiver um diretório para seus bots JavaScript, crie um e altere os diretórios para ele. (Estamos criando um diretório para seus bots JavaScript em geral, apesar de criarmos apenas um bot neste tutorial.)

   ```bash
   md myJsBots
   cd myJsBots
   ```

1. Certifique-se de que sua versão do npm esteja atualizada.

   ```bash
   npm install -g npm
   ```

1. Em seguida, instale Yeoman e o gerador para JavaScript.

   ```bash
   npm install -g yo generator-botbuilder
   ```

1. Em seguida, use o gerador para criar um bot de eco.

   ```bash
   yo botbuilder
   ```

Yeoman pede-lhe alguma informação para criar o seu bot. Neste tutorial, use os valores padrão.

- Digite um nome para o seu bot. (myChatBot)
- Digite uma descrição. (Demonstram os principais recursos do Microsoft Bot Framework)
- Escolha o idioma do seu bot. (JavaScript)
- Escolha o modelo a ser usado. (Echo)

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Na verdade, você não precisa escrever nenhum código adicional.

> [!NOTE]
> Se você optar por criar um bot `Basic`, será necessário um modelo de linguagem do LUIS. É possível criar um em [luis.ai](https://www.luis.ai). Após a criação do modelo, atualize o arquivo .bot. O arquivo do bot deverá ser semelhante a [este](../v4sdk/bot-builder-service-file.md).

## <a name="start-your-bot"></a>Inicie seu bot

Em um terminal ou prompt de comando, altere os diretórios para aquele criado para o seu bot e inicie-o com `npm start`. Neste ponto, seu bot está em execução localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

1. Instale o Emulador do Bot Framework.
2. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador.
3. Selecione o arquivo .bot localizado no diretório em que você criou o projeto.

Envie uma mensagem para seu bot e o bot responderá com uma mensagem.
![Emulador em execução](../media/emulator-v4/js-quickstart.png)

## <a name="additional-resources"></a>Recursos adicionais

Confira [túnel (ngrok)](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-(ngrok)) para saber como se conectar a um bot hospedado remotamente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como funcionam os bots](../v4sdk/bot-builder-basics.md)
