---
ms.openlocfilehash: f6d12b0031ec14e8c7c6e8bee876e7abb96c01f6
ms.sourcegitcommit: eacf1522d648338eebefe2cc5686c1f7866ec6a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174576"
---
## <a name="prerequisites"></a>Prerequisites

- [Visual Studio Code](https://www.visualstudio.com/downloads)
- [Node.js](https://nodejs.org/)
- [Yeoman](http://yeoman.io/), que usa um gerador para criar um bot para você
- [git](https://git-scm.com/)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [restify](http://restify.com/) e programação assíncrona em JavaScript

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
1. Se você ainda não tiver um diretório para seus bots JavaScript, crie um e altere os diretórios para ele. (Estamos criando um diretório para seus bots JavaScript em geral, apesar de criarmos apenas um bot neste tutorial.)

   ```bash
   mkdir myJsBots
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

- Digite um nome para o seu bot. (my-chat-bot)
- Digite uma descrição. (Demonstram os principais recursos do Microsoft Bot Framework)
- Escolha o idioma do seu bot. (JavaScript)
- Escolha o modelo a ser usado. (Bot de Eco – https://aka.ms/generator-botbuilder-templates)

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Na verdade, você não precisa escrever nenhum código adicional.

> [!NOTE]
> Se você optar por criar um bot `Core`, será necessário um modelo de linguagem do LUIS. É possível criar um em [luis.ai](https://www.luis.ai). Após a criação do modelo, atualize o arquivo de configuração.

## <a name="start-your-bot"></a>Inicie seu bot

Em um terminal ou prompt de comando, altere os diretórios para aquele criado para o seu bot e inicie-o com `npm start`. Neste ponto, seu bot está em execução localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

1. Instale o Emulador do Bot Framework.
2. Clique no link **Criar uma nova configuração de bot** na guia de “Boas-vindas” do emulador. 
3. Preencha os campos de seu bot. Use o endereço da página inicial do seu bot (normalmente http://localhost:3978), e acrescente informações de roteamento '/api/messages' para esse endereço.
4. Em seguida, clique em **Salvar e conectar-se**.

Envie uma mensagem para seu bot e o bot responderá com uma mensagem.
![Emulador em execução](../media/emulator-v4/js-quickstart.png)
