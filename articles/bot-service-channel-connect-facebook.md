---
title: Conectar um bot ao Facebook Messenger – Serviço de Bot
description: Saiba como configurar a conexão de um bot com o Facebook Messenger.
keywords: Facebook Messenger, canal de bot, Aplicativo Facebook, ID do Aplicativo, Segredo do Aplicativo, bot do Facebook, credenciais
manager: kamrani
ms.topic: article
ms.author: kamrani
ms.service: bot-service
ms.date: 01/16/2020
ms.openlocfilehash: 84544f5c4082f88991718c5d84dc2aead05f5eb2
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80250135"
---
# <a name="connect-a-bot-to-facebook"></a>Conectar um bot ao Facebook

Seu bot pode ser conectado ao Facebook Messenger e ao Workplace do Facebook, para que ele possa se comunicar com usuários nas duas plataformas. O tutorial a seguir mostra como conectar um bot a esses dois canais.

> [!NOTE]
> A interface do usuário do Facebook pode parecer ligeiramente diferente de acordo com a versão que está sendo usada.

## <a name="connect-a-bot-to-facebook-messenger"></a>Conectar um bot ao Facebook Messenger

Para saber mais sobre o desenvolvimento para o Facebook Messenger, confira a [documentação da plataforma Messenger](https://developers.facebook.com/docs/messenger-platform). Talvez você deseje examinar as [diretrizes de pré-lançamento](https://developers.facebook.com/docs/messenger-platform/product-overview/launch#app_public), o [início rápido](https://developers.facebook.com/docs/messenger-platform/guides/quick-start) e o [guia de instalação](https://developers.facebook.com/docs/messenger-platform/guides/setup) do Facebook.

Para configurar um bot para se comunicar usando o Facebook Messenger, habilite o Facebook Messenger em uma página do Facebook e, em seguida, conecte o bot.

### <a name="copy-the-page-id"></a>Copiar a ID da Página

O bot é acessado por meio de uma Página do Facebook.

1. [Crie uma Página do Facebook](https://www.facebook.com/bookmarks/pages) ou acesse uma página existente.

1. Abra a página **Sobre** da Página do Facebook e, em seguida, copie e salve a **ID da Página**.

### <a name="create-a-facebook-app"></a>Criar um aplicativo do Facebook

1. No navegador, navegue para [Criar um novo aplicativo do Facebook](https://developers.facebook.com/quickstarts/?platform=web).
1. Insira o nome do aplicativo e clique no botão **Criar ID do Aplicativo do Facebook**.

    ![Criar aplicativo](media/channels/fb-create-messenger-bot-app.png)

1. Na caixa de diálogo exibida, insira o endereço de email e clique no botão **Criar ID do Aplicativo**.

    ![Criar uma ID do Aplicativo](media/channels/fb-create-messenger-bot-app-id.png)

1. Realize as etapas do assistente.

1. Insira as informações de verificação necessárias e clique no botão **Ignorar Início Rápido** no canto superior direito.

1. No painel esquerdo da próxima janela exibida, expanda *Configurações* e clique em **Básico**.

1. No painel à direita, copie e salve a **ID do Aplicativo** e o **Segredo do Aplicativo**.

    ![Copiar a ID do Aplicativo e Segredo do Aplicativo](media/channels/fb-messenger-bot-get-appid-secret.png)

1. No painel esquerdo, em *Configurações*, clique em **Avançado**.

1. No painel direito, defina o controle deslizante **Permitir Acesso da API às Configurações do Aplicativo** como **Sim**.

    ![Copiar a ID do Aplicativo e Segredo do Aplicativo](media/channels/fb-messenger-bot-api-settings.png)

1. Na parte inferior direita da página, clique no botão **Salvar Alterações**.

### <a name="enable-messenger"></a>Habilitar o Messenger

1. No painel esquerdo, clique em **Dashboard**.
1. No painel direito, role para baixo e, na caixa **Messenger**, clique no botão **Configurar**. A entrada do Messenger é exibida na seção *PRODUTOS* no painel esquerdo.  

    ![Habilitar o Messenger](media/channels/fb-messenger-bot-enable-messenger.png)

### <a name="generate-a-page-access-token"></a>Gerar um Token de Acesso da Página

1. No painel esquerdo, na entrada do Messenger, clique em **Configurações**.
1. No painel direito, role para baixo e, na seção **Geração de Token**, selecione a página de destino.

    ![Habilitar o Messenger](media/channels/fb-messenger-bot-select-messenger-page.png)

1. Clique no botão **Editar Permissões** para conceder pages_messaging ao aplicativo para gerar um token de acesso.
1. Siga as etapas do assistente. Na última etapa, aceite as configurações padrão e clique no botão **Concluído**. No final, um **token de acesso à página** é gerado.

    ![Permissões do Messenger](media/channels/fb-messenger-bot-permissions.png)

1. Copie e salve o **Token de Acesso da Página**.

### <a name="enable-webhooks"></a>Habilitar webhooks

Para enviar mensagens e outros eventos do bot para o Facebook Messenger, você precisa habilitar a integração de webhooks. Neste momento, vamos deixar as etapas de configuração do Facebook pendentes. Trataremos delas depois.

1. No navegador, abra uma nova janela e navegue até o [portal do Azure](https://portal.azure.com/).

1. Na lista Recurso, clique no registro do recurso de bot e, na folha relacionada, clique em **Canais**.

1. No painel direito, clique no ícone do **Facebook**.

1. No assistente, insira as informações do Facebook armazenadas nas etapas anteriores. Se as informações estiverem corretas, na parte inferior do assistente, você deverá ver a **URL de retorno de chamada** e o **token de verificação**. Copie-os e armazene-os.  

    ![configuração de canal do fb messenger](media/channels/fb-messenger-bot-config-channel.PNG)

1. Clique no botão **Salvar** .

1. Vamos voltar às configurações do Facebook. No painel direito, role para baixo e, na seção **Webhooks**, clique no botão **Assinar Eventos**. Isso é para encaminhar eventos de mensagens do Facebook Messenger para o bot.

    ![Habilitar webhooks](media/channels/fb-messenger-bot-webhooks.PNG)

1. Na caixa de diálogo exibida, insira os valores de **URL de Retorno de Chamada** e **Token de Verificação** armazenados anteriormente. Em **Campos de Assinatura**, selecione *mensagem\_entregas*, *mensagens*, *mensagens\_opções*, e *mensagens\_postbacks*.

    ![Configurar webhooks](media/channels/fb-messenger-bot-config-webhooks.png)

1. Clique no botão **Verificar e Salvar**.

1. Selecione a página do Facebook para assinar o webhook. Clique no botão **Assinar**.

    ![Página Configurar webhooks](media/channels/fb-messenger-bot-config-webhooks-page.PNG)

### <a name="submit-for-review"></a>Enviar para revisão

O Facebook exige uma URL da Política de Privacidade e uma URL dos Termos de Serviço na página de configurações básicas do aplicativo. A página [Código de Conduta](https://investor.fb.com/corporate-governance/code-of-conduct/default.aspx) contém links de recursos de terceiros para ajudar na criação de uma política de privacidade. A página [Termos de Uso](https://www.facebook.com/terms.php) contém termos de exemplo para ajudar na criação de um documento de Termos de Serviço apropriado.

Após a conclusão do bot, o Facebook tem seu próprio [processo de revisão](https://developers.facebook.com/docs/messenger-platform/app-review) para aplicativos que são publicados no Messenger. O bot será testado para garantir que está em conformidade com as [Políticas de Plataforma](https://developers.facebook.com/docs/messenger-platform/policy-overview) do Facebook.

### <a name="make-the-app-public-and-publish-the-page"></a>Tornar o Aplicativo público e publicar a Página

> [!NOTE]
> Enquanto um aplicativo não é publicado, ele fica no [Modo de Desenvolvimento](https://developers.facebook.com/docs/apps/managing-development-cycle). A funcionalidade de API e de plug-in só funcionará para administradores, desenvolvedores e testadores.

Depois que a análise for bem-sucedida, no Painel de Aplicativos em Revisão do Aplicativo, defina o aplicativo como Público.
Garanta que a Página do Facebook associada a esse bot seja publicada. O status é exibido nas configurações de Páginas.

## <a name="connect-a-bot-to-facebook-workplace"></a>Conectar um bot ao Workplace do Facebook

> [!NOTE]
> Em 16 de dezembro de 2019, o Workplace by Facebook mudou o modelo de segurança para integrações personalizadas. As integrações anteriores criadas usando o Microsoft Bot Framework v4 precisam ser atualizadas para usar os adaptadores de Facebook do Bot Framework de acordo com as instruções acima antes de 28 de fevereiro de 2020.
>
> O Facebook vai apenas considerar integrações com acesso limitado a dados do Workplace (permissões de baixa sensibilidade) elegíveis para uso contínuo até 31 de dezembro de 2020 se tais integrações tiverem sido concluídas e aprovadas na RFI de Segurança e se o desenvolvedor entrar em contato antes de 15 de janeiro de 2020 pelo [Suporte Direto](https://my.workplace.com/work/admin/direct_support) para solicitar o uso contínuo do aplicativo.
>
> Os adaptadores do Bot Framework estão disponíveis para bots do [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) e [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter).

O Workplace do Facebook é uma versão do Facebook voltada para os negócios, que permite aos funcionários se conectar e colaborar com facilidade. Ele contém vídeos ao vivo, feeds de notícias, grupos, mensageiro, reações, pesquisa e postagens de tendências. Também dá suporte a:

- Análise e integrações. Um painel com análise, integração, logon único e provedores de identidade que as empresas usam para integrar o Workplace aos seus sistemas de TI existentes.
- Grupos de várias empresas. Espaços compartilhados nos quais funcionários de diferentes organizações podem trabalhar juntos e colaborar.

Consulte a [Central de Ajuda do Workplace](https://workplace.facebook.com/help/work/) para saber mais sobre o Workplace do Facebook e a [Documentação para desenvolvedor do Workplace](https://developers.facebook.com/docs/workplace) para obter diretrizes sobre como desenvolver para Workplace do Facebook.

Para usar o Workplace do Facebook com o bot, é necessário criar uma conta do Workplace e uma integração personalizada para conectar o bot.

### <a name="create-a-workplace-premium-account"></a>Criar uma conta Premium do Workplace

1. Envie um aplicativo ao [Workplace](https://www.facebook.com/workplace) em nome de sua empresa.
1. Depois que o aplicativo for aprovado, você receberá um email com um convite de participação. A resposta pode demorar um pouco.
1. No convite por email, clique em **Começar**.
1. Insira as informações de seu perfil.
    > [!TIP]
    > Defina-se como administrador do sistema. Lembre-se de que somente os administradores do sistema podem criar integrações personalizadas.
1. Clique em **Prévia do perfil** e verifique se as informações estão corretas.
1. Acesse *Teste gratuito*.
1. Crie uma **senha**.
1. Clique em **Convidar colegas de trabalho** para convidar os funcionários a entrar. Os funcionários que você convidou se tornarão membros assim que entrarem. Eles passarão por um processo de entrada semelhante, conforme descrito nestas etapas.

### <a name="create-a-custom-integration"></a>Criar uma integração personalizada

Crie uma [integração personalizada](https://developers.facebook.com/docs/workplace/custom-integrations-new) para o Workplace executando as etapas descritas abaixo. Quando você cria uma integração personalizada, ocorre a criação de um aplicativo com permissões definidas e uma página do tipo 'Bot' visível apenas dentro de sua comunidade de Workplace.

1. No **Painel de Administração**, abra a guia **Integrações**.
1. Clique no botão **Criar seu próprio aplicativo personalizado**.

    ![Integração do Workplace](media/channels/fb-integration.png)

1. Escolha um nome de exibição e uma foto de perfil para o aplicativo. Essas informações serão compartilhadas com a página do tipo 'Bot'.
1. Defina **Permitir Acesso da API às Configurações do Aplicativo** como "Sim".
1. Copie e armazene com segurança a ID do Aplicativo, o Segredo do Aplicativo e o Token do Aplicativo mostrado para você.

    ![Chaves do Workplace](media/channels/fb-keys.png)

1. Agora você concluiu a criação de uma integração personalizada. Você pode encontrar a página do tipo 'Bot' em sua comunidade do Workplace, conforme mostrado abaixo.

    ![Página do Workplace](media/channels/fb-page.png)

### <a name="update-your-bot-code-with-facebook-adapter"></a>Atualizar seu código de bot com o adaptador do Facebook

O código-fonte do seu bot precisa ser atualizado para incluir um adaptador para comunicar com o Workplace by Facebook. Os adaptadores estão disponíveis para bots do [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) e [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter).

### <a name="provide-facebook-credentials"></a>Fornecer as credenciais do Facebook

Você precisará atualizar appsettings.json do seu bot com os valores de **ID do Aplicativo do Facebook**, **Segredo do Aplicativo do Facebook**, **Token de Acesso da Página** copiados anteriormente do Workplace do Facebook. Em vez de uma pageID tradicional, use os números que seguem o nome de integrações em sua página **Sobre**. Siga estas instruções para atualizar o código-fonte do bot no [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) ou [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter).

### <a name="submit-for-review"></a>Enviar para revisão

Consulte a seção **Conectar um bot ao Facebook Messenger** e [Documentação de desenvolvedor do Workplace](https://developers.facebook.com/docs/workplace) para obter detalhes.

### <a name="make-the-app-public-and-publish-the-page"></a>Tornar o Aplicativo público e publicar a Página

Consulte a seção **Conectar um bot ao Facebook Messenger** para obter detalhes.

### <a name="setting-the-api-version"></a>Definir a versão da API

Se você receber uma notificação sobre a substituição de uma determinada versão da API do Graph do Facebook, vá para [página de desenvolvedores do Facebook](https://developers.facebook.com). Navegue até as **Configurações do aplicativo** do seu bot e vá para **Configurações > Avançadas > Atualizar versão de API**, depois troque para **Atualizar todas as chamadas** 3.0.

![Atualização de versão da API](media/channels/fb-version-upgrade.png)

## <a name="connect-a-bot-to-facebook-using-the-facebook-adapter"></a>Conectar um bot ao Facebook usando o adaptador do Facebook

Use o adaptador do Facebook do Bot Framework para conectar seu bot com o Facebook Workplace do Facebook. Para se conectar ao Facebook Messenger, você pode usar o canal do Facebook ou o adaptador do Facebook.
Os adaptadores do Facebook estão disponíveis para bots do [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) e [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter).

Neste artigo, você aprenderá a conectar um bot ao Facebook usando o adaptador.  Este artigo explicará como modificar o exemplo EchoBot para conectá-lo a um aplicativo do Facebook.

As instruções a seguir abordam a implementação em C# do adaptador do Facebook. Para obter instruções sobre como usar o adaptador do JavaScript, parte das bibliotecas BotKit, [consulte a documentação do Facebook sobre BotKit](https://botkit.ai/docs/v4/platforms/facebook.html).

### <a name="prerequisites"></a>Pré-requisitos

- O [código de exemplo do EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)
- Uma conta do Facebook para Desenvolvedores. Se você não tiver uma conta, poderá [criar uma aqui](https://developers.facebook.com).

### <a name="create-a-facebook-app-page-and-gather-credentials"></a>Criar um aplicativo e uma página do Facebook e coletar credenciais

1. Faça logon em [https://developers.facebook.com](https://developers.facebook.com). Clique em **Meus Aplicativos** no menu principal e clique em **Criar Aplicativo** no menu suspenso.

![Criar aplicativo](media/bot-service-channel-connect-facebook/create-app-button.png)

1. Na janela pop-up exibida, insira um nome para seu novo aplicativo e clique em **Criar ID do Aplicativo**.

![Definir nome do aplicativo](media/bot-service-channel-connect-facebook/app-name.png)

#### <a name="set-up-messenger-and-associate-a-facebook-page"></a>Configurar o Messenger e associar uma página do Facebook

1. Depois que seu aplicativo tiver sido criado, você verá uma lista de produtos disponíveis para configuração. Clique no botão **Configurar** ao lado do produto **Messenger**.

1. Agora você precisa associar seu novo aplicativo a uma página do Facebook (se você não tiver uma página existente que desejar usar, poderá criar uma clicando em **Criar Página** na seção **Tokens de Acesso**). Clique em **Adicionar ou Remover Páginas**, selecione a página que deseja associar ao seu aplicativo e clique em **Avançar**. Deixe a configuração **Gerenciar e acessar conversas de Página no Messenger** habilitada e clique em **Concluído**.

![Configurar o messenger](media/bot-service-channel-connect-facebook/app-page-permissions.png)

1. Depois de associar sua página, clique no botão **Gerar Token** para gerar um token de acesso de página.  Anote este token pois você precisará dele em uma etapa posterior ao configurar seu aplicativo de bot.

#### <a name="obtain-your-app-secret"></a>Obter seu segredo de aplicativo

1. No menu esquerdo, clique em **Configurações** e clique em **Básicas** para navegar até a página de configuração básica do seu aplicativo.

1. Na página de configurações básicas, clique no botão **Mostrar** ao lado do **Segredo do Aplicativo**.  Anote este segredo pois você precisará dele em uma etapa posterior ao configurar seu aplicativo de bot.

### <a name="wiring-up-the-facebook-adapter-in-your-bot"></a>Conectar o adaptador do Facebook em seu bot

Agora que você tem seu aplicativo, sua página e suas credenciais do Facebook, você precisa configurar seu aplicativo de bot.

#### <a name="install-the-facebook-adapter-nuget-package"></a>Instalar o pacote NuGet do adaptador do Facebook

Adicione o pacote NuGet [Microsoft.Bot.Builder.Adapters.Facebook](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Facebook/). Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs).

#### <a name="create-a-facebook-adapter-class"></a>Criar uma classe de adaptador do Facebook

Crie uma classe herdada da classe ***FacebookAdapter***. Essa classe atuará como nosso adaptador para o canal do Facebook e incluirá recursos de tratamento de erro (semelhante à classe ***BotFrameworkAdapterWithErrorHandler*** que já está no exemplo, usada para manipular outras solicitações do Serviço de Bot do Azure).

```csharp
public class FacebookAdapterWithErrorHandler : FacebookAdapter
{
    public FacebookAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
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

#### <a name="create-a-new-controller-for-handling-facebook-requests"></a>Criar um controlador para manipular as solicitações do Facebook

Criaremos um controlador que lidará com as solicitações do Facebook em um novo ponto de extremidade 'api/facebook', em vez do 'api/messages' padrão usado para solicitações de canais do Serviço de Bot do Azure.  Ao adicionar outro ponto de extremidade ao bot, você poderá aceitar solicitações de canais do Serviço de Bot, assim como do Facebook, usando o mesmo bot.

```csharp
[Route("api/facebook")]
[ApiController]
public class FacebookController : ControllerBase
{
    private readonly FacebookAdapter _adapter;
    private readonly IBot _bot;

    public FacebookController(FacebookAdapter adapter, IBot bot)
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

#### <a name="inject-the-facebook-adapter-in-your-bot-startupcs"></a>Injetar o adaptador do Facebook no startup.cs do bot

Adicione a seguinte linha ao método ***ConfigureServices*** no arquivo startup.cs. Isso registrará o adaptador do Facebook e o disponibilizará para a nova classe de controlador.  As definições de configuração que você adicionou na etapa anterior serão usadas automaticamente pelo adaptador.

```csharp
services.AddSingleton<FacebookAdapter, FacebookAdapterWithErrorHandler>();
```

Depois de ser adicionado, o método ***ConfigureServices*** ficará assim.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Facebook Adapter
    services.AddSingleton<FacebookAdapter, FacebookAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

#### <a name="obtain-a-url-for-your-bot"></a>Obter uma URL para o bot

Agora que você conectou o adaptador em seu projeto de bot, é necessário fornecer ao Facebook o ponto de extremidade correto para seu aplicativo para que seu bot receba mensagens. Você também precisa dessa URL para concluir a configuração do seu aplicativo de bot.

Para concluir esta etapa, [implante o bot no Azure](https://aka.ms/bot-builder-deploy-az-cli) e anote a URL do bot implantado.

> [!NOTE]
> Se você não estiver pronto para implantar o bot no Azure ou desejar depurar o bot ao usar o adaptador do Facebook, poderá usar uma ferramenta como a [ngrok](https://www.ngrok.com) (que você provavelmente já terá instalada se tiver usado o emulador do Bot Framework anteriormente) para fazer um túnel para o bot em execução local e fornecer uma URL acessível publicamente para isso.
>
> Se desejar criar um túnel ngrok e obter uma URL para o bot, use o comando a seguir em uma janela do terminal (isso pressupõe que o bot local esteja em execução na porta 3978; altere os números de porta no comando se o bot não estiver).
>
> ```cmd
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

#### <a name="add-facebook-app-settings-to-your-bots-configuration-file"></a>Adicionar configurações do aplicativo do Facebook ao arquivo de configuração do bot

Adicione as configurações mostradas abaixo ao seu arquivo appSettings.json em seu projeto de bot. Você popula **FacebookAppSecret** e **FacebookAccessToken** usando os valores coletados ao criar e configurar seu aplicativo do Facebook. **FacebookVerifyToken** deve ser uma cadeia de caracteres aleatória que você cria e será usada para verificar se o ponto de extremidade do bot é autêntico quando chamado pelo Facebook.

```json
  "FacebookVerifyToken": "",
  "FacebookAppSecret": "",
  "FacebookAccessToken": ""
```

Depois de popular as configurações acima, você deverá reimplantar (ou reiniciar, se estiver em execução localmente com o ngrok) seu bot.

### <a name="complete-configuration-of-your-facebook-app"></a>Concluir a configuração do aplicativo do Facebook

A etapa final é configurar o ponto de extremidade do Messenger do seu novo aplicativo do Facebook para verificar se o bot recebe mensagens.

1. No painel do seu aplicativo, clique em **Messenger** no menu esquerdo e, em seguida, em **Configurações**.

1. Na seção **Webhooks**, clique **Adicionar URL de Retorno de Chamada**.

1. Na caixa de texto **URL de Retorno de Chamada**, insira a URL do seu bot, além do ponto de extremidade `api/facebook` especificado em seu controlador recém-criado. Por exemplo, `https://yourboturl.com/api/facebook`. Na caixa de texto **Token de Verificação**, insira o token de verificação criado anteriormente e usado no arquivo appSettings.json do aplicativo de bot.

    ![Editar a url de retorno de chamada](media/bot-service-channel-connect-facebook/edit-callback-url.png)

1. Clique em **Verificar e Salvar**. Verifique se o bot está em execução, uma vez que o Facebook fará uma solicitação para o ponto de extremidade do seu aplicativo e verifique-o usando seu **Token de Verificação**.

1. Depois que a URL de retorno de chamada tiver sido verificada, clique no botão **Adicionar Assinaturas** que é mostrado agora.  Na janela pop-up, selecione as assinaturas a seguir e clique em **Salvar**.

    - **messages**
    - **messaging_postbacks**
    - **messaging_optins**
    - **messaging_deliveries**

    ![Assinaturas de Webhook](media/bot-service-channel-connect-facebook/webhook-subscriptions.png)

### <a name="test-your-bot-with-adapter-in-facebook"></a>Testar o bot com o adaptador no Facebook

Agora você pode testar se seu bot está conectado ao Facebook corretamente enviando uma mensagem pela Página do Facebook que você associou ao seu novo aplicativo do Facebook.  

1. Navegue até a Página do Facebook.

1. Clique no botão **Adicionar um Botão**.

    ![Adicionar um botão](media/bot-service-channel-connect-facebook/add-button.png)

1. Selecione **Entrar em contato com você** e **Enviar Mensagem** e clique em **Avançar**.

    ![Adicionar um botão](media/bot-service-channel-connect-facebook/button-settings.png)

1. Quando perguntado **Para onde você gostaria que este botão enviasse as pessoas?** , selecione **Messenger** e clique em **Concluir**.

    ![Adicionar um botão](media/bot-service-channel-connect-facebook/button-settings-2.png)

1. Passe o mouse sobre o novo botão **Enviar Mensagem** mostrado agora em sua Página do Facebook e clique em **Botão de Teste** no menu pop-up.  Isso iniciará uma nova conversa com seu aplicativo por Facebook Messenger, que você pode usar para testar o envio de mensagens para o bot. Depois que a mensagem for recebida pelo bot, ele enviará uma mensagem de volta para você, ecoando o texto da sua mensagem.

Você também pode testar esse recurso usando o [exemplo de bot para o adaptador do Facebook](https://aka.ms/csharp-61-facebook-adapter-sample) populando o arquivo appSettings.json com os mesmos valores descritos nas etapas acima.

## <a name="see-also"></a>Confira também

- **Código de exemplo**. Use o bot de exemplo [Facebook-events](https://aka.ms/facebook-events) para explorar a comunicação do bot com o Facebook Messenger.
