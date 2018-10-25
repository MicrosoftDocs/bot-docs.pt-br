---
title: Criar um bot com o SDK do construtor de Bot para .NET | Microsoft Docs
description: Crie um bot com o Bot Builder SDK for .NET, um poderoso framework de construção bot.
keywords: SDK do Bot Builder, criar um bot, início rápido, .NET, introdução, bot C#
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 09/23/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d299e4bdfd503475bf1ec560da2aff1d3a199e47
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326383"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-net"></a>Criar um bot com o SDK do construtor de Bot para .NET
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Este guia de início rápido oferece orientações sobre como criar um bot usando o modelo de C# e testá-lo com o Bot Framework Emulator. 

## <a name="prerequisites"></a>Pré-requisitos
- Visual Studio [2017](https://www.visualstudio.com/downloads)
- Modelo de SDK v4 do Bot Builder para [C#](https://botbuilder.myget.org/feed/aitemplates/package/vsix/BotBuilderV4.fbe0fc50-a6f1-4500-82a2-189314b7bea2)
- Bot Framework [Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)
- Conhecimento de [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) e programação assíncrona em [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Criar um bot
Instale o modelo BotBuilderVSIX.vsix que você baixou na seção de pré-requisitos. 

No Visual Studio, crie um novo projeto de bot.

![Projeto do Visual Studio](../media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Se for necessário, altere o tipo de build do projeto para ``.Net Core 2.1`` e atualize [Pacotes do NuGet](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio).

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Na verdade, você não precisa escrever nenhum código adicional.

## <a name="start-your-bot-in-visual-studio"></a>Iniciar seu bot no Visual Studio

Quando você clica no botão de execução, o Visual Studio cria o aplicativo, implanta-o no localhost e inicia o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, seu bot está em execução localmente.

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Clique no link **Abrir Bot** na guia "Boas-vindas" do emulador. 
2. Escolha o arquivo .bot localizado no diretório em que você criou a solução do Visual Studio.

## <a name="interact-with-your-bot"></a>Interagir com o bot

Envie uma mensagem para seu bot e o bot responderá com uma mensagem.

![Emulador em execução](../media/emulator-v4/emulator-running.png)

> [!NOTE]
> Se você perceber que a mensagem não pode ser enviada, talvez seja necessário reiniciar seu computador, pois o ngrok ainda não recebeu os privilégios necessários em seu sistema (só precisa ser feito uma vez).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como funcionam os bots](../v4sdk/bot-builder-basics.md) 
