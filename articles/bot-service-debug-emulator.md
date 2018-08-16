---
title: Teste e depure bots usando o Bot Framework Emulator | Microsoft Docs
description: Aprenda como inspecionar, testar e depurar bots usando o aplicativo de desktop do Bot Framework Emulator.
keywords: transcrição, ferramenta msbot, serviços de linguagem, reconhecimento de fala
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/30/2018
ms.openlocfilehash: bc1da99c7d0f7a6431ad0c2746b8583ef7bfbd5f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296999"
---
# <a name="debug-bots-with-the-bot-framework-emulator"></a>Depurar bots com o Emulador Bot Framework

O Bot Framework Emulator é um aplicativo de desktop que permite que os desenvolvedores de bot testem e depurem seus bots, seja local ou remotamente. Usando o emulador, você pode conversar com seu bot e inspecionar as mensagens que seu bot envia e recebe. O emulador exibe mensagens como elas apareceriam em uma interface de usuário do bate-papo na Web e registra solicitações e respostas JSON à medida que você troca mensagens com seu bot. 

> [!TIP] 
> Antes de implantar seu bot na nuvem, execute-o localmente e teste-o usando o emulador. Você pode testar seu bot usando o emulador mesmo se você ainda não [registrou](~/bot-service-quickstart-registration.md) com o Bot Framework ou o configurou para rodar em qualquer canal.

![Interface do usuário do emulador](media/emulator-v4/emulator-welcome.png)

## <a name="download-the-bot-framework-emulator"></a>Baixe o emulador do Bot Framework

