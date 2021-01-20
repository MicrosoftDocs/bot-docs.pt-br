---
title: Bot .NET com extensão de serviço de aplicativo de linha direta
titleSuffix: Bot Service
description: Saiba como configurar os bots do .NET para trabalhar com pipes nomeados. Consulte Como habilitar a extensão do serviço de aplicativo de linha direta e como configurar os bots para usar a extensão.
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 01/16/2020
ms.openlocfilehash: 7e78a674fc4d435fbdcbeefb68187ec6063f8505
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576188"
---
# <a name="configure-net-bot-for-extension"></a>Configurar o bot do .NET para a extensão

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como atualizar um bot para trabalhar com pipes nomeados e como habilitar a extensão de serviço de aplicativo de linha direta no recurso de serviço de Azure App em que o bot está hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas a seguir, você precisa ter um bot de aplicativo Web criado no Azure.

## <a name="enable-direct-line-app-service-extension"></a>Habilitar extensão de serviço de aplicativo de linha direta

Esta seção descreve como habilitar a extensão do serviço de aplicativo de linha direta usando a chave de extensão do serviço de aplicativo da configuração de canal de linha direta do bot.

### <a name="update-bot-code"></a>Atualizar código de bot

> [!NOTE]
> Os pacotes de visualização do NuGet **Microsoft. bot. Builder. StreamingExtensions** foram preteridos. A partir do v 4.8, o SDK contém um `Microsoft.Bot.Builder.Streaming` namespace. Se um bot usou anteriormente os pacotes de visualização, eles devem ser removidos antes de você seguir as etapas abaixo.

1. No Visual Studio, abra o projeto do bot.
1. Certifique-se de que o projeto usa a versão 4,8 ou posterior do SDK do bot Framework.
1. Permitir que seu aplicativo use pipes nomeados:
    - Abra o arquivo **Startup.cs** .
    - No `Configure` método, adicione uma chamada para o `UseNamedPipes` método.

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

1. Salve o arquivo **Startup.cs** .

1. Publique o bot em seu recurso de bot do aplicativo Web do Azure para implantar o código atualizado.

### <a name="enable-bot-direct-line-app-service-extension"></a>Habilitar extensão do serviço de aplicativo de linha direta de bot

1. Na portal do Azure, localize o recurso de **bot do aplicativo Web** .
1. No menu do painel esquerdo, em **Gerenciamento de bot** , clique em **canais** para configurar os canais de serviço de **bot do Azure** do qual o bot aceita mensagens.
1. Se ainda não estiver habilitado, clique no canal de **linha direta** e siga as instruções para habilitar o canal.
1. Na tabela **conectar-se a canais** , clique no link **Editar** na linha da **linha direta** .
1. Role para baixo até a seção **chaves de extensão do serviço de aplicativo** .
1. Clique no link **Mostrar** para revelar uma das chaves. Copie esse valor para uso posterior.

    ![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys.png)

1. Navegue até a home page, clique no ícone **serviços de aplicativos** na parte superior da página. Você também pode exibir o menu do portal e, em seguida, clicar no item de menu **serviços de aplicativos** , no painel esquerdo. A página **serviços de aplicativos** é exibida.
1. Na caixa de pesquisa, insira o nome do recurso de **bot do aplicativo Web** . O recurso será listado.
Observe que, se você passar o mouse sobre o ícone ou o item de menu, obterá a lista dos últimos recursos que você exibiu. É provável que o recurso de **bot do aplicativo Web** seja listado.
1. Clique no link do recurso.
1. Na seção **configurações** , clique no item de menu **configuração** .
1. No painel direito, adicione as seguintes novas configurações:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionKey|O valor da chave de extensão do serviço de aplicativo que você copiou anteriormente.|
    |DIRECTLINE_EXTENSION_VERSION|mais recente|

