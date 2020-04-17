---
title: Conectar um bot com o WeChat – Serviço de Bot
description: Saiba como configurar a conexão do bot com o WeChat.
keywords: WeChat, Tencent, canal de bot, aplicativo WeChat, bot do WeChat, ID do aplicativo, Segredo do Aplicativo, credenciais
author: seaen
manager: kamrani
ms.topic: article
ms.author: egorn
ms.service: bot-service
ms.date: 11/01/2019
ms.openlocfilehash: 9abba3093ce819f7ebc07bb03e342da797971f25
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75791787"
---
# <a name="connect-a-bot-to-wechat"></a>Conectar um bot ao WeChat

Configure seu bot para se comunicar com as pessoas que usam a Plataforma de Contas Oficial do WeChat.

## <a name="download-wechat-adapter-for-bot-framework"></a>Baixar o Adaptador do WeChat para o Bot Framework

O Adaptador do WeChat para o Microsoft Bot Framework é um adaptador de software livre no GitHub. [Baixe o Adaptador do WeChat para o Bot Framework](https://github.com/microsoft/BotFramework-WeChat/).

## <a name="create-a-wechat-account"></a>Criar uma conta do WeChat

Para configurar um bot para se comunicar usando o WeChat, você precisa criar uma conta oficial do WeChat em [Plataforma de Conta Oficial do WeChat](https://mp.weixin.qq.com/?lang=en_US) e, em seguida, conectar o bot ao aplicativo. Atualmente, oferecemos suporte apenas para conta de serviço.

### <a name="change-your-prefer-language"></a>Alterar seu idioma de preferência

Você pode alterar o idioma de exibição que prefere antes do logon.

 ![change_language](./media/channels/wechat-change-language.png)

### <a name="register-a-service-account"></a>Registrar uma conta de serviço

Uma conta de serviço real precisa ser verificada pelo WeChat. Você não pode habilitar o webhook antes que a conta seja verificada. Para criar sua própria conta de serviço, siga [estas](https://kf.qq.com/product/weixinmp.html#hid=87) instruções.
Para resumir, basta clicar em Registrar Agora na parte superior, selecionar a conta de serviço e seguir as instruções.

 ![register_account](./media/channels/wechat-register-account.png)

### <a name="sandbox-account"></a>Conta de área restrita

Se quiser apenas testar a integração entre o WeChat e o bot, você poderá usar uma conta de área restrita em vez de criar a conta de serviço. Saiba mais sobre a criação de uma [conta de área restrita](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login).

## <a name="enable-wechat-adapter-to-bot"></a>Habilitar o Adaptador do WeChat para o bot

O projeto de bot é um projeto comum do SDK do Bot Framework V4. Antes de poder iniciá-lo, você precisa ter certeza de que pode executar o bot. Baixe o [Adaptador do WeChat para o Bot Framework](https://github.com/microsoft/BotFramework-WeChat/).

### <a name="prerequisites"></a>Pré-requisitos

    .NET Core SDK (version 2.2.x)

### <a name="add-reference-to-wechat-adapter-source"></a>Adicionar referência à origem do Adaptador do WeChat

Faça referência diretamente ao projeto do adaptador do WeChat ou adicione ~/BotFramework-WeChat/libraries/csharp_dotnetcore/outputpackages como origem do NuGet local.

### <a name="inject-wechat-adapter-in-your-bot-startupcs"></a>Injetar o Adaptador do WeChat no Startup.cs do seu bot

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);

    // Create the storage we'll be using for User and Conversation state. (Memory is great for testing purposes.)
    services.AddSingleton<IStorage, MemoryStorage>();

    // Create the User state. (Used in this bot's Dialog implementation.)
    services.AddSingleton<UserState>();

    // Create the Conversation state. (Used by the Dialog system itself.)
    services.AddSingleton<ConversationState>();

    // Load WeChat settings.
    var wechatSettings = new WeChatSettings();
    Configuration.Bind("WeChatSettings", wechatSettings);
    services.AddSingleton<WeChatSettings>(wechatSettings);

    // Configure hosted serivce.
    services.AddSingleton<IBackgroundTaskQueue, BackgroundTaskQueue>();
    services.AddHostedService<QueuedHostedService>();
    services.AddSingleton<WeChatHttpAdapter>();

    // The Dialog that will be run by the bot.
    services.AddSingleton<MainDialog>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

### <a name="update-your-bot-controller"></a>Atualizar o controlador do seu bot

```csharp
[Route("api/messages")]
[ApiController]
public class BotController : ControllerBase
{  
    private readonly IBot _bot;
    private readonly WeChatHttpAdapter _weChatHttpAdapter;
    private readonly string Token;
    public BotController(IBot bot, WeChatHttpAdapter weChatAdapter)
    {
        _bot = bot;
        _weChatHttpAdapter = weChatAdapter;
    }

    [HttpPost("/WeChat")]
    [HttpGet("/WeChat")]
    public async Task PostWeChatAsync([FromQuery] SecretInfo secretInfo)
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await _weChatHttpAdapter.ProcessAsync(Request, Response, _bot, secretInfo);
    }
}
```

### <a name="setup-appsettingsjson"></a>Configurar appsettings.json

Você precisará configurar o appsettings.json antes de iniciar o bot. O que você precisa pode ser encontrado abaixo.

```json
"WeChatSettings": {
    "UploadTemporaryMedia": true,
    "PassiveResponseMode": false,
    "Token": "",
    "EncodingAESKey": "",
    "AppId": "",
    "AppSecret": ""
}
```

#### <a name="service-account"></a>Conta de serviço

Se já tiver uma conta de serviço e estiver pronto para implantar, você poderá encontrar **AppID**, **AppSecret**, **EncodingAESKey** e **Token** nas configurações básicas na barra de navegação à esquerda, conforme mostrado abaixo.

Não se esqueça de que você precisa configurar a lista de permissões de IP; caso contrário, o WeChat não aceitará sua solicitação.

 ![serviceaccount_console](./media/channels/wechat-serviceaccount-console.png)

#### <a name="sandbox-account"></a>Conta de área restrita

A conta da área restrita não tem **EncodingAESKey** e a mensagem do WeChat não foi criptografada. Apenas deixe EncodingAESKey em branco. Você tem apenas três parâmetros aqui, **appID**, **appsecret** e **Token**.

 ![sandbox_account](./media/channels/wechat-sandbox-account.png)

### <a name="start-bot-and-set-endpoint-url"></a>Iniciar o bot e definir a URL do ponto de extremidade

Agora você pode definir o back-end do seu bot. Antes de fazer isso, você precisa iniciar o bot antes de salvar as configurações. O WeChat enviará uma solicitação a você para verificar a URL.
Defina o ponto de extremidade em um padrão semelhante a **https://your_end_point/WeChat** ou então defina suas configurações pessoais da mesma forma que você fez no BotController.cs

 ![sandbox_account2](./media/channels/wechat-sandbox-account-2.png)

### <a name="subscribe-your-official-account"></a>Assine sua conta oficial

Você pode encontrar um código QR para assinar sua conta de teste como no WeChat.

 ![assinar](./media/channels/wechat-subscribe.png)

## <a name="test-through-wechat"></a>Testar por meio de WeChat

Está tudo pronto, você pode experimentar o que fez em seu cliente do WeChat. Você pode experimentar nosso bot de exemplo na pasta de testes. Esse bot de exemplo inclui o adaptador do WeChat e está integrado com o bot de eco e o bot de Cartões.

 ![chat](./media/channels/wechat-chat.png)
