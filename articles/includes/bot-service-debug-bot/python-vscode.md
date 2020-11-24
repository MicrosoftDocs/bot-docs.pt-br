---
ms.openlocfilehash: fa2156a9f35db7a450c02f35bc477415a9185093
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456772"
---
### <a name="to-set-breakpoints-in-visual-studio-code"></a>Para definir pontos de interrupção no Visual Studio Code

No Visual Studio Code, você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Confira também [Criar um bot com o SDK do Bot Framework para Python](~/python/bot-builder-python-quickstart.md).

1. Instale a [extensão Python](https://aka.ms/vscode-python-extension) no VS Code, caso você ainda não o tenha feito. Essa extensão fornece suporte avançado para Python no VS Code, incluindo a depuração.
1. Inicie o VS Code e abra a pasta de projeto do bot.
1. Defina os pontos de interrupção conforme necessário. Você pode definir pontos de interrupção passando o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.
1. Selecione o `app.py`.
1. Na barra de menus, clique em **Debug** e clique em **Iniciar depuração**.
1. Selecione **Arquivo do Python** para depurar o arquivo selecionado.

   ![Definir pontos de interrupção do Python no VS Code](~/media/bot-service-debug-bot/bot-debug-python-breakpoints.png)

1. Inicie o Bot Framework Emulator e conecte-se ao bot, conforme descrito no artigo [Depurar com o Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator).
1. No emulador, envie uma mensagem de bot (por exemplo, envie a mensagem "Hi"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar Python no VS Code](~/media/bot-service-debug-bot/bot-debug-python-breakpoint-caught.png)

Para obter mais informações, confira [Depurar código em Python](https://aka.ms/bot-debug-python).
