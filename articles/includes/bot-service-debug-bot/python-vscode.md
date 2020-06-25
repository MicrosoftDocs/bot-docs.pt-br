---
ms.openlocfilehash: 54a254670346080bc20065d4b3f7fd7270497391
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073629"
---
### <a name="to-set-breakpoints-in-visual-studio-code"></a>Para definir pontos de interrupção no Visual Studio Code

No Visual Studio Code, você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Confira também [Criar um bot com o SDK do Bot Framework para Python](~/python/bot-builder-python-quickstart.md).

1. Instale a [extensão Python](https://aka.ms/vscode-python-extension) no VS Code, caso você ainda não o tenha feito. Essa extensão fornece suporte avançado para Python no VS Code, incluindo a depuração.
1. Inicie o VS Code e abra a pasta de projeto do bot.
1. Defina os pontos de interrupção conforme necessário. Você pode definir pontos de interrupção passando o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.
1. Selecione o `app.py`.
1. Na barra de menus, clique em **Debug** e clique em **Iniciar depuração**.
1. Selecione **Arquivo do Python** para depurar o arquivo selecionado.

   ![Definir pontos de interrupção](~/media/bot-service-debug-bot/bot-debug-python-breakpoints.png)

1. Inicie o Bot Framework Emulator e conecte-se ao bot, conforme descrito no artigo [Depurar com o Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator).
1. Do emulador, envie uma mensagem ao bot (por exemplo: envie a mensagem "Olá"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar no VS Code](~/media/bot-service-debug-bot/bot-debug-python-breakpoint-caught.png)

Para obter mais informações, confira [Depurar código em Python](https://aka.ms/bot-debug-python).
