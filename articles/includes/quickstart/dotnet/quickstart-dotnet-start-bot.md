---
ms.openlocfilehash: 28ddca45d7720478a2239c8d9050ccfaca5861d9
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759295"
---

<!-- Include under "Start your bot" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-dotnet-sdk-quickstart.md -->

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)

No Visual Studio, inicie o projeto. Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot está sendo executado localmente na porta 3978.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vc)

### <a name="run-with-visual-studio-code"></a>Executar com Visual Studio Code

Para executar o bot da VS Code:

1. Abra a pasta do projeto bot.
1. Vá para **executar** e, em seguida, selecione **executar sem depuração**.

   > [!div class="mx-imgBorder"]
   > ![execução de VSC](../../../media/azure-bot-quickstarts/bot-builder-dotnet-vsc-run.png)

   - Selecione o ambiente de **.NET Core** .

   > [!div class="mx-imgBorder"]
   > ![VSC env](../../../media/azure-bot-quickstarts/bot-builder-dotnet-vsc-environment.png)

   - Se esse comando tiver atualizado as configurações de inicialização, salve as alterações e execute o comando novamente.

Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot está sendo executado localmente na porta 3978.

# <a name="command-line"></a>[Linha de comando](#tab/cl)

Para executar o bot localmente em um prompt de comando ou terminal:

1. Altere os diretórios para a pasta do projeto para o bot.
1. Use `dotnet run` para iniciar o bot.

   ```cmd
   dotnet run
   ```

Isso criará o aplicativo e o implantará no localhost. A página da Web padrão do aplicativo não será exibida, mas, neste ponto, o bot está em execução localmente na porta 3978.

---
