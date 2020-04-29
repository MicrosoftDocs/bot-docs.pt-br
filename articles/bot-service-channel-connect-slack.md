---
title: Conectar um bot ao Slack – Serviço de Bot
description: Saiba como configurar a conexão do bot com o Slack.
keywords: conectar um bot, canal de bot, bot do Slack, aplicativo de mensagens do Slack, adaptador do Slack
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 04/20/2020
ms.openlocfilehash: 1c2ec05986311d39de7835b2ba3a466acfbc5833
ms.sourcegitcommit: 2412f96ad8f74dfa615c71f566c5befffb920658
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/25/2020
ms.locfileid: "82158853"
---
# <a name="connect-a-bot-to-slack"></a>Conectar um bot ao Slack

Este artigo descreve como configurar o canal do Slack para permitir que o usuário se comunique com um bot.

Você pode executar uma das seguintes abordagens para conectar o canal do Slack a um bot:

- Configurar o Slack no Azure.
- Usar o adaptador do Slack (disponível apenas para o SDK do C# por enquanto).

# <a name="configure-slack-in-azure"></a>[Configurar o Slack no Azure](#tab/abs)

## <a name="prerequisites"></a>Pré-requisitos

- Um bot implantado no Azure
- Um aplicativo do Slack

## <a name="create-a-bot-in-azure"></a>Criar um bot no Azure

Para criar e implantar um bot no Azure, confira [Tutorial: Criar e implantar um bot básico](./v4sdk/bot-builder-tutorial-basic-deploy.md).

## <a name="create-a-slack-application-to-communicate-with-your-bot"></a>Criar um aplicativo do Slack para se comunicar com o bot

1. Em seu navegador, faça logon no [Slack][slack portal].
1. Navegue até [Criar um aplicativo Slack][slack applications].
1. Clique no botão **Criar Aplicativo**.

    ![Configurar o bot](media/channels/slack-NewApp.png)

1. Clique no botão **Criar Aplicativo**.
1. Insira um Nome de Aplicativo e selecione uma equipe de Desenvolvimento do Slack.
Se você ainda não for membro de uma equipe de Desenvolvimento do Slack, [crie ou ingresse em uma](https://slack.com/).

    ![Criar aplicativo](media/channels/slack-CreateApp.png)

1. Clique no botão **Criar Aplicativo**. O Slack criará seu aplicativo e gerará uma **ID do Cliente** e um **Segredo do Cliente**.

### <a name="add-a-new-redirect-url"></a>Adicionar uma nova URL de Redirecionamento

1. No painel esquerdo, clique no link **OAuth e Permissões**.
2. No painel direito, clique no botão **Adicionar uma nova URL de Redirecionamento**.
3. Digite o valor a seguir `https://slack.botframework.com`.
4. Clique no botão **Adicionar** .
5. Clique no botão **Salvar URLs**.

    ![Adicionar URL de redirecionamento](media/channels/slack-RedirectURL.png)

### <a name="add-bot-token-scopes"></a>Adicionar escopos de token de bot

1. No painel esquerdo, clique no link **OAuth e Permissões**.
1. No painel direito, role para baixo até **Escopos**.
1. Na seção **Escopos de Token de Bot**, clique no botão **Adicionar um Escopo OAuth**.
1. Selecione os seguintes escopos, também mostrados na imagem:

    - `app_mentions:read`
    - `channels:history`
    - `channels:read`
    - `chat:write`
    - `groups:history`
    - `groups:read`
    - `im:history`
    - `mpim:history`

    ![escopos de bot do slack](media/channels/slack-bot-scopes.PNG)

1. No painel esquerdo, selecione o link **Página Inicial do Aplicativo**.
1. No painel direito, role para baixo e habilite **Sempre Mostrar Meu Bot como Online**.

    ![exibição do bot do slack](media/channels/slack-bot-display.PNG)

### <a name="subscribe-to-bot-events"></a>Assinar eventos de Bot

Execute estas etapas para assinar seis eventos de bot específicos. Ao assinar eventos de bot, seu aplicativo receberá uma notificação das atividades do usuário na URL que você especificar.

> [!TIP]
> Seu identificador de bot é o nome do seu bot.
> Para localizar o identificador de um bot, visite o [portal do Azure][azure portal].
> Selecione o serviço de aplicativo bot que você criou e registre o nome do bot.

1. Selecione a guia **Assinaturas de Evento**.
2. Defina **Habilitar Eventos** como **Ligado**.
3. Na **URL de solicitação**, insira `https://slack.botframework.com/api/Events/{YourBotHandle}`, no qual `{YourBotHandle}` é seu identificador de bot, sem os colchetes. O identificador de bot usado neste exemplo é **ContosoBot**.

   ![Assinar eventos: superior](media/channels/slack-SubscribeEvents-a.png)

4. Em **Assinar Eventos de Bot**, clique em **Adicionar Evento de Usuário do Bot**.
5. Na lista de eventos, selecione esses seis tipos de evento:

    - `member_joined_channel`
    - `member_left_channel`
    - `message.channels`
    - `message.groups`
    - `message.im`
    - `message.mpim`

   ![Assinar eventos: meio](media/channels/slack-SubscribeEvents-b.png)

6. Clique em **Salvar Alterações**.

   ![Assinar eventos: inferior](media/channels/slack-SubscribeEvents-c.png)

### <a name="add-and-configure-interactive-messages-optional"></a>Adicionar e configurar mensagens interativas (opcional)

Se o seu bot usar funcionalidades específicas do Slack, como botões, execute estas etapas:

1. Selecione a guia **Componentes Interativos** e clique em **Habilitar Componentes Interativos**.
2. Insira `https://slack.botframework.com/api/Actions` como a **URL da Solicitação**.
3. Clique no botão **Salvar alterações**.

    ![Habilitar mensagens](media/channels/slack-MessageURL.png)

### <a name="gather-credentials"></a>Obter credenciais

Selecione a guia **Informações Básicas** e role até a seção **Credenciais de Aplicativo**.
A ID do Cliente, o Segredo do Cliente e o Token de Verificação, necessários para a configuração do seu bot do Slack, são exibidos.

![Obter credenciais](media/channels/slack-AppCredentials.png)

### <a name="submit-credentials"></a>Enviar credenciais

1. Em uma janela separada do navegador, navegue até o [portal do Azure][azure portal].
1. Selecione o aplicativo de bot que você deseja conectar ao Slack.
1. No painel esquerdo, selecione a entrada **Canais**.
1. No painel direito, clique no ícone **Slack**.
1. No formulário **Inserir suas credenciais do Slack**, cole as *Credenciais de Aplicativo* relevantes do site do Slack.
1. A **URL da Página de Aterrissagem** é opcional. Você pode omitir ou alterá-la.
1. Clique em **Save** (Salvar).

    ![Enviar credenciais](media/channels/slack-SubmitCredentials.png)

    Se tudo funcionar corretamente, no portal do Azure, você verá o canal do Slack conectado.

    ![Canal do Slack conectado](media/channels/slack-Connected.png)

1. Na página *Configurar Slack*, verifique se o controle deslizante ao lado do botão Salvar está definido como **Habilitado**. Seu bot agora está configurado para se comunicar com os usuários no Slack.

    ![Bot do Slack habilitado](media/channels/slack-Botenabled.png)


### <a name="test-slack-bot-communication"></a>Testar a comunicação do bot do Slack

1. Em seu navegador, navegue até a [página de logon do Slack][slack portal].

    ![Logon do Slack](media/channels/slack-Login.png)

1. Insira a URL do seu workspace.
1. Clique no botão **Continuar**. Isso levará você para a sua última página de chat.
1. Conecte-se com seu aplicativo de bot do Slack. A imagem a seguir mostra um exemplo.

    ![Aplicativo de bot do Slack](media/channels/slack-Botapp.png)

1. Insira uma mensagem na caixa de entrada.
A imagem a seguir mostra a resposta ao usar um Echo Bot.

    ![Resposta do bot do Slack](media/channels/slack-Botchat.png)

### <a name="create-an-add-to-slack-button"></a>Criar um botão Adicionar ao Slack

O Slack fornece HTML que pode ser usado para ajudar os usuários do Slack a localizar seu bot na seção *Adicionar o botão Slack*[desta página](https://api.slack.com/docs/slack-button).
Para usar este HTML com o bot, substitua o valor de href (começa com `https://`) pela URL encontrada nas configurações do canal do Slack do seu bot.
Execute estas etapas para obter a URL de substituição.

1. Em seu navegador, navegue até o [portal do Azure][azure portal].
1. Selecione seu aplicativo de bot.
1. No painel esquerdo, selecione a entrada **Canais**.
1. No painel direito, clique com o botão direito do mouse no link do **Slack**.
1. No menu suspenso, clique em **Copiar link**.

    ![Botão do Slack](media/channels/slack-Button.png)

1. Copie a URL copiada da área de transferência e cole-a no HTML fornecido para o botão do Slack. Essa URL substitui o valor de href fornecido pelo Slack para este bot.

Os usuários autorizados podem clicar no botão **Adicionar ao Slack** fornecido por esse HTML modificado para acessar seu bot no Slack.

# <a name="slack-adapter"></a>[Adaptador do Slack](#tab/adapter)

## <a name="connect-a-bot-to-slack-using-the-slack-adapter"></a>Conectar um bot ao Slack usando o adaptador do Slack

Além do canal do Slack disponível no Serviço de Bot do Azure, você também pode usar o **adaptador do Slack** para conectar o bot com o Slack.
Este artigo explicará como fazê-lo modificando o [exemplo do EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot) para conectá-lo a um aplicativo do Slack.

Você também pode usar o exemplo do [adaptador do Slack](https://aka.ms/csharp-60-slack-adapter-sample) e popular o arquivo appSettings.json com os valores descritos nas próximas seções. Este exemplo tem etapas adicionais que mostram exemplos de compartilhamento de links, recebimento de anexos e envio de mensagens interativas.

> [!NOTE]
> As instruções a seguir abordam a implementação em C# do adaptador do Slack. Para obter instruções sobre como usar o adaptador do JS, confira a [adaptador do Slack para o Bot Builder](https://botkit.ai/docs/v4/platforms/slack.html).

### <a name="prerequisites"></a>Pré-requisitos

* O [exemplo do EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Acesso a um workspace do Slack com permissões suficientes para criar e gerenciar aplicativos em [https://api.slack.com/apps](https://api.slack.com/apps). Se você não tiver acesso a um ambiente do Slack, poderá criar um workspace [gratuitamente](https://www.slack.com).

### <a name="create-a-slack-application-for-your-bot"></a>Criar um aplicativo do Slack para o seu bot

Faça logon no [Slack](https://slack.com/signin) e [crie um canal do aplicativo Slack](https://api.slack.com/apps).

![Configurar o bot](media/channels/slack-NewApp.png)

Clique no botão "criar novo aplicativo".

#### <a name="create-an-app-and-assign-a-development-slack-team"></a>Criar um aplicativo e atribuir uma equipe de desenvolvimento do Slack

Insira um **Nome de Aplicativo** e selecione um **Workspace de Desenvolvimento do Slack**. Se você ainda não for membro de uma equipe de desenvolvimento do Slack, [crie ou ingresse em uma](https://slack.com/).

![Criar aplicativo](media/channels/slack-CreateApp.png)

Clique em **Criar Aplicativo**. O Slack criará seu aplicativo e vai gerar uma ID do cliente e um segredo do cliente.

#### <a name="gather-required-configuration-settings-for-your-bot"></a>Reunir as definições de configuração necessárias para o bot

Depois que o aplicativo é criado, colete as informações a seguir. Você precisará disso para conectar o bot ao Slack.

1. Tome nota do **Token de Verificação** e do **Segredo de Assinatura** na guia **Informações Básicas** e mantenha-os para definir as configurações do bot mais tarde.

    ![Tokens do Slack](media/bot-service-adapter-connect-slack/slack-tokens.png)

1. No painel esquerdo, clique no link **Instalar Aplicativo** no menu **Configurações**.
1. No painel direito, clique no link **escopo de permissão**.
1. Na seção **Escopos**, clique no botão **Adicionar um Escopo OAuth**.
1. Verifique se você selecionou os escopos conforme descrito nesta [seção](bot-service-channel-connect-slack.md#add-bot-token-scopes).
1. Clique no botão **Instalar Aplicativo no Workspace**.
1. Na página que é exibida, clique no botão **Permitir**.
1. Depois que o aplicativo for instalado, copie o **Token de Acesso OAuth do Usuário de Bot**. Salve-o para, posteriormente, definir suas configurações de bot.

### <a name="wiring-up-the-slack-adapter-in-your-bot"></a>Conectando o adaptador do Slack ao seu bot

#### <a name="install-the-slack-adapter-nuget-package"></a>Instalar o pacote NuGet do adaptador do Slack

Adicione o pacote NuGet [Microsoft.Bot.Builder.Adapters.Slack](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Slack/). Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs)

#### <a name="create-a-slack-adapter-class"></a>Criar uma classe de adaptador do Slack

Crie uma classe nova que herda da classe ***SlackAdapter***. Essa classe atuará como nosso adaptador para o canal do Slack e incluirá recursos de tratamento de erro (semelhante à classe ***BotFrameworkAdapterWithErrorHandler*** que já está no exemplo, usada para lidar com outras solicitações do Serviço de Bot do Azure).

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

Criaremos um novo controlador que lidará com as solicitações do aplicativo do Slack em um novo ponto de extremidade 'api/slack', em vez do 'api/messages' padrão usado para solicitações de canais do Serviço de Bot do Azure.  Ao adicionar outro ponto de extremidade ao bot, você poderá aceitar solicitações de canais do Serviço de Bot, assim como do Slack, usando o mesmo bot.

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

Adicione a seguinte linha ao método ***ConfigureServices*** no arquivo startup.cs. Isso registrará o adaptador do Slack e o disponibilizará para a nova classe de controlador.  As definições de configuração que você adicionou na etapa anterior serão usadas automaticamente pelo adaptador.

```csharp
services.AddSingleton<SlackAdapter, SlackAdapterWithErrorHandler>();
```

Depois de ser adicionado, o método ***ConfigureServices*** ficará assim.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
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

Para concluir esta etapa, [implante o bot no Azure](https://aka.ms/bot-builder-deploy-az-cli) e anote a URL do bot implantado.

> [!NOTE]
> Se você não estiver pronto para implantar o bot no Azure ou desejar depurar o bot ao usar o adaptador do Slack, poderá usar uma ferramenta como a [ngrok](https://www.ngrok.com) (que você provavelmente já terá instalada se usou o emulador do Bot Framework anteriormente) para fazer um túnel para o bot em execução localmente e fornecer uma URL acessível publicamente para isso.
>
> Se desejar criar um túnel ngrok e obter uma URL para o bot, use o comando a seguir em uma janela do terminal (isso pressupõe que o bot local esteja em execução na porta 3978; altere os números de porta no comando se o bot não estiver).
>
> ```
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

#### <a name="update-your-slack-app"></a>Atualizar seu aplicativo do Slack

Navegue de volta para o [painel da API do Slack]([https://api.slack.com/apps]) e selecione seu aplicativo.  Agora você precisa configurar duas URLs para seu aplicativo e assinar os eventos apropriados.

1. Na guia **OAuth & permissões**, a **URL de Redirecionamento** deve ser a URL do bot, além do ponto de extremidade `api/slack` especificado em seu controlador recém-criado. Por exemplo, `https://yourboturl.com/api/slack`.

![URL de redirecionamento do Slack](media/bot-service-adapter-connect-slack/redirect-url.png)

1. Na guia **Assinaturas de Evento**, preencha a **URL de Solicitação** com a mesma URL usada na etapa 1.

1. Habilite os eventos usando o botão de alternância na parte superior da página.

1. Expanda a seção **Assinar eventos de bot** e use o botão **Adicionar Evento do Usuário de Bot** para assinar os eventos **im_created** e **message.im**.

![Assinaturas de evento do Slack](media/bot-service-adapter-connect-slack/event-subscriptions.png)

### <a name="test-your-bot-with-adapter-in-slack"></a>Testar o bot com o adaptador no Slack

Seu aplicativo do Slack agora está configurado e você pode fazer logon no workspace do Slack no qual você instalou o aplicativo. Você o verá listado na seção "Aplicativos" do menu à esquerda. Selecione seu aplicativo e tente enviar uma mensagem. Você deve vê-la ecoada de volta para você na janela de mensagens instantâneas. Esta imagem mostra um exemplo:

![Resposta do bot do Slack](media/channels/slack-BotAdapterchat.png)

---

<!-- Footnote-style links -->

[azure portal]: https://ms.portal.azure.com
[slack portal]: https://slack.com/signin
[slack applications]: https://api.slack.com/apps