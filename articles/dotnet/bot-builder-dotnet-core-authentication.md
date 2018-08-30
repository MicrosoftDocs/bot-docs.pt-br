---
title: Autenticação de atividades usando o .NET Core | Microsoft Docs
description: Saiba como autenticar as atividades de bots usando o .NET Core.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 431367cf4afe702fd83feff60b0ee4e260d50f17
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905758"
---
# <a name="authenticating-activities-using-net-core"></a>Autenticação de atividades usando o .NET Core

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Se você optar por desenvolver seu bot usando o [.NET Core](/dotnet/core/index), poderá usar o [Bot Framework Connector](bot-builder-dotnet-connector.md) para enviar e receber mensagens de [atividade](https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html) de seu bot. Para usar o serviço do Connector, você precisará configurar o modelo de autenticação adequado para a versão da estrutura de destino.

O Bot Framework Connector.AspNetCore dá suporte às seguintes versões do ASP.NET:
* Para o .NET Core v1.1/AspNetCore1.x use **Microsoft.Bot.Connector.AspNetCore 1.x**
* Para o .NET Core v2.0/AspNetCore2.x use **Microsoft.Bot.Connector.AspNetCore 2.x**

Este artigo mostrará como configurar o modelo de autenticação para a estrutura específica dos destinos do bot.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [.NET Core](https://www.microsoft.com/net/download/windows). Instalar a versão do .NET Core de destino (por exemplo: .NET Core v1.1 ou .NET Core v 2.0).
* [Registre um bot](~/bot-service-quickstart-registration.md). Registre seu bot para obter uma AppID e a senha necessária para o processo de autenticação.

## <a name="create-a-net-core-project"></a>Criar projeto do .NET Core

Para criar seu projeto .NET Core, faça o seguinte:

1. Abra o Visual Studio 2017 e clique em **Arquivo > Novo > Projeto...**.
2. Expanda o nó **Visual C#** e clique em **.NET Core**.
3. Escolha o tipo de projeto **Aplicativo Web ASP.NET Core** e preencha as informações do projeto (por exemplo: os campos Nome, Local e Nome da solução).
4. Clique em **OK**.
5. Verifique se o projeto está direcionando *.NET Core* e a versão desejada do *ASP.NET Core*. Por exemplo, a captura de tela abaixo mostra que o projeto está direcionando **.NET Core** e **ASP.NET Core 2.0**:

![Criar um projeto ASP.NET Core v2.0](~/media/dotnet-core-authentication/create-asp-net-core-2x-project.png)
 
  > [!NOTE]
  > Se você estiver direcionando **ASP.NET Core 2.0**, verifique se a versão instalada em nosso computador é pelo menos 2.x e acima.

6. Escolha o tipo de projeto **API Web**.
7. Clique em **OK** para criar o projeto.

## <a name="download-the-nuget-package"></a>Baixar o pacote NuGet

Para usar o **Bot Framework Connector**, instale o pacote do NuGet apropriado para sua versão do .NET Core. Para instalar o pacote do NuGet, proceda da seguinte maneira:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e **Gerenciar Pacotes NuGet...**
2. Clique em **Pesquisar** e procure **Connector.ASPNetCore**. 
3. Escolha a versão de destino. Por exemplo, a captura de tela abaixo mostra a versão **2.0.0.3** selecionada. Para instalar a versão 1.1.3.2, clique no menu suspenso **Versão** e escolha a versão **1.1.3.2** em vez disso.
![Pacote NuGet para a versão 2.0.0.3 do ASP.NET Core](~/media/dotnet-core-authentication/nuget-package-net-core-version.png)
4. Clique em **Instalar**.

## <a name="update-the-appsettingsjson"></a>Atualizar o arquivo appsettings.json

O Bot Framework Connector exige sua AppID e a senha autenticar o bot. Você pode definir esses valores no **appsettings.json** do seu projeto de aplicativo Web.

> [!NOTE]
> Para encontrar a **AppID** e a **AppPassword** do bot, confira [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

### <a name="appsettingsjson-for-net-core-v11"></a>appsettings.json para .NET Core v1.1:

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "MicrosoftAppId": "<MicrosoftAppId>",
  "MicrosoftAppPassword": "<MicrosoftAppPassword>"
}
```

### <a name="appsettingsjson-for-net-core-v20"></a>appsettings.json para .NET Core v2.0:

```json
{
  "Logging": {
    "IncludeScopes": false,
    "Debug": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "Console": {
      "LogLevel": {
        "Default": "Warning"
      }
    }
  },
  "MicrosoftAppId": "<MicrosoftAppId>",
  "MicrosoftAppPassword": "<MicrosoftAppPassword>"
}
```

## <a name="update-the-startupcs-class"></a>Atualizar a classe startup.cs

Atualizar a classe **startup.cs**. Dependendo da sua versão do projeto, atualize o fragmento de código apropriado para permitir que a autenticação funcione.

### <a name="startupcs-for-net-core-v11"></a>startup.cs para .NET Core v1.1

```cs
public class Startup
    {
        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();
            Configuration = builder.Build();
        }

        public IConfigurationRoot Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton(_ => Configuration);

            services.AddSingleton(typeof(ICredentialProvider), new StaticCredentialProvider(
                Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppIdKey)?.Value,
                Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppPasswordKey)?.Value));

            // Add framework services.
            services.AddMvc(options =>
            {
                options.Filters.Add(typeof(TrustServiceUrlAttribute));
            });
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IServiceProvider serviceProvider, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            loggerFactory.AddConsole(Configuration.GetSection("Logging"));
            loggerFactory.AddDebug();

            app.UseStaticFiles();

            ICredentialProvider credentialProvider = serviceProvider.GetService<ICredentialProvider>();

            app.UseBotAuthentication(credentialProvider);

            app.UseMvc();
        }
    }
