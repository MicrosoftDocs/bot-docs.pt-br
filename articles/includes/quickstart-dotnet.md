---
ms.openlocfilehash: 85c847f3dfdea41b12daa24874aa43c1b68c5631
ms.sourcegitcommit: eb0e5dec0ecd4e375d33825030b1ba46ff6e032c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791240"
---
## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019 ou posterior](https://www.visualstudio.com/downloads)
- [Modelo de SDK do Bot Framework v4 para C#](https://aka.ms/bot-vsix)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) e [programação assíncrona em C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Criar um bot

Instale o [modelo BotBuilderVSIX.vsix](https://aka.ms/bot-vsix) que você baixou na seção de pré-requisitos.

No Visual Studio, crie um projeto de bot usando o modelo **Echo Bot (Bot Framework v4 – .NET Core 3.1)** . Escolha **Bots de IA** nos tipos de projetos para mostrar apenas os modelos de bot.

> [!div class="mx-imgBorder"]
> ![Caixa de diálogo Criar um projeto do Visual Studio](../media/azure-bot-quickstarts/bot-builder-dotnet-project-vs2019.png)

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Você não precisa de nenhum código adicional para testar o bot.

> [!NOTE]
> Se você criar um bot `Core`, precisará ter um modelo de linguagem do LUIS. (Crie um modelo de linguagem em [luis.ai](https://www.luis.ai)). Após a criação do modelo, atualize o arquivo de configuração.

> [!NOTE]
> As versões do .NET Core 2.1 e 3.1 dos modelos C# estão disponíveis.
> Ao criar bots no Visual Studio 2019, você deverá usar os modelos do .NET Core 3.1.
> As amostras de bot atuais usam modelos do .NET Core 3.1. Encontre as amostras que usam modelos do .NET Core 2.1 no branch [4.7-archive](https://github.com/microsoft/BotBuilder-Samples/tree/4.7-archive/samples/csharp_dotnetcore) do repositório BotBuilder-Samples.
> Para obter informações sobre como implantar bots do .NET Core 3.1 no Azure, confira [Implantar seu bot](~/bot-builder-deploy-az-cli.md).

## <a name="start-your-bot-in-visual-studio"></a>Iniciar seu bot no Visual Studio

Inicie o projeto no Visual Studio. Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo.

Neste ponto, o bot está sendo executado localmente na porta 3978.

## <a name="start-the-emulator-and-connect-to-your-bot"></a>Iniciar o Emulador e conectar-se ao seu bot

Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Instale o Emulador do Bot Framework.

2. Clique em **Abrir Bot** na guia **Bem-vindo** do Emulador.

3. Insira a URL do bot, que é a URL da porta local, com /api/messages adicionado ao caminho, normalmente `http://localhost:3978/api/messages`.

   <!--This is the same process in the Emulator for all three languages.-->
   ![abrir uma tela do bot](../media/python/quickstart/open-bot.png)

4. E clique em **Conectar**.

   Envie uma mensagem para seu bot e o bot responderá com uma mensagem.

   > [!div class="mx-imgBorder"]
   > ![Emulador em execução](../media/emulator-v4/cs-quickstart.png)

<!--
> [!NOTE]
> If you see that the message cannot be sent, you might need to restart your machine as ngrok didn't get the needed privileges on your system yet (only needs to be done one time).
-->
