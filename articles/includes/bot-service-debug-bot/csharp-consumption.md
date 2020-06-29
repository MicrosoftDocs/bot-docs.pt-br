---
ms.openlocfilehash: 7ba5eda74f54706ff897a602aea2727da2da0564
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081205"
---
### <a name="debug-a-consumption-plan-c-functions-bot"></a><a id="debug-csharp-serverless"></a> Depurar um plano de consumo C\# bot de funções

O ambiente sem servidor C\# do plano de consumo no serviço de Bot tem muito mais em comum com o Node.js do que um típico aplicativo C\# porque ele requer um host de runtime, bem como o mecanismo de Node. No Azure, o runtime faz parte do ambiente de hospedagem na nuvem, mas você deve replicar esse ambiente localmente em sua área de trabalho.

#### <a name="prerequisites"></a>Pré-requisitos

Antes que você possa depurar seu bot C# de plano de consumo, você deve concluir essas tarefas.

- Baixe o código-fonte para o bot (do Azure), conforme descrito em [Configurar implantação contínua](https://aka.ms/bot-framework-emulator-publish-continuous-deployment).
- Baixe e instale o [emulador do Bot Framework](https://aka.ms/Emulator-wiki-getting-started).
- Instalação da <a href="https://www.npmjs.com/package/azure-functions-cli" target="_blank">CLI do Azure Functions</a>.
- Instale o <a href="https://github.com/dotnet/cli" target="_blank">DotNet CLI</a>.

Se você quiser ser capaz de depurar seu código usando pontos de interrupção no Visual Studio 2017, você também deve concluir essas tarefas.

- Baixe e instale <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> (Community Edition ou superior).
- Baixe e instale o <a href="https://visualstudiogallery.msdn.microsoft.com/e6bf6a3d-7411-4494-8a1e-28c1a8c4ce99" target="_blank">Extensão do Visual Studio Executor de tarefas de comando</a>.

> [!NOTE]
> Atualmente, não há suporte para Visual Studio Code.

#### <a name="debug-a-consumption-plan-c-functions-bot-using-the-emulator"></a>Depurar um bot de funções C# do plano de consumo usando o emulador

A maneira mais simples para depurar seu bot localmente é iniciar o bot e, em seguida, conectá-lo ao emulador do Bot Framework.
Primeiro, abra um prompt de comando e navegue até a pasta onde o arquivo **project.json** está localizado no repositório. Em seguida, execute o comando `dotnet restore` para restaurar os vários pacotes que são referenciados no seu bot.

> [!NOTE]
> O Visual Studio 2017 altera como o Visual Studio trata as dependências.
> Enquanto o Visual Studio 2015 usa **project.json** para tratar as dependências, o Visual Studio 2017 usa um modelo **csproj** durante o carregamento no Visual Studio.
> Se você estiver usando o Visual Studio 2017, baixe este arquivo [ **.csproj**](https://aka.ms/v3-dotnet-debug-csproj) para a pasta **/messages** em seu repositório antes de executar o comando `dotnet restore`.

![Prompt de comando](~/media/bot-service-debug-bot/csharp-azureservice-debug-envconfig.png)

Em seguida, execute `debughost.cmd` para carregar e iniciar seu bot.

![Prompt de comando executa debughost.cmd](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughost.png)

Neste ponto, o bot está em execução localmente. Na janela do console, copie o ponto de extremidade que debughost está escutando (neste exemplo, `http://localhost:3978`). Em seguida, inicie o emulador de Bot Framework e cole o ponto de extremidade na barra de endereços do emulador. Neste exemplo, você também deve acrescentar `/api/messages` ao ponto de extremidade. Uma vez que você não precisa de segurança para depuração local, você pode deixar os campos **ID do aplicativo Microsoft** e **Microsoft App Password** em branco. Clique em **Connect** para estabelecer uma conexão ao seu bot usando o ponto de extremidade especificado.

![Configurar o emulador](~/media/bot-service-debug-bot/mac-azureservice-emulator-config.png)

Depois de conectar o emulador ao seu bot, envie uma mensagem ao seu bot digitando um texto na caixa de texto que está localizada na parte inferior da janela do emulador (ou seja, onde aparece **Digite sua mensagem...**  no canto inferior esquerdo). Ao usar os painéis **Log** e **Inspetor** no lado direito da janela do emulador, você pode exibir as solicitações e respostas conforme enquanto as mensagens são trocadas entre o emulador e o bot.

![testar por meio do emulador](~/media/bot-service-debug-bot/mac-azureservice-debug-emulator.png)

Além disso, você pode exibir os detalhes do log na janela do console.

![Janela do console](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughostlogging.png)