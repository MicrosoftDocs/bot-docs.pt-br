---
ms.openlocfilehash: cf13fcd665c43d805d26f40f9dfb0fa66e0c5b95
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456774"
---
### <a name="to-set-breakpoints-in-visual-studio"></a>Para definir pontos de interrupção no Visual Studio

No Visual Studio (VS), você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS, faça o seguinte:

1. Navegue até a pasta de bot e abra o arquivo **.sln**. Isso abrirá a solução no VS.
1. Na barra de menus, clique em **Compilar** e clique em **Compilar Solução**.
1. No **Gerenciador de Soluções**, clique no arquivo **.cs** e defina os pontos de interrupção conforme necessário. Esse arquivo define sua lógica principal do bot. No VS, você pode definir pontos de interrupção ao passar o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção será definido. Se você clicar no ponto novamente, o ponto de interrupção será removido.
1. No menu, clique em **Depurar** e clique em **Iniciar Depuração**. Neste ponto, o bot está em execução localmente.

   ![Definir ponto de interrupção em C# no VS](~/media/bot-service-debug-bot/breakpoint-set-vs.png)

<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->

1. Inicie o emulador de Bot Framework e conecte-se ao seu bot, conforme descrito na seção acima.
1. No emulador, envie uma mensagem para o bot (por exemplo: enviar a mensagem "Olá"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar C# no VS](~/media/bot-service-debug-bot/breakpoint-caught-vs.png)
