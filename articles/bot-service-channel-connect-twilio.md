---
title: Conectar um bot ao Twilio – Serviço de Bot
description: Saiba como configurar os bots para usar o twilio para se comunicar com as pessoas. Consulte Como conectar bots ao twilio por meio de um adaptador twilio ou um aplicativo TwiML.
keywords: Twilio, canais de bot, SMS, Aplicativo, telefone, configurar o Twilio, comunicação em nuvem, texto
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/9/2018
ms.openlocfilehash: cfbabb0d2497d2712cf5a6d19c31082311bfd1b8
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456930"
---
# <a name="connect-a-bot-to-twilio"></a>Conectar um bot ao Twilio

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Configure seu bot para se comunicar com as pessoas que usam a plataforma de comunicação em nuvem Twilio.

## <a name="log-in-to-or-create-a-twilio-account-for-sending-and-receiving-sms-messages"></a>Faça logon ou crie uma conta do Twilio para enviar e receber mensagens SMS

Caso não tenha uma conta do Twilio, <a href="https://www.twilio.com/try-twilio" target="_blank">crie uma conta</a>.

## <a name="create-a-twiml-application"></a>Criar um aplicativo TwiML

<a href="https://support.twilio.com/hc/articles/223180928-How-Do-I-Create-a-TwiML-App-" target="_blank">Crie um aplicativo TwiML</a> seguindo as instruções.

![Criar aplicativo](~/media/channels/twi-StepTwiml.png)

Em **Propriedades**, insira um **NOME AMIGÁVEL**. Neste tutorial, usamos "Meu aplicativo TwiML" como exemplo. A **URL DE SOLICITAÇÃO** em Voz pode ser deixada em branco. Em **Mensagens**, a **URL de Solicitação** deve ser `https://sms.botframework.com/api/sms`.

## <a name="select-or-add-a-phone-number"></a>Selecionar ou adicionar um número de telefone

Siga <a href = "https://support.twilio.com/hc/articles/223180048-Adding-a-Verified-Phone-Number-or-Caller-ID-with-Twilio" target="_blank">estas</a> instruções para adicionar uma ID de chamador verificada por meio do site do console. Depois de terminar, você verá seu número verificado em **Números Ativos** sob **Gerenciar Números**.

![Definir número de telefone](~/media/channels/twi-StepPhone.png)

## <a name="specify-application-to-use-for-voice-and-messaging"></a>Especificar o aplicativo a ser usado para Voz e Mensagens

Clique no número e vá até **Configurar**. Em Voz e Mensagens, defina **CONFIGURAR COM** como Aplicativo TwiML e defina **Aplicativo TWIML** como Meu aplicativo TwiML. Depois de concluir, clique em **Salvar**.

![Especificar o aplicativo](~/media/channels/twi-StepPhone2.png)

Volte para **Gerenciar Números**, você verá que a configuração de Voz e Mensagens foram alteradas para Aplicativo TwiML.

![Número especificado](~/media/channels/twi-StepPhone3.png)


## <a name="gather-credentials"></a>Obter credenciais

Volte para o [página inicial do console](https://www.twilio.com/console/), você verá o SID da sua Conta e o Token de Autenticação no painel do projeto, conforme mostrado abaixo.

![Coletar credenciais do aplicativo](~/media/channels/twi-StepAuth.png)

## <a name="submit-credentials"></a>Enviar credenciais

Em uma janela separada, volte para o site do Bot Framework em https://dev.botframework.com/. 

- Selecione **Meus bots** e escolha o Bot que você deseja conectar ao Twilio. Você será direcionado para o portal do Azure.
- Selecione **Canais** sob **Gerenciamento de Bot**. Clique no ícone de Twilio (SMS).
- Insira o Número de Telefone, o SID da Conta e o Token de Autenticação que você registrou anteriormente. Depois de concluir, clique em **Salvar**.

![Enviar credenciais](~/media/channels/twi-StepSubmit.png)

Depois que você concluir essas etapas, o bot será configurado com êxito para se comunicar com os usuários usando o Twilio.

## <a name="connect-a-bot-to-twilio-using-the-twilio-adapter"></a>Conectar um bot ao Twilio usando o adaptador do Twilio

Assim como o canal disponível no Serviço de Bot do Azure para conectar o bot ao Twilio, você também pode usar o adaptador do Twilio. Neste artigo, você aprenderá a conectar um bot ao Twilio usando o adaptador.  Este artigo explicará como modificar o exemplo EchoBot para conectá-lo a um aplicativo do Twilio.

> [!NOTE]
> As instruções a seguir abordam a implementação em C# do adaptador do Twilio. Para obter instruções sobre como usar o adaptador JS, parte das bibliotecas BotKit, [consulte a documentação do Twilio sobre BotKit](https://botkit.ai/docs/v4/platforms/twilio.html).

### <a name="prerequisites"></a>Pré-requisitos

* O [código de exemplo do EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Uma conta do Twilio. Se você não tiver uma conta do Twilio, poderá [criar uma aqui](https://www.twilio.com/try-twilio).

### <a name="get-a-twilio-number-and-gather-account-credentials"></a>Obter um número do Twilio e coletar as credenciais da conta

1. Faça logon no [Twilio](https://twilio.com/console). Do lado direito da página, você verá o **SID DA CONTA** e o **TOKEN DE AUTENTICAÇÃO** da sua conta. Anote-os, pois você precisará deles depois ao configurar o aplicativo de bot.

2. Escolha **Voz Programável** nas opções em **Introdução ao Twilio**.

![Introdução à Voz Programável](~/media/bot-service-channel-connect-twilio/get-started-voice.png)

3. Na próxima página, clique no botão **Obtenha seu primeiro número do Twilio**.  Uma janela pop-up mostrará um novo número, que você pode aceitar clicando em **Escolha este número** (ou você pode pesquisar um número diferente seguindo as instruções na tela).

4. Depois de ter escolhido seu número, anote-o, pois você precisará dele ao configurar seu aplicativo de bot em uma etapa posterior.

### <a name="wiring-up-the-twilio-adapter-in-your-bot"></a>Conectar o adaptador do Twilio em seu bot

Agora que você tem seu número e suas credenciais de conta do Twilio, é necessário configurar seu aplicativo de bot.

#### <a name="install-the-twilio-adapter-nuget-package"></a>Instalar o pacote NuGet do adaptador do Twilio

Adicione o pacote NuGet [Microsoft.Bot.Builder.Adapters.Twilio](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Twilio/). Para obter mais informações sobre como usar o NuGet, confira [Instalar e gerenciar pacotes no Visual Studio](https://aka.ms/install-manage-packages-vs).

#### <a name="create-a-twilio-adapter-class"></a>Criar uma classe de adaptador do Twilio

Crie uma nova classe que herde da classe **_TwilioAdapter_* _. Essa classe atuará como adaptador para o canal twilio e incluirá recursos de tratamento de erros (semelhante à classe _*_BotFrameworkAdapterWithErrorHandler_*_ já no exemplo, usada para lidar com outras solicitações do serviço de bot do Azure).

```csharp
public class TwilioAdapterWithErrorHandler : TwilioAdapter
{
    public TwilioAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
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

#### <a name="create-a-new-controller-for-handling-twilio-requests"></a>Criar um controlador para lidar com as solicitações do Twilio

Crie um controlador que lidará com as solicitações do Twilio em um novo ponto de extremidade 'api/twilio', em vez do 'api/messages' padrão usado para solicitações de canais do Serviço de Bot do Azure.  Ao adicionar outro ponto de extremidade ao bot, você poderá aceitar solicitações de canais do Serviço de Bot, assim como do Twilio, usando o mesmo bot.

```csharp
[Route("api/twilio")]
[ApiController]
public class TwilioController : ControllerBase
{
    private readonly TwilioAdapter _adapter;
    private readonly IBot _bot;

    public TwilioController(TwilioAdapter adapter, IBot bot)
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
        await _adapter.ProcessAsync(Request, Response, _bot, default);
    }
}
```

#### <a name="inject-the-twilio-adapter-in-your-bot-startupcs"></a>Injetar o adaptador do Twilio no startup.cs do seu bot

Adicione a seguinte linha ao método _*_configuraservices_*_ no arquivo Startup.cs. Isso registrará o adaptador do Twilio e o disponibilizará para sua nova classe de controlador.  As definições de configuração que você adicionou na etapa anterior serão usadas automaticamente pelo adaptador.

```csharp
services.AddSingleton<TwilioAdapter, TwilioAdapterWithErrorHandler>();
```

Depois de adicionado, _*_o método configureservices_*_ deve ser assim.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and Emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Twilio Adapter
    services.AddSingleton<TwilioAdapter, TwilioAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

#### <a name="obtain-a-url-for-your-bot"></a>Obter uma URL para o bot

Agora que você conectou o adaptador em seu projeto de bot, é necessário identificar o ponto de extremidade correto a ser fornecido ao Twilio a fim de verificar se seu bot recebe mensagens. Você também precisará dessa URL para concluir a configuração do seu aplicativo de bot.

Para concluir esta etapa, [implante o bot no Azure](https://aka.ms/bot-builder-deploy-az-cli) e anote a URL do bot implantado.

> [!NOTE]
> Se você não estiver pronto para implantar o bot no Azure ou desejar depurar o bot ao usar o adaptador twilio, poderá usar uma ferramenta como [ngrok](https://www.ngrok.com) (que provavelmente já terá instalado se você tiver usado o emulador do bot Framework anteriormente) para realizar o túnel para o bot em execução localmente e fornecer uma URL acessível publicamente para isso. 
> 
> Se desejar criar um túnel ngrok e obter uma URL para o bot, use o comando a seguir em uma janela do terminal (isso pressupõe que o bot local esteja em execução na porta 3978; altere os números de porta no comando se o bot não estiver).
> 
> ```
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

#### <a name="add-twilio-app-settings-to-your-bots-configuration-file"></a>Adicionar configurações do aplicativo do Twilio ao arquivo de configuração do bot

Adicione as configurações mostradas abaixo ao seu arquivo appSettings.json em seu projeto de bot. Você preenche _ * TwilioNumber * *, **TwilioAccountSid** e **TwilioAuthToken** usando os valores coletados ao criar o número de twilio. **TwilioValidationUrl** deve ser a URL do bot, além do ponto de extremidade `api/twilio` especificado no controlador recém-criado. Por exemplo, `https://yourboturl.com/api/twilio`.


```json
  "TwilioNumber": "",
  "TwilioAccountSid": "",
  "TwilioAuthToken": "",
  "TwilioValidationUrl", ""
```

Depois de popular as configurações acima, você deverá reimplantar (ou reiniciar, se estiver em execução localmente com o ngrok) seu bot.

### <a name="complete-configuration-of-your-twilio-number"></a>Concluir a configuração do número do Twilio

A etapa final é configurar o ponto de extremidade de mensagens do seu novo número do Twilio para verificar se o bot recebe mensagens.

1. Navegue até a [página Números Ativos](https://www.twilio.com/console/phone-numbers/incoming) do Twilio.

2. Clique no número de telefone criado na etapa anterior.

3. Na seção **Mensagens**, conclua a seção **UMA MENSAGEM CHEGA** escolhendo **Webhook** no menu suspenso e preenchendo a caixa de texto com o ponto de extremidade do bot usado para preencher a configuração **TwilioValidationUrl** na etapa anterior, como `https://yourboturl.com/api/twilio`.

![Configurar o webhook do número do Twilio](~/media/bot-service-channel-connect-twilio/twilio-number-messaging-settings.png)

4. Clique no botão **Salvar** .

### <a name="test-your-bot-with-adapter-in-twilio"></a>Testar o bot com o adaptador no Twilio

Agora você pode testar se o bot está conectado ao Twilio corretamente enviando uma mensagem SMS para o número do Twilio.  Depois que a mensagem for recebida pelo bot, ele enviará uma mensagem de volta para você, ecoando o texto da sua mensagem.

Você também pode testar esse recurso usando o [bot de exemplo para o adaptador do Twilio](https://aka.ms/csharp-63-twilio-adapter-sample) preenchendo o appSettings.json com os mesmos valores descritos nas etapas acima.
