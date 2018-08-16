---
title: Criar um bot com o SDK do Bot Builder para .NET | Microsoft Docs
description: Criar um bot com o SDK do Bot Builder para .NET, um framework de construção de bot avançado.
keywords: SDK do Bot Builder, criar um bot, início rápido, .NET, introdução
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 04/27/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 98c6103f0cd38bf6d3f477735dafcf01952304d5
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297411"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-v4-for-net"></a>Criar um bot com o SDK do Bot Builder v4 para .NET
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Este início rápido irá orientá-lo na construção de um bot, usando o modelo de Aplicativo de Bot e o SDK do Bot Builder para .NET e, em seguida, testá-o com o Bot Framework Emulator. Isso é baseado no [SDK v4 do Microsoft Bot Builder](https://github.com/Microsoft/botbuilder-dotnet).

## <a name="prerequisites"></a>Pré-requisitos
- Visual Studio [2017](https://www.visualstudio.com/downloads)
- Modelo de SDK v4 do Bot Builder para [C#](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4)
- [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)
- Conhecimento de [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) e programação assíncrona em [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Criar um bot
Instale o modelo BotBuilderVSIX.vsix que você baixou na seção de pré-requisito. 

No Visual Studio, crie um novo projeto de bot.

![Projeto do Visual Studio](../media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Se necessário, atualize os [pacotes NuGet](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio).

## <a name="explore-code"></a>Explorar código
Abra o arquivo Startup.cs para revisar o código no método `ConfigureServices(IServiceCollection services)`. O middleware `CatchExceptionMiddleware` é adicionado ao pipeline de mensagens. Ele trata quaisquer exceções lançadas por outros middlewares ou pelo método OnTurn. 

```cs
options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
{
    await context.TraceActivity("EchoBot Exception", exception);
    await context.SendActivity("Sorry, it looks like something went wrong!");
}));
```

O middleware de estado de conversa usa armazenamento na memória. Ele lê e grava o EchoState para armazenamento.  A contagem de turnos na classe EchoState controla o número de mensagens enviadas ao bot. É possível usar uma técnica semelhante para manter o estado entre os turnos.

```cs
 IStorage dataStore = new MemoryStorage();
 options.Middleware.Add(new ConversationState<EchoState>(dataStore));
```

O método `Configure(IApplicationBuilder app, IHostingEnvironment env)` chama `.UseBotFramework` para rotear as atividades de entrada ao adaptador de bot. 

O método `OnTurn(ITurnContext context)` no arquivo EchoBot.cs é usado para verificar o tipo de atividade de entrada e enviar uma resposta ao usuário. 

```cs
public async Task OnTurn(ITurnContext context)
{
    // This bot is only handling Messages
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context
        var state = context.GetConversationState<EchoState>();

        // Bump the turn count. 
        state.TurnCount++;

        // Echo back to the user whatever they typed.
        await context.SendActivity($"Turn {state.TurnCount}: You sent '{context.Activity.Text}'");
    }
}
```
## <a name="start-your-bot"></a>Iniciar o bot

- A página `default.html` será exibida em um navegador.
- Anote o número da porta do localhost da página. Essa informação será necessária para interagir com o bot.

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar o bot

Neste ponto, o bot está executando localmente.
Em seguida, inicie o emulador e, em seguida, conecte o bot no emulador:

1. Clique no link **criar uma nova configuração de bot** na guia de "Boas-Vindas" do emulador. 

2. Insira um **Nome do bot** e insira o caminho do diretório para o código de bot. O arquivo de configuração do bot será salvo neste caminho.

3. Digite `http://localhost:port-number/api/messages` no campo **URL do Ponto de Extremidade**, em que *número da porta* corresponde ao número da porta mostrado no navegador onde o aplicativo está em execução.

4. Clique em **Conectar** para conectar o bot. Não é necessário especificar **ID do Aplicativo da Microsoft** e **Senha do Aplicativo da Microsoft**. Por enquanto, é possível deixar esses campos em branco. Você receberá essas informações posteriormente, quando registrar o bot.

## <a name="interact-with-your-bot"></a>Interagir com o bot

Envie "Olá" para o bot e o bot irá responder com "Turno 1: Você enviou um Olá" para a mensagem.

## <a name="next-steps"></a>Próximas etapas

Em seguida, [implante o bot para Azure ](../bot-builder-howto-deploy-azure.md) ou, consulte os conceitos que explicam um bot e como ele funciona.

> [!div class="nextstepaction"]
> [Conceitos básicos do Bot](../v4sdk/bot-builder-basics.md)
