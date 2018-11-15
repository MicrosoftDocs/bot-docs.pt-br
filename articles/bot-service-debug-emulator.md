---
title: Teste e depure bots usando o Bot Framework Emulator | Microsoft Docs
description: Aprenda como inspecionar, testar e depurar bots usando o aplicativo de desktop do Bot Framework Emulator.
keywords: transcrição, ferramenta msbot, serviços de linguagem, reconhecimento de fala
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/13/2018
ms.openlocfilehash: f3a6a57a5fd01061493e5c216875f0c4210483f6
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645606"
---
# <a name="debug-with-the-emulator"></a>Depurar com o emulador

O Bot Framework Emulator é um aplicativo de desktop que permite que os desenvolvedores de bot testem e depurem seus bots, seja local ou remotamente. Usando o emulador, você pode conversar com seu bot e inspecionar as mensagens que seu bot envia e recebe. O emulador exibe mensagens como elas apareceriam em uma interface de usuário do bate-papo na Web e registra solicitações e respostas JSON à medida que você troca mensagens com seu bot. Antes de implantar seu bot na nuvem, execute-o localmente e teste-o usando o emulador. Você pode testar seu bot usando o emulador mesmo se você ainda não o [criou](./bot-service-quickstart.md) com o Serviço de Bot do Azure ou o configurou para rodar em qualquer canal.

## <a name="prerequisites"></a>Pré-requisitos
- Instalar [Emulador](https://aka.ms/Emulator-wiki-getting-started)
- Instalar software de tunelamento [ngrok][ngrokDownload]

## <a name="connect-to-a-bot-running-on-localhost"></a>Conectar-se a um bot em execução no localhost

Para se conectar a um bot em execução localmente, clique em **Abrir bot** e selecione o arquivo .bot. 

![Interface do usuário do emulador](media/emulator-v4/emulator-welcome.png)

## <a name="view-detailed-message-activity-with-the-inspector"></a>Exibir a atividade de mensagem detalhada com o Inspetor de

Envie uma mensagem ao seu bot e o bot deve responder de volta. Você pode clicar na bolha de mensagem dentro da janela de conversa e inspecionar a atividade JSON bruta usando o recurso **INSPETOR** à direita da janela. Quando selecionada, o balão de mensagens ficará amarelo e o objeto JSON de atividade será exibido à esquerda da janela de bate-papo. Essas informações de JSON incluem metadados principais, incluindo channelID, tipo de atividade, id de conversa, mensagem de texto, URL do ponto de extremidade, etc. Você pode inspecionar as atividades de inspeção enviadas pelo usuário, bem como as atividades com as quais o bot responde. 

![Atividade de mensagem do emulador](media/emulator-v4/emulator-view-message-activity-02.png)

## <a name="save-and-load-conversations-with-bot-transcripts"></a>Salvar e carregar conversas com transcrições de bot

As atividades no emulador podem ser salvas como transcrições. Em uma janela de chat ao vivo aberta, selecione **Salvar transcrição como** para o arquivo de transcrição. O botão **Iniciar novamente** pode ser usado a qualquer momento para limpar uma conversa e reiniciar uma conexão com o bot.  

![Emulador salvar transcrições](media/emulator-v4/emulator-live-chat.png)

Para carregar transcrições, basta selecionar **Arquivo > Abrir arquivo de transcrição** e selecionar a transcrição. Uma nova janela Transcrição será aberta e renderizará a atividade da mensagem na janela de saída. 

![Transcrições de carga do emulador](media/emulator-v4/emulator-load-transcript.png)

## <a name="add-services"></a>Adicionar serviços 

Você pode facilmente adicionar um aplicativo LUIS, uma base de conhecimento QnA ou um modelo de expedição em seu arquivo .bot diretamente do emulador. Quando o arquivo .bot é carregado, selecione o botão de serviços na extremidade esquerda da janela do emulador. Você verá opções no menu **Serviços** para adicionar o LUIS, o QnA Maker e a Expedição. 

Para adicionar um aplicativo de serviço, basta clicar no botão **+** e selecionar o serviço que você deseja adicionar. Você será solicitado a entrar no portal do Azure para adicionar o serviço para o arquivo de bot e conectar o serviço ao seu aplicativo de bot. 

![Conectar-se do LUIS](media/emulator-v4/emulator-connect-luis-btn.png)

Quando um dos serviços está conectado, você pode voltar a uma janela de bate-papo ao vivo e verificar se seus serviços estão conectados e funcionando. 

![QnA conectado](media/emulator-v4/emulator-view-message-activity.png)

## <a name="inspect-services"></a>Inspecionar serviços

Com o novo emulador v4, você também pode inspecionar as respostas JSON de LUIS e QnA. Usando um bot com um serviço de idiomas conectado, você pode selecionar **trace** na janela LOG no canto inferior direito. Esta nova ferramenta também fornece recursos para atualizar seus serviços de idioma diretamente do emulador. 

![Inspetor LUIS](media/emulator-v4/emulator-luis-inspector.png)

Com um serviço LUIS conectado, você notará que o link de rastreamento especifica **Luis Trace**. Quando selecionado, você verá a resposta bruta de seu serviço LUIS, que inclui intenções, entidades e suas pontuações especificadas. Você também tem a opção de reatribuir intenções para suas declarações de usuário. 

![Inspetor do QnA](media/emulator-v4/emulator-qna-inspector.png)

Com um serviço QnA conectado, o log exibirá **QnA Trace** e, quando selecionado, você poderá visualizar o par de perguntas e respostas associado a essa atividade, juntamente com uma pontuação de confiança. A partir daqui, você pode adicionar frases de pergunta alternativo para uma resposta.

## <a name="configure-ngrok"></a>Configurar ngrok

Se você estiver usando o Windows e estiver executando o Bot Framework Emulator atrás de um firewall ou outro limite de rede e quiser se conectar a um bot hospedado remotamente, deverá instalar e configurar o **software de encapsulamento ngrok**. O Bot Framework Emulator integra-se perfeitamente com o software de tunelamento ngrok (desenvolvido por [inconshreveable][inconshreveable]), e pode iniciá-lo automaticamente quando for necessário.

Abra as **Configurações do emulador**, digite o caminho para ngrok, selecione se deseja ou não ignorar ngrok para endereços locais e, em seguida, clique em **Salvar**.

![ngrok path](media/emulator-v4/emulator-ngrok-path.png)

## <a name="additional-resources"></a>Recursos adicionais

O Bot Framework Emulator é open source. Você pode [contribuir com][EmulatorGithubContribute] para o desenvolvimento e [enviar bugs e sugestões][EmulatorGithubBugs].



[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/
