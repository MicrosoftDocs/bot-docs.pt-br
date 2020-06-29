---
title: Desenvolver bot do DirectLine Speech – Serviço de Bot
description: Desenvolver bot do DirectLine Speech
keywords: desenvolver bot do Direct Line Speech, bot de fala
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/01/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4d5094c7785093d624617d0ed6f1816f3fd0b360
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81395629"
---
# <a name="use-direct-line-speech-in-your-bot"></a>Usar o Direct Line Speech no seu bot

[!INCLUDE [applies-to-v4](includes/applies-to.md)]

O Direct Line Speech usa uma nova funcionalidade de streaming baseada em WebSocket do Bot Framework para trocar mensagens entre o canal do Direct Line Speech e o seu bot. Após a habilitação do canal do Direct Line Speech no Portal do Azure, você precisará atualizar seu bot para que ouça e aceite essas conexões de WebSocket. As instruções a seguir explicam como fazer isso.

## <a name="step-1-upgrade-to-the-latest-version-of-the-sdk"></a>Etapa 1: Atualizar para a versão mais recente do SDK

Para o Direct Line Speech, verifique se você está usando a versão mais recente do SDK do Bot Builder.

## <a name="step-2-update-your-net-core-bot-codeif-your-bot-uses-addbot-and-usebotframework-instead-of-a-botcontroller"></a>Etapa 2: Atualizar o código do bot do .NET Core, se o bot usar AddBot e UseBotFramework em vez de um BotController

Se criou um bot usando a v4 do SDK do Bot Builder anterior à versão 4.3.2, seu bot provavelmente não inclui um BotController; em vez disso, ele usa os métodos AddBot() e UseBotFramework() do arquivo Startup.cs para expor o ponto de extremidade POST em que o bot recebe mensagens. Para expor o novo ponto de extremidade de streaming, você precisará adicionar um BotController e remover os métodos AddBot() e UseBotFramework(). Estas instruções explicam as alterações que precisam ser feitas. Se você já tem essas alterações, passe para a próxima etapa.

Adicione um novo controlador MVC ao seu projeto de bot, adicionando um arquivo chamado BotController.cs. Adicione o código do controlador a este arquivo:

```cs

[Route("api/messages")]

[ApiController]

public class BotController : ControllerBase
{
    private readonly IBotFrameworkHttpAdapter _adapter;
    private readonly IBot _bot;
    public BotController(IBotFrameworkHttpAdapter adapter, IBot bot)
    {
        _adapter = adapter;

        _bot = bot;
    }

    [HttpPost, HttpGet]
    public async Task ProcessMessageAsync()
    {
        await _adapter.ProcessAsync(Request, Response, _bot);
    }
}
```

No arquivo **Startup.cs**, localize o método Configure. Remova a linha `UseBotFramework()` e verifique se você tem estas linhas para `UseWebSockets`:

```cs

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    ...
    app.UseDefaultFiles();
    app.UseStaticFiles();
    app.UseWebSockets();
    app.UseMvc();
    ...
}
```

Também no arquivo Startup.cs, localize o método ConfigureServices. Remova a linha `AddBot()` e verifique se você tem linhas para adicionar seu `IBot` e um `BotFrameworkHttpAdapter`:

```cs

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    services.AddSingleton<ICredentialProvider, ConfigurationCredentialProvider>();
    services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>();

    // Create the Bot Framework Adapter.
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkHttpAdapter>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

O restante do código do bot permanece o mesmo.

## <a name="step3-ensure-websockets-are-enabled"></a>Etapa3: Verificar se o WebSockets está habilitado

Quando você criar um bot pelo Portal do Azure usando um dos modelos, como EchoBot, obterá um bot que inclui um controlador MVC do ASP.NET, que expõe um ponto de extremidade GET e POST e também usará o WebSockets. Estas instruções explicam como adicionar esses elementos ao bot se você está atualizando ou não tem o bot de um modelo.

Abra **BotController.cs** na pasta Controladores em sua solução

Localize o método `PostAsync` na classe e atualize a decoração de [HttpPost] para [HttpPost, HttpGet]:

```cs

[HttpPost, HttpGet]
public async Task PostAsync()
{
    await _adapter.ProcessAsync(Request, Response, _bot);
}
```

Salve e feche BotController.cs.

Abra **Startup.cs** na raiz de sua solução.

Em Startup.cs, navegue até o final do método Configure. Antes da chamada a  `app.UseMvc()`, adicione uma chamada a  `app.UseWebSockets()`. Isso é importante, pois a ordem dessas chamadas de uso importa. O final do método deve ser semelhante a este:

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    ...
    app.UseDefaultFiles();
    app.UseStaticFiles();
    app.UseWebSockets();
    app.UseMvc();
    ...
}

```
O restante do código do bot permanece o mesmo.



Etapa 4: Definir opcionalmente o campo Falar em Atividades para personalizar o que é falado para o usuário

Por padrão, todas as mensagens enviadas por meio do Direct Line Speech para o usuário serão faladas.

Opcionalmente, você pode personalizar como a mensagem é falada definindo o campo Falar de qualquer Atividade enviada do bot:

```cs

public IActivity Speak(string message)
{
    var activity = MessageFactory.Text(message);
    string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>

        <voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>" +
        $"{message}" + "</voice></speak>";

    activity.Speak = body;
    return activity;
}
```

O snippet a seguir mostra como usar a função Falar anterior:

```cs

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(Speak($"Echo: {turnContext.Activity.Text}"), cancellationToken);
}
```

## <a name="additional-information"></a>Informações adicionais

- Para obter um exemplo completo de como criar e usar um bot habilitado por voz, confira o [Tutorial: habilite seu bot por voz usando o SDK de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk).

- Para obter mais informações sobre como trabalhar com atividades, confira  [como os bots funcionam](https://aka.ms/how-bots-work) e  [como enviar e receber mensagens de texto](https://aka.ms/bot-service-send-receive-text-messages).
