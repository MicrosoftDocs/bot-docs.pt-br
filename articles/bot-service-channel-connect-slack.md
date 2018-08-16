---
title: Conectar um bot ao Slack | Microsoft Docs
description: Saiba como configurar a conexão do bot com o Slack.
keywords: conectar um bot, canal de bot, bot do Slack, aplicativo de mensagens do Slack
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 56fe0af4d34e6e0aa4bc420112c541a410aa1301
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296824"
---
# <a name="connect-a-bot-to-slack"></a>Conectar um bot ao Slack

Configure seu bot para se comunicar com as pessoas que usam o aplicativo de mensagens Slack.

## <a name="create-a-slack-application-for-your-bot"></a>Criar um aplicativo Slack para o seu bot

Faça logon no Slack e [crie um aplicativo Slack](https://api.slack.com/applications/new).

![Configurar o bot](~/media/channels/slack-NewApp.png)

## <a name="create-an-app-and-assign-a-development-slack-team"></a>Criar um aplicativo e atribuir uma equipe de Desenvolvimento do Slack

Insira um Nome de Aplicativo e selecione uma equipe de Desenvolvimento do Slack. Se você ainda não for membro de uma equipe de Desenvolvimento do Slack, [crie ou ingresse em uma](https://slack.com/).

![Criar aplicativo](~/media/channels/slack-CreateApp.png)

Clique em **Criar Aplicativo**. O Slack criará seu aplicativo e gerará uma ID de Cliente e Segredo do Cliente.

## <a name="add-a-new-redirect-url"></a>Adicionar uma nova URL de Redirecionamento

Em seguida, você adicionará uma nova URL de Redirecionamento.

1. Selecione a guia **OAuth e Permissões**.
2. Clique em **Adicionar uma nova URL de Redirecionamento**.
3. Digite https://slack.botframework.com.
4. Clique em **Adicionar**.
5. Clique em **Salvar URLs**.

![Adicionar URL de redirecionamento](~/media/channels/slack-RedirectURL.png)

## <a name="create-a-slack-bot-user"></a>Criar um Usuário de Bot do Slack

Ao adicionar um Usuário de Bot, você pode atribuir um nome de usuário para o seu bot e escolher se ele sempre será mostrado como online.

1. Selecione a guia **Usuários de Bot**.
2. Clique em **Adicionar um Usuário de Bot**.

![Criar bot](~/media/channels/slack-CreateBot.png)

Clique em **Adicionar Usuário de Bot** para validar suas configurações, defina **Sempre Mostrar meu Bot Online** como **Ligado** e, em seguida, clique em **Salvar Alterações**.

![Criar bot](~/media/channels/slack-CreateApp-AddBotUser.png)

## <a name="subscribe-to-bot-events"></a>Assinar eventos de Bot

Execute estas etapas para assinar seis eventos de bot específicos. Ao assinar eventos de bot, seu aplicativo receberá uma notificação das atividades do usuário na URL que você especificar.

> [!TIP]
> Seu identificador de bot é uma propriedade do seu bot. Para localizar o identificador de um bot, visite [https://dev.botframework.com/bots](https://dev.botframework.com/bots), escolha um bot e clique em **CONFIGURAÇÕES**.

1. Selecione a guia **Assinaturas de Evento**.
2. Defina **Habilitar Eventos** como **Ligado**.
3. Na **URL da Solicitação**, insira esta URL, mas substitua `{YourBotHandle}` por seu identificador de bot.
        `https://slack.botframework.com/api/Events/{YourBotHandle}`
4. Em **Assinar Eventos de Bot**, clique em **Adicionar Evento de Usuário do Bot**.
5. Na lista de eventos, clique em Adicionar **Evento de Usuário do Bot** e selecione os seis tipos de evento a seguir:
    * `member_joined_channel`
    * `member_left_channel`
    * `message.channels`
    * `message.groups`
    * `message.im`
    * `message.mpim`
6. Clique em **Salvar Alterações**.

![Assinar eventos](~/media/channels/slack-EnableEvents.png)

## <a name="add-and-configure-interactive-messages-optional"></a>Adicionar e configurar mensagens interativas (opcional)

Se o seu bot usar funcionalidades específicas do Slack, como botões, execute estas etapas:

1. Selecione a guia **Componentes Interativos** e clique em **Habilitar Componentes Interativos**.
2. Insira `https://slack.botframework.com/api/Actions` como a **URL da Solicitação**.
3. Clique no botão **Habilitar Mensagens Interativas** e, em seguida, clique no botão **Salvar alterações**.

![Habilitar mensagens](~/media/channels/slack-MessageURL.png)

## <a name="gather-credentials"></a>Obter credenciais

Selecione a guia **Informações Básicas** e role até a seção **Credenciais de Aplicativo**.
A ID do Cliente, o Segredo do Cliente e o Token de Verificação, necessários para a configuração do seu bot do Slack, são exibidos.

![Obter credenciais](~/media/channels/slack-AppCredentials.png)

## <a name="submit-credentials"></a>Enviar credenciais

Em uma janela separada do navegador, volte para o site do Bot Framework em `https://dev.botframework.com/`.

1. Selecione **Meus bots** e escolha o Bot que você deseja conectar ao Slack.
2. Na seção **Adicionar um canal**, clique no ícone do Slack.
3. Na seção **Inserir suas credenciais do Slack**, cole as Credenciais de Aplicativo do site do Slack nos campos apropriados.
4. A **URL da Página de Aterrissagem** é opcional. Você pode omitir ou alterá-la.
5. Clique em **Salvar**.

![Enviar credenciais](~/media/channels/slack-SubmitCredentials.png)

Siga as instruções para autorizar o acesso ao seu aplicativo Slack para sua equipe de Desenvolvimento do Slack.

## <a name="enable-the-bot"></a>Habilitar o bot

Na página Configurar Slack, confirme se o controle deslizante ao lado do botão Salvar é definido como **Habilitado**.
Seu bot está configurado para se comunicar com os usuários no Slack.

## <a name="create-an-add-to-slack-button"></a>Criar um botão Adicionar ao Slack

O Slack fornece HTML que pode ser usado para ajudar os usuários do Slack a localizar seu bot na seção *Adicionar o botão Slack* [desta página](https://api.slack.com/docs/slack-button).
Para usar este HTML com o bot, substitua o valor de href (começa com `https://`) pela URL encontrada nas configurações do canal do Slack do seu bot.
Execute estas etapas para obter a URL de substituição.

1. Em [https://dev.botframework.com/bots](https://dev.botframework.com/bots), clique em seu bot.
2. Clique em **CANAIS**, clique com o botão direito do mouse na entrada **Slack** e clique em **Copiar link**. Agora, essa URL está em sua área de transferência.
3. Copie essa URL da área de transferência e cole-a no HTML fornecido para o botão do Slack. Essa URL substitui o valor de href fornecido pelo Slack para este bot.

Os usuários autorizados podem clicar no botão **Adicionar ao Slack** fornecido por esse HTML modificado para acessar seu bot no Slack.
