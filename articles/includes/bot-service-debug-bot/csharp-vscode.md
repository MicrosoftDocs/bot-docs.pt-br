---
ms.openlocfilehash: 5e5779462ccf4f9a1852d2428e879ed9f2876904
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073638"
---
### <a name="to-set-breakpoints-in-visual-studio-code"></a>Para definir pontos de interrupção no Visual Studio Code

No Visual Studio Code, você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS Code, faça o seguinte:

1. Inicie o VS Code e abra a pasta de projeto do bot.
1. Defina os pontos de interrupção conforme necessário. Você pode definir pontos de interrupção passando o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.
1. Na barra de menus, clique em **Executar** e clique em **Iniciar Depuração**. O bot começará a ser executado no modo de depuração do Terminal no Visual Studio Code.

   ![Definir ponto de interrupção no VS Code](~/media/bot-service-debug-bot/csharp-breakpoint-set.png)

1. Inicie o Bot Framework Emulator e conecte-se ao bot, conforme descrito no artigo [Depurar com o Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator).
1. Do emulador, envie uma mensagem ao bot (por exemplo: envie a mensagem "Olá"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar no VS](~/media/bot-service-debug-bot/breakpoint-caught-vscode.png)