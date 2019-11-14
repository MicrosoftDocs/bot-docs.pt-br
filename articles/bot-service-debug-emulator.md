---
title: Teste e depure bots usando o Bot Framework Emulator | Microsoft Docs
description: Aprenda como inspecionar, testar e depurar bots usando o aplicativo de desktop do Bot Framework Emulator.
keywords: transcrição, ferramenta msbot, serviços de linguagem, reconhecimento de fala
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/26/2019
ms.openlocfilehash: de15fbc7fb000bde3b62883405149d22f3ac23a0
ms.sourcegitcommit: 312a4593177840433dfee405335100ce59aac347
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933642"
---
# <a name="debug-with-the-emulator"></a>Depurar com o emulador

O Bot Framework Emulator é um aplicativo de desktop que permite que os desenvolvedores de bot testem e depurem seus bots, seja local ou remotamente. Usando o emulador, você pode conversar com seu bot e inspecionar as mensagens que seu bot envia e recebe. O emulador exibe mensagens como elas apareceriam em uma interface de usuário do bate-papo na Web e registra solicitações e respostas JSON à medida que você troca mensagens com seu bot. Antes de implantar seu bot na nuvem, execute-o localmente e teste-o usando o emulador. Você pode testar seu bot usando o emulador mesmo se você ainda não o [criou](./bot-service-quickstart.md) com o Serviço de Bot do Azure ou o configurou para rodar em qualquer canal.

## <a name="prerequisites"></a>Pré-requisitos
- Instalar o [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started)

## <a name="run-a-bot-locally"></a>Executar um bot localmente
Antes de conectar o bot ao Bot Framework Emulator, você precisa executar o bot localmente. Você pode usar o Visual Studio, o Visual Studio Code ou a linha de comando para executar o bot. Para executar um bot usando a linha de comando, faça o seguinte:


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

* Vá para o prompt de comando e altere o diretório para o diretório de projeto do bot.
* Inicie o bot executando o seguinte comando: 
    ```
    dotnet run
    ```
