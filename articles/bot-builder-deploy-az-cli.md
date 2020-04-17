---
title: Implantar o bot – Serviço de Bot
description: Implantar seu bot na nuvem do Azure
keywords: implantar bot, implantar bot no azure, publicar bot
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 03/23/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9e4c67f644e9797f3e210546a91d09f5161aa100
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80499859"
---
# <a name="deploy-your-bot"></a>Implantar seu bot

[!INCLUDE [applies-to](./includes/applies-to.md)]

Neste artigo, mostraremos como implantar um bot básico no Azure. Explicaremos como preparar seu bot para implantação, implantá-lo no Azure e testá-lo no Webchat. Seria útil ler este artigo antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [deploy prerequisite](~/includes/deploy/snippet-prerequisite.md)]

## <a name="prepare-for-deployment"></a>Preparar para a implantação

[!INCLUDE [deploy prepare intro](~/includes/deploy/snippet-prepare-deploy-intro.md)]

### <a name="1-login-to-azure"></a>1. Logon no Azure

[!INCLUDE [deploy az login](~/includes/deploy/snippet-az-login.md)]

### <a name="2-set-the-subscription"></a>2. Definir a assinatura

[!INCLUDE [deploy az subscription](~/includes/deploy/snippet-az-set-subscription.md)]

### <a name="3-create-the-application-registration"></a>3. Criar o registro de aplicativo

[!INCLUDE [deploy create app registration](~/includes/deploy/snippet-create-app-registration.md)]

### <a name="4-create-the-bot-application-service"></a>4. Criar o serviço de aplicativo de bot

Ao criar o serviço de aplicativo de bot, você pode implantar o bot em um grupo de recursos novo ou existente. Escolha a opção que funcione melhor para você.

> [!IMPORTANT]
> Os bots do Python não podem ser implantados em um grupo de recursos que contém serviços/bots do Windows.  Vários bots do Python podem ser implantados no mesmo grupo de recursos, mas criam outros serviços (LUIS, P e R etc.) em outro grupo de recursos.

Você deve ter o caminho correto para o diretório de modelos de implantação do ARM do projeto de bot `DeploymentTemplates`, pois precisará dele para atribuir o valor a `template-file`.

#### <a name="deploy-via-arm-template-with-new-resource-group"></a>**Implantar por meio do modelo ARM (com **novo** Grupo de Recursos)**

<!-- ##### Create Azure resources -->
[!INCLUDE [ARM with new resource group](~/includes/deploy/snippet-ARM-new-resource-group.md)]


#### <a name="deploy-via-arm-template-with-existing--resource-group"></a>**Implantar por meio do modelo ARM (com Grupo de Recursos **existente**)**

[!INCLUDE [ARM with existing resource group](~/includes/deploy/snippet-ARM-existing-resource-group.md)]

---

### <a name="5-prepare-your-code-for-deployment"></a>5. Preparar seu código para implantação

[!INCLUDE [Work around for .NET Core 3.1 SDK](~/includes/deploy/samples-workaround-3-1.md)]

#### <a name="51-retrieve-or-create-necessary-iiskudu-files"></a>5.1 Recuperar ou criar os arquivos IIS/Kudu necessários

[!INCLUDE [retrieve or create IIS/Kudu files](~/includes/deploy/snippet-IIS-Kudu-files.md)]

#### <a name="52-zip-up-the-code-directory-manually"></a>5.2 Compactar o diretório de código manualmente

[!INCLUDE [zip up code](~/includes/deploy/snippet-zip-code.md)]

## <a name="deploy-code-to-azure"></a>Implantar código no Azure

[!INCLUDE [deploy code to Azure](~/includes/deploy/snippet-deploy-code-to-az.md)]

## <a name="test-in-web-chat"></a>Testar no Webchat

[!INCLUDE [test in web chat](~/includes/deploy/snippet-test-in-web-chat.md)]

## <a name="additional-information"></a>Informações adicionais

Implantar seu bot no Azure envolve pagar pelos serviços que você usa. O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a implantação contínua](bot-service-build-continuous-deployment.md)

<!-- ## Appendix

[!INCLUDE [deploy csharp bot to Azure](~/includes/deploy/snippet-deploy-simple-csharp-echo-bot.md)] -->
