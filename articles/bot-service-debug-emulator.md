---
title: Teste e depure bots usando o Bot Framework Emulator | Microsoft Docs
description: Aprenda como inspecionar, testar e depurar bots usando o aplicativo de desktop do Bot Framework Emulator.
keywords: transcrição, ferramenta msbot, serviços de linguagem, reconhecimento de fala
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/26/2019
ms.openlocfilehash: 307a6bf697e274391336a0d216c64da85232616d
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65033306"
---
# <a name="debug-with-the-emulator"></a>Depurar com o emulador

O Bot Framework Emulator é um aplicativo de desktop que permite que os desenvolvedores de bot testem e depurem seus bots, seja local ou remotamente. Usando o emulador, você pode conversar com seu bot e inspecionar as mensagens que seu bot envia e recebe. O emulador exibe mensagens como elas apareceriam em uma interface de usuário do bate-papo na Web e registra solicitações e respostas JSON à medida que você troca mensagens com seu bot. Antes de implantar seu bot na nuvem, execute-o localmente e teste-o usando o emulador. Você pode testar seu bot usando o emulador mesmo se você ainda não o [criou](./bot-service-quickstart.md) com o Serviço de Bot do Azure ou o configurou para rodar em qualquer canal.

## <a name="prerequisites"></a>Pré-requisitos
- Instalar [Emulador](https://aka.ms/Emulator-wiki-getting-started)

## <a name="connect-to-a-bot-running-on-localhost"></a>Conectar-se a um bot em execução no localhost

![Interface do usuário do emulador](media/emulator-v4/emulator-welcome.png)

Para conectar a um bot em execução localmente, clique em **Abrir bot** ou selecione o arquivo de configuração pré-configurado (um arquivo .bot). Você não precisa de um arquivo de configuração para se conectar ao bot, mas o emulador ainda funciona com um, se o seu bot tiver. Se o bot é executado com credenciais da Conta Microsoft (MSA), insira essas credenciais também.

![Interface do usuário do emulador](media/emulator-v4/emulator-open-bot.png)

## <a name="view-detailed-message-activity-with-the-inspector"></a>Exibir a atividade de mensagem detalhada com o Inspetor de

Envie uma mensagem ao seu bot e o bot deve responder de volta. Você pode clicar na bolha de mensagem dentro da janela de conversa e inspecionar a atividade JSON bruta usando o recurso **INSPETOR** à direita da janela. Quando selecionada, o balão de mensagens ficará amarelo e o objeto JSON de atividade será exibido à esquerda da janela de bate-papo. Essas informações de JSON incluem metadados principais, incluindo channelID, tipo de atividade, id de conversa, mensagem de texto, URL do ponto de extremidade, etc. Você pode inspecionar as atividades de inspeção enviadas pelo usuário, bem como as atividades com as quais o bot responde. 

![Atividade de mensagem do emulador](media/emulator-v4/emulator-view-message-activity-03.png)

## <a name="save-and-load-conversations-with-bot-transcripts"></a>Salvar e carregar conversas com transcrições de bot

As atividades no emulador podem ser salvas como transcrições. Em uma janela de chat ao vivo aberta, selecione **Salvar transcrição como** para o arquivo de transcrição. O botão **Iniciar novamente** pode ser usado a qualquer momento para limpar uma conversa e reiniciar uma conexão com o bot.  

![Emulador salvar transcrições](media/emulator-v4/emulator-save-transcript.png)

Para carregar transcrições, basta selecionar **Arquivo > Abrir arquivo de transcrição** e selecionar a transcrição. Uma nova janela Transcrição será aberta e renderizará a atividade da mensagem na janela de saída. 

![Transcrições de carga do emulador](media/emulator-v4/emulator-load-transcript.png)

## <a name="add-services"></a>Adicionar serviços 

Você pode facilmente adicionar um aplicativo LUIS, uma base de conhecimento QnA ou um modelo de expedição ao seu bot, diretamente do emulador. Quando o bot for carregado, selecione o botão de serviços, na extremidade esquerda da janela do emulador. Você verá opções no menu **Serviços** para adicionar o LUIS, o QnA Maker e a Expedição. 

Para adicionar um aplicativo de serviço, basta clicar no botão **+** e selecionar o serviço que você deseja adicionar. Você será solicitado a entrar no portal do Azure para adicionar o serviço para o arquivo de bot e conectar o serviço ao seu aplicativo de bot. 

> [!IMPORTANT]
> A adição de serviços só funcionará se você estiver usando um arquivo de configuração `.bot`. O serviços precisarão ser adicionados independentemente. Para obter detalhes, confira [Gerenciar recursos do bot](v4sdk/bot-file-basics.md) ou os artigos de instruções individuais do serviço que está tentando adicionar.
>
> Se você não estiver usando um arquivo `.bot`, o painel à esquerda não terá seus serviços listados (mesmo que seu bot use serviços) e exibirá *Serviços não disponíveis*.

![Conectar-se do LUIS](media/emulator-v4/emulator-connect-luis-btn.png)

Quando um dos serviços está conectado, você pode voltar a uma janela de bate-papo ao vivo e verificar se seus serviços estão conectados e funcionando. 

![QnA conectado](media/emulator-v4/emulator-view-message-activity.png)

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

## <a name="login-to-azure"></a>Logon no Azure

Você pode usar o Emulador para fazer logon em sua conta do Azure. Isso é particularmente útil para adicionar e gerenciar os serviços que dependem do seu bot. Confira [acima](#add-services) para saber mais sobre os serviços que você pode gerenciar usando o Emulador.

### <a name="to-login"></a>Para fazer logon

![Logon do Azure](media/emulator-v4/emulator-azure-login.png)

Para fazer logon
- Você pode clicar em Arquivo -> Entrar com o Azure.
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

O Bot Framework Emulator é open source. Você pode [contribuir com][EmulatorGithubContribute] para o desenvolvimento e [enviar bugs e sugestões][EmulatorGithubBugs].

Para solucionar problemas, confira [solucionar problemas gerais](bot-service-troubleshoot-bot-configuration.md) e outros artigos de solução de problemas nesta seção.

## <a name="next-steps"></a>Próximas etapas

Salvar uma conversa em um arquivo de transcrição permite redigir e repetir rapidamente um determinado conjunto de interações para depuração.

> [!div class="nextstepaction"]
> [Depurar o bot usando arquivos de transcrição](~/v4sdk/bot-builder-debug-transcript.md)

<!-- Footnote-style URLs -->

[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/
