---
title: Conectar um bot ao Slack – Serviço de Bot
description: Saiba como conectar bots à margem de atraso. Use o serviço de bot do Azure ou o adaptador de margem de atraso para configurar bots para responder a mensagens de margem de atraso e atividade de canal.
keywords: conectar um bot, canal de bot, bot do Slack, aplicativo de mensagens do Slack, adaptador do Slack
author: arturl
ms.author: arturl
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/09/2020
ms.openlocfilehash: 0e5cb90864b3f8e1a4c51cbbe13d40e71c135ff3
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824036"
---
# <a name="connect-a-bot-to-slack"></a>Conectar um bot ao Slack

Há duas maneiras pelas quais você pode configurar o aplicativo de mensagens de margem de atraso:

- Usar o portal do Serviço de Bot do Azure para conectar seu bot
- Usar o adaptador do Slack

## <a name="azure-bot-service-portal"></a>[Portal do Serviço de Bot do Azure](#tab/abs)

## <a name="create-a-slack-application-for-your-bot"></a>Criar um aplicativo do Slack para o seu bot

Faça logon no [Slack](https://slack.com/signin) e [crie um canal do aplicativo Slack](https://api.slack.com/apps).

![Configurar o bot](~/media/channels/slack-NewApp.png)

## <a name="create-an-app-and-assign-a-development-slack-team"></a>Criar um aplicativo e atribuir uma equipe de Desenvolvimento do Slack

Insira um Nome de Aplicativo e selecione uma equipe de Desenvolvimento do Slack. Se você ainda não for membro de uma equipe de Desenvolvimento do Slack, [crie ou ingresse em uma](https://slack.com/).

![Criar aplicativo](~/media/channels/slack-CreateApp.png)

Clique em **Criar Aplicativo**. O Slack criará seu aplicativo e gerará uma ID de Cliente e Segredo do Cliente.

## <a name="add-a-new-redirect-url"></a>Adicionar uma nova URL de Redirecionamento

Em seguida, você adicionará uma nova URL de Redirecionamento.

1. Selecione a guia **OAuth e Permissões**.
2. Clique em **Adicionar uma nova URL de Redirecionamento**.
3. Insira [https://slack.botframework.com](https://slack.botframework.com) .
4. Clique em **Adicionar**.
5. Clique em **Salvar URLs**.

![Adicionar URL de redirecionamento](~/media/channels/slack-RedirectURL.png)

## <a name="create-a-slack-bot-user"></a>Criar um Usuário de Bot do Slack

Ao adicionar um Usuário de Bot, você pode atribuir um nome de usuário para o seu bot e escolher se ele sempre será mostrado como online.

1. Selecione a guia **Usuários de Bot**.
2. Clique em **Adicionar um Usuário de Bot**.

![Criar margem de atraso da imagem do bot](~/media/channels/slack-CreateBot.png)

Clique em **Adicionar Usuário de Bot** para validar suas configurações, defina **Sempre Mostrar meu Bot Online** como **Ligado** e, em seguida, clique em **Salvar Alterações**.

![CreateApp da margem de atraso AddBotUser imagem](~/media/channels/slack-CreateApp-AddBotUser.png)

## <a name="subscribe-to-bot-events"></a>Assinar eventos de Bot

Execute estas etapas para assinar seis eventos de bot específicos. Ao assinar eventos de bot, seu aplicativo receberá uma notificação das atividades do usuário na URL que você especificar.

> [!TIP]
> Seu identificador de bot é o nome do seu bot. Para localizar o identificador de um bot, visite [https://dev.botframework.com/bots](https://dev.botframework.com/bots) , escolha um bot e registre o nome do bot.

1. Selecione a guia **Assinaturas de Evento**.
2. Defina **Habilitar Eventos** como **Ligado**.
3. Na **URL de solicitação**, insira `https://slack.botframework.com/api/Events/{YourBotHandle}`, no qual `{YourBotHandle}` é seu identificador de bot, sem os colchetes. O identificador de bot usado neste exemplo é **ContosoBot**.

   ![Assinar eventos: superior](~/media/channels/slack-SubscribeEvents-a.png)

4. Em **Assinar Eventos de Bot**, clique em **Adicionar Evento de Usuário do Bot**.
5. Na lista de eventos, selecione esses seis tipos de evento:
    * `member_joined_channel`
    * `member_left_channel`
    * `message.channels`
    * `message.groups`
    * `message.im`
    * `message.mpim`

   ![Assinar eventos: meio](~/media/channels/slack-SubscribeEvents-b.png)

6. Clique em **Salvar Alterações**.

   ![Assinar eventos: inferior](~/media/channels/slack-SubscribeEvents-c.png)

## <a name="add-and-configure-interactive-messages-optional"></a>Adicionar e configurar mensagens interativas (opcional)

Se o seu bot usar funcionalidades específicas do Slack, como botões, execute estas etapas:

1. Selecione a guia **atalhos de interatividade &** e habilite a **interatividade**.
2. Insira `https://slack.botframework.com/api/Actions` como a **URL da Solicitação**.
3. Clique no botão **Salvar alterações**.

![Habilitar mensagens](~/media/channels/slack-MessageURL.png)

## <a name="gather-credentials"></a>Obter credenciais

Selecione a guia **Informações Básicas** e role até a seção **Credenciais de Aplicativo**.
A ID do Cliente, o Segredo do Cliente e o Token de Verificação, necessários para a configuração do seu bot do Slack, são exibidos.

![Obter credenciais](~/media/channels/slack-AppCredentials.png)

## <a name="submit-credentials"></a>Enviar credenciais

Em uma janela separada do navegador, volte para o site do Bot Framework em `https://dev.botframework.com/`.

1. Selecione **Meus bots** e escolha o Bot que você deseja conectar ao Slack.
2. Na seção **Canais**, clique no ícone do Slack.
3. Na seção **Inserir suas credenciais do Slack**, cole as Credenciais de Aplicativo do site do Slack nos campos apropriados.
4. A **URL da Página de Aterrissagem** é opcional. Você pode omitir ou alterá-la.
5. Clique em **Save** (Salvar).

![Enviar credenciais](~/media/channels/slack-SubmitCredentials.png)

Siga as instruções para autorizar o acesso ao seu aplicativo Slack para sua equipe de Desenvolvimento do Slack.

## <a name="enable-the-bot"></a>Habilitar o bot

Na página Configurar Slack, confirme se o controle deslizante ao lado do botão Salvar é definido como **Habilitado**.
Seu bot está configurado para se comunicar com os usuários no Slack.

## <a name="create-an-add-to-slack-button"></a>Criar um botão Adicionar ao Slack

O Slack fornece HTML que pode ser usado para ajudar os usuários do Slack a localizar seu bot na seção *Adicionar o botão Slack*[desta página](https://api.slack.com/docs/slack-button).
Para usar este HTML com o bot, substitua o valor de href (começa com `https://`) pela URL encontrada nas configurações do canal do Slack do seu bot.
Execute estas etapas para obter a URL de substituição.

1. Em [https://dev.botframework.com/bots](https://dev.botframework.com/bots) , clique em seu bot.
2. Clique em **Canais**, clique com o botão direito na entrada **Slack** e clique em **Copiar link**. Agora, essa URL está em sua área de transferência.
3. Copie essa URL da área de transferência e cole-a no HTML fornecido para o botão do Slack. Essa URL substitui o valor de href fornecido pelo Slack para este bot.

Os usuários autorizados podem clicar no botão **Adicionar ao Slack** fornecido por esse HTML modificado para acessar seu bot no Slack.

> [!NOTE]
> A partir do canal de margem de atraso de junho de 2020 dá suporte a escopos de permissão v2 de margem de atraso que permitem que o bot especifique seus recursos e permissões de maneira mais granular. Todos os canais de margem de atraso configurados recentemente usarão os escopos v2. Para alternar o bot para os escopos v2, exclua e recrie a configuração de canal de margem de atraso na folha canais. O link que você colou no valor href do HTML contém escopos que podem ser refinados conforme necessário. Consulte [https://api.slack.com/scopes](https://api.slack.com/scopes) a lista completa de escopos disponíveis.

## <a name="slack-adapter"></a>[Adaptador do Slack](#tab/adapter)

## <a name="connect-a-bot-to-slack-using-the-slack-adapter"></a>Conectar um bot ao Slack usando o adaptador do Slack

Assim como o canal disponível no Serviço de Bot do Azure para conectar o bot com o Slack, você também pode usar o adaptador do Slack. Neste artigo, você aprenderá a conectar um bot ao Slack usando o adaptador.  Este artigo explicará como modificar o exemplo EchoBot para conectá-lo a um aplicativo do Slack.

> [!NOTE]
> As instruções a seguir abordam a implementação em C# do adaptador do Slack. Para obter instruções sobre como usar o adaptador JS, parte das bibliotecas BotKit, [consulte a documentação do Slack sobre BotKit](https://botkit.ai/docs/v4/platforms/slack.html).

## <a name="prerequisites"></a>Pré-requisitos

* O [código de exemplo do EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Acesso a um workspace do Slack com permissões suficientes para criar e gerenciar aplicativos em [https://api.slack.com/apps](https://api.slack.com/apps). Se você não tiver acesso a um ambiente do Slack, poderá criar um workspace [gratuitamente](https://www.slack.com).

## <a name="create-a-slack-application-and-configure-it-for-your-bot"></a>Criar um aplicativo de margem de atraso e configurá-lo para o bot

Faça logon no [Slack](https://slack.com/signin) e [crie um canal do aplicativo Slack](https://api.slack.com/apps).

![Configurar o bot](~/media/channels/slack-NewApp.png)

Clique no botão "criar novo aplicativo".

### <a name="create-an-app-and-assign-a-development-slack-team"></a>Criar um aplicativo e atribuir uma equipe de desenvolvimento do Slack

Insira um **Nome de Aplicativo** e selecione um **Workspace de Desenvolvimento do Slack**. Se você ainda não for membro de uma equipe de desenvolvimento do Slack, [crie ou ingresse em uma](https://slack.com/).

![Criar aplicativo](~/media/channels/slack-CreateApp.png)

Clique em **Criar Aplicativo**. O Slack criará seu aplicativo e vai gerar uma ID do cliente e um segredo do cliente.

### <a name="gather-required-configuration-settings-for-your-bot"></a>Reunir as definições de configuração necessárias para o bot

Depois que o aplicativo é criado, colete as informações a seguir. Você precisará disso para conectar o bot ao Slack.

1. Tome nota do **Token de Verificação** e do **Segredo de Assinatura** na guia **Informações Básicas** e mantenha-os para definir as configurações do bot mais tarde.

![Tokens do Slack](~/media/bot-service-adapter-connect-slack/slack-tokens.png)

2. Navegue até a página **Instalar Aplicativo** no menu **Configurações** e siga as instruções para instalar seu aplicativo em uma equipe do Slack.  Depois de instalado, copie o **Token de Acesso OAuth do Usuário do Bot** e, novamente, mantenha isso para definir as configurações de bot mais tarde.

## <a name="wiring-up-the-slack-adapter-in-your-bot"></a>Conectando o adaptador do Slack ao seu bot

### <a name="install-the-slack-adapter-nuget-package"></a>Instalar o pacote NuGet do adaptador do Slack

Adicione o pacote NuGet [Microsoft.Bot.Builder.Adapters.Slack](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Slack/). Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs)

### <a name="create-a-slack-adapter-class"></a>Criar uma classe de adaptador do Slack

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

### <a name="create-a-new-controller-for-handling-slack-requests"></a>Criar um controlador para lidar com as solicitações do Slack

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

### <a name="add-slack-app-settings-to-your-bots-configuration-file"></a>Adicionar configurações do aplicativo do Slack ao arquivo de configuração do bot

Adicione as 3 configurações mostradas abaixo ao arquivo appSettings.json do projeto de bot, preenchendo cada uma com os valores coletados anteriormente ao criar o aplicativo do Slack.

```json
  "SlackVerificationToken": "",
  "SlackBotToken": "",
  "SlackClientSigningSecret": ""
```

### <a name="inject-the-slack-adapter-in-your-bot-startupcs"></a>Injetar o adaptador do Slack no startup.cs do bot

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

## <a name="complete-configuration-of-your-slack-app"></a>Concluir a configuração do aplicativo do Slack

### <a name="obtain-a-url-for-your-bot"></a>Obter uma URL para o bot

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

### <a name="update-your-slack-app"></a>Atualizar seu aplicativo do Slack

Navegue de volta para o [painel da API do Slack](https://api.slack.com/apps) e selecione seu aplicativo.  Agora você precisa configurar duas URLs para seu aplicativo e assinar os eventos apropriados.

1. Na guia **OAuth & permissões**, a **URL de Redirecionamento** deve ser a URL do bot, além do ponto de extremidade `api/slack` especificado em seu controlador recém-criado. Por exemplo, `https://yourboturl.com/api/slack`.

![URL de redirecionamento do Slack](~/media/bot-service-adapter-connect-slack/redirect-url.png)

2. Na guia **Assinaturas de Evento**, preencha a **URL de Solicitação** com a mesma URL usada na etapa 1.

3. Habilite os eventos usando o botão de alternância na parte superior da página.

4. Expanda a seção **Assinar eventos de bot** e use o botão **Adicionar Evento do Usuário de Bot** para assinar os eventos **im_created** e **message.im**.

![Assinaturas de evento do Slack](~/media/bot-service-adapter-connect-slack/event-subscriptions.png)

## <a name="test-your-bot-with-adapter-in-slack"></a>Testar o bot com o adaptador no Slack

Seu aplicativo do Slack agora está configurado e você já pode fazer logon no workspace do Slack no qual você instalou o aplicativo. (Você o verá listado na seção ' aplicativos ' do menu à esquerda.) Selecione seu aplicativo e tente enviar uma mensagem. Você deve vê-la ecoada de volta para você na janela de mensagens instantâneas.

Você também pode testar esse recurso usando o [bot de exemplo para o adaptador do Slack](https://aka.ms/csharp-60-slack-adapter-sample) preenchendo o arquivo appSettings.json com os mesmos valores descritos nas etapas acima. Este exemplo tem etapas adicionais descritas no arquivo LEIAME para mostrar exemplos de compartilhamento de link, recebimento de anexos e envio de mensagens interativas.

---
