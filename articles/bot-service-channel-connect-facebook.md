---
title: Conectar um bot ao Facebook Messenger | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Facebook Messenger.
keywords: Facebook Messenger, canal de bot, Aplicativo Facebook, ID do Aplicativo, Segredo do Aplicativo, bot do Facebook, credenciais
author: RobStand
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 0a9ad7d51234b417d5d0f27dbcffe4ce839ba94a
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296668"
---
# <a name="connect-a-bot-to-facebook-messenger"></a>Conectar um bot ao Facebook Messenger

Para saber mais sobre o desenvolvimento para o Facebook Messenger, confira a [documentação da plataforma Messenger](https://developers.facebook.com/docs/messenger-platform). Talvez você deseje examinar as [diretrizes de pré-lançamento](https://developers.facebook.com/docs/messenger-platform/product-overview/launch#app_public), o [início rápido](https://developers.facebook.com/docs/messenger-platform/guides/quick-start) e o [guia de instalação](https://developers.facebook.com/docs/messenger-platform/guides/setup) do Facebook.

Para configurar um bot para se comunicar usando o Facebook Messenger, habilite o Facebook Messenger em uma página do Facebook e, em seguida, conecte o bot ao aplicativo.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

> [!NOTE]
> A interface do usuário do Facebook pode parecer ligeiramente diferente de acordo com a versão que está sendo usada.

## <a name="copy-the-page-id"></a>Copiar a ID da Página

O bot é acessado por meio de uma Página do Facebook. [Crie uma Página do Facebook](https://www.facebook.com/bookmarks/pages) ou acesse uma página existente.

* Abra a página **Sobre** da Página do Facebook e, em seguida, copie e salve a **ID da Página**.

## <a name="create-a-facebook-app"></a>Criar um aplicativo do Facebook

[Crie um Aplicativo do Facebook](https://developers.facebook.com/quickstarts/?platform=web) na Página e gere uma ID do Aplicativo e um Segredo do Aplicativo para ele.

![Criar uma ID do Aplicativo](~/media/channels/FB-CreateAppId.png)

* Copie e salve a **ID do Aplicativo** e o **Segredo do Aplicativo**.

![Salvar a ID do Aplicativo e o segredo](~/media/channels/FB-get-appid.png)

Defina o controle deslizante "Permitir Acesso da API às Configurações do Aplicativo" como "Sim".

![Configurações do aplicativo](~/media/bot-service-channel-connect-facebook/api_settings.png)

## <a name="enable-messenger"></a>Habilitar o Messenger


Habilite o Facebook Messenger no novo Aplicativo do Facebook.

* Na página **Instalação do Produto** do aplicativo, clique em **Introdução** e, em seguida, clique em **Introdução** novamente.


![Habilitar o Messenger](~/media/channels/FB-AddMessaging1.png)

## <a name="generate-a-page-access-token"></a>Gerar um Token de Acesso da Página

No painel **Geração de Token** da seção Messenger, selecione a Página de destino. Um Token de Acesso da Página será gerado.

* Copie e salve o **Token de Acesso da Página**.

![Gerar token](~/media/channels/FB-generateToken.png)

## <a name="enable-webhooks"></a>Habilitar webhooks

Clique em **Configurar Webhooks** para encaminhar eventos de mensagens do Facebook Messenger para o bot.

![Habilitar o webhook](~/media/channels/FB-webhook.png)

## <a name="provide-webhook-callback-url-and-verify-token"></a>Fornecer a URL de retorno de chamada do webhook e verificar o token

Volte para o [Portal do Bot Framework](https://dev.botframework.com/). Abra o bot, clique na guia **Canais** e, em seguida, clique em **Facebook Messenger**.

* Copie os valores de **URL de Retorno de Chamada** e **Verificar Token** do portal.

![Copiar valores](~/media/channels/fb-callbackVerify.png)

1. Volte ao Facebook Messenger e cole os valores de **URL de Retorno de Chamada** e **Verificar Token**.

2. Em **Campos de Assinatura**, selecione *mensagem\_entregas*, *mensagens*, *mensagens\_opções*, e *mensagens\_postbacks*.

3. Clique em **Verificar e Salvar**.

![Configurar o webhook](~/media/channels/FB-webhookConfig.png)

4. Assine o webhook na página do Facebook.

![Assinar o webhook](~/media/bot-service-channel-connect-facebook/subscribe-webhook.png)


## <a name="provide-facebook-credentials"></a>Fornecer as credenciais do Facebook

No Portal do Bot Framework, cole os valores de **ID da Página**, **ID do Aplicativo**, **Segredo do Aplicativo** e **Token de Acesso da Página** copiados do Facebook Messenger anteriormente.

![Inserir as credenciais](~/media/channels/fb-credentials2.png)

## <a name="submit-for-review"></a>Enviar para revisão

O Facebook exige uma URL da Política de Privacidade e uma URL dos Termos de Serviço na página de configurações básicas do aplicativo. A página [Código de Conduta](https://aka.ms/bf-conduct) contém links de recursos de terceiros para ajudar na criação de uma política de privacidade. A página [Termos de Uso](https://aka.ms/bf-terms) contém termos de exemplo para ajudar na criação de um documento de Termos de Serviço apropriado.

Após a conclusão do bot, o Facebook tem seu próprio [processo de revisão](https://developers.facebook.com/docs/messenger-platform/app-review) para aplicativos que são publicados no Messenger. O bot será testado para garantir que está em conformidade com as [Políticas de Plataforma](https://developers.facebook.com/docs/messenger-platform/policy-overview) do Facebook.

## <a name="make-the-app-public-and-publish-the-page"></a>Tornar o Aplicativo público e publicar a Página

> [!NOTE]
> Enquanto um aplicativo não é publicado, ele fica no [Modo de Desenvolvimento](https://developers.facebook.com/docs/apps/managing-development-cycle). A funcionalidade de API e de plug-in só funcionará para administradores, desenvolvedores e testadores.

Depois que a análise for bem-sucedida, no Painel de Aplicativos em Revisão do Aplicativo, defina o aplicativo como Público.
Garanta que a Página do Facebook associada a esse bot seja publicada. O status é exibido nas configurações de Páginas.