* Copie o número da porta na linha antes de *Aplicativo iniciado. Pressione Ctrl + C para desligar.*

    ![Número da porta do C#](media/bot-service-debug-emulator/csharp_port_number.png)


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

* Vá para o prompt de comando e altere o diretório para o diretório de projeto do bot.
* Inicie o bot executando o seguinte comando:
    ```
    node index.js
    ```
* Copie o número da porta em que o restify está escutando.

    ![Número da porta do JS](media/bot-service-debug-emulator/js_port_number.png)
---

Neste ponto, seu bot está em execução localmente. 


## <a name="connect-to-a-bot-running-on-localhost"></a>Conectar-se a um bot em execução no localhost

<!-- auth config steps -->
### <a name="configure-the-emulator-for-authentication"></a>Configurar o emulador para autenticação

Se um bot exigir autenticação, exibindo uma caixa de diálogo de logon, você deverá configurar o emulador, conforme mostrado abaixo.

#### <a name="using-sign-in-verification-code"></a>Como usar o código de verificação de entrada

1. Inicie o emulador.
1. No emulador, clique no ícone de engrenagem na parte inferior esquerda ou na guia **Configurações do Emulador** no canto superior direito.
1. Marque a caixa **Usar um código de verificação de entrada para OAuthCards**.
1. Marque a caixa **Ignorar ngrok para endereço local**
1. Clique no botão **Salvar**.

Ao clicar no botão de logon exibido pelo bot, um código de validação será gerado.
Insira o código na caixa chat de entrada do bot para que a autenticação ocorra.
Depois disso, será possível executar as operações permitidas.

Como alternativa é possível executar as etapas descritas abaixo.

#### <a name="using-authentication-tokens"></a>Como usar os tokens de autenticação

1. Inicie o emulador.
1. No emulador, clique no ícone de engrenagem na parte inferior esquerda ou na guia **Configurações do Emulador** no canto superior direito.
1. Marque a caixa **Usar tokens de autenticação da versão 1.0**.
1. Insira o caminho local para a ferramenta **ngrok**. Para obter mais informações sobre a ferramenta, consulte [ngrok](https://ngrok.com/).
1. Marque a caixa **Executar ngrok quando o emulador for iniciado**.
1. Clique no botão **Salvar**.

Ao clicar no botão de logon exibido pelo bot, você receberá uma solicitação para inserir suas credenciais. Um token de autenticação será gerado. Depois disso, será possível executar as operações permitidas.


![Interface do usuário do emulador](media/emulator-v4/emulator-welcome.png)

Para se conectar a um bot em execução localmente, clique em **Abrir bot**. Adicione o número da porta que você copiou anteriormente na URL a seguir e cole a URL atualizada na barra URL do Bot:

*http://localhost:**número da porta**/api/messages*

![Interface do usuário do emulador](media/bot-service-debug-emulator/open_bot_emulator.png)

Se o bot é executado com [credenciais da MSA (Conta Microsoft)](#use-bot-credentials), insira-as também.

### <a name="use-bot-credentials"></a>Usar credenciais do bot

Quando você abre o bot, defina a **ID do aplicativo Microsoft** e a **senha do aplicativo Microsoft** se seu bot está em execução com as credenciais. Se você criou seu bot com o Serviço de Bot do Azure, as credenciais estão disponíveis no Serviço de Aplicativo do bot, na seção **Configurações -> Configuração**. Se não souber os valores, você poderá remover aqueles do arquivo de configuração do bot sendo executado localmente e depois executar o bot no emulador. Se o bot não estiver em execução com essas configurações, você também não precisará executar o emulador com as configurações. 

## <a name="view-detailed-message-activity-with-the-inspector"></a>Exibir a atividade de mensagem detalhada com o Inspetor de

Envie uma mensagem ao bot, que deve responder de volta. Você pode clicar na bolha de mensagem dentro da janela de conversa e inspecionar a atividade JSON bruta usando o recurso **INSPETOR** à direita da janela. Quando selecionada, o balão de mensagens ficará amarelo e o objeto JSON de atividade será exibido à esquerda da janela de bate-papo. As informações de JSON incluem metadados principais, incluindo ID do canal, tipo de atividade, ID da conversa, mensagem de texto, URL de ponto de extremidade, etc. Você pode inspecionar as atividades de inspeção enviadas pelo usuário, bem como as atividades com as quais o bot responde.

![Atividade de mensagem do emulador](media/emulator-v4/emulator-view-message-activity-03.png)

> [!TIP]
> Você pode depurar alterações de estado em um bot conectado a um canal adicionando [middleware de inspeção](bot-service-debug-inspection-middleware.md) ao bot.

<!--
## Save and load conversations with bot transcripts

Activities in the emulator can be saved as transcripts. From an open live chat window, select **Save Transcript As** to the transcript file. The **Start Over** button can be used any time to clear a conversation and restart a connection to the bot.  

![Emulator save transcripts](media/emulator-v4/emulator-save-transcript.png)

To load transcripts, simply select **File > Open Transcript File** and select the transcript. A new Transcript window will open and render the message activity to the output window. 

![Emulator load transcripts](media/emulator-v4/emulator-load-transcript.png)
--->
<!---
## Add services 

You can easily add a LUIS app, QnA knowledge base, or dispatch model to your bot directly from the emulator. When the bot is loaded, select the services button on the far left of the emulator window. You will see options under the **Services** menu to add LUIS, QnA Maker, and Dispatch. 

To add a service app, simply click on the **+** button and select the service you want to add. You will be prompted to sign in to the Azure portal to add the service to the bot file, and connect the service to your bot application. 

> [!IMPORTANT]
> Adding services only works if you're using a `.bot` configuration file. Services will need to be added independently. For details on that, see [Manage bot resources](v4sdk/bot-file-basics.md) or the individual how to articles for the service you're trying to add.
>
> If you are not using a `.bot` file, the left pane won't have your services listed (even if your bot uses services) and will display *Services not available*.

![LUIS connect](media/emulator-v4/emulator-connect-luis-btn.png)

When either service is connected, you can go back to a live chat window and verify that your services are connected and working. 

![QnA connected](media/emulator-v4/emulator-view-message-activity.png)

--->

## <a name="inspect-services"></a>Inspecionar serviços

Com o novo emulador v4, você também pode inspecionar as respostas JSON de LUIS e QnA. Usando um bot com um serviço de idiomas conectado, você pode selecionar **trace** na janela LOG no canto inferior direito. Esta nova ferramenta também fornece recursos para atualizar seus serviços de idioma diretamente do emulador. 

![Inspetor LUIS](media/emulator-v4/emulator-luis-inspector.png)

Com um serviço LUIS conectado, você notará que o link de rastreamento especifica **Luis Trace**. Quando selecionado, você verá a resposta bruta de seu serviço LUIS, que inclui intenções, entidades e suas pontuações especificadas. Você também tem a opção de reatribuir intenções para suas declarações de usuário. 

![Inspetor do QnA](media/emulator-v4/emulator-qna-inspector.png)

Com um serviço QnA conectado, o log exibirá **QnA Trace** e, quando selecionado, você poderá visualizar o par de perguntas e respostas associado a essa atividade, juntamente com uma pontuação de confiança. A partir daqui, você pode adicionar frases de pergunta alternativo para uma resposta.

<!--## Configure ngrok

If you are using Windows and you are running the Bot Framework Emulator behind a firewall or other network boundary and want to connect to a bot that is hosted remotely, you must install and configure **ngrok** tunneling software. The Bot Framework Emulator integrates tightly with ngrok tunnelling software (developed by [inconshreveable][inconshreveable]), and can launch it automatically when it is needed.

Open the **Emulator Settings**, enter the path to ngrok, select whether or not to bypass ngrok for local addresses, and click **Save**.

![ngrok path](media/emulator-v4/emulator-ngrok-path.png)
-->

<!---## Login to Azure

You can use Emulator to login in to your Azure account. This is particularly helpful for you to add and manage services your bot depends on. 
See [above](#add-services) to learn more about services you can manage using the Emulator.
-->

### <a name="login-to-azure"></a>Logon no Azure
Você pode usar o Emulador para fazer logon em sua conta do Azure. Isso é particularmente útil para adicionar e gerenciar os serviços que dependem do seu bot. Faça logon no Azure seguindo estas etapas:
- Clique em Arquivo -> Entrar com o Azure ![Logon do Azure](media/emulator-v4/emulator-azure-login.png)
- Na tela de boas-vindas, clique em Entrar com sua conta do Azure. Opcionalmente, o Emulador pode manter você conectado entre as reinicializações dele.
![Logon do Azure](media/emulator-v4/emulator-azure-login-success.png)

## <a name="disabling-data-collection"></a>Desabilitar a coleta de dados

Se decidir que não deseja permitir que o Emulador colete dados de uso, você pode facilmente desabilitar a coleta de dados, seguindo estas etapas:

1. Navegue até a página de configurações do Emulador, clicando no botão de Configurações (ícone de engrenagem), na barra de navegação à esquerda.

    ![desabilitar coleta de dados](media/emulator-v4/emulator-disable-data-1.png)

2. Desmarque a caixa de seleção *Ajude-nos a melhorar o Emulador, permitindo-nos coletar dados de uso*, na seção **Coleta de dados**.

    ![desabilitar coleta de dados](media/emulator-v4/emulator-disable-data-2.png)

3. Clique no botão "Salvar".

    ![desabilitar coleta de dados](media/emulator-v4/emulator-disable-data-3.png)
    
Se você mudar de ideias, poderá habilitá-lo novamente marcando a caixa de seleção.

## <a name="additional-resources"></a>Recursos adicionais

O Bot Framework Emulator é open source. Você pode [contribuir][EmulatorGithubContribute] com o desenvolvimento e [enviar bugs e sugestões][EmulatorGithubBugs].

Para solucionar problemas, confira [solucionar problemas gerais](bot-service-troubleshoot-bot-configuration.md) e outros artigos de solução de problemas nesta seção.

## <a name="next-steps"></a>Próximas etapas

Use o middleware de inspeção para depurar um bot conectado a um canal.

> [!div class="nextstepaction"]
> [Depurar o bot usando arquivos de transcrição](bot-service-debug-inspection-middleware.md)

<!--
Saving a conversation to a transcript file allows you to quickly draft and replay a certain set of interactions for debugging.

> [!div class="nextstepaction"]
> [Debug your bot using transcript files](~/v4sdk/bot-builder-debug-transcript.md)
-->

<!-- Footnote-style URLs -->

[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/
