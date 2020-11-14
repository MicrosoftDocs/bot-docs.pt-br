---
ms.openlocfilehash: fd5193aeab7f3a9a95f55ba9c0269e645c18a960
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94596873"
---

<!-- Include under "Start your bot" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-dotnet-sdk-quickstart.md -->

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)

No Visual Studio, inicie o projeto. Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot está sendo executado localmente na porta 3978.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vc)

### <a name="run-with-visual-studio-code"></a>Executar com Visual Studio Code

1. Abra a pasta do projeto bot.
1. Na barra de menus, clique em **executar**.
1. No menu suspenso, selecione **executar sem depuração**.

   ![execução de VSC](~/media/azure-bot-quickstarts/bot-builder-dotnet-vsc-run.png)

1. Selecione o ambiente de **.NET Core** .

   ![VSC env](~/media/azure-bot-quickstarts/bot-builder-dotnet-vsc-environment.png)

Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot está sendo executado localmente na porta 3978.

# <a name="command-line"></a>[Linha de comando](#tab/cl)

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