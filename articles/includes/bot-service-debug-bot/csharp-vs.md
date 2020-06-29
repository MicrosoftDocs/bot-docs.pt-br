---
ms.openlocfilehash: fe6ec9a8a9fa3b6d16508ba5f6dd2b943a87dee3
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073655"
---
### <a name="to-set-breakpoints-in-visual-studio"></a>Para definir pontos de interrupção no Visual Studio

No Visual Studio (VS), você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS, faça o seguinte:

1. Navegue até a pasta de bot e abra o arquivo **.sln**. Isso abrirá a solução no VS.
2. Na barra de menus, clique em **Compilar** e clique em **Compilar Solução**.
3. No **Gerenciador de Soluções**, clique no arquivo **.cs** e defina os pontos de interrupção conforme necessário. Esse arquivo define sua lógica principal do bot. No VS, você pode definir pontos de interrupção ao passar o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção será definido. Se você clicar no ponto novamente, o ponto de interrupção será removido.
4. No menu, clique em **Depurar** e clique em **Iniciar Depuração**. Neste ponto, o bot está em execução localmente.

   ![Definir ponto de interrupção no VS](~/media/bot-service-debug-bot/breakpoint-set-vs.png)

<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->

5. Inicie o emulador de Bot Framework e conecte-se ao seu bot, conforme descrito na seção acima.
6. No emulador, envie uma mensagem a seu bot (por exemplo: enviar a mensagem "Olá"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar no VS](~/media/bot-service-debug-bot/breakpoint-caught-vs.png)