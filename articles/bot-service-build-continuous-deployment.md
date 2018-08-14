---
title: Configurar a implantação contínua para o Serviço de Bot | Microsoft Docs
description: Saiba como configurar a implantação contínua do controle do código-fonte para um Serviço de Bot.
keywords: implantação contínua, publicar, implantar, portal do Azure
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/08/2018
ms.openlocfilehash: 596d264c4df72959c71ab353e5038175fc2bed31
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296818"
---
# <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua

A implantação contínua permite que você desenvolva seu bot localmente. A implantação contínua é útil se o bot faz check-in em um controle do código-fonte como o **GitHub** ou o **Visual Studio Team Services**. Enquanto você fizer check-in das alterações novamente no repositório de origem, as alterações serão implantadas automaticamente no Azure.

> [!NOTE]
> Depois que a implantação contínua é configurada, o [editor de códigos online](bot-service-build-online-code-editor.md) se torna *READ ONLY*.

Este tópico mostrará como configurar a implantação contínua para o **GitHub** e o **Visual Studio Team Services**.

## <a name="continuous-deployment-using-github"></a>Implantação contínua usando o GitHub

Para configurar a implantação contínua usando o GitHub, faça o seguinte:

1. [Crie um fork](https://help.github.com/articles/fork-a-repo/) do repositório GitHub que contenha o código que você deseja implantar no Azure.
2. No portal do Azure, acesse a folha **Criar** do bot e clique em **Configurar implantação contínua**. 
3. Clique em **Instalação**.
   
   ![Configurar a implantação contínua](~/media/azure-bot-build/continuous-deployment-setup.png)

4. Clique em **Escolher fonte** e escolha **GitHub**.

   ![Escolher o GitHub](~/media/azure-bot-build/continuous-deployment-setup-github.png)

5. Clique em **Autorização** e, em seguida, clique no botão **Autorizar** e siga os prompts para fornecer autorização ao Azure para acessar sua conta do GitHub.

A implantação contínua com a instalação do GitHub foi concluída. Sempre que você fizer uma confirmação, as alterações serão implantadas automaticamente no Azure.

## <a name="continuous-deployment-using-visual-studio"></a>Implantação contínua usando o Visual Studio

1. Na folha **Criar** do bot, clique em **Configurar implantação contínua**. 
2. Clique em **Instalação**.
   
   ![Configurar a implantação contínua](~/media/azure-bot-build/continuous-deployment-setup.png)

3. Clique em **Escolher fonte** e escolha **Visual Studio Team Services**.

   ![Escolher o Visual Studio Team Services](~/media/azure-bot-build/continuous-deployment-setup-vs.png)

4. Clique em **Escolha sua conta** e escolha uma conta.

> [!NOTE]
> Caso não veja sua conta, verifique se ela está vinculada à sua assinatura do Azure.
> Para obter mais informações, confira [Vinculando sua conta do VSTS à sua assinatura do Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App#linking-your-vsts-account-to-your-azure-subscription).

5. Clique em **Escolher um projeto** e escolha um projeto.

> [!NOTE]
> Há suporte apenas para projetos GIT do VSTS.

6. Clique em **Escolher Branch** e escolha um branch para branch.
7. Clique em **OK** para concluir o processo de instalação.

   ![Configuração do Visual Studio](~/media/azure-bot-build/continuous-deployment-setup-vs-configuration.png)

A implantação contínua com a instalação do Visual Studio Team Services foi concluída. Sempre que você fizer uma confirmação, as alterações serão implantadas automaticamente no Azure.

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Enquanto o bot é configurado para a implantação contínua, você não pode usar o editor de códigos online para fazer alterações no bot. Caso deseje usar o editor de códigos online, desabilite temporariamente a implantação contínua.

Para desabilitar a implantação contínua, faça o seguinte:

1. Na folha **Criar** do bot, clique em **Configurar implantação contínua**. 
2. Clique em **Desconectar** para desabilitar a implantação contínua. Para habilitar novamente a implantação contínua, repita as etapas das seções apropriadas acima.

## <a name="next-steps"></a>Próximas etapas
Agora que o bot foi configurado para a implantação contínua, teste o código usando o Webchat online.

> [!div class="nextstepaction"]
> [Testar no Webchat](bot-service-manage-test-webchat.md)
