---
title: Conectar um bot ao Slack – Serviço de Bot
description: Saiba como conectar bots à margem de atraso. Use o serviço de bot do Azure ou o adaptador de margem de atraso para configurar bots para responder a mensagens de margem de atraso e atividade de canal.
keywords: conectar um bot, canal de bot, bot do Slack, aplicativo de mensagens do Slack, adaptador do Slack
author: arturl
ms.author: arturl
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/30/2020
ms.openlocfilehash: a42c12ea4329eb0dbfcf90ff49b3f85d5c1a2abd
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576268"
---
# <a name="connect-a-bot-to-slack"></a>Conectar um bot ao Slack

[!INCLUDE [applies-to-v4](~/includes/applies-to-v4-current.md)]

Este artigo mostra como adicionar um canal de margem de atraso a um bot. Selecione uma das seguintes maneiras para configurar o **aplicativo de margem de atraso**:

- Usar o portal do Serviço de Bot do Azure para conectar seu bot
- Usar o adaptador do Slack

## <a name="azure-bot-service-portal"></a>[Portal do Serviço de Bot do Azure](#tab/abs)

## <a name="create-a-slack-application-for-your-bot"></a>Criar um aplicativo do Slack para o seu bot

1. No navegador, entre na [margem de atraso](https://slack.com/signin).
1. Navegue até [o painel de aplicativos](https://api.slack.com/apps) .

    ![Configurar o bot](~/media/channels/slack-NewApp.png)

1. Clique em **Criar Novo Aplicativo**.
1. Na caixa **nome do aplicativo** , digite o nome do seu aplicativo de margem de atraso.
1. Na caixa **equipe de margem de atraso do desenvolvimento** , insira o nome da sua equipe de desenvolvimento. Se você ainda não for membro de uma equipe de Desenvolvimento do Slack, [crie ou ingresse em uma](https://slack.com/).

    ![Criar aplicativo](~/media/channels/slack-CreateApp.png)

1. Clique em **Criar Aplicativo**.

### <a name="add-a-new-redirect-url"></a>Adicionar uma nova URL de redirecionamento

1. No painel esquerdo, selecione o item de menu **permissões de & OAuth** .
1. No painel direito, clique em **Adicionar uma nova URL de redirecionamento**.
1. Na caixa, digite `https://slack.botframework.com/` .
1. Clique em **Adicionar**.
1. Clique em **Salvar URLs**.

    ![Adicionar URL de redirecionamento](~/media/channels/slack-RedirectURL.png)

### <a name="subscribe-to-bot-events"></a>Assinar eventos de Bot

Execute estas etapas para assinar seis eventos de bot específicos. Ao assinar eventos de bot, seu aplicativo receberá uma notificação das atividades do usuário na URL que você especificar.

> [!TIP]
> Seu identificador de bot é o nome do seu bot. Para localizar o identificador de um bot, visite [https://dev.botframework.com/bots](https://dev.botframework.com/bots) , escolha um bot e registre o nome do bot.

1. No painel esquerdo, selecione o item **assinaturas de evento** .
1. No painel direito, defina **habilitar eventos** como **ativado**.
1. Na **URL de solicitação**, insira `https://slack.botframework.com/api/Events/{YourBotHandle}`, no qual `{YourBotHandle}` é seu identificador de bot, sem os colchetes.

   ![eventos de assinatura](~/media/channels/slack-subscribe-events.png)

1. Em **Assinar Eventos de Bot**, clique em **Adicionar Evento de Usuário do Bot**.
1. Na lista de eventos, selecione esses seis tipos de evento:
    - `member_joined_channel`
    - `member_left_channel`
    - `message.channels`
    - `message.groups`
    - `message.im`
    - `message.mpim`

   ![lista de eventos assinados](~/media/channels/slack-subscribed-events.png)

1. Na parte inferior da tela, clique em **salvar alterações**.

À medida que você adiciona eventos na margem de atraso, ele lista os escopos que você precisa solicitar. Os escopos necessários dependerão dos eventos que você assinar e de como você pretende respondê-los.
Para escopos com suporte de margem de atraso, consulte [escopos e permissões](https://api.slack.com/scopes). Consulte também [noções básicas sobre escopos OAuth para bots](https://api.slack.com/tutorials/understanding-oauth-scopes-bot).

> [!NOTE]
> A partir do canal de margem de atraso de junho de 2020 dá suporte a escopos de permissão v2 de margem de atraso que permitem que o bot especifique seus recursos e permissões de maneira mais granular. Todos os canais de margem de atraso configurados recentemente usarão os escopos v2. Para alternar o bot para os escopos v2, exclua e recrie a configuração de canal de margem de atraso na folha canais de portal do Azure.

### <a name="add-and-configure-interactive-messages-optional"></a>Adicionar e configurar mensagens interativas (opcional)

Se o seu bot usar funcionalidades específicas do Slack, como botões, execute estas etapas:

1. Selecione a guia **atalhos de interatividade &** e habilite a **interatividade**.
2. Insira `https://slack.botframework.com/api/Actions` como a **URL da Solicitação**.
3. Clique no botão **Salvar alterações**.

    ![Habilitar mensagens](~/media/channels/slack-MessageURL.png)

### <a name="add-the-slack-channel-to-the-bot"></a>Adicionar o canal de margem de atraso ao bot

1. No painel esquerdo, selecione o item de **informações básico** .
1. No painel direito, role até a seção **credenciais do aplicativo** . A **ID do cliente**, o **segredo do cliente** e o **segredo de assinatura** necessários para configurar seu canal de bot de margem de atraso são exibidos. Copie e armazene essas credenciais em um local seguro.

    ![Obter credenciais](~/media/channels/slack-AppCredentials.png)

1. Abra o bot na [portal do Azure](https://portal.azure.com/).
1. No painel esquerdo, selecione **canais**,
1. No painel direito, selecione o ícone de **margem de atraso** .
1. Cole as credenciais do aplicativo de margem de atraso salvas nas etapas anteriores nos campos apropriados.
1. A **URL da Página de Aterrissagem** é opcional. Você pode omitir ou alterá-la.

    ![Enviar credenciais](~/media/channels/slack-SubmitCredentials.png)

1. Clique em **Salvar**.
    Siga as instruções para autorizar o acesso ao seu aplicativo Slack para sua equipe de Desenvolvimento do Slack.
1. Na página Configurar margem de atraso, confirme se o controle deslizante pelo botão salvar está definido como **habilitado**.
O bot agora está configurado para se comunicar com os usuários na margem de atraso.

### <a name="create-an-add-to-slack-button"></a>Criar um botão Adicionar ao Slack

O Slack fornece HTML que pode ser usado para ajudar os usuários do Slack a localizar seu bot na seção *Adicionar o botão Slack*[desta página](https://api.slack.com/docs/slack-button).
Para usar este HTML com o bot, substitua o valor de href (começa com `https://`) pela URL encontrada nas configurações do canal do Slack do seu bot.
Execute estas etapas para obter a URL de substituição.

1. Em [https://dev.botframework.com/bots](https://dev.botframework.com/bots) , clique em seu bot.
2. Clique em **Canais**, clique com o botão direito na entrada **Slack** e clique em **Copiar link**. Agora, essa URL está em sua área de transferência.
3. Copie essa URL da área de transferência e cole-a no HTML fornecido para o botão do Slack. Essa URL substitui o valor de href fornecido pelo Slack para este bot.

Os usuários autorizados podem clicar no botão **Adicionar ao Slack** fornecido por esse HTML modificado para acessar seu bot no Slack.

> [!NOTE]
> O link que você colou no valor href do HTML contém escopos que podem ser refinados conforme necessário. Consulte [escopos e permissões](https://api.slack.com/scopes) para obter a lista completa de escopos disponíveis.

## <a name="slack-adapter"></a>[Adaptador do Slack](#tab/adapter)

### <a name="connect-a-bot-to-slack-using-the-slack-adapter"></a>Conectar um bot ao Slack usando o adaptador do Slack

Assim como o canal disponível no Serviço de Bot do Azure para conectar o bot com o Slack, você também pode usar o adaptador do Slack. Neste artigo, você aprenderá a conectar um bot ao Slack usando o adaptador. Este artigo explicará como modificar o exemplo EchoBot para conectá-lo a um aplicativo do Slack.

> [!NOTE]
> As instruções a seguir abordam a implementação em C# do adaptador do Slack. Para obter instruções sobre como usar o adaptador JS, parte das bibliotecas BotKit, [consulte a documentação do Slack sobre BotKit](https://botkit.ai/docs/v4/platforms/slack.html).

### <a name="prerequisites"></a>Pré-requisitos

- O [código de exemplo do EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

- Acesso a um workspace do Slack com permissões suficientes para criar e gerenciar aplicativos em [https://api.slack.com/apps](https://api.slack.com/apps). Se você não tiver acesso a um ambiente do Slack, poderá criar um workspace [gratuitamente](https://www.slack.com).

### <a name="create-a-slack-application-and-configure-it-for-your-bot"></a>Criar um aplicativo de margem de atraso e configurá-lo para o bot

Faça logon no [Slack](https://slack.com/signin) e [crie um canal do aplicativo Slack](https://api.slack.com/apps).

![Configurar o adaptador de bot](~/media/channels/slack-NewApp.png)

Clique no botão "criar novo aplicativo".

#### <a name="create-an-app-and-assign-a-development-slack-team"></a>Criar um aplicativo e atribuir uma equipe de desenvolvimento do Slack

Insira um **Nome de Aplicativo** e selecione um **Workspace de Desenvolvimento do Slack**. Se você ainda não for membro de uma equipe de desenvolvimento do Slack, [crie ou ingresse em uma](https://slack.com/).

![Criar aplicativo](~/media/channels/slack-CreateApp.png)

Clique em **Criar Aplicativo**. O Slack criará seu aplicativo e vai gerar uma ID do cliente e um segredo do cliente.

#### <a name="gather-required-configuration-settings-for-your-bot"></a>Reunir as definições de configuração necessárias para o bot

Depois que o aplicativo é criado, colete as informações a seguir. Você precisará disso para conectar o bot ao Slack.

1. Tome nota do **Token de Verificação** e do **Segredo de Assinatura** na guia **Informações Básicas** e mantenha-os para definir as configurações do bot mais tarde.

![Tokens do Slack](~/media/bot-service-adapter-connect-slack/slack-tokens.png)

2. Navegue até a página **Instalar Aplicativo** no menu **Configurações** e siga as instruções para instalar seu aplicativo em uma equipe do Slack.  Depois de instalado, copie o **Token de Acesso OAuth do Usuário do Bot** e, novamente, mantenha isso para definir as configurações de bot mais tarde.

### <a name="wiring-up-the-slack-adapter-in-your-bot"></a>Conectando o adaptador do Slack ao seu bot

#### <a name="install-the-slack-adapter-nuget-package"></a>Instalar o pacote NuGet do adaptador do Slack

Adicione o pacote NuGet [Microsoft.Bot.Builder.Adapters.Slack](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Slack/). Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](/nuget/tools/package-manager-ui)

#### <a name="create-a-slack-adapter-class"></a>Criar uma classe de adaptador do Slack

Crie uma nova classe que herde da classe **_SlackAdapter_* _. Essa classe atuará como nosso adaptador para o canal de margem de atraso e incluirá recursos de tratamento de erros (semelhante à classe _*_BotFrameworkAdapterWithErrorHandler_*_ já no exemplo, usada para lidar com outras solicitações do serviço de bot do Azure).

```csharp
public class SlackAdapterWithErrorHandler : SlackAdapter
{
    public SlackAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
    : base(configuration, logger)
    {
        OnTurnError = async (turnContext, exception) =>
        {
            // Log any leaked exception from the application.
            logger.LogError(exception, $"[OnTurnError] unhandled error : {exception.Message}");

            // Send a message to the user
            await turnContext.SendActivityAsync("The bot encountered an error or bug.");
            await turnContext.SendActivityAsync("To continue to run this bot, please fix the bot source code.");

            // Send a trace activity, which will be displayed in the Bot Framework Emulator
            await turnContext.TraceActivityAsync("OnTurnError Trace", exception.Message, "https://www.botframework.com/schemas/error", "TurnError");
        };
    }
}
```

#### <a name="create-a-new-controller-for-handling-slack-requests"></a>Criar um controlador para lidar com as solicitações do Slack

Criamos um novo controlador que tratará das solicitações de seu aplicativo de margem de atraso, em um novo ponto de extremidade, `api/slack` em vez do padrão `api/messages` usado para solicitações de canais do serviço de bot do Azure.  Ao adicionar outro ponto de extremidade ao bot, você poderá aceitar solicitações de canais do Serviço de Bot, assim como do Slack, usando o mesmo bot.

```csharp
[Route("api/slack")]
[ApiController]
public class SlackController : ControllerBase
{
    private readonly SlackAdapter _adapter;
    private readonly IBot _bot;

    public SlackController(SlackAdapter adapter, IBot bot)
    {
        _adapter = adapter;
        _bot = bot;
    }

    [HttpPost]
    [HttpGet]
    public async Task PostAsync()
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await _adapter.ProcessAsync(Request, Response, _bot);
    }
}
```

#### <a name="add-slack-app-settings-to-your-bots-configuration-file"></a>Adicionar configurações do aplicativo do Slack ao arquivo de configuração do bot

Adicione as 3 configurações mostradas abaixo ao arquivo appSettings.json do projeto de bot, preenchendo cada uma com os valores coletados anteriormente ao criar o aplicativo do Slack.

```json
  "SlackVerificationToken": "",
  "SlackBotToken": "",
  "SlackClientSigningSecret": ""
```

#### <a name="inject-the-slack-adapter-in-your-bot-startupcs"></a>Injetar o adaptador do Slack no startup.cs do bot

Adicione a seguinte linha ao método _*_configuraservices_*_ no arquivo Startup.cs. Isso registrará o adaptador do Slack e o disponibilizará para a nova classe de controlador.  As definições de configuração que você adicionou na etapa anterior serão usadas automaticamente pelo adaptador.

```csharp
services.AddSingleton<SlackAdapter, SlackAdapterWithErrorHandler>();
```

Depois de adicionado, _*_o método configureservices_*_ deve ser assim.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and Emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Slack Adapter
    services.AddSingleton<SlackAdapter, SlackAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

### <a name="complete-configuration-of-your-slack-app"></a>Concluir a configuração do aplicativo do Slack

#### <a name="obtain-a-url-for-your-bot"></a>Obter uma URL para o bot

Agora que você criou um aplicativo do Slack e conectou o adaptador ao seu projeto de bot, a última etapa é apontar o aplicativo do Slack para o ponto de extremidade correto no bot e assinar seu aplicativo para garantir que o bot receba mensagens.  Para fazer isso, seu bot deve estar em execução, para que o Slack verifique se a URL para o ponto de extremidade é válida.

Para concluir esta etapa, [implante o bot no Azure](bot-builder-deploy-az-cli.md) e anote a URL do bot implantado.

> [!NOTE]
> Se você não estiver pronto para implantar o bot no Azure ou desejar depurar o bot ao usar o adaptador de margem de atraso, poderá usar uma ferramenta como [ngrok](https://www.ngrok.com) (que provavelmente já terá instalado se você tiver usado o emulador do bot Framework anteriormente) para realizar o túnel para o bot em execução localmente e fornecer uma URL acessível publicamente para isso.
>
> Se desejar criar um túnel ngrok e obter uma URL para o bot, use o comando a seguir em uma janela do terminal (isso pressupõe que o bot local esteja em execução na porta 3978; altere os números de porta no comando se o bot não estiver).
>
> ```cmd
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

#### <a name="update-your-slack-app"></a>Atualizar seu aplicativo do Slack

Navegue de volta para o [painel da API do Slack](https://api.slack.com/apps) e selecione seu aplicativo.  Agora você precisa configurar duas URLs para seu aplicativo e assinar os eventos apropriados.

1. Na guia _ *OAuth & Permissions**, a **URL de redirecionamento** deve ser a URL do bot, além do `api/slack` ponto de extremidade que você especificou em seu controlador recém-criado. Por exemplo, `https://yourboturl.com/api/slack`.

![URL de redirecionamento do Slack](~/media/bot-service-adapter-connect-slack/redirect-url.png)

2. Na guia **Assinaturas de Evento**, preencha a **URL de Solicitação** com a mesma URL usada na etapa 1.

3. Habilite os eventos usando o botão de alternância na parte superior da página.

4. Expanda a seção **Assinar eventos de bot** e use o botão **Adicionar Evento do Usuário de Bot** para assinar os eventos **im_created** e **message.im**.

![Assinaturas de evento do Slack](~/media/bot-service-adapter-connect-slack/event-subscriptions.png)

---

## <a name="test-your-application-in-slack"></a>Testar seu aplicativo na margem de atraso

1. Faça logon no espaço de trabalho de margem de atraso onde você instalou seu aplicativo ( `http://<your work space>-group.slack.com/` ). Você o verá listado na seção **aplicativos** no painel esquerdo.
1. No painel esquerdo, selecione seu aplicativo.
1. No painel direito, escreva e mensagem e envie-a para o aplicativo. Se você usou um bot de eco, o aplicativo ecoa de volta a mensagem, conforme mostrado na figura abaixo.

    ![teste de aplicativo](~/media/channels/slack-echobotapp-test.png)

Você também pode testar esse recurso usando o [bot de exemplo para o adaptador do Slack](https://aka.ms/csharp-60-slack-adapter-sample) preenchendo o arquivo appSettings.json com os mesmos valores descritos nas etapas acima. Este exemplo tem etapas adicionais descritas no arquivo LEIAME para mostrar exemplos de compartilhamento de link, recebimento de anexos e envio de mensagens interativas.
