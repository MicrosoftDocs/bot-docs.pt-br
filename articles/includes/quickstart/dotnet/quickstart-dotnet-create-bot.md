---
ms.openlocfilehash: d800747de42dd0db21167c33fc353bd2cedd3f69
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94596868"
---
<!-- Include under "Create a bot" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-dotnet-sdk-quickstart.md -->

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)
### <a name="build-with-visual-studio"></a>Crie com o Visual Studio

No Visual Studio, crie um projeto de bot usando o modelo **Echo Bot (Bot Framework v4 – .NET Core 3.1)** . Escolha **Bots de IA** nos tipos de projetos para mostrar apenas os modelos de bot.

> [!div class="mx-imgBorder"]
> ![Caixa de diálogo Criar um projeto do Visual Studio](~/media/azure-bot-quickstarts/bot-builder-dotnet-project-vs2019.png)

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Você não precisa de nenhum código adicional para testar o bot.

> [!NOTE]
> Se você optar por criar um bot `Core`, será necessário um modelo de linguagem LUIS. Você pode criar um modelo de linguagem em [Luis.ai](https://www.luis.ai). Após a criação do modelo, atualize o arquivo de configuração.

<!--
> [!NOTE]
> If you see that the message cannot be sent, you might need to restart your machine as ngrok didn't get the needed privileges on your system yet (only needs to be done one time).
-->

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vc)

### <a name="build-with-visual-studio-code"></a>Criar com Visual Studio Code

Verifique se o [.NET Core 3,1](https://dotnet.microsoft.com/download) está instalado.

1. No Visual Studio Code, abra uma nova janela de terminal.
1. Crie um novo projeto de bot usando o seguinte comando:

   ```cmd
      dotnet new echobot -n MyEchoBot
   ```

# <a name="command-line"></a>[Linha de comando](#tab/cl)

### <a name="build-with-command-line"></a>Compilar com linha de comando

1. Abra uma nova janela de terminal.
1. Crie um novo projeto de bot usando o seguinte comando:

   ```cmd
      dotnet new echobot -n MyEchoBot
   ```

---