Baixar pacotes para Mac, Windows e Linux estão disponíveis por meio de [página de versões do GitHub](https://github.com/Microsoft/BotFramework-Emulator/releases).

## <a name="connect-to-a-bot-running-on-local-host"></a>Conectar-se para um bot em execução no host local

![Pontos de extremidade do emulador](media/emulator-v4/emulator-endpoint.png)

Para se conectar a um bot em execução localmente, selecione a guia de Gerenciador de Bot no canto superior esquerdo. Clique no ícone **+** na guia **Endpoint**. Aqui você pode especificar seu endpoint para a mesma porta em que seu bot está rodando localmente para se conectar a ele. Clique em **Enviar** e você será redirecionado para uma janela de bate-papo ao vivo, onde poderá interagir com seu bot.

## <a name="view-detailed-message-activity-with-the-inspector"></a>Exibir a atividade de mensagem detalhada com o Inspetor de

![Atividade de mensagem do emulador](media/emulator-v4/emulator-view-message-activity-02.png)

Você pode clicar em qualquer bolhas de mensagem dentro da janela de conversa e inspecionar a atividade JSON bruto usando o **INSPETOR** recurso à direita da janela. Quando selecionada, o balão de mensagens ficará amarelo e o objeto JSON de atividade será exibido à esquerda da janela de bate-papo. Essas informações de JSON incluem metadados principais, incluindo channelID, tipo de atividade, id de conversa, mensagem de texto, URL do terminal, etc. Você pode ver as atividades de inspeção enviadas pelo usuário, bem como as atividades com as quais o bot responde. 

Você também pode usar o [Inspetor de Canais](bot-service-channel-inspector.md) para visualizar os recursos suportados em canais específicos.


## <a name="save-and-load-conversations-with-bot-transcripts"></a>Salvar e carregar conversas com transcrições de bot

A atividade da mensagem no emulador pode ser salva como transcrições. Em uma janela de bate-papo ao vivo aberta, você pode selecionar **Salvar transcrição como** para nomear e selecionar o local do arquivo de saída de transcrição. 

>[!TIP]
> O botão **Iniciar novamente** pode ser usado a qualquer momento para limpar uma conversa e reiniciar uma conexão com o bot.  

![Emulador salvar transcrições](media/emulator-v4/emulator-live-chat.png)

Para carregar transcrições, basta selecionar **Arquivo** --> **Abra o arquivo do Tranascript** e selecione a transcrição. Uma nova janela Transcrição será aberta e renderizará a atividade da mensagem na janela de saída. 

![Transcrições de carga do emulador](media/emulator-v4/emulator-load-transcript.png)

## <a name="author-transcripts-with-chatdown"></a>Transcrições do autor com o Chatdown

A ferramenta [Chatdown](https://github.com/Microsoft/botbuilder-tools/tree/master/Chatdown) é um gerador de transcrição que consome um arquivo [markdown](https://daringfireball.net/projects/markdown/syntax) para gerar transcrições de atividades. Você pode criar suas próprias transcrições completamente no formato de marcação e salvá-las como um arquivo **.chat** para gerar transcrições. Isso é útil para rapidamente criar cenários de conversa fictícios durante o desenvolvimento de bot.  

### <a name="prerequisites"></a>Pré-requisitos

- [Bot Framework emulador](https://github.com/Microsoft/BotFramework-Emulator/releases) v. 4 ou superior 
- [Node.js](https://nodejs.org/en/)
 
O Chatdown está disponível como um módulo npm que requer o Node.js. Para instalar o Chatdown, instale-o globalmente em sua máquina. 

```
npm install -g chatdown
```
### <a name="create-and-load-transcript-transcript-files"></a>Criar e carregar arquivos transcritos de transcrição ###

A seguir, um exemplo de como criar um arquivo **.chat**. Esses arquivos são markdown que contêm 2 partes:
- Cabeçalho que define os participantes da conversa (usuário, bot)
- A conversa de ida e volta entre os participantes

```
user=John Doe
bot=Bot

bot: Hello!
user: hey
bot: [Typing][Delay=3000]
What can I do for you?
user: Actually nevermind, goodbye.
bot: bye!
```
[Clique aqui](https://github.com/Microsoft/botbuilder-tools/tree/master/Chatdown/Examples) para exibir mais exemplos de arquivos .chat. 

Para gerar o arquivo de transcrição, usando o comando **chatdown** em sua CLI, insira o nome do arquivo .chat, seguido por '>' e o nome do arquivo de transcrição de saída. 

```
chatdown sample.chat > sample.transcript
```
## <a name="manage-bot-resources-with-the-msbot-tool"></a>Gerenciar recursos de bot com a ferramenta MSBot

A nova ferramenta [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/MSBot) permite que você crie um arquivo **.bot**, que armazena metadados sobre diferentes serviços que seu bot consome, tudo em um único local. Esse arquivo também permite que seu bot se conecte a esses serviços a partir da CLI.A ferramenta está disponível como um módulo npm, para instalá-lo, execute:

```
npm install -g msbot 
```
![Janela MSBot CLI](media/emulator-v4/msbot-cli-window.png)


Para criar um arquivo bot, na sua CLI, insira **msbot init** seguido pelo nome do seu bot e pelo endpoint da URL de destino, por exemplo:

```shell
msbot init --name az-cli-bot --endpoint http://localhost:3984/api/messages
```
![Botfile](media/emulator-v4/botfile-generated.png)

>**Observação:** o bot usado para este guia é um bot de eco simples, gerado da extensão de bot da CLI do Azure. [Clique aqui](https://github.com/Microsoft/botbuilder-tools/tree/master/AzureCli) para saber mais sobre a criação de bots com CLI do Azure. 

Com o arquivo .bot, agora você pode facilmente carregar seu bot no emulador. O arquivo .bot também é necessário para registrar pontos de extremidade diferentes e componentes de linguagem ao seu bot. 

![Bot-arquivo-lista suspensa](media/emulator-v4/bot-file-dropdown.png)

## <a name="add-language-services"></a>Adicionar serviços de linguagem 

Você pode facilmente registrar um aplicativo LUIS ou uma base de conhecimento QnA em seu arquivo .bot diretamente do emulador. Quando o arquivo .bot é carregado, selecione o botão de serviços na extremidade esquerda da janela do emulador. Você verá opções no menu **Serviços** para adicionar o LUIS, o Criador de QnA, o Dispatch, os pontos de extremidade e o Serviço de Bot do Azure. 

Para adicionar um aplicativo LUIS, basta clicar no botão **+** no menu LUIS, inserir suas credenciais do aplicativo LUIS e clicar em **Enviar**. Isso registra o aplicativo LUIS para o arquivo .bot e se conectar ao serviço ao seu aplicativo bot. 

![Conectar-se do LUIS](media/emulator-v4/emulator-connect-luis-btn.png)

Da mesma forma, para adicionar uma base de conhecimento QnA, basta clicar no botão **+** no menu QnA, inserir suas credenciais da base de conhecimento do QnA Maker e clicar em **Enviar**. Sua base de conhecimento será registrada no arquivo .bot e pronta para uso. 

![Conectar-se do QnA](media/emulator-v4/emulator-connect-qna-btn.png)

Quando um dos serviços está conectado, você pode voltar a uma janela de bate-papo ao vivo e verificar se seus serviços estão conectados e funcionando. 

![QnA conectado](media/emulator-v4/emulator-view-message-activity.png)

## <a name="inspect-language-services"></a>Inspecione os serviços de linguagem

Com o novo emulador v4, você também pode inspecionar as respostas JSON de LUIS e QnA. Usando um bot com um serviço de idiomas conectado, você pode selecionar **trace** na janela LOG no canto inferior direito. Esta nova ferramenta também fornece recursos para atualizar seus serviços de idioma diretamente do emulador. 

![Inspetor LUIS](media/emulator-v4/emulator-luis-inspector.png)

Com um serviço LUIS conectado, você notará que o link de rastreamento especifica **Luis Trace**. Quando selecionado, você verá a resposta bruta de seu serviço LUIS, que inclui intenções, entidades e suas pontuações especificadas. Você também tem a opção de reatribuir intenções para suas declarações de usuário. 

![Inspetor do QnA](media/emulator-v4/emulator-qna-inspector.png)

Com um serviço QnA conectado, o log exibirá **QnA Trace** e, quando selecionado, você poderá visualizar o par de perguntas e respostas associado a essa atividade, juntamente com uma pontuação de confiança. A partir daqui, você pode adicionar frases de pergunta alternativo para uma resposta.

[!TIP]
> Esses recursos estão disponíveis somente para bots SDK v4 


## <a name="speech-recognition"></a>Reconhecimento de fala
O Bot Framework Emulator suporta reconhecimento de fala através da [API de Fala dos Serviços Cognitivos](/azure/cognitive-services/Speech/home). Isso permite que você exercite seu bot habilitado para fala, ou habilidade Cortana, por meio da fala no emulador durante o desenvolvimento. O Bot Framework Emulator fornece reconhecimento de fala gratuitamente por até três horas por bot por dia. 

## <a id="ngrok"></a> Instalar e configurar o ngrok

Se você estiver usando o Windows e estiver executando o Bot Framework Emulator atrás de um firewall ou outro limite de rede e quiser se conectar a um bot hospedado remotamente, deverá instalar e configurar o **software de encapsulamento ngrok**. O Bot Framework Emulator integra-se fortemente com o [software de tunelamento ngrok][ngrokDownload] (desenvolvido por [inconshreveable][inconshreveable]), e pode iniciá-lo automaticamente quando for necessário.

Para instalar **ngrok** no Windows e configure o emulador para usá-lo, conclua estas etapas: 

1. Baixe o [ngrok] [ ngrokDownload] executável em seu computador local.

2. Abrir caixa de diálogo de configurações do aplicativo do emulador, digite o caminho para ngrok, selecione se deseja ou não ignorar ngrok para endereços locais e, em seguida, clique em **salvar**.

![ngrok path](media/emulator-v4/emulator-ngrok-path.png)

## <a name="additional-resources"></a>Recursos adicionais

O Bot Framework Emulator é open source. Você pode [contribuir com][EmulatorGithubContribute] para o desenvolvimento e [enviar bugs e sugestões][EmulatorGithubBugs].


[EmulatorGithub]: https://github.com/Microsoft/BotFramework-Emulator
[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/
[BotFrameworkDevPortal]: https://dev.botframework.com/


[EmulatorConnectPicture]: ~/media/emulator/emulator-connect_localhost_credentials.png
[EmulatorNgrokPath]: ~/media/emulator/emulator-configure_ngrok_path.png
[EmulatorNgrokMonitor]: ~/media/emulator/emulator-testbot-ngrok-monitoring.png
[EmulatorUI]: ~/media/emulator/emulator-ui-new.png

[TroubleshootingGuide]: ~/bot-service-troubleshoot-general-problems.md
[TroubleshootingAuth]: ~/bot-service-troubleshoot-authentication-problems.md
[NodeGetStarted]: ~/nodejs/bot-builder-nodejs-quickstart.md
[CSGetStarted]: ~/dotnet/bot-builder-dotnet-quickstart.md
