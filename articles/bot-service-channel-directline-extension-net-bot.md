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
ms.openlocfilehash: 04868384268049befd3da7b39582614524542ce9
ms.sourcegitcommit: 36d6f06ffafad891f6efe4ff7ba921de8a306a94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76895634"
---
# <a name="configure-net-bot-for-extension"></a>Configurar o bot do .NET para a extensão

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Este artigo descreve como atualizar um bot para trabalhar com **pipes nomeados** e como habilitar a extensão de serviço de aplicativo do Direct Line no recurso de **Serviço de Aplicativo do Azure** em que o bot está hospedado.  

## <a name="prerequisites"></a>Prerequisites

Para executar as etapas descritas a seguir, você precisa ter o recurso do **Serviço de Aplicativo do Azure** e o **Serviço de Aplicativo** relacionado no Azure.

## <a name="enable-direct-line-app-service-extension"></a>Habilitar a Extensão de Serviço de Aplicativo do Direct Line

Esta seção descreve como habilitar a extensão de serviço de aplicativo do Direct Line usando chaves da configuração do canal de seu bot e o recurso do **Serviço de Aplicativo do Azure** em que o bot está hospedado.

## <a name="update-net-bot-to-use-direct-line-app-service-extension"></a>Atualize o bot do .NET para usar a extensão do Serviço de Aplicativo do Direct Line

> [!NOTE]
> `Microsoft.Bot.Builder.StreamingExtensions` são pacotes em versão prévia e não serão atualizados. O SDK v4.7 contém o [código de streaming](https://github.com/microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder/Streaming) e você não precisa instalar os Pacotes de Streaming separadamente.

1. No Visual Studio, abra o projeto do bot.
2. Adicione o pacote **NuGet da Extensão de Streaming** ao seu projeto:
    1. Em seu projeto, clique o botão direito do mouse em **Dependências** e selecione **Gerenciar Pacotes NuGet**.
    2. Na guia *Procurar*, clique em **Incluir pré-lançamento** para mostrar os pacotes em versão prévia.
    3. Selecione o pacote **Microsoft.Bot.Builder.StreamingExtensions**.
    4. Clique no botão **Instalar** para instalar o pacote; leia e concorde com o contrato de licença.
3. Permite que o aplicativo use o **Bot Framework NamedPipe**:
    - Abra o arquivo `Startup.cs` .
    - No método ``Configure``, adicione código a ``UseBotFrameworkNamedPipe``

    ```csharp

    using Microsoft.Bot.Builder.StreamingExtensions;

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

        // Allow bot to use named pipes.
        app.UseBotFrameworkNamedPipe();

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
1. Clique no link **Mostrar** para revelar uma das chaves e, em seguida, copie seu valor.

![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys.png)

### <a name="enable-the-direct-line-app-service-extension"></a>Habilitar a Extensão de Serviço de Aplicativo do Direct Line

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/)
1. Na portal do Azure, localize a página do recursos **Serviço de Aplicativo do Azure** para o aplicativo Web em que o bot está ou será hospedado
1. Clique em **Configuração**. Na seção *Configurações do aplicativo*, adicione as seguintes novas configurações:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionKey|<App_Service_Extension_Key_From_Section_1>|
    |DIRECTLINE_EXTENSION_VERSION|mais recente|

1. Na seção *Configuração*, clique na seção de configurações **Geral** e ative os **Web Sockets**
1. Clique em **Salvar** para salvar as configurações. Isso reinicia o Serviço de Aplicativo do Azure.

## <a name="confirm-direct-line-app-extension-and-the-bot-are-initialized"></a>Confirme se a Extensão do Aplicativo do Direct Line e o bot foram inicializados

No navegador, navegue até https://<your_app_service>.azurewebsites.net/.bot. Se tudo estiver correto, a página retornará este conteúdo JSON: `{"k":true,"ib":true,"ob":true,"initialized":true}`. Essas são as informações que você obtém quando **tudo funciona corretamente**, onde

- **k** determina se a ASE (Extensão do Serviço de Aplicativo) do Direct Line pode ler uma chave de extensão do Serviço de Aplicativo com base na configuração. 
- **initialized** determina se a ASE do Direct Line pode usar a chave de Extensão do Serviço de Aplicativo para baixar os metadados do bot do Serviço de Bot do Azure
- **ib** determina se a ASE do Direct Line pode estabelecer uma conexão de entrada com o bot.
- **ob** determina se a ASE do Direct Line pode estabelecer uma conexão de saída com o bot.
