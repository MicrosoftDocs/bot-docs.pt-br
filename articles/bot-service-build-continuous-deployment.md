---
title: Configurar a implantação contínua para o Serviço de Bot – Serviço de Bot
description: Saiba como configurar a implantação contínua do controle do código-fonte para um Serviço de Bot.
keywords: implantação contínua, publicar, implantar, portal do Azure
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/10/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7662e5e2d933d7a4500da8eadc0d914f9978800d
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75789188"
---
# <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua

[!INCLUDE [applies-to](./includes/applies-to.md)]

Este artigo mostra como configurar a implantação contínua para seu bot. Você pode habilitar a implantação contínua para implantar automaticamente as alterações no código do seu repositório de código-fonte para o Azure. Neste tópico, abordaremos como configurar a implantação contínua para o GitHub. Para obter informações sobre como configurar a implantação contínua com outros sistemas de controle de código-fonte, consulte a seção de recursos adicionais na parte inferior desta página.

## <a name="prerequisites"></a>Prerequisites
- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://portal.azure.com) antes de começar.
- Você **precisa** [implantar o bot no Azure](bot-builder-deploy-az-cli.md) antes de habilitar a implantação contínua.

## <a name="prepare-your-repository"></a>Preparar o repositório
Certifique-se de que a raiz do repositório tenha os arquivos corretos em seu projeto. Isso permitirá que você possa obter builds automáticos a partir do servidor de builds Kudu do Serviço de Aplicativo do Azure. 

|Runtime | Arquivos do diretório raiz |
|:-------|:---------------------|
| ASP.NET Core | .sln ou .csproj |
| Node.js | server.js, app.js ou package.json com um script de início |
| Python | app.py |


## <a name="continuous-deployment-using-github"></a>Implantação contínua usando o GitHub
Para habilitar a implantação contínua com o GitHub, navegue até a página **Serviço de Aplicativo** de seu bot no portal do Azure.

Clique em **Centro de Implantação** > **GitHub** > **Autorizar**.

![Implantação contínua](~/media/azure-bot-build/azure-deployment.png)

Na janela do navegador que será aberta, clique em **Autorizar AzureAppService**. 

![Permissão de Github do Azure](~/media/azure-bot-build/azure-deployment-github.png)

Depois de autorizar o **AzureAppService**, retorne ao **Centro de Implantação** no portal do Azure.

1. Clique em **Continuar**. 

1. Selecione **Serviço de build do Serviço de Aplicativo**.

1. Clique em **Continuar**.

1. Selecione **Organização**, **Repositório** e **Branch**.

1. Clique em **Continuar** e em **Concluir** para terminar a configuração.

Agora a implantação contínua com o GitHub está definida. Sempre que você confirmar no repositório do código-fonte, suas alterações serão automaticamente implantadas no Serviço de Bot do Azure.

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Enquanto o bot é configurado para a implantação contínua, você não pode usar o editor de códigos online para fazer alterações no bot. Caso deseje usar o editor de códigos online, desabilite temporariamente a implantação contínua.

Para desabilitar a implantação contínua, faça o seguinte:
1. No [portal do Azure](https://portal.azure.com), vá até a folha **Todas as configurações do Serviço de Aplicativo** de seu bot e clique em **Centro de Implantação**. 
1. Clique em **Desconectar** para desabilitar a implantação contínua. Para habilitar novamente a implantação contínua, repita as etapas das seções apropriadas acima.

## <a name="additional-resources"></a>Recursos adicionais
- Para habilitar a implantação contínua a partir do BitBucket e do Azure DevOps Services, consulte [implantação contínua usando o Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment).


