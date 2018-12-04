---
title: Depurar um bot | Microsoft Docs
description: Saiba como depurar um bot criado usando o Serviço de Bot.
author: v-ducvo
ms.author: v-ducvo
keywords: SDK do Bot Builder, depurar o bot, testar o bot, emulador do bot, emulador
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/13/2018
ms.openlocfilehash: fcda5c57a96b6f93aad965e868c824577616f630
ms.sourcegitcommit: 6c719b51c9e4e84f5642100a33fe346b21360e8a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52452038"
---
# <a name="debug-a-bot"></a>Depurar um bot

Este artigo descreve como depurar seu bot usando um ambiente de desenvolvimento integrado (IDE), como o Visual Studio ou Visual Studio Code e o emulador de Bot Framework. Embora você possa usar esses métodos para depurar qualquer bot localmente, este artigo usa um bot de [C#](~/dotnet/bot-builder-dotnet-sdk-quickstart.md) e de [JS](~/javascript/bot-builder-javascript-quickstart.md) criado no início rápido.

## <a name="prerequisites"></a>Pré-requisitos 
- Baixe e instale o [emulador do Bot Framework](https://aka.ms/Emulator-wiki-getting-started).
- Baixe e instale o [Visual Studio Code](https://code.visualstudio.com) ou o [Visual Studio](https://www.visualstudio.com/downloads) (Community Edition ou superior).

### <a name="debug-a-javascript-bot-using-command-line-and-emulator"></a>Depure um bot de JavaScript usando a linha de comando e o emulador

Para executar um bot de JavaScript usando a linha de comando e testar o bot com o emulador, faça o seguinte:
1. Na linha de comando, altere os diretórios para o diretório de projeto do seu bot.
1. Inicie o bot, executando o comando **node app.js**.
1. Inicie o emulador e conecte-se ao ponto de extremidade do bot (por exemplo: **http://localhost:3978/api/messages**). Se esta for a primeira vez que você estiver executando o bot, clique em **Arquivo > Novo Bot** e siga as instruções na tela. Caso contrário, clique em **Arquivo > Abrir Bot** para abrir um bot existente. Uma vez que este bot estiver em execução localmente no seu computador, você pode deixar os campos **ID do aplicativo MSA** e **senha de aplicativo do MSA** em branco. Para obter mais informações, consulte [Depurar com o emulador](bot-service-debug-emulator.md).
1. No emulador, envie uma mensagem a seu bot (por exemplo: enviar a mensagem "Olá"). 
1. Use os painéis **Inspector** e **Log** no lado direito da janela do emulador para depurar seu bot. Por exemplo, clicar em qualquer uma das bolhas de mensagens (por exemplo: a bolha de mensagem "Olá" na captura de tela abaixo) mostra os detalhes dessa mensagem no painel **Inspetor**. Você pode usá-lo para exibir as solicitações e respostas, já que as mensagens são trocadas entre o emulador e o bot. Como alternativa, você pode clicar em qualquer texto vinculado no painel **Log** para exibir os detalhes no painel **Inspetor**.

   ![Painel Inspetor no emulador](~/media/bot-service-debug-bot/emulator_inspector.png)

### <a name="debug-a-javascript-bot-using-breakpoints-in-visual-studio-code"></a>Depurar um bot de JavaScript usando os pontos de interrupção no Visual Studio Code

No Visual Studio Code, você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS Code, faça o seguinte:

1. Inicie o VS Code e abra a pasta de projeto do bot.
2. Na barra de menus, clique em **Debug** e clique em **Iniciar depuração**. Se você for solicitado a selecionar um mecanismo de tempo de execução para executar seu código, selecione **Node. js**. Neste ponto, o bot está em execução localmente. 
<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->
3. Defina o ponto de interrupção conforme necessário. No VS Code, você pode definir pontos de interrupção ao passar o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.

   ![Definir ponto de interrupção no VS Code](~/media/bot-service-debug-bot/breakpoint-set.png)

4. Inicie o emulador de Bot Framework e conecte-se ao seu bot, conforme descrito na seção acima. 
5. No emulador, envie uma mensagem a seu bot (por exemplo: enviar a mensagem "Olá"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar no VS Code](~/media/bot-service-debug-bot/breakpoint-caught.png)

### <a name="debug-a-c-bot-using-breakpoints-in-visual-studio"></a>Depurar um bot de C# usando os pontos de interrupção no Visual Studio

No Visual Studio (VS), você pode definir pontos de interrupção e executar o bot no modo de depuração para percorrer seu código. Para definir pontos de interrupção no VS, faça o seguinte:

1. Navegue até a pasta de bot e abra o arquivo **.sln**. Isso abrirá a solução no VS.
2. Na barra de menus, clique em **Compilar** e clique em **Compilar Solução**.
3. No **Gerenciador de Soluções**, clique em **EchoWithCounterBot.cs**. Esse arquivo define sua lógica principal do bot. Defina o ponto de interrupção conforme necessário. No VS, você pode definir pontos de interrupção ao passar o mouse sobre a coluna à esquerda dos números de linha. Um pequeno ponto vermelho será exibido. Se você clicar no ponto, o ponto de interrupção é definido. Se você clicar no ponto novamente, o ponto de interrupção é removido.
5. Na barra de menus, clique em **Debug** e clique em **Iniciar depuração**. Neste ponto, o bot está em execução localmente. 

<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->

   ![Definir ponto de interrupção no VS](~/media/bot-service-debug-bot/breakpoint-set-vs.png)

7. Inicie o emulador de Bot Framework e conecte-se ao seu bot, conforme descrito na seção acima. 
8. No emulador, envie uma mensagem a seu bot (por exemplo: enviar a mensagem "Olá"). A execução será interrompida na linha onde você colocar o ponto de interrupção.

   ![Depurar no VS](~/media/bot-service-debug-bot/breakpoint-caught-vs.png)

::: moniker range="azure-bot-service-3.0" 

## <a id="debug-csharp-serverless"></a> Depurar um plano de consumo C\# bot de funções

O ambiente sem servidor C\# do plano de consumo no serviço de Bot tem muito mais em comum com o Node.js do que um típico aplicativo C\# porque ele requer um host de tempo de execução, bem como o mecanismo de Node. No Azure, o tempo de execução faz parte do ambiente de hospedagem na nuvem, mas você deve replicar esse ambiente localmente em sua área de trabalho. 

### <a name="prerequisites"></a>Pré-requisitos

Antes que você possa depurar seu bot C# de plano de consumo, você deve concluir essas tarefas.

- Baixe o código-fonte para o bot (do Azure), conforme descrito em [Configurar implantação contínua](bot-service-continuous-deployment.md).
- Baixe e instale o [emulador do Bot Framework](https://aka.ms/Emulator-wiki-getting-started).
- Instalação da <a href="https://www.npmjs.com/package/azure-functions-cli" target="_blank">CLI do Azure Functions</a>.
- Instale o <a href="https://github.com/dotnet/cli" target="_blank">DotNet CLI</a>.
  
Se você quiser ser capaz de depurar seu código usando pontos de interrupção no Visual Studio 2017, você também deve concluir essas tarefas.
  
- Baixe e instale <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> (Community Edition ou superior).
- Baixe e instale o <a href="https://visualstudiogallery.msdn.microsoft.com/e6bf6a3d-7411-4494-8a1e-28c1a8c4ce99" target="_blank">Extensão do Visual Studio Executor de tarefas de comando</a>.

> [!NOTE]
> Atualmente, não há suporte para Visual Studio Code.

### <a name="debug-a-consumption-plan-c-functions-bot-using-the-emulator"></a>Depurar um bot de funções C# do plano de consumo usando o emulador

A maneira mais simples para depurar seu bot localmente é iniciar o bot e, em seguida, conectá-lo ao emulador do Bot Framework. 
Primeiro, abra um prompt de comando e navegue até a pasta onde o arquivo **project.json** está localizado no repositório. Em seguida, execute o comando `dotnet restore` para restaurar os vários pacotes que são referenciados no seu bot.

> [!NOTE]
> O Visual Studio 2017 altera como o Visual Studio trata as dependências. Enquanto o Visual Studio 2015 usa **project.json** para tratar as dependências, o Visual Studio 2017 usa um modelo **csproj** durante o carregamento no Visual Studio. Se você estiver usando o Visual Studio 2017, <a href="https://aka.ms/bf-debug-project">baixe este arquivo **.csproj** </a> para a pasta **/mensagens** em seu repositório antes de executar o comando `dotnet restore`.

![Prompt de comando](~/media/bot-service-debug-bot/csharp-azureservice-debug-envconfig.png)

Em seguida, execute `debughost.cmd` para carregar e iniciar seu bot. 

![Prompt de comando executa debughost.cmd](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughost.png)

Neste ponto, o bot está em execução localmente. Na janela do console, copie o ponto de extremidade que debughost está escutando (neste exemplo, `http://localhost:3978`). Em seguida, inicie o emulador de Bot Framework e cole o ponto de extremidade na barra de endereços do emulador. Neste exemplo, você também deve acrescentar `/api/messages` ao ponto de extremidade. Uma vez que você não precisa de segurança para depuração local, você pode deixar os campos **ID do aplicativo Microsoft** e **Microsoft App Password** em branco. Clique em **Connect** para estabelecer uma conexão ao seu bot usando o ponto de extremidade especificado.

![Configurar o emulador](~/media/bot-service-debug-bot/mac-azureservice-emulator-config.png)

Depois de conectar o emulador ao seu bot, envie uma mensagem ao seu bot digitando um texto na caixa de texto que está localizada na parte inferior da janela do emulador (ou seja, onde aparece **Digite sua mensagem...**  no canto inferior esquerdo). Ao usar os painéis **Log** e **Inspetor** no lado direito da janela do emulador, você pode exibir as solicitações e respostas conforme enquanto as mensagens são trocadas entre o emulador e o bot.

![testar por meio do emulador](~/media/bot-service-debug-bot/mac-azureservice-debug-emulator.png)

Além disso, você pode exibir os detalhes do log na janela do console.

![Janela do console](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughostlogging.png)

::: moniker-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Depure seu bot usando arquivos de transcrição](~/v4sdk/bot-builder-debug-transcript.md).
