---
title: Bot .NET com a extensão de serviço de aplicativo do Direct Line
titleSuffix: Bot Service
description: Habilite o bot .NET para trabalhar com a extensão de serviço de aplicativo do Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 01/16/2020
ms.openlocfilehash: 4e9377f768b43f442209266689d3a5565be30ce7
ms.sourcegitcommit: 7bf72623d9abf15e1444e8946535724f500643c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143865"
---
# <a name="configure-net-bot-for-extension"></a>Configurar o bot do .NET para a extensão

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Este artigo descreve como atualizar um bot para trabalhar com **pipes nomeados** e como habilitar a extensão de serviço de aplicativo do Direct Line no recurso de **Serviço de Aplicativo do Azure** em que o bot está hospedado. Leia este artigo complementar [Criar um cliente do .NET para se conectar à extensão do Serviço de Aplicativo do Direct Line](bot-service-channel-directline-extension-net-client.md).


## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas a seguir, você precisa ter o recurso de **registro do canal de bot** e o serviço de aplicativo de **bot** relacionado (seu bot) no Azure.

## <a name="enable-direct-line-app-service-extension"></a>Habilitar extensão de serviço de aplicativo de linha direta

Esta seção descreve como habilitar a extensão do serviço de aplicativo de linha direta usando a chave de extensão do serviço de aplicativo da configuração de canal de linha direta do bot.

### <a name="update-bot-code"></a>Atualizar código de bot

> [!NOTE]
> Os pacotes de visualização do `Microsoft.Bot.Builder.StreamingExtensions` foram preteridos. O SDK v4.8 agora contém o [código de streaming](https://github.com/microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder/Streaming). Se um bot usou anteriormente os pacotes de visualização, eles devem ser removidos antes de você seguir as etapas abaixo.

1. No Visual Studio, abra o projeto do bot.
1. Certifique-se de que o projeto usa a versão 4,8 ou superior do SDK Bot Builder. Verifique também se o pacote [Microsoft. bot. Connector. directize](https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine/3.0.3-Preview1) versão v 3.0.3-Preview1 ou posterior está instalado.
1. Permite que o aplicativo use o **Bot Framework NamedPipe**:
    - Abra o arquivo `Startup.cs` .
    - No método ``Configure``, adicione código a ``UseNamedPipes``

    ```csharp

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseHsts();
        }

        app.UseDefaultFiles();
        app.UseStaticFiles();

        // Allow the bot to use named pipes.
        app.UseNamedPipes(System.Environment.GetEnvironmentVariable("APPSETTING_WEBSITE_SITE_NAME") + ".directline");

        app.UseMvc();
    }
    ```

1. Salve o arquivo `Startup.cs`.

1. Abra o arquivo `appsettings.json` e insira os seguintes valores:
    1. `"MicrosoftAppId": "<secret Id>"`
    2. `"MicrosoftAppPassword": "<secret password>"`

    Os valores são o **AppID** e o **appSecret** associado ao registro de canais de bot.

1. **Publique** o bot em seu Serviço de Aplicativo do Azure.

### <a name="enable-bot-direct-line-app-service-extension"></a>Habilitar extensão do serviço de aplicativo de linha direta de bot

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/)
1. No portal do Azure, localize o recurso do **Serviço de Bot do Azure**
1. Clique em **Canais** para configurar os canais do bot
1. Se ele ainda não estiver habilitado, clique no canal **Direct Line** para habilitá-lo.
1. Se ele já estiver habilitado, na tabela Conectar-se a canais, clique no link **Editar** na linha do Direct Line.
1. Role para baixo até a seção Chaves da Extensão do Serviço de Aplicativo.
1. Clique no link **Mostrar** para revelar uma das chaves e, em seguida, copie e salve seu valor. Você usará esse valor nas etapas abaixo.

    ![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys.png)

1. Na portal do Azure, localize a página de recursos do **serviço de aplicativo bot** .
1. No painel esquerdo, na seção *configurações do aplicativo* , clique no item de **configuração** .
1. No painel direito, adicione as seguintes novas configurações:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionKey|<App_Service_Extension_Key>|
    |DIRECTLINE_EXTENSION_VERSION|mais recente|

    Em que *App_Service_Extension_Key* é o valor salvo anteriormente.

1. Na seção *Configuração*, clique na seção de configurações **Geral** e ative os **Web Sockets**
1. Clique em **Salvar** para salvar as configurações. Isso reinicia o Serviço de Aplicativo do Azure.

## <a name="confirm-direct-line-app-extension-and-the-bot-are-configured"></a>Confirme se a extensão do aplicativo de linha direta e o bot estão configurados

No navegador, navegue até https://<your_app_service>.azurewebsites.net/.bot.
Se tudo estiver correto, a página retornará este conteúdo JSON: `{"v":"123","k":true,"ib":true,"ob":true,"initialized":true}`. Essas são as informações que você obtém quando **tudo funciona corretamente**, onde

- **v** exibe a versão de build da ASE (Extensão de Serviço de Aplicativo) do Direct Line.
- **k** determina se a ASE do Direct Line pode ler uma chave de Extensão de Serviço de Aplicativo com base na respectiva configuração.
- **initialized** determina se a ASE do Direct Line pode usar a chave de Extensão do Serviço de Aplicativo para baixar os metadados do bot do Serviço de Bot do Azure
- **ib** determina se a ASE do Direct Line pode estabelecer uma conexão de entrada com o bot.
- **ob** determina se a ASE do Direct Line pode estabelecer uma conexão de saída com o bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar cliente do .NET](./bot-service-channel-directline-extension-net-client.md)
