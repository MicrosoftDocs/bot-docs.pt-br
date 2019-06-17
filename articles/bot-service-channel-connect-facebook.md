---
title: Conectar um bot ao Facebook Messenger | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Facebook Messenger.
keywords: Facebook Messenger, canal de bot, Aplicativo Facebook, ID do Aplicativo, Segredo do Aplicativo, bot do Facebook, credenciais
author: RobStand
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/12/2018
ms.openlocfilehash: 36d98c6eeb368399ee11ef9a048bb42922103f16
ms.sourcegitcommit: e276008fb5dd7a37554e202ba5c37948954301f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693606"
---
# <a name="connect-a-bot-to-facebook"></a>Conectar um bot ao Facebook

Seu bot pode ser conectado ao Facebook Messenger e ao Workplace do Facebook, para que ele possa se comunicar com usuários nas duas plataformas. O tutorial a seguir mostra, passo a passo, como conectar um bot a esses dois canais.

> [!NOTE]
> A interface do usuário do Facebook pode parecer ligeiramente diferente de acordo com a versão que está sendo usada.

## <a name="connect-a-bot-to-facebook-messenger"></a>Conectar um bot ao Facebook Messenger

Para saber mais sobre o desenvolvimento para o Facebook Messenger, confira a [documentação da plataforma Messenger](https://developers.facebook.com/docs/messenger-platform). Talvez você deseje examinar as [diretrizes de pré-lançamento](https://developers.facebook.com/docs/messenger-platform/product-overview/launch#app_public), o [início rápido](https://developers.facebook.com/docs/messenger-platform/guides/quick-start) e o [guia de instalação](https://developers.facebook.com/docs/messenger-platform/guides/setup) do Facebook.

Para configurar um bot para se comunicar usando o Facebook Messenger, habilite o Facebook Messenger em uma página do Facebook e, em seguida, conecte o bot ao aplicativo.

### <a name="copy-the-page-id"></a>Copiar a ID da Página

O bot é acessado por meio de uma Página do Facebook. [Crie uma Página do Facebook](https://www.facebook.com/bookmarks/pages) ou acesse uma página existente.

* Abra a página **Sobre** da Página do Facebook e, em seguida, copie e salve a **ID da Página**.

### <a name="create-a-facebook-app"></a>Criar um aplicativo do Facebook

[Crie um Aplicativo do Facebook](https://developers.facebook.com/quickstarts/?platform=web) na Página e gere uma ID do Aplicativo e um Segredo do Aplicativo para ele.

![Criar uma ID do Aplicativo](~/media/channels/FB-CreateAppId.png)

* Copie e salve a **ID do Aplicativo** e o **Segredo do Aplicativo**.

![Salvar a ID do Aplicativo e o segredo](~/media/channels/FB-get-appid.png)

Defina o controle deslizante "Permitir Acesso da API às Configurações do Aplicativo" como "Sim".

![Configurações do aplicativo](~/media/bot-service-channel-connect-facebook/api_settings.png)

### <a name="enable-messenger"></a>Habilitar o Messenger

Habilite o Facebook Messenger no novo Aplicativo do Facebook.

![Habilitar o Messenger](~/media/channels/FB-AddMessaging1.png)

### <a name="generate-a-page-access-token"></a>Gerar um Token de Acesso da Página

No painel **Geração de Token** da seção Messenger, selecione a Página de destino. Um Token de Acesso da Página será gerado.

* Copie e salve o **Token de Acesso da Página**.

![Gerar token](~/media/channels/FB-generateToken.png)

### <a name="enable-webhooks"></a>Habilitar webhooks

Clique em **Configurar Webhooks** para encaminhar eventos de mensagens do Facebook Messenger para o bot.

![Habilitar o webhook](~/media/channels/FB-webhook.png)

### <a name="provide-webhook-callback-url-and-verify-token"></a>Fornecer a URL de retorno de chamada do webhook e verificar o token

No [portal do Azure](https://portal.azure.com/), abra o bot, clique na guia **Canais** e, em seguida, clique em **Facebook Messenger**.

* Copie os valores de **URL de Retorno de Chamada** e **Verificar Token** do portal.

![Copiar valores](~/media/channels/fb-callbackVerify.png)

1. Volte ao Facebook Messenger e cole os valores de **URL de Retorno de Chamada** e **Verificar Token**.

2. Em **Campos de Assinatura**, selecione *mensagem\_entregas*, *mensagens*, *mensagens\_opções*, e *mensagens\_postbacks*.

3. Clique em **Verificar e Salvar**.

![Configurar o webhook](~/media/channels/FB-webhookConfig.png)

4. Assine o webhook na página do Facebook.

![Assinar o webhook](~/media/bot-service-channel-connect-facebook/subscribe-webhook.png)


### <a name="provide-facebook-credentials"></a>Fornecer as credenciais do Facebook

No portal do Azure, cole os valores de **ID do Aplicativo do Facebook**, **Segredo do Aplicativo do Facebook**, **ID da Página** e **Token de Acesso da Página** copiados do Facebook Messenger anteriormente. Você pode usar o mesmo bot em várias páginas do facebook, adicionando outras ids de página e outros tokens de acesso.

![Inserir as credenciais](~/media/channels/fb-credentials2.png)

### <a name="submit-for-review"></a>Enviar para revisão

O Facebook exige uma URL da Política de Privacidade e uma URL dos Termos de Serviço na página de configurações básicas do aplicativo. A página [Código de Conduta](https://investor.fb.com/corporate-governance/code-of-conduct/default.aspx) contém links de recursos de terceiros para ajudar na criação de uma política de privacidade. A página [Termos de Uso](https://www.facebook.com/terms.php) contém termos de exemplo para ajudar na criação de um documento de Termos de Serviço apropriado.

Após a conclusão do bot, o Facebook tem seu próprio [processo de revisão](https://developers.facebook.com/docs/messenger-platform/app-review) para aplicativos que são publicados no Messenger. O bot será testado para garantir que está em conformidade com as [Políticas de Plataforma](https://developers.facebook.com/docs/messenger-platform/policy-overview) do Facebook.

### <a name="make-the-app-public-and-publish-the-page"></a>Tornar o Aplicativo público e publicar a Página

> [!NOTE]
> Enquanto um aplicativo não é publicado, ele fica no [Modo de Desenvolvimento](https://developers.facebook.com/docs/apps/managing-development-cycle). A funcionalidade de API e de plug-in só funcionará para administradores, desenvolvedores e testadores.

Depois que a análise for bem-sucedida, no Painel de Aplicativos em Revisão do Aplicativo, defina o aplicativo como Público.
Garanta que a Página do Facebook associada a esse bot seja publicada. O status é exibido nas configurações de Páginas.

## <a name="connect-a-bot-to-facebook-workplace"></a>Conectar um bot ao Workplace do Facebook

Consulte a [Central de Ajuda do Workplace](https://workplace.facebook.com/help/work/) para saber mais sobre o Workplace do Facebook e a [Documentação para desenvolvedor do Workplace](https://developers.facebook.com/docs/workplace) para obter diretrizes sobre como desenvolver para Workplace do Facebook.

Para configurar um bot a fim de se comunicar usando o Workplace do Facebook, crie uma integração personalizada e conecte o bot a ela.

### <a name="create-a-facebook-workplace-premium-account"></a>Criar uma conta Premium do Workplace do Facebook

Seguindo [estas](https://www.facebook.com/workplace) instruções para criar uma conta Premium do Workplace do Facebook e definir você mesmo como administrador do sistema. Saiba que apenas o administrador do sistema de um Workplace pode criar integrações personalizadas.

### <a name="create-a-custom-integration"></a>Criar uma integração personalizada

Quando você cria uma integração personalizada, ocorre a criação de um aplicativo com permissões definidas e uma página do tipo 'Bot' visível apenas dentro de sua comunidade de Workplace.

Crie uma [integração personalizada](https://developers.facebook.com/docs/workplace/custom-integrations-new) para seu Workplace executando as etapas abaixo:

- No **Painel de Administração**, abra a guia **Integrações**.
- Clique no botão **Criar seu próprio aplicativo personalizado**.

![Integração do Workplace](~/media/channels/fb-integration.png)

- Escolha um nome de exibição e uma foto de perfil para o aplicativo. Essas informações serão compartilhadas com a página do tipo 'Bot'.
- Defina **Permitir Acesso da API às Configurações do Aplicativo** como "Sim".
- Copie e armazene com segurança a ID do Aplicativo, o Segredo do Aplicativo e o Token do Aplicativo mostrado para você.

![Chaves do Workplace](~/media/channels/fb-keys.png)

Agora você concluiu a criação de uma integração personalizada. Você pode encontrar a página do tipo 'Bot' em sua comunidade do Workplace, conforme mostrado abaixo.

![Página do Workplace](~/media/channels/fb-page.png)

### <a name="provide-facebook-credentials"></a>Fornecer as credenciais do Facebook

No portal do Azure, cole os valores de **ID do Aplicativo do Facebook**, **Segredo do Aplicativo do Facebook**, **Token de Acesso da Página** copiados anteriormente do Workplace do Facebook. Em vez de uma pageID tradicional, use os números que seguem o nome de integrações em sua página **Sobre**. Semelhante a conexão a um bot para Facebook Messenger, os webhooks podem ser conectados com as credenciais mostradas no Azure.

### <a name="submit-for-review"></a>Enviar para revisão
Consulte a seção **Conectar um bot ao Facebook Messenger** e [Documentação de desenvolvedor do Workplace](https://developers.facebook.com/docs/workplace) para obter detalhes.

### <a name="make-the-app-public-and-publish-the-page"></a>Tornar o Aplicativo público e publicar a Página
Consulte a seção **Conectar um bot ao Facebook Messenger** para obter detalhes.

## <a name="setting-the-api-version"></a>Definir a versão da API

Se você receber uma notificação sobre a substituição de uma determinada versão da API do Graph do Facebook, vá para [página de desenvolvedores do Facebook](https://developers.facebook.com). Navegue até as **Configurações do aplicativo** do seu bot e vá para **Configurações > Avançadas > Atualizar versão de API**, depois troque para **Atualizar todas as chamadas** 3.0.

![Atualização de versão da API](~/media/channels/facebook-version-upgrade.png)

## <a name="sample-code"></a>Exemplo de código

Para obter mais referência, o bot de exemplo <a href="https://aka.ms/facebook-events" target="_blank">Facebook-events</a> pode ser usado para explorar a comunicação do bot com o Facebook Messenger.

## <a name="also-available-as-an-adapter"></a>Também disponível como um adaptador

Esse canal também [está disponível como um adaptador](https://botkit.ai/docs/v4/platforms/facebook.html). Para ajudá-lo a escolher entre um adaptador e um canal, confira [Adaptadores disponíveis no momento](bot-service-channel-additional-channels.md#currently-available-adapters).
