---
ms.openlocfilehash: 5e103e1e260db865d32a1c2081ef157143abe2c1
ms.sourcegitcommit: eb0e5dec0ecd4e375d33825030b1ba46ff6e032c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791314"
---
## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [restify](http://restify.com/) e programação assíncrona em JavaScript
- [Visual Studio Code](https://www.visualstudio.com/downloads) ou seu IDE favorito, se você quiser editar o código de bot.

> [!NOTE]
> A instalação das ferramentas de compilação do Windows listadas abaixo só é necessária se você usar o Windows como seu sistema operacional de desenvolvimento.
> Para algumas instalações, a etapa de instalação para restify está dando um erro relacionado ao node-gyp.
> Se esse for o caso, você poderá tentar executar esse comando com permissões elevadas.
> Essa chamada também pode ficar suspensa sem sair se o python já estiver instalado no seu sistema:

> ```bash
> # only run this command if you are on Windows. Read the above note.
> npm install -g windows-build-tools
> ```

## <a name="create-a-bot"></a>Criar um bot

Para criar seu bot e inicializar seus pacotes

1. Abra um prompt de comandos com privilégios elevados ou terminais.

1. Alterne para o diretório dos seus bots do JavaScript. Crie-o primeiro se você ainda não tiver um.

   ```bash
   mkdir myJsBots
   cd myJsBots
   ```

1. Certifique-se de que sua versão do npm esteja atualizada.

   ```bash
   npm install -g npm
   ```

1. Em seguida, instale ou atualize o Yeoman e o gerador para JavaScript. (O Yeoman é uma ferramenta para criar aplicativos. Para saber mais, confira [yeoman.io](https://yeoman.io)).

   ```bash
   npm install -g yo generator-botbuilder
   ```

1. Em seguida, use o gerador para criar um bot de eco.

   ```bash
   yo botbuilder
   ```

   Yeoman pede-lhe alguma informação para criar o seu bot. Neste tutorial, use os valores padrão.

   ```text
   ? What's the name of your bot? my-chat-bot
   ? What will your bot do? Demonstrate the core capabilities of the Microsoft Bot Framework
   ? What programming language do you want to use? JavaScript
   ? Which template would you like to start with? Echo Bot - https://aka.ms/bot-template-echo
   ? Looking good.  Shall I go ahead and create your new bot? Yes
   ```

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Você não precisa de nenhum código adicional para testar o bot.

> [!NOTE]
> Se você optar por criar um bot `Core`, será necessário um modelo de linguagem LUIS. (Você pode criar um modelo de linguagem em [luis.ai](https://www.luis.ai)). Após a criação do modelo, atualize o arquivo de configuração.

## <a name="start-your-bot"></a>Inicie seu bot

Em um terminal ou prompt de comando, altere os diretórios para aquele criado para o seu bot e inicie-o com `npm start`.

```bash
cd my-chat-bot
npm start
```

Neste ponto, seu bot está em execução localmente na porta 3978.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

1. Instale o Emulador do Bot Framework.

2. Clique em **Abrir Bot** na guia **Bem-vindo** do Emulador.

3. Insira a URL do bot, que é a URL da porta local, com /api/messages adicionado ao caminho, normalmente `http://localhost:3978/api/messages`.

   <!--This is the same process in the Emulator for all three languages.-->
   ![abrir uma tela de bot](../media/python/quickstart/open-bot.png)

4. E clique em **Conectar**.

   Envie uma mensagem para seu bot e o bot responderá com uma mensagem.

   ![Emulador em execução](../media/emulator-v4/js-quickstart.png)
