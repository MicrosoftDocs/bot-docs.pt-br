---
ms.openlocfilehash: 0319658a28e2c5cac65310b93acc3cbcad7c6b03
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124418"
---
## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019 ou posterior](https://www.visualstudio.com/downloads)
- [Modelo de SDK do Bot Framework v4 para C#](https://aka.ms/bot-vsix)
- [.NET Core 3.1](https://dotnet.microsoft.com/download)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) e [programação assíncrona em C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/index)


## <a name="templates"></a>Modelos

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)

## <a name="visual-studio-templates"></a>modelos do Visual Studio

Instale o [modelo BotBuilderVSIX.vsix](https://aka.ms/bot-vsix) que você baixou na seção de pré-requisitos.

[!INCLUDE [dotnet vsix templates info](~/includes/vsix-templates-versions.md)]

# <a name="visual-studio-code--command-line"></a>[Visual Studio Code/linha de comando](#tab/vc+cl)

## <a name="visual-studio-code-and-command-line-templates"></a>Visual Studio Code e modelos de linha de comando

Os modelos do .NET Core ajudarão você a criar rapidamente novos bots de AI de conversa usando o bot Framework v4. A partir de maio de 2020, esses modelos e o código que eles geram exigem o .NET Core 3,1.

Em uma janela de console, execute as etapas mostradas abaixo.

1. Instale [SDK do .NET Core](https://dotnet.microsoft.com/download) versão 3,1 ou superior.
1. Você pode usar esse comando para determinar qual versão da interface de linha de comando do .NET Core está instalada.

   ```cmd
   dotnet --version
   ```

1. Instale os 3 modelos de C# da estrutura de bot: os modelos de bot de eco, núcleo e vazio.

   ```cmd
   dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
   dotnet new -i Microsoft.Bot.Framework.CSharp.CoreBot
   dotnet new -i Microsoft.Bot.Framework.CSharp.EmptyBot
   ```

1. Verifique se os modelos foram instalados corretamente.

   ```cmd
   dotnet new --list
   ```

> [!NOTE]
> As etapas de instalação acima instalarão todos os 3 modelos do bot Framework. Você não precisa instalar todos os 3 modelos e pode instalar apenas aqueles que serão usados. Este artigo utiliza o modelo de bot de eco.

---

## <a name="build-and-run-the-bot"></a>Compilar e executar o bot

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)

## <a name="build-and-run-the-bot-in-visual-studio"></a>Compilar e executar o bot no Visual Studio

No Visual Studio, crie um projeto de bot usando o modelo **Echo Bot (Bot Framework v4 – .NET Core 3.1)** . Escolha **Bots de IA** nos tipos de projetos para mostrar apenas os modelos de bot.

> [!div class="mx-imgBorder"]
> ![Caixa de diálogo Criar um projeto do Visual Studio](../media/azure-bot-quickstarts/bot-builder-dotnet-project-vs2019.png)

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Você não precisa de nenhum código adicional para testar o bot.

Inicie o projeto. Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot está sendo executado localmente na porta 3978.

> [!NOTE]
> Se você optar por criar um bot `Core`, será necessário um modelo de linguagem LUIS. (Você pode criar um modelo de linguagem em [luis.ai](https://www.luis.ai)). Após a criação do modelo, atualize o arquivo de configuração.

<!--
> [!NOTE]
> If you see that the message cannot be sent, you might need to restart your machine as ngrok didn't get the needed privileges on your system yet (only needs to be done one time).
-->

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vc)

## <a name="build-and-run-the-bot-in-visual-studio-code"></a>Criar e executar o bot no Visual Studio Code

Verifique se o [.NET Core 3,1](https://dotnet.microsoft.com/download) está instalado.

### <a name="create-a-new-project"></a>Criar um novo projeto

1. No Visual Studio Code, abra uma nova janela de terminal.
1. Crie um novo projeto de bot usando um dos comandos mostrados abaixo.

   1. Bot de eco

      ```cmd
         dotnet new echobot -n MyEchoBot
      ```

   1. Bot principal

      ```cmd
         dotnet new corebot -n MyCoreBot
      ```

   1. CoreBot com CoreBot. Test Project

      ```cmd
         dotnet new corebot -n MyCoreBotWithTests --include-tests
      ```

   1. Bot vazio

      ```cmd
         dotnet new emptybot -n MyEmptyBot
      ```

### <a name="run-the-bot-locally"></a>Executar o bot localmente

1. Abra a pasta do projeto bot.
1. Na barra de menus, clique em **executar**.
1. No menu suspenso, selecione **executar sem depuração**.

   ![execução de VSC](../media/azure-bot-quickstarts/bot-builder-dotnet-vsc-run.png)

1. Selecione o ambiente de **.NET Core** .

   ![execução de VSC](../media/azure-bot-quickstarts/bot-builder-dotnet-vsc-environment.png)

Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot está sendo executado localmente na porta 3978.

# <a name="command-line"></a>[Linha de comando](#tab/cl)

## <a name="build-and-run-the-bot-with-command-line"></a>Compilar e executar o bot com a linha de comando

### <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra uma nova janela de terminal.
1. Crie um novo projeto de bot usando um dos comandos mostrados abaixo.

   1. Bot de eco

      ```cmd
         dotnet new echobot -n MyEchoBot
      ```

   1. Bot principal

      ```cmd
         dotnet new corebot -n MyCoreBot
      ```

   1. CoreBot com CoreBot. Test Project

      ```cmd
         dotnet new corebot -n MyCoreBotWithTests --include-tests
      ```

   1. Bot vazio

      ```cmd
         dotnet new emptybot -n MyEmptyBot
      ```

### <a name="run-the-bot-locally"></a>Executar o bot localmente

Para executar o bot localmente, execute os comandos mostrados abaixo.

1. Altere para a pasta do projeto (por exemplo, EchoBot).

   ```cmd
      cd EchoBot
   ```

1. Execute o bot.

   ```cmd
      dotnet run
   ```

Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot está sendo executado localmente na porta 3978.

---

## <a name="start-the-emulator-and-connect-to-your-bot"></a>Iniciar o Emulador e conectar-se ao seu bot

1. Instale o Emulador do Bot Framework.

1. Clique em **Abrir Bot** na guia **Bem-vindo** do Emulador.

1. Insira a URL do bot, que é a URL da porta local, com /api/messages adicionado ao caminho, normalmente `http://localhost:3978/api/messages`.

   <!--This is the same process in the Emulator for all three languages.-->
   ![abrir uma tela de bot](../media/python/quickstart/open-bot.png)

1. E clique em **Conectar**.

   Envie uma mensagem para o bot e o bot responderá de volta.

   > [!div class="mx-imgBorder"]
   > ![Emulador em execução](../media/emulator-v4/cs-quickstart.png)
