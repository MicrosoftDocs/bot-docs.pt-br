---
title: Conectar um bot ao Webex Teams | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Webex por meio do adaptador do Webex.
keywords: bot adapter, Webex, Webex bot
author: garypretty
manager: kamrani
ms.topic: article
ms.author: gapretty
ms.service: bot-service
ms.date: 01/21/2020
ms.openlocfilehash: bf00b73dcc130a58eb5353b185387a4640aae1be
ms.sourcegitcommit: e37cf15f4907910560f34445a0fbdd7ae75b4787
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440021"
---
# <a name="connect-a-bot-to-webex-teams-using-the-webex-adapter"></a>Conectar um bot ao Webex Teams usando o adaptador do Webex

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Neste artigo, você aprenderá a conectar um bot ao Webex usando o adaptador disponível no SDK.  Este artigo explicará como modificar o exemplo EchoBot para conectá-lo a um aplicativo do Webex.

> [!NOTE]
> As instruções a seguir abordam a implementação em C# do adaptador do Webex. Para obter instruções sobre como usar a implementação em JavaScript, parte das bibliotecas BotKit, [consulte a documentação do Webex sobre BotKit](https://botkit.ai/docs/v4/platforms/webex.html).

## <a name="prerequisites"></a>Pré-requisitos

* O [código de exemplo do EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Acesso a uma equipe do Webex com permissões suficientes para fazer logon e criar/gerenciar aplicativos em [https://developer.webex.com/my-apps](https://developer.webex.com/my-apps). Se você não tiver acesso a uma equipe do Webex, poderá criar uma conta gratuitamente em [www.webex.com](https://www.webex.com).

## <a name="create-a-webex-bot-app"></a>Criar um aplicativo de bot do Webex

1. Faça logon no [Painel do desenvolvedor do Webex](https://developer.webex.com/my-apps) e clique no botão "Criar um aplicativo".

2. Na próxima tela, escolha criar um Bot do Webex clicando em "Criar um bot".

3. Na próxima tela, insira um nome, nome de usuário e uma descrição apropriados para o bot e escolha um ícone ou carregue uma imagem de sua preferência.

    ![configuração de bot](~/media/bot-service-adapter-connect-webex/create-bot.png)

    Clique no botão "Adicionar bot".

4. Na próxima página, você receberá um token de acesso para o novo aplicativo do Webex. Anote esse token, pois você precisará dele ao configurar o bot.

    ![o token de acesso](~/media/bot-service-adapter-connect-webex/create-bot-settings.png)

## <a name="wiring-up-the-webex-adapter-in-your-bot"></a>Conectando o adaptador do Webex ao seu bot

Antes de concluir a configuração do aplicativo do Webex, você precisa conectar o adaptador do Webex ao bot.

### <a name="install-the-webex-adapter-nuget-package"></a>Instalar o pacote NuGet do adaptador do Webex

Adicione o pacote NuGet [Microsoft.Bot.Builder.Adapters.Webex](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Webex/). Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs)

### <a name="create-a-webex-adapter-class"></a>Criar uma classe de adaptador do Webex

Crie uma nova classe que herde da classe ***WebexAdapter**_. Essa classe atuará como o adaptador para o canal do Webex. Ele inclui recursos de tratamento de erros (muito parecido com a classe _*_BotFrameworkAdapterWithErrorHandler_*_ já no exemplo, usado para lidar com solicitações do serviço de bot do Azure).

[!code-csharp[Webex Adapter With Error Handler](~/../botbuilder-samples/samples/csharp_dotnetcore/62.webex-adapter/Adapters/WebexAdapterWithErrorHandler.cs?range=11-29)]

### <a name="create-a-new-controller-for-handling-webex-requests"></a>Criar um controlador para lidar com as solicitações do Webex

Criaremos um controlador que manipulará as solicitações do aplicativo do Webex em um novo ponto de extremidade "api/webex", em vez do "api/messages" padrão usado para solicitações de canais do Serviço de Bot do Azure.  Ao adicionar outro ponto de extremidade ao bot, você poderá aceitar solicitações de canais do Serviço de Bot (ou adaptadores adicionais), assim como do Webex, usando o mesmo bot.

[!code-csharp[Webex Controller](~/../botbuilder-samples/samples/csharp_dotnetcore/62.webex-adapter/Controllers/WebexController.cs?range=12-32)]


### <a name="inject-webex-adapter-in-your-bot-startupcs"></a>Injetar o Adaptador do Webex no Startup.cs do seu bot

Adicione a seguinte linha ao método _*_configureservices_*_ no arquivo Startup.cs, que registrará seu adaptador WebEx e o tornará disponível para a nova classe de controlador.  As definições de configuração, descritas na próxima etapa, serão usadas automaticamente pelo adaptador.

```csharp
services.AddSingleton<WebexAdapter, WebexAdapterWithErrorHandler>();
```

Depois de adicionado, _*_o método configureservices_*_ deve ser assim.

[!code-csharp[Webex Controller](~/../botbuilder-samples/samples/csharp_dotnetcore/62.webex-adapter/Startup.cs?range=18-31)]


### <a name="add-webex-adapter-settings-to-your-bots-configuration-file"></a>Adicionar configurações do adaptador do Webex ao arquivo de configuração do bot

1. Adicione as quatro configurações mostradas abaixo ao arquivo appSettings.json em seu projeto de bot.

[!code-csharp[Webex Controller](~/../botbuilder-samples/samples/csharp_dotnetcore/62.webex-adapter/appsettings.json?range=1-6)]

2. Preencha a configuração _*WebexAccessToken** dentro do token de acesso de bot do WebEx, que foi gerado ao criar seu aplicativo de bot de WebEx nas etapas anteriores. Deixe as outras três configurações vazias por enquanto, até reunirmos as informações necessárias para elas em etapas posteriores.

## <a name="complete-configuration-of-your-webex-app-and-bot"></a>Concluir a configuração do seu aplicativo e bot do Webex

### <a name="create-and-update-a-webex-webhook"></a>Criar e atualizar um webhook do Webex

Agora que você criou um aplicativo do Webex e vinculou o adaptador ao seu projeto de bot, as etapas finais são configurar um webhook do Webex, apontá-lo para o ponto de extremidade correto no bot e assinar seu aplicativo para garantir que o bot receba mensagens e anexos. Para fazer isso, seu bot deve estar em execução, para que o Webex verifique se a URL para o ponto de extremidade é válida.

1. Para concluir esta etapa, [implante o bot no Azure](https://aka.ms/bot-builder-deploy-az-cli) e anote a URL do bot implantado. Seu ponto de extremidade de mensagens do Webex é a URL para o bot, a qual será a URL do aplicativo implantado (ou ponto de extremidade ngrok), além de "/api/webex" (por exemplo, `https://yourbotapp.azurewebsites.net/api/webex`).

    > [!NOTE]
    > Se você não estiver pronto para implantar o bot no Azure ou desejar depurar o bot ao usar o adaptador do Webex, poderá usar uma ferramenta como a [ngrok](https://www.ngrok.com) (que você provavelmente já terá instalada se usou o emulador do Bot Framework anteriormente) para fazer um túnel para o bot em execução localmente e fornecer uma URL acessível publicamente para isso.
    >
    > Se desejar criar um túnel ngrok e obter uma URL para o bot, use o comando a seguir em uma janela do terminal (isso pressupõe que o bot local esteja em execução na porta 3978; altere os números de porta no comando se o bot não estiver).
    >
    > ```cmd
    > ngrok.exe http 3978 -host-header="localhost:3978"
    > ```

2. Navegue até [https://developer.webex.com/docs/api/v1/webhooks](https://developer.webex.com/docs/api/v1/webhooks).


3. Clique no link para o **POST** método post `https://webexapis.com/v1/webhooks` (com a descrição *criar um webhook*). Isso exibirá um formulário que permite enviar uma solicitação ao ponto de extremidade.

    ![ponto de extremidade do webhook](~/media/bot-service-adapter-connect-webex/webex-webhook-post-endpoint.png)

4. Preencha o formulário com os seguintes detalhes:

    1. **Nome** -o nome de seu webhook, por exemplo, *mensagens webhook*.
    1. **TargetURL** -a URL completa para o ponto de extremidade WebEx do bot, como `https://yourbotapp.azurewebsites.net/api/webex` ).
    1. **recursos** -mensagens.
    1. criado pelo **evento** .
    1. **filtro** -deixe em branco.
    1. **segredo** -um segredo de sua escolha para proteger seu webhook. Posteriormente, você o adicionará ao seu bot `appsettings.json` .

    ![formulário de webhook](~/media/bot-service-adapter-connect-webex/webex-webhook-form.png)

5. Clique em **Executar**, que deve criar seu webhook e fornecer uma mensagem de êxito.

### <a name="complete-the-remaining-settings-in-your-bot-application"></a>Concluir as configurações restantes em seu aplicativo de bot

Conclua as três configurações restantes no arquivo appsettings.json do bot (você já preencheu a **WebexAccessToken** em uma etapa anterior).

* WebexPublicAddress (a URL completa para o ponto de extremidade do Webex do bot)
* WebexSecret (o segredo que você forneceu ao criar o webhook na etapa anterior)
* WebexWebhookName (o nome que você forneceu ao webhook na etapa anterior)

## <a name="re-deploy-your-bot-in-your-webex-team"></a>Reimplante o bot na equipe do Webex

Agora que você concluiu a configuração das definições do bot no appsettings.json, reimplante o bot (ou reinicie o bot se estiver fazendo um túnel para um ponto de extremidade local usando o ngrok).  Agora a configuração do aplicativo e bot do Webex está concluída.
Faça logon na equipe do Webex em [https://www.webex.com](https://www.webex.com) e faça um chat com o bot enviando uma mensagem, da mesma maneira que você entraria em contato com outra pessoa.

![pessoa de contato](~/media/bot-service-adapter-connect-webex/webex-contact-person.png)
