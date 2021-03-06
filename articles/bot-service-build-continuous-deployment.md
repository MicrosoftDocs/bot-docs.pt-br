---
title: Configurar a implantação contínua para o Serviço de Bot – Serviço de Bot
description: Saiba como configurar a implantação contínua do controle do código-fonte para um Serviço de Bot.
keywords: implantação contínua, publicar, implantar, portal do Azure
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/23/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fff1e89af38f766bb50e6d262b3aa575044bc68e
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576348"
---
# <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo mostra como configurar a implantação contínua para seu bot. Você pode habilitar a implantação contínua para implantar automaticamente as alterações no código do seu repositório de código-fonte para o Azure. Neste tópico, abordaremos como configurar a implantação contínua para o GitHub. Para obter informações sobre como configurar a implantação contínua com outros sistemas de controle de código-fonte, confira a seção de recursos adicionais na parte inferior desta página.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://portal.azure.com) antes de começar.
- Você **precisa** [implantar o bot no Azure](bot-builder-deploy-az-cli.md) antes de habilitar a implantação contínua.

## <a name="prepare-your-repository"></a>Preparar o repositório

Certifique-se de que a raiz do repositório tenha os arquivos corretos em seu projeto. Isso permitirá que você possa obter builds automáticos por meio do provedor de build.

|Runtime | Arquivos do diretório raiz |
|:-------|:---------------------|
| ASP.NET Core | .sln ou .csproj |
| Node.js | server.js, app.js ou package.json com um script de início |
| Python | app.py |

## <a name="continuous-deployment-using-github"></a>Implantação contínua usando o GitHub

Para habilitar a implantação contínua com o GitHub, navegue até a página **Serviço de Aplicativo** de seu bot no portal do Azure.

1. Clique em **Centro de Implantação** > **GitHub** > **Autorizar**.

   ![Implantação do continuos](~/media/azure-bot-build/azure-deployment.png)

   1. Na janela do navegador que será aberta, clique em **Autorizar AzureAppService**.

      ![Permissão de Github do Azure](~/media/azure-bot-build/azure-deployment-github.png)

   1. Depois de autorizar o **AzureAppService**, retorne ao **Centro de Implantação** no portal do Azure.

1. Clique em **Continuar**.

      > [!div class="mx-imgBorder"]
      > ![Prosseguir para provedor de build](~/media/azure-bot-build/azure-deployment-continue.png)

1. Na página **Provedor de build**, selecione o provedor de build que deseja usar e clique em **Continuar**.

1. Na página **Configurar**, insira as informações necessárias e clique em **Continuar**. As informações necessárias dependerão de qual serviço de controle do código-fonte e provedor de compilação você escolheu.

1. Na página **Resumo**, examine as configurações e clique em **Concluir**.

Agora a implantação contínua com o GitHub está definida. Novas confirmações no repositório e no branch selecionados agora são implantadas continuamente em seu aplicativo do Serviço de Aplicativo. Você pode acompanhar as confirmações e implantações na página do **Centro de Implantação**.

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Enquanto o bot é configurado para a implantação contínua, você não pode usar o editor de códigos online para fazer alterações no bot. Caso deseje usar o editor de códigos online, desabilite temporariamente a implantação contínua.

Para desabilitar a implantação contínua, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com), vá até a folha **Todas as configurações do Serviço de Aplicativo** de seu bot e clique em **Centro de Implantação**.
1. Clique em **Desconectar** para desabilitar a implantação contínua. Para habilitar novamente a implantação contínua, repita as etapas das seções apropriadas acima.

## <a name="additional-resources"></a>Recursos adicionais

- Para obter mais informações sobre a implantação contínua no Azure, confira [Implantação contínua no Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment).
- Quando você usa ações do GitHub para o provedor de build, um fluxo de trabalho é criado em seu repositório. Você pode saber mais sobre como usar as [GitHub Actions](https://help.github.com/en/actions) no site do GitHub.
