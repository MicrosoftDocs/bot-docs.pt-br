---
title: Configurar a implantação contínua para o Serviço de Bot | Microsoft Docs
description: Saiba como configurar a implantação contínua do controle do código-fonte para um Serviço de Bot.
keywords: implantação contínua, publicar, implantar, portal do Azure
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
ms.openlocfilehash: 62cbbcc560e049776b8aa891c167b9a6eaba3264
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997793"
---
# <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua
Se seu código for verificado no **GitHub** ou no **Azure DevOps (anteriormente conhecido como Visual Studio Team Services)**, use a implantação contínua para implantar automaticamente as alterações de código do seu repositório de origem para o Azure. Neste tópico, abordaremos como configurar a implantação contínua para **GitHub** e **Azure DevOps**.

> [!NOTE]
> Depois que a implantação contínua é configurada, o editor de códigos online no portal do Azure se torna *READ ONLY*.

## <a name="continuous-deployment-using-github"></a>Implantação contínua usando o GitHub

Para configurar a implantação contínua usando o GitHub, faça o seguinte:

1. Use o repositório GitHub que contenha o código-fonte que você deseja implantar no Azure. Você pode [bifurcar](https://help.github.com/articles/fork-a-repo/) um repositório existente ou criar seu próprio repositório e carregar o código-fonte relacionado em seu repositório GitHub.
2. No [portal do Azure](https://portal.azure.com), acesse a folha **Criar** do bot e clique em **Configurar implantação contínua**. 
3. Clique em **Instalação**.
   
   ![Configurar a implantação contínua](~/media/azure-bot-build/continuous-deployment-setup.png)

4. Clique em **Escolher fonte** e selecione **GitHub**.

   ![Escolher o GitHub](~/media/azure-bot-build/continuous-deployment-setup-github.png)

5. Clique em **Autorização** e, em seguida, clique no botão **Autorizar** e siga os prompts para fornecer autorização ao Azure para acessar sua conta do GitHub.

6. Clique em **Escolher projeto** e selecione um projeto.

7. Clique em **Escolher ramificação** e selecione uma ramificação.

8. Clique em **OK** para concluir o processo de instalação.

Agora a sua implantação contínua com a instalação do GitHub foi concluída. Sempre que você confirmar no repositório do código-fonte, suas alterações serão automaticamente implantadas no Serviço de Bot do Azure.

## <a name="continuous-deployment-using-azure-devops"></a>Implantação contínua usando o Azure DevOps

1. No [portal do Azure](https://portal.azure.com), na folha **Criar** do bot, clique em **Configurar implantação contínua**. 
2. Clique em **Instalação**.
   
   ![Configurar a implantação contínua](~/media/azure-bot-build/continuous-deployment-setup.png)

3. Clique em **Escolher fonte** e selecione **Visual Studio Team Services**. Lembre-se que o Visual Studio Team Services agora se chama Azure DevOps Services.

   ![Escolher o Visual Studio Team Services](~/media/azure-bot-build/continuous-deployment-setup-vs.png)

4. Clique em **Escolha sua conta** e selecione uma conta.

> [!NOTE]
> Caso não veja sua conta, verifique se ela está vinculada à sua assinatura do Azure. Para vincular sua conta à sua assinatura do Azure, acesse o Portal do Azure, abra **Organizações do Azure DevOps Services (anteriormente conhecido como Team Services)**. Será exibida uma lista de organizações que você possui no seu Azure DevOps. Clique naquela com o código-fonte do bot que você deseja implantar, você encontrará um botão **Conectar AAD** botão. Se a organização que você escolher não estiver vinculada à sua assinatura do Azure, esse botão estará habilitado. Então, clique neste botão para configurar a conexão. Talvez você precise aguardar algum tempo antes que ela entra em vigor.

5. Clique em **Escolher projeto** e selecione um projeto.

> [!NOTE]
> Há suporte apenas para projetos GIT do VSTS.

6. Clique em **Escolher ramificação** e selecione uma ramificação.
7. Clique em **OK** para concluir o processo de instalação.

   ![Configuração do Visual Studio](~/media/azure-bot-build/continuous-deployment-setup-vs-configuration.png)

Agora a sua implantação contínua com a instalação do Azure DevOps foi concluída. Sempre que você fizer uma confirmação, as alterações serão implantadas automaticamente no Azure.

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Enquanto o bot é configurado para a implantação contínua, você não pode usar o editor de códigos online para fazer alterações no bot. Caso deseje usar o editor de códigos online, desabilite temporariamente a implantação contínua.

Para desabilitar a implantação contínua, faça o seguinte:

1. Na folha **Criar** do bot, clique em **Configurar implantação contínua**. 
2. Clique em **Desconectar** para desabilitar a implantação contínua. Para habilitar novamente a implantação contínua, repita as etapas das seções apropriadas acima.

## <a name="additional-information"></a>Informações adicionais
- [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/?view=vsts)
