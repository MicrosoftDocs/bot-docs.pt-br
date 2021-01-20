---
ms.openlocfilehash: 39addd1c2d203e8c624fd36215bb756e76b52a71
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605062"
---
<!-- Include under "Create a bot" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-dotnet-sdk-quickstart.md -->

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)

### <a name="build-with-visual-studio"></a>Crie com o Visual Studio

No Visual Studio, crie um projeto de bot usando o modelo **Echo Bot (Bot Framework v4 – .NET Core 3.1)** . Escolha **Bots de IA** nos tipos de projetos para mostrar apenas os modelos de bot.

> [!div class="mx-imgBorder"]
> ![Caixa de diálogo Criar um projeto do Visual Studio](../../../media/azure-bot-quickstarts/bot-builder-dotnet-project-vs2019.png)

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Você não precisa de nenhum código adicional para testar o bot.

> [!NOTE]
> Se você criar um bot _principal_ , precisará de um modelo de linguagem Luis. Você pode criar um modelo de linguagem em [Luis.ai](https://www.luis.ai). Após a criação do modelo, atualize o arquivo de configuração.

<!--
> [!NOTE]
> If you see that the message cannot be sent, you might need to restart your machine as ngrok didn't get the needed privileges on your system yet (only needs to be done one time).
-->

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vc)

### <a name="build-with-visual-studio-code"></a>Criar com Visual Studio Code

Verifique se o [.NET Core 3,1](https://dotnet.microsoft.com/download) está instalado.

1. No Visual Studio Code, abra uma nova janela de terminal.
1. Navegue até o diretório no qual você deseja criar seu projeto de bot.
1. Crie um novo projeto de bot usando o seguinte comando:

   ```cmd
   dotnet new echobot -n MyEchoBot
   ```

# <a name="command-line"></a>[Linha de comando](#tab/cl)

### <a name="build-with-command-line"></a>Compilar com linha de comando

1. Abra uma nova janela de terminal.
1. Navegue até o diretório no qual você deseja criar seu projeto de bot.
1. Crie um novo projeto de bot usando o seguinte comando:

   ```cmd
   dotnet new echobot -n MyEchoBot
   ```

---
