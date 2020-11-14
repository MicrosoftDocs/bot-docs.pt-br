---
ms.openlocfilehash: 1fc0a207f23ac57114fd0ac86b4bb83305db8b23
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94597438"
---
<!-- Include under "Prerequisites" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-javascript-quickstart.md -->

- [Node.js](https://nodejs.org/)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [restify](http://restify.com/) e programação assíncrona em JavaScript
- [Visual Studio Code](https://www.visualstudio.com/downloads) ou seu IDE favorito, se você quiser editar o código de bot.

### <a name="templates"></a>Modelos

Para instalar o Yeoman e o gerador de Yeoman para o bot Framework v4:

1. Abra um prompt de comandos com privilégios elevados ou terminais.

1. Alterne para o diretório dos seus bots do JavaScript. Crie-o primeiro se você ainda não tiver um.

   ```bash
   mkdir myJsBots
   cd myJsBots
   ```

1. Verifique se você tem as versões mais recentes do NPM e do Yeoman.

   ```cmd
   npm install -g npm
   npm install -g yo
   ```

1. Instale o gerador de Yeoman.
Yeoman é uma ferramenta para a criação de aplicativos. Para obter mais informações, consulte [Yeoman.Io](https://yeoman.io).

    ```cmd
        npm install -g generator-botbuilder
    ```

> [!NOTE]
> A instalação das ferramentas de compilação do Windows listadas abaixo só é necessária se você usar o Windows como seu sistema operacional de desenvolvimento.
> Para algumas instalações, a etapa de instalação para restify está dando um erro relacionado ao node-gyp.
> Se esse for o caso, você poderá tentar executar esse comando com permissões elevadas.
> Essa chamada também pode ficar suspensa sem sair se o python já estiver instalado no seu sistema:

> ```bash
> # only run this command if you are on Windows. Read the above note.
> npm install -g windows-build-tools
> ```