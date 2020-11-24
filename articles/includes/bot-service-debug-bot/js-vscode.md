---
ms.openlocfilehash: 87084076b137cc92de81c0518a25e5452cbbd3ab
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456771"
---
### <a name="to-set-breakpoints-in-visual-studio-code"></a>Para definir pontos de interrupção no Visual Studio Code

No Visual Studio Code, você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS Code, faça o seguinte:

1. Inicie o VS Code e abra a pasta de projeto do bot.
2. Na barra de menus, clique em **Debug** e clique em **Iniciar depuração**. Se você for solicitado a selecionar um mecanismo de runtime para executar seu código, selecione **Node. js**. Neste ponto, o bot está em execução localmente.
3. Clique no arquivo **.js** e defina os pontos de interrupção conforme necessário. No VS Code, você pode definir pontos de interrupção ao passar o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.

   ![Definir o ponto de interrupção de JavaScript no VS Code](~/media/bot-service-debug-bot/breakpoint-set.png)

4. Inicie o Bot Framework Emulator e conecte-se ao bot, conforme descrito no artigo [Depurar com o Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator).
5. No emulador, envie uma mensagem de bot (por exemplo, envie a mensagem "Hi"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar JavaScript no VS Code](~/media/bot-service-debug-bot/breakpoint-caught.png)
