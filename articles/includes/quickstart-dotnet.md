---
ms.openlocfilehash: 2096aa342fe954b9f9f1d128bc080c0e0e6efdce
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360796"
---
## <a name="prerequisites"></a>Pré-requisitos
- Visual Studio [2017](https://www.visualstudio.com/downloads)
- Modelo de SDK do Bot Framework v4 para [C#](https://aka.ms/bot-vsix)
- Bot Framework [Emulator](https://aka.ms/Emulator-wiki-getting-started)
- Conhecimento de [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) e programação assíncrona em [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Criar um bot
Instale o modelo BotBuilderVSIX.vsix que você baixou na seção de pré-requisitos.

No Visual Studio, crie um novo projeto de bot usando o modelo do Bot Framework Echo Bot V4.

![Projeto do Visual Studio](~/media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Se for necessário, altere o tipo de build do projeto para ``.Net Core 2.1``. Se também for necessário, atualize os [pacotes NuGet](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio) do `Microsoft.Bot.Builder`.

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Na verdade, você não precisa escrever nenhum código adicional.

## <a name="start-your-bot-in-visual-studio"></a>Iniciar seu bot no Visual Studio

Quando você clica no botão de execução, o Visual Studio cria o aplicativo, implanta-o no localhost e inicia o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, seu bot está em execução localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador. 
2. Escolha o arquivo .bot localizado no diretório em que você criou a solução do Visual Studio.

## <a name="interact-with-your-bot"></a>Interagir com o bot

Envie uma mensagem para seu bot e o bot responderá com uma mensagem.

![Emulador em execução](~/media/emulator-v4/emulator-running.png)

> [!NOTE]
> Se você perceber que a mensagem não pode ser enviada, talvez seja necessário reiniciar seu computador, pois o ngrok ainda não recebeu os privilégios necessários em seu sistema (só precisa ser feito uma vez).
