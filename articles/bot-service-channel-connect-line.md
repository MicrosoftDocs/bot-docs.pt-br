---
title: Conectar um bot ao LINE | Microsoft Docs
description: Saiba como configurar a conexão do bot com o LINE.
keywords: conectar um bot, canal do bot, bot do LINE, credenciais, configurar, telefone
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 1/7/2019
ms.openlocfilehash: 8be0c7f89595e3222e5170fc7f11d052f9cb6851
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298476"
---
# <a name="connect-a-bot-to-line"></a>Conectar um bot ao LINE

Você pode configurar seu bot para se comunicar com as pessoas através do aplicativo LINE.

## <a name="log-into-the-line-console"></a>Fazer logon no console do LINE

Faça logon no [console do desenvolvedor do LINE](https://developers.line.biz/console/register/messaging-api/provider/) de sua conta do LINE usando *Entrar com LINE*. 

> [!NOTE]
> Se você ainda não o fez, [baixe o LINE](https://line.me/) e, em seguida, acesse as configurações para registrar seu endereço de email.

### <a name="register-as-a-developer"></a>Registrar-se como desenvolvedor

Se essa for a sua primeira vez no console do desenvolvedor do LINE, insira seu nome e endereço de email para criar uma conta de desenvolvedor.

![Captura de tela do LINE para o registro como desenvolvedor](./media/channels/LINE-screenshot-1.png)

## <a name="create-a-new-provider"></a>Criar um novo provedor

Primeiro, se você ainda não tiver um provedor configurado, crie um para seu bot. O provedor é a entidade (individual ou empresa) que oferece seu aplicativo.

![Captura de tela do LINE para criar um provedor](./media/channels/LINE-screenshot-2.png)

## <a name="create-a-messaging-api-channel"></a>Criar um canal de API de Mensagens

Em seguida, crie um novo canal de API de Mensagens. 

![Captura de tela do LINE para tipo de canal](./media/channels/LINE-channel-type-selection.png)

Crie um novo canal de API de Mensagens clicando no quadrado verde.

![Captura de tela do LINE para criar um canal](./media/channels/LINE-create-channel.png)

O nome não pode incluir “LINE” ou alguma cadeia de caracteres semelhante a esta. Preencha os campos obrigatórios e confirme as configurações do canal.

![Captura de tela do LINE para configurações do canal](./media/channels/LINE-screenshot-4.png)

## <a name="get-necessary-values-from-your-channel-settings"></a>Obter os valores necessários a partir das configurações do canal

Depois de confirmar as configurações do canal, você será direcionado para uma página semelhante a esta.

![Captura de tela do LINE para página do canal](./media/channels/LINE-screenshot-5.png)

Clique no canal criado para acessar as configurações de canal e, em seguida, role para baixo até encontrar as **Informações básicas > Segredo do canal**. Salve-o em um local qualquer temporariamente. Verifique se os recursos disponíveis incluem `PUSH_MESSAGE`.

![Captura de tela do LINE para segredo do canal](./media/channels/LINE-screenshot-6.png)

Em seguida, continue rolando para baixo até **Configurações de mensagens**. Você verá um campo **Token de acesso do canal**, com um botão *emitir*. Clique nesse botão para obter o token de acesso e, por enquanto, salve-o também.

![Captura de tela do LINE para token do canal](./media/channels/LINE-screenshot-8.png)

## <a name="connect-your-line-channel-to-your-azure-bot"></a>Conectar o canal do LINE ao bot do Azure

Faça logon no [portal do Azure](https://portal.azure.com/), localize seu bot e clique em **Canais**. 

![Captura de tela do LINE para configurações do Azure](./media/channels/LINE-channel-setting-2.png)

Escolha o canal do LINE e cole o segredo e o token de acesso do canal acima nos campos apropriados. Certifique-se de salvar as alterações.

Copie a URL do webhook personalizado que o Azure oferece.

![Captura de tela do LINE para configurações do Azure](./media/channels/LINE-channel-setting-1.png)

## <a name="configure-line-webhook-settings"></a>Definir as configurações de webhook do LINE

Em seguida, volte para o console do desenvolvedor do LINE e cole a URL do webhook do Azure em **Configurações de mensagens > URL do webhook** e clique em **Verificar** para verificar a conexão. Se você acabou de criar o canal no Azure, ele pode levar alguns minutos para entrar em vigor.

Em seguida, habilite **Configurações de mensagens > Usar webhooks**.

> [!IMPORTANT]
> No Console do Desenvolvedor do LINE, você deve primeiro definir a URL do webhook e, só depois, definir **Usar webhooks = Habilitado**. Habilitar webhooks inicialmente com uma URL vazia não definirá o status de habilitado, mesmo que a interface do usuário informe o contrário.

Depois de adicionar uma URL de webhook e, em seguida, habilitar webhooks, certifique-se de recarregar esta página e verificar se essas alterações foram definidas corretamente.

![Captura de tela do LINE para webhooks](./media/channels/LINE-screenshot-9.png)

## <a name="test-your-bot"></a>Testar seu bot

Depois que você concluir essas etapas, o bot será configurado com êxito para se comunicar com os usuários no LINE e estará pronto para testes.

### <a name="add-your-bot-to-your-line-mobile-app"></a>Adicionar seu bot ao aplicativo móvel do LINE

No console do desenvolvedor do LINE, navegue até a página de configurações e você verá um código QR do bot. 

No aplicativo móvel do LINE, vá até a guia de navegação mais à direita, com as reticências [ **...** ], e toque no ícone do código QR. 

![Captura de tela de aplicativo móvel do LINE](./media/channels/LINE-screenshot-12.jpg)

Aponte o leitor do código QR para o código QR no console do desenvolvedor. Já deve ser possível interagir com seu bot no aplicativo móvel do LINE e testar seu bot.

### <a name="automatic-messages"></a>Mensagens automáticas

Ao começar a testar seu bot, você notará que o bot envia mensagens inesperadas que não são aquelas que você especificou na atividade `conversationUpdate`.  O diálogo deve ser semelhante a esse:

![Captura de tela de conversa no LINE](./media/channels/LINE-screenshot-conversation.jpg)

Para evitar o envio dessas mensagens, você precisa desativar as mensagens de resposta automática.

![Captura de tela de resposta automática no LINE](./media/channels/LINE-screenshot-10.png)

Como alternativa, você pode optar por manter essas mensagens. Nesse caso, pode ser uma boa ideia clicar em “Definir mensagem” e editá-la.

![Captura de tela do LINE para definir a resposta automática](./media/channels/LINE-screenshot-11.png)

## <a name="troubleshooting"></a>solução de problemas

* Caso seu bot não esteja respondendo a nenhuma das mensagens, navegue até seu bot no portal do Azure e escolha Testar no Webchat.  
    * Se o bot funcionar no Webchat, mas não responder no LINE, recarregue a página do Console do desenvolvedor do LINE e repita as instruções para o webhook acima. Certifique-se de que define a **URL do webhook** antes de habilitar os webhooks.
    * Se o bot não funcionar no Webchat, depure o problema do bot e, em seguida, volte e conclua a configuração do canal do LINE.

