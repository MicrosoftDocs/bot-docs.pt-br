---
title: Bot .NET com a extensão de serviço de aplicativo do Direct Line
titleSuffix: Bot Service
description: Saiba como configurar os bots do .NET para trabalhar com pipes nomeados. Consulte Como habilitar extensões de serviço de aplicativo de linha direta e como configurar bots para usar as extensões.
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 01/16/2020
ms.openlocfilehash: c9dd4165aabe16155c86abd7263acac54e2e5941
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456720"
---
# <a name="configure-net-bot-for-extension"></a>Configurar o bot do .NET para a extensão

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como atualizar um bot para trabalhar com **pipes nomeados** e como habilitar a extensão de serviço de aplicativo de linha direta no recurso de **serviço de Azure app** em que o bot está hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas a seguir, você precisa ter um **bot de aplicativo Web**(seu bot) criado no Azure.

## <a name="enable-direct-line-app-service-extension"></a>Habilitar extensão de serviço de aplicativo de linha direta

Esta seção descreve como habilitar a extensão do serviço de aplicativo de linha direta usando a chave de extensão do serviço de aplicativo da configuração de canal de linha direta do bot.

### <a name="update-bot-code"></a>Atualizar código de bot

> [!NOTE]
> Os pacotes de visualização do `Microsoft.Bot.Builder.StreamingExtensions` foram preteridos. O SDK v 4.8 agora contém a [biblioteca de streaming](https://github.com/microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder/Streaming). Se um bot usou anteriormente os pacotes de visualização, eles devem ser removidos antes de você seguir as etapas abaixo.

1. No Visual Studio, abra o projeto do bot.
1. Certifique-se de que o projeto usa a versão 4,8 ou superior do SDK Bot Builder.
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

1. **Publique** o bot em seu recurso de bot do aplicativo Web do Azure para implantar o código atualizado.

### <a name="enable-bot-direct-line-app-service-extension"></a>Habilitar extensão do serviço de aplicativo de linha direta de bot

1. Na portal do Azure, localize o recurso de **bot do aplicativo Web** .
1. No menu do painel esquerdo, em *Gerenciamento de bot* , clique em **canais** para configurar os canais de serviço de **bot do Azure** do qual o bot aceita mensagens.
1. Se ainda não estiver habilitado, clique no canal de **linha direta** e siga as instruções para habilitar o canal.
1. Na tabela **conectar-se a canais** , clique no link **Editar** na linha da linha direta.
1. Role para baixo até a seção **Chaves de Extensão do Serviço de Aplicativo**.
1. Clique no link **Mostrar** para revelar uma das chaves. Você usará esse valor nas etapas abaixo.

    ![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys.png)

1. No menu do painel esquerdo, na seção *configurações do aplicativo* , clique no item de **configuração** .
1. No painel direito, adicione as seguintes novas configurações:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionKey|<App_Service_Extension_Key>|
    |DIRECTLINE_EXTENSION_VERSION|mais recente|

    Em que *App_Service_Extension_Key* é o valor salvo anteriormente.

1. Se o bot estiver hospedado em um soberanas ou em uma nuvem do Azure restrita (ou seja, você não acessar o Azure por meio do [Portal público](https://portal.azure.com)), também será necessário adicionar a seguinte nova configuração:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionABSEndpoint|<URL_of_Direct_Line_App_Gateway>|

    Onde *URL_of_Direct_Line_App_Gateway* é específico para a nuvem do Azure em que o bot está hospedado. Para USGov, esse valor é `https://directline.botframework.azure.us/v3/extension` .

1. Ainda dentro da seção de *configuração* , clique na seção configurações **gerais** e ative o **Web Sockets**
1. Clique em **Salvar** para salvar as configurações. Isso reinicia o Serviço de Aplicativo do Azure.

## <a name="confirm-direct-line-app-extension-and-the-bot-are-configured"></a>Confirme se a extensão do aplicativo de linha direta e o bot estão configurados

No navegador, navegue até `https://<your_app_service>.azurewebsites.net/.bot`.
Se tudo estiver correto, a página retornará este conteúdo JSON: `{"v":"123","k":true,"ib":true,"ob":true,"initialized":true}`. Essas são as informações que você obtém quando **tudo funciona corretamente**, onde

- **v** exibe a versão de build da ASE (Extensão de Serviço de Aplicativo) do Direct Line.
- **k** determina se a ASE do Direct Line pode ler uma chave de Extensão de Serviço de Aplicativo com base na respectiva configuração.
- **initialized** determina se a ASE do Direct Line pode usar a chave de Extensão do Serviço de Aplicativo para baixar os metadados do bot do Serviço de Bot do Azure
- **ib** determina se a ASE do Direct Line pode estabelecer uma conexão de entrada com o bot.
- **ob** determina se a ASE do Direct Line pode estabelecer uma conexão de saída com o bot.

## <a name="troubleshooting"></a>Solução de problemas

- Se os valores *IB* e *OB* exibidos pelo ponto de *extremidade *. bot* forem falsos, isso significa que o bot e a extensão do serviço de aplicativo Direct line não poderão se conectar entre si.
    1. Verifique se o código para usar pipes nomeados foi adicionado ao bot.
    1. Confirme se o bot é capaz de iniciar e executar de forma alguma. Ferramentas úteis são **testadas no Webchat**, conectando um canal adicional, depuração remota ou registro em log.
    1. Reinicie o **serviço de Azure app** inteiro no qual o bot está hospedado, para garantir uma inicialização limpa de todos os processos.

- Se você estiver recebendo "erro HTTP 500,34-ANCM de hospedagem mista", é devido ao Bot tentar usar o modelo de Hospedagem de *inprocesso* . Isso é remediado com a definição explícita do bot para executar *OutOfProcess* em vez disso. Consulte [modelo de hospedagem fora do processo.](https://docs.microsoft.com/aspnet/core/host-and-deploy/aspnet-core-module?view=aspnetcore-3.1&preserve-view=true#out-of-process-hosting-model)

- Se o valor *inicializado* do **ponto de extremidade. bot** for falso, isso significa que a extensão do serviço de aplicativo de linha direta não pode validar a **chave de extensão do serviço de aplicativo** adicionada às configurações de *aplicativo* do bot acima.
    1. Confirme se o valor foi inserido corretamente.
    1. Alterne para a **chave de extensão do serviço de aplicativo** alternativa mostrada na página de configuração do canal de **linha direta** do bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar o chat Web com a extensão do serviço de aplicativo de linha direta](./bot-service-channel-directline-extension-webchat-client.md)
