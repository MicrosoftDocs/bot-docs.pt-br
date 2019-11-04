---
title: Conectar um bot ao Facebook Messenger | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Facebook Messenger.
keywords: Facebook Messenger, canal de bot, Aplicativo Facebook, ID do Aplicativo, Segredo do Aplicativo, bot do Facebook, credenciais
manager: kamrani
ms.topic: article
ms.author: kamrani
ms.service: bot-service
ms.date: 10/28/2019
ms.openlocfilehash: 0821d9cac50828cc7802c4be8221538d4e1e620d
ms.sourcegitcommit: b2819741e2bcace92510e7b250cd5c1ab667d774
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031227"
---
# <a name="connect-a-bot-to-facebook"></a>Conectar um bot ao Facebook

Seu bot pode ser conectado ao Facebook Messenger e ao Workplace do Facebook, para que ele possa se comunicar com usuários nas duas plataformas. O tutorial a seguir mostra como conectar um bot a esses dois canais.

> [!NOTE]
> A interface do usuário do Facebook pode parecer ligeiramente diferente de acordo com a versão que está sendo usada.

## <a name="connect-a-bot-to-facebook-messenger"></a>Conectar um bot ao Facebook Messenger

> [!NOTE]
> A partir de 16 de dezembro de 2019, o Workplace do Facebook alterará o modelo de segurança para integrações personalizadas.  As integrações atuais criadas com o Microsoft Bot Framework precisam ser atualizadas para usar o Bot Framework Adapter (disponível em JavaScript/Node.js) e implantadas usando um Aplicativo Web no Azure.  Novos bots do Workplace desenvolvidos usando o Microsoft Bot Framework também devem usar o adaptador do Facebook do JavaScript. Saiba mais sobre como [usar o adaptador do Facebook](https://aka.ms/botframework-workplace-adapter). As instruções abaixo funcionarão apenas até 16 de dezembro de 2019.

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

### <a name="provide-facebook-credentials"></a>Fornecer as credenciais do Facebook

No portal do Azure, cole os valores de **ID do Aplicativo do Facebook**, **Segredo do Aplicativo do Facebook**, **Token de Acesso da Página** copiados anteriormente do Workplace do Facebook. Em vez de uma pageID tradicional, use os números que seguem o nome de integrações em sua página **Sobre**. Semelhante a conexão a um bot para Facebook Messenger, os webhooks podem ser conectados com as credenciais mostradas no Azure.

### <a name="submit-for-review"></a>Enviar para revisão
Consulte a seção **Conectar um bot ao Facebook Messenger** e [Documentação de desenvolvedor do Workplace](https://developers.facebook.com/docs/workplace) para obter detalhes.

### <a name="make-the-app-public-and-publish-the-page"></a>Tornar o Aplicativo público e publicar a Página
Consulte a seção **Conectar um bot ao Facebook Messenger** para obter detalhes.

## <a name="setting-the-api-version"></a>Definir a versão da API

Se você receber uma notificação sobre a substituição de uma determinada versão da API do Graph do Facebook, vá para [página de desenvolvedores do Facebook](https://developers.facebook.com). Navegue até as **Configurações do aplicativo** do seu bot e vá para **Configurações > Avançadas > Atualizar versão de API**, depois troque para **Atualizar todas as chamadas** 3.0.

![Atualização de versão da API](media/channels/fb-version-upgrade.png)

## <a name="see-also"></a>Consulte também

- **Código de exemplo**. Use o bot de exemplo <a href="https://aka.ms/facebook-events" target="_blank">Facebook-events</a> para explorar a comunicação do bot com o Facebook Messenger.

- **Disponível como um adaptador**. Esse canal também [está disponível como um adaptador](https://botkit.ai/docs/v4/platforms/facebook.html). Para ajudá-lo a escolher entre um adaptador e um canal, confira [Adaptadores disponíveis no momento](bot-service-channel-additional-channels.md#currently-available-adapters).