1. Se o bot estiver hospedado em uma nuvem do Azure soberanas ou de outra forma restrita, em que você não acessa o Azure por meio do [Portal público](https://portal.azure.com), também será necessário adicionar a seguinte nova configuração:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionABSEndpoint|O ponto de extremidade específico para a nuvem do Azure em que o bot está hospedado. Para a nuvem USGov, por exemplo, o ponto de extremidade é `https://directline.botframework.azure.us/v3/extension` .|

1. Ainda na seção **configuração** , clique na seção configurações **gerais** e ative o **Web Sockets**.
1. Clique em **Salvar** para salvar as configurações. Isso reinicia o Serviço de Aplicativo do Azure.

## <a name="confirm-the-extension-and-the-bot-are-configured"></a>Confirme se a extensão e o bot estão configurados

No navegador, navegue até `https://<your_app_service>.azurewebsites.net/.bot`.
Se tudo estiver correto, a página retornará este conteúdo JSON: `{"v":"123","k":true,"ib":true,"ob":true,"initialized":true}`. Essas são as informações que você obtém quando *tudo funciona corretamente*, onde

- **v** exibe a versão de Build da extensão do serviço de aplicativo de linha direta.
- **k** determina se a extensão pode ler uma chave de extensão de sua configuração.
- **inicializado** determina se a extensão pode usar a chave de extensão para baixar os metadados do bot do serviço de bot do Azure.
- **IB** determina se a extensão pode estabelecer uma conexão de entrada com o bot.
- **OB** determina se a extensão pode estabelecer uma conexão de saída com o bot.

## <a name="troubleshooting"></a>Solução de problemas

- Se os valores **IB** e **OB** exibidos pelo **ponto de extremidade. bot** forem falsos, isso significa que o bot e a extensão do serviço de aplicativo Direct line não poderão se conectar entre si.
    1. Verifique se o código para usar pipes nomeados foi adicionado ao bot.
    1. Confirme se o bot é capaz de iniciar e executar de forma alguma. Ferramentas úteis são **testadas no Webchat**, conectando um canal adicional, depuração remota ou registro em log.
    1. Reinicie o **serviço de Azure app** inteiro no qual o bot está hospedado, para garantir uma inicialização limpa de todos os processos.

- Se você receber uma mensagem "erro HTTP 500,34-ANCM de hospedagem mista", o bot está tentando usar o `InProcess` modelo de hospedagem. Isso é remediado com a definição explícita do bot para ser executado `OutOfProcess` . Consulte [modelo de hospedagem fora do processo](https://docs.microsoft.com/aspnet/core/host-and-deploy/aspnet-core-module?view=aspnetcore-3.1&preserve-view=true#out-of-process-hosting-model) na documentação do AZP.NET Core para obter mais informações.

- Se o valor **inicializado** do **ponto de extremidade. bot** for falso, isso significa que a extensão do serviço de aplicativo de linha direta não pode validar a chave de extensão do serviço de aplicativo adicionada às **configurações de aplicativo** do bot acima.
    1. Confirme se o valor foi inserido corretamente.
    1. Alterne para a chave de extensão alternativa mostrada na página **Configurar linha direta** de seu bot.

- Se você tentar usar o OAuth com a extensão de serviço de aplicativo de linha direta e encontrar o erro "não é possível obter a AppId de bot da declaração de audiência". Um `ClaimsIdentity` com o `AudienceClaim` atribuído precisa ser definido no `BotFrameworkHttpAdapter` . Para fazer isso, um desenvolvedor pode subclassificar o adaptador semelhante ao exemplo abaixo:

```csharp
public class AdapterWithStaticClaimsIdentity : BotFrameworkHttpAdapter
{
    public AdapterWithStaticClaimsIdentity(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger, ConversationState conversationState = null)
        : base(configuration, logger)
    {
        // Manually create the ClaimsIdentity and create a Claim with a valid AudienceClaim and the AppID for a bot using the Direct Line App Service extension.
        var appId = configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppIdKey)?.Value;
        ClaimsIdentity = new ClaimsIdentity(new List<Claim>{
            new Claim(AuthenticationConstants.AudienceClaim, appId)
        });
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar o chat Web com a extensão do serviço de aplicativo de linha direta](./bot-service-channel-directline-extension-webchat-client.md)
