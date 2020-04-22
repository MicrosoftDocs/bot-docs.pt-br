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
ms.openlocfilehash: e695a35b1f98a2ec8cfe5cbb24c13cf5cf18a648
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81395689"
---
# <a name="configure-net-bot-for-extension"></a>Configurar o bot do .NET para a extensão

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Este artigo descreve como atualizar um bot para trabalhar com **pipes nomeados** e como habilitar a extensão de serviço de aplicativo do Direct Line no recurso de **Serviço de Aplicativo do Azure** em que o bot está hospedado. Leia este artigo complementar [Criar um cliente do .NET para se conectar à extensão do Serviço de Aplicativo do Direct Line](bot-service-channel-directline-extension-net-client.md).


## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas a seguir, você precisa ter o recurso do **Serviço de Aplicativo do Azure** e o **Serviço de Aplicativo** relacionado no Azure.

## <a name="enable-direct-line-app-service-extension"></a>Habilitar a Extensão de Serviço de Aplicativo do Direct Line

Esta seção descreve como habilitar a extensão de serviço de aplicativo do Direct Line usando chaves da configuração do canal de seu bot e o recurso do **Serviço de Aplicativo do Azure** em que o bot está hospedado.

## <a name="update-net-bot-to-use-direct-line-app-service-extension"></a>Atualize o bot do .NET para usar a extensão do Serviço de Aplicativo do Direct Line

1. No Visual Studio, abra o projeto do bot.
2. Verifique se o projeto está usando a versão 4.8 ou superior do SDK do Bot Builder.
3. Permite que o aplicativo use o **Bot Framework NamedPipe**:
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

4. Salve o arquivo `Startup.cs`.
5. Abra o arquivo `appsettings.json` e insira os seguintes valores:
    1. `"MicrosoftAppId": "<secret Id>"`
    2. `"MicrosoftAppPassword": "<secret password>"`

    Os valores são a **appID** e o **appSecret** associados ao grupo de registro do serviço.

6. **Publique** o bot em seu Serviço de Aplicativo do Azure.

### <a name="gather-your-direct-line-extension-keys"></a>Reunir suas chaves de Extensão do Direct Line

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/)
1. No portal do Azure, localize o recurso do **Serviço de Bot do Azure**
1. Clique em **Canais** para configurar os canais do bot
1. Se ele ainda não estiver habilitado, clique no canal **Direct Line** para habilitá-lo.
1. Se ele já estiver habilitado, na tabela Conectar-se a canais, clique no link **Editar** na linha do Direct Line.
1. Role para baixo até a seção Chaves da Extensão do Serviço de Aplicativo.
1. Clique no link **Mostrar** para revelar uma das chaves e, em seguida, copie e salve o valor dela. Você usará esse valor na próxima seção.

![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys.png)

### <a name="enable-the-direct-line-app-service-extension"></a>Habilitar a Extensão de Serviço de Aplicativo do Direct Line

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/)
1. Na portal do Azure, localize a página do recursos **Serviço de Aplicativo do Azure** para o aplicativo Web em que o bot está ou será hospedado
1. Clique em **Configuração**. Na seção *Configurações do aplicativo*, adicione as seguintes novas configurações:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionKey|<App_Service_Extension_Key>|
    |DIRECTLINE_EXTENSION_VERSION|mais recente|

    Em que *App_Service_Extension_Key* é o valor salvo anteriormente.

1. Na seção *Configuração*, clique na seção de configurações **Geral** e ative os **Web Sockets**
1. Clique em **Salvar** para salvar as configurações. Isso reinicia o Serviço de Aplicativo do Azure.

## <a name="confirm-direct-line-app-extension-and-the-bot-are-initialized"></a>Confirme se a Extensão do Aplicativo do Direct Line e o bot foram inicializados

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
