---
ms.openlocfilehash: 0570ec6a44c9fe1b007c1fd1b8c335288baa63cb
ms.sourcegitcommit: dbbfcf45a8d0ba66bd4fb5620d093abfa3b2f725
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67464852"
---
## <a name="prerequisites"></a>Pré-requisitos
- [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads)
- [Modelo de SDK do Bot Framework v4 para C#](https://aka.ms/bot-vsix)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) e [programação assíncrona em C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Criar um bot
Instale o modelo BotBuilderVSIX.vsix que você baixou na seção de pré-requisitos.

No Visual Studio, crie um novo projeto de bot usando o modelo do **Echo Bot (Bot Framework v4)** .

![Projeto do Visual Studio](~/media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Se for necessário, altere o tipo de build do projeto para ``.Net Core 2.1``. Se também for necessário, atualize os [pacotes NuGet](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) do `Microsoft.Bot.Builder`.

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Na verdade, você não precisa escrever nenhum código adicional.

## <a name="start-your-bot-in-visual-studio"></a>Iniciar seu bot no Visual Studio

Quando você clica no botão de execução, o Visual Studio cria o aplicativo, implanta-o no localhost e inicia o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, seu bot está em execução localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Criar uma nova configuração de bot** na guia de “Boas-vindas” do emulador. 
2. Preencha os campos de seu bot. Use o endereço da página inicial do seu bot (normalmente http://localhost:3978), e acrescente informações de roteamento '/api/messages' para esse endereço.
3. Em seguida, clique em **Salvar e conectar-se**.

## <a name="interact-with-your-bot"></a>Interagir com o bot

Envie uma mensagem para seu bot e o bot responderá com uma mensagem.

![Emulador em execução](~/media/emulator-v4/emulator-running.png)

> [!NOTE]
> Caso você perceba que a mensagem não pode ser enviada, talvez seja necessário reiniciar seu computador, pois o ngrok ainda não recebeu os privilégios necessários em seu sistema (só precisa ser feito uma vez).
