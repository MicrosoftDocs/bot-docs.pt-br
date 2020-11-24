---
ms.openlocfilehash: 80dd572a23fc590ea50b393c1f95af412ee6d4cc
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456773"
---
### <a name="to-set-breakpoints-in-visual-studio-code"></a>Para definir pontos de interrupção no Visual Studio Code

No Visual Studio Code, você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS Code, faça o seguinte:

1. Inicie o VS Code e abra a pasta de projeto do bot.
1. Defina os pontos de interrupção conforme necessário. Você pode definir pontos de interrupção passando o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.
1. Na barra de menus, clique em **Executar** e clique em **Iniciar Depuração**. O bot começará a ser executado no modo de depuração do Terminal no Visual Studio Code.

   ![Definir ponto de interrupção em C# no VS Code](~/media/bot-service-debug-bot/csharp-breakpoint-set.png)

1. Inicie o Bot Framework Emulator e conecte-se ao bot, conforme descrito no artigo [Depurar com o Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator).
1. No emulador, envie uma mensagem de bot (por exemplo, envie a mensagem "Hi"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar C# no VS Code](~/media/bot-service-debug-bot/breakpoint-caught-vscode.png)
