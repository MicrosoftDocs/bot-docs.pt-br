---
title: Criar um bot com o SDK do construtor de Bot para .NET | Microsoft Docs
description: Crie um bot com o Bot Builder SDK for .NET, um poderoso framework de construção bot.
keywords: Bot Builder SDK, crie um bot, quickstart, começando
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 04/27/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 16ca658dddbba987545b7aa18c1e8f63f13983f1
ms.sourcegitcommit: 97bb24f15041caccef4ca5736aa14f144881e0c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39567565"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-net"></a>Criar um bot com o SDK do construtor de Bot para .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-quickstart.md)
> - [Node.js](../nodejs/bot-builder-nodejs-quickstart.md)
> - [Serviço de bot](../bot-service-quickstart.md)
> - [REST](../rest-api/bot-framework-rest-connector-quickstart.md)

O <a href="https://github.com/Microsoft/BotBuilder" target="_blank">Bot Builder SDK para .NET</a> é uma estrutura fácil de usar para o desenvolvimento de bots usando o Visual Studio e o Windows. O SDK aproveita o C# para fornecer uma maneira familiar para os desenvolvedores .NET criarem bots poderosos.


Este tutorial ajuda você a criar um bot usando o modelo Bot Application e o Bot Builder SDK para .NET e, em seguida, testando-o com o Bot Framework Emulator.

## <a name="prerequisites"></a>Pré-requisitos
1. Visual Studio [2017](https://www.visualstudio.com/).

2. No Visual Studio, atualize todos os [extensões](https://docs.microsoft.com/en-us/visualstudio/extensibility/how-to-update-a-visual-studio-extension) em suas versões mais recentes.

3. Modelo de bot para [C#](https://marketplace.visualstudio.com/items?itemName=BotBuilder.BotBuilderV3).

> [!TIP]
> O diretório de modelos de projetos do Visual Studio 2017 está normalmente localizado em `%USERPROFILE%\Documents\Visual Studio 2017\Templates\ProjectTemplates\Visual C#\` e o diretório de modelos de itens está em `%USERPROFILE%\Documents\Visual Studio 2017\Templates\ItemTemplates\Visual C#\`

## <a name="create-your-bot"></a>Crie seu bot

Abra o Visual Studio e crie um novo projeto C#. Escolha o modelo do **Simple Echo Bot Application** para o seu novo projeto.

![O Visual Studio criar projeto](../media/connector-getstarted-create-project.png)

> [!NOTE]
> O Visual Studio pode dizer que você precisa baixar e instalar o [IIS Express](https://www.microsoft.com/en-us/download/details.aspx?id=48264). 

Graças ao modelo do Aplicativo Bot, seu projeto contém todo o código necessário para criar o bot neste tutorial. Na verdade, você não precisa escrever nenhum código adicional. No entanto, antes de passarmos ao teste seu bot, dar uma olhada rápida em alguns dos códigos que o modelo de aplicativo Bot fornecido.

> [!TIP] 
> Se necessário, atualize os [pacotes do NuGet](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio).

## <a name="explore-the-code"></a>Explore o código

Primeiro, o `Post` método em **Controllers \ MessagesController.cs** recebe a mensagem do usuário e chama a caixa de diálogo raiz.

```csharp
public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
{
    if (activity.GetActivityType() == ActivityTypes.Message)
    {
        await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
    }
    else
    {
        HandleSystemMessage(activity);
    }
    var response = Request.CreateResponse(HttpStatusCode.OK);
    return response;
}

```

A caixa de diálogo raiz processa a mensagem e gera uma resposta. O `MessageReceivedAsync` método dentro **Dialogs\RootDialog.cs** envia uma resposta que ecoa esse valor de volta a mensagem do usuário, prefixada com o texto é enviado e terminando no texto ' que foi *##* caracteres, onde *##* representa o número de caracteres na mensagem do usuário.

```csharp
public class RootDialog : IDialog<object>
{
    public Task StartAsync(IDialogContext context)
    {
        context.Wait(MessageReceivedAsync);

        return Task.CompletedTask;
    }

    private async Task MessageReceivedAsync(IDialogContext context, IAwaitable<object> result)
    {
        var activity = await result as Activity;

        // Calculate something for us to return
        int length = (activity.Text ?? string.Empty).Length;

        // Return our reply to the user
        await context.PostAsync($"You sent {activity.Text} which was {length} characters");

        context.Wait(MessageReceivedAsync);
    }
}
```

## <a name="test-your-bot"></a>Testar seu bot

[!INCLUDE [Get started test your bot](../includes/snippet-getstarted-test-bot.md)]

### <a name="start-your-bot"></a>Inicie seu bot

Depois de instalar o emulador, inicie seu bot no Visual Studio usando um navegador como o host do aplicativo.
Esta captura de tela do Visual Studio mostra que o bot será iniciado no Microsoft Edge quando o botão de execução for clicado.

![Projeto do Visual Studio para executar](../media/connector-getstarted-start-bot-locally.png)

Quando você clica no botão de execução, o Visual Studio cria o aplicativo, implanta-o no host local e inicia o navegador da Web para exibir a página **default.htm** do aplicativo.
Por exemplo, aqui está o aplicativo **htm** página mostrada no Microsoft Edge:

![Bot Visual do Studio executando localhost](../media/connector-getstarted-bot-running-localhost.png)

> [!NOTE]
> Você pode modificar os **htm** arquivo dentro de seu projeto para especificar o nome e descrição do seu aplicativo bot.

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

Neste ponto, seu bot está em execução localmente.
Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:

1. Crie uma nova configuração de bot. Tipo de `http://localhost:port-number/api/messages` na barra de endereços, onde *número da porta* corresponde ao número da porta no navegador onde seu aplicativo está sendo executado.

2. Clique em **salvar e conecte-se**. Você não precisará especificar **ID do aplicativo Microsoft** e **Microsoft App Password**. Você pode deixar esses campos em branco por enquanto. Você receberá essas informações posteriormente, quando você [registrar seu bot](~/bot-service-quickstart-registration.md).

### <a name="test-your-bot"></a>Testar seu bot

Agora que seu bot está rodando localmente e está conectado ao emulador, teste seu bot digitando algumas mensagens no emulador.
Você deve ver que o bot responde a cada mensagem que você envia, retornando sua mensagem precedida com o texto "Você enviou" e terminando com o texto "que era *##* caracteres ', onde *##* é o número total de caracteres na mensagem que você enviou.


> [!TIP]
> No emulador, clique em qualquer bolhas de fala em sua conversa. Detalhes sobre a mensagem aparecerão no painel Detalhes, no formato JSON.

Você criou um bot com sucesso usando o modelo Bot Application SD Builder SDK para .NET!

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um bot simples usando o modelo de aplicativo Bot e o SDK do construtor de Bot do .NET e verificada a funcionalidade do bot, usando o emulador de Bot Framework.

Em seguida, aprenda sobre os principais conceitos do SDK do Bot Builder para .NET.

> [!div class="nextstepaction"]
> [Principais conceitos no SDK do construtor de Bot para .NET](bot-builder-dotnet-concepts.md)
