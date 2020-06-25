---
ms.openlocfilehash: 776822d34675a3b2c40d0563ac13c3ff4fbe4d96
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073623"
---
### <a name="to-set-breakpoints-in-visual-studio-code"></a>Para definir pontos de interrupção no Visual Studio Code

No Visual Studio Code, você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS Code, faça o seguinte:

1. Inicie o VS Code e abra a pasta de projeto do bot.
2. Na barra de menus, clique em **Debug** e clique em **Iniciar depuração**. Se você for solicitado a selecionar um mecanismo de runtime para executar seu código, selecione **Node. js**. Neste ponto, o bot está em execução localmente.
3. Clique no arquivo **.js** e defina os pontos de interrupção conforme necessário. No VS Code, você pode definir pontos de interrupção ao passar o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.

   ![Definir ponto de interrupção no VS Code](~/media/bot-service-debug-bot/breakpoint-set.png)

4. Inicie o Bot Framework Emulator e conecte-se ao bot, conforme descrito no artigo [Depurar com o Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator).
5. Do emulador, envie uma mensagem ao bot (por exemplo: envie a mensagem "Olá"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar no VS Code](~/media/bot-service-debug-bot/breakpoint-caught.png)