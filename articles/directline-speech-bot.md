---
title: Desenvolver bot do DirectLine Speech | Microsoft Docs
description: Desenvolver bot do DirectLine Speech
keywords: desenvolver bot do direct line speech, bot de fala
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 05/02/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d2536d244d12b8d9785fa42eddede83bd2a456ad
ms.sourcegitcommit: a4181f35dbe6a8b107eea28122372f524e19880a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65030199"
---
## <a name="use-direct-line-speech-in-your-bot"></a>Usar o Direct Line Speech no seu bot 

[!INCLUDE [applies-to-v4](includes/applies-to.md)]

O Direct Line Speech usa uma nova funcionalidade de streaming baseada em WebSocket do Bot Framework para trocar mensagens entre o canal do Direct Line Speech e o seu bot. Após a habilitação do canal do Direct Line Speech no Portal do Azure, você precisará atualizar seu bot para que ouça e aceite essas conexões de WebSocket. As instruções a seguir explicam como fazer isso.

## <a name="add-the-nuget-package"></a>Adicionar o pacote NuGet
Na versão prévia do Direct Line Speech, há pacotes NuGet adicionais que você precisa incluir no seu bot. Esses pacotes estão hospedados em um feed do NuGet no myget.org:
1.  Abra o projeto do bot no Visual Studio.

2.  Acesse Gerenciar Pacotes NuGet nas propriedades do seu projeto de bot.

3.  Se você ainda não tiver `https://botbuilder.myget.org/F/experimental/api/v3/index.json` como fonte, adicione-o como feed usando as configurações de feed do NuGet, na parte superior direita.

4.  Selecione essa fonte de NuGet e adicione um do pacote `Microsoft.Bot.Protocol.StreamingExtensions.NetCore`.

5.  Aceite quaisquer avisos para concluir a adição do pacote ao projeto.

## <a name="option-1-update-your-net-core-bot-code-if-your-bot-has-a-botcontrollercs"></a>Opção 1: Atualizar o código de bot do .NET Core _se o bot tiver um BotController.cs_
Quando você cria um novo bot pelo Portal do Azure usando um dos modelos, como EchoBot, obtém um bot que inclui um controlador MVC do ASP.NET, que expõe um único ponto de extremidade POST. Estas instruções explicam como expandir isso para também expor um ponto de extremidade para aceitar o ponto de extremidade de streaming de WebSocket, que é um ponto de extremidade GET.
1.  Abrir BotController.cs da pasta Controladores na solução

2.  Localize o método PostAsync na classe e atualize a decoração de [HttpPost] para [HttpPost, HttpGet]:
```cs
[HttpPost, HttpGet]
public async Task PostAsync()
{ 
    await _adapter.ProcessAsync(Request, Response, _bot);
}
```

3.  Salve e feche BotController.cs.

4.  Abra Startup.cs na raiz da sua solução.

5.  Adicione um novo namespace:

```cs
using Microsoft.Bot.Protocol.StreamingExtensions.NetCore;
```

6.  No método ConfigureServices, substitua o uso de AdapterWithErrorHandler por WebSocketEnabledHttpAdapter na chamada apropriada de servoces.AddSingleton:

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...    

    // Create the Bot Framework Adapter.
    services.AddSingleton<IBotFrameworkHttpAdapter, WebSocketEnabledHttpAdapter>();

    services.AddTransient<IBot, EchoBot>();

    ...
}
```

7. Ainda no Startup.cs, navegue até o final do método ConfigureServices. Antes da chamada call app.UseMvc(); (isso é essencial, pois a ordem das chamadas Use importa), adicione app.UseWebSockets();. O final do método deve ser semelhante ao abaixo:

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

8.  O restante do código do bot permanece o mesmo.

## <a name="option-2-update-your-net-core-bot-code-if-your-bot-uses-addbot-and-usebotframework-instead-of-a-botcontroller"></a>Opção 2: Atualizar o código do bot do .NET Core, _se o bot usar AddBot e UseBotFramework em vez de um BotController_

Se criou um bot usando a v4 do SDK do Bot Builder anterior à versão 4.3.2, seu bot provavelmente não inclui um BotController; em vez disso, ele usa os métodos AddBot() e UseBotFramework() do arquivo Startup.cs para expor o ponto de extremidade POST em que o bot recebe mensagens. Para expor o novo ponto de extremidade de streaming, você precisará adicionar um BotController e remover os métodos AddBot() e UseBotFramework(). Estas instruções explicam as alterações que precisam ser feitas.

1.  Adicione um novo controlador MVC ao seu projeto de bot, adicionando um arquivo chamado BotController.cs. Adicione o código do controlador a este arquivo:

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
2.  No arquivo Startup.cs, localize o método Configure. Remova a linha UseBotFramework() e verifique se você tem estas linhas:

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

3.  Também no arquivo Startup.cs, localize o método ConfigureServices. Remova a linha AddBot() e verifique se você tem estas linhas:

```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    services.AddSingleton<ICredentialProvider, ConfigurationCredentialProvider>();

    services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>();

    // Create the Bot Framework Adapter.
    services.AddSingleton<IBotFrameworkHttpAdapter, WebSocketEnabledHttpAdapter>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```
4.  O restante do código do bot permanece o mesmo.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Conectar um bot ao Direct Line Speech (versão prévia)](./bot-service-channel-connect-directlinespeech.md)