---
title: Implantar seu bot no Azure | Microsoft Docs
description: Implante seu bot na nuvem do Azure.
keywords: implantar bot, implantar o azure, registro do canal do bot, publicar o visual studio
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 05/14/2018
ms.openlocfilehash: 70a3b7f093bb80dd16c854c65331c141fbba3725
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297330"
---
# <a name="deploy-your-bot-to-azure"></a>Implantar seu bot no Azure

Depois de ter criado e verificado seu bot localmente, é possível utilizá-lo no Azure para torná-lo acessível em qualquer lugar. Para fazer isso, primeiro implante o bot no Azure em um Serviço de Aplicativo e, em seguida, configure seu bot com o Serviço de Bot do Azure usando o item do Registro de Canais de Bot.

## <a name="publish-from-visual-studio"></a>Publicar a partir do Visual Studio

Use o Visual Studio para criar seus recursos no Azure e publicar seu código.

Na janela do Gerenciador de Soluções, clique com o botão direito do mouse no nó do projeto e escolha Publicar.

![configuração de publicação](media/azure-bot-quickstarts/getting-started-publish-setting.png)

2. Na caixa de diálogo Escolher um destino de publicação, verifique se a opção **Serviço de Aplicativo** está marcada à esquerda e **Criar Novo** está marcada à direita.

3. Clique no botão Publicar.

4. No canto superior direito da caixa de diálogo, verifique se ela exibe a ID de usuário correta de sua assinatura do Azure.

![publicar principal](media/azure-bot-quickstarts/getting-started-publish-main.png)

5. Insira o Nome do Aplicativo, Assinatura, Grupo de Recursos e Informações do Plano de Hospedagem.

6. Quando estiver pronto, clique em Criar. A conclusão desse processo pode levar alguns minutos.

7. Uma vez concluído, um navegador da Web será aberto exibindo a URL pública do bot.

8. Faça uma cópia desta URL (a URL será algo como https://<yourbotname>.azurewebsites.net/).

> [!NOTE] 
> Você precisará usar a versão HTTPS da URL ao registrar seu bot. O Azure fornece suporte a SSL com o Serviço de Aplicativo do Azure.

## <a name="create-your-bot-channels-registration"></a>Criar seu registro de canais de bot
Com seu bot implantado no Azure, será preciso registrá-lo com o Serviço de Bot do Azure.

1. Acesse o portal do Azure em https://portal.azure.com.

2. Entre usando a mesma identidade utilizada anteriormente no Visual Studio para publicar seu bot.

3. Clique em Criar um recurso.

4. Na caixa de texto Pesquisar no Marketplace, digite Registro de Canais de Bot e pressione Enter.

5. Na lista retornada, escolha Registro de Canais de Bot:

![Publicar](media/azure-bot-quickstarts/getting-started-bot-registration.png)

6. Na folha que é aberta, clique em Criar.

7. Forneça um nome para seu bot.

8. Escolha a mesma Assinatura na qual você implantou o código do seu bot.

9. Escolha seu grupo Recursos existente para definir o local.

10. Você pode escolher a camada de preços F0 para desenvolvimento e teste.

11. Insira a URL do seu bot. Lembre-se de começar com HTTPS e de adicionar /api/messages. Por exemplo https://yourbotname.azurewebsites.net/api/messages

12. Desative o Application Insights por enquanto.

13. Clique na senha e ID do Aplicativo da Microsoft

14. Na nova folha, clique em Criar Nova.

15. Na nova folha que é exibida à direita, clique em "Criar ID do Aplicativo no Portal de Registro do Aplicativo" que será aberto em uma nova guia do navegador.

![bot msa](media/azure-bot-quickstarts/getting-started-msa.png)

16. Na nova guia, faça uma cópia da ID do Aplicativo e salve-a em algum lugar. 

17. Clique no botão Gerar uma senha do aplicativo para continuar.

18. Uma caixa de diálogo do navegador é aberta contendo a senha do seu aplicativo. Essa senha é fornecida uma única vez. Copie e salve essa senha em algum lugar que poderá ser acessado posteriormente.

19. Clique em OK depois que a senha for salva.

20. Basta fechar a guia do navegador e voltar para a guia do Portal do Azure.

21. Cole sua ID do Aplicativo e Senha nos campos corretos e clique em OK.

22. Agora, clique em Criar para configurar o registro do canal. Isso pode levar alguns segundos para ser concluído.

## <a name="update-your-bots-application-settings"></a>Atualizar as Configurações de Aplicativo do seu bot
Para que o bot possa autenticar com o Serviço de Bot do Azure, você precisará adicionar duas configurações às Configurações de aplicativo do bot no Serviço de Aplicativo do Azure. 

1. Clique em Serviços de Aplicativos. Digite o nome do seu bot na caixa de texto de Assinaturas. Em seguida, clique no nome do seu bot na lista.

![serviço de aplicativo](media/azure-bot-quickstarts/getting-started-app-service.png)

2. Na lista de opções à esquerda, dentro das opções do seu bot, localize a opção Configurações do Aplicativo na seção Configurações e clique nela.

![id do bot](media/azure-bot-quickstarts/getting-started-app-settings-1.png)

3. Role até encontrar a seção Configurações do Aplicativo.

![bot msa](media/azure-bot-quickstarts/getting-started-app-settings-2.png)

4. Clique em Adicionar nova configuração.

5. Digite **MicrosoftAppId** no nome e sua ID do Aplicativo no valor.

6. Clique em Adicionar nova configuração

7. Digite **MicrosoftAppPassword** no nome e sua senha no valor.

8. No canto superior, clique no botão Salvar.

## <a name="test-your-bot-in-production"></a>Teste seu bot em Produção
Neste ponto, é possível testar seu bot no Azure usando o cliente de Webchat interno.

1. Volte para o grupo de recursos no portal

2. Abra o registro do bot.

3. Em Gerenciamento de bot, escolha Teste no Webchat.

![teste no webchat](media/azure-bot-quickstarts/getting-started-test-webchat.png)

4. Digite uma mensagem como `Hi` e pressione Enter. O bot retornará `Turn 1: You sent Hi`.