```

### <a name="startupcs-for-net-core-v20"></a>startup.cs para .NET Core v2.0:

```cs
public class Startup
    {
        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();
            Configuration = builder.Build();
        }

        public IConfiguration Configuration { get; }
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton(_ => Configuration);

            var credentialProvider = new StaticCredentialProvider(Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppIdKey)?.Value,
                Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppPasswordKey)?.Value);

            services.AddAuthentication(
                    options =>
                    {
                        options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                        options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                    }
                )
                .AddBotAuthentication(credentialProvider);

            services.AddSingleton(typeof(ICredentialProvider), credentialProvider);

            services.AddMvc(options =>
            {
                options.Filters.Add(typeof(TrustServiceUrlAttribute));
            });
        }


        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            app.UseStaticFiles();
            app.UseAuthentication();
            app.UseMvc();
        }
    }
```

## <a name="create-the-messagescontrollercs-class"></a>Criar a classe MessagesController.cs

No **Gerenciador de Soluções**, adicione uma nova classe vazia chamada **MessagesController.cs**. Na classe **MessagesController.cs**, atualize o método **Post** com o código a seguir. Isso permitirá que seu bot envie e receba mensagens para o usuário.

```cs
private IConfiguration configuration;

public MessagesController(IConfiguration configuration)
{
    this.configuration = configuration;
}


[Authorize(Roles = "Bot")]
[HttpPost]
public async Task<OkResult> Post([FromBody] Activity activity)
{
    if (activity.Type == ActivityTypes.Message)
    {
        //MicrosoftAppCredentials.TrustServiceUrl(activity.ServiceUrl);
        var appCredentials = new MicrosoftAppCredentials(configuration);
        var connector = new ConnectorClient(new Uri(activity.ServiceUrl), appCredentials);

        // return our reply to the user
        var reply = activity.CreateReply("HelloWorld");
        await connector.Conversations.ReplyToActivityAsync(reply);
    }
    else
    {
        //HandleSystemMessage(activity);
    }
    return Ok();
}
```
