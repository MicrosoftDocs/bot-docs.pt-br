---
title: Tutorial para implantar um serviço bot-bot básico
description: Saiba como implantar bots no Azure. Consulte as etapas para preparar para implantação, implantação e testar bots.
keywords: bot de eco, implantar, azure, tutorial
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/02/2020
ms.openlocfilehash: 9bb5d57e952207c4766bb46bc02b2d49e91e712b
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759139"
---
# <a name="tutorial-deploy-a-basic-bot"></a>Tutorial: implantar um bot básico

[!INCLUDE [applies-to-v4](~/includes/applies-to-v4-current.md)]

Este tutorial descreve como implantar um bot básico no Azure. Ele explica como preparar seu bot para implantação, implantar o bot no Azure e testar o bot usando o chat da Web.
Seria útil se você ler este artigo antes de seguir as etapas, para que você entenda totalmente o que está envolvido na implantação de um bot.

- Se você ainda não criou um bot básico, leia o [tutorial: criar um bot básico](bot-builder-tutorial-create-basic-bot.md) .
- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Você saberá como:
> [!div class="checklist"]
> * Preparar um bot básico para implantação
> * Implantar o bot no Azure
> * Testá-lo usando o chat da Web

> [!IMPORTANT]
> Verifique se você está usando a versão mais recente da [CLI do Azure](/cli/azure/). Se você estiver usando uma versão da CLI do Azure mais antiga que [2.2.0](https://github.com/MicrosoftDocs/azure-docs-cli/blob/master/docs-ref-conceptual/release-notes-azure-cli.md#march-10-2020), encontrará erros de preterimento de comandos da CLI. Além disso, não misture a implantação da CLI do Azure mostrada neste artigo com a implantação do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [deploy prerequisites](../includes/deploy/snippet-prerequisite.md)]

## <a name="prepare-for-deployment"></a>Preparar para a implantação

Este artigo pressupõe que você tenha um bot pronto para implantação. Para obter informações sobre como criar um bot de eco simples, consulte o artigo [tutorial: criar um bot básico](bot-builder-tutorial-create-basic-bot.md) . Você também pode usar um dos exemplos fornecidos no repositório de [Amostras do Bot Framework](https://github.com/Microsoft/BotBuilder-Samples/blob/master/README.md).

[!INCLUDE [deploy prepare intro](../includes/deploy/snippet-prepare-deploy-intro.md)]

### <a name="1-login-to-azure"></a>1. Logon no Azure

[!INCLUDE [deploy az login](../includes/deploy/snippet-az-login.md)]

### <a name="2-set-the-subscription"></a>2. Definir a assinatura

[!INCLUDE [deploy az subscription](../includes/deploy/snippet-az-set-subscription.md)]

### <a name="3-create-an-app-registration"></a>3. Crie um registro de aplicativo

[!INCLUDE [deploy create app registration](../includes/deploy/snippet-create-app-registration.md)]

### <a name="4-deploy-via-arm-template"></a>4. Implantar por meio do modelo do Resource Manager

Ao criar o serviço de aplicativo de bot, você pode implantar o seu bot em um grupo de recursos novo ou existente, ambos por meio do [modelo do ARM (Azure Resource Manager)](/azure/azure-resource-manager/templates/overview). Um modelo ARM é um arquivo JSON que define declarativamente um ou mais recursos do Azure e as dependências entre os recursos implantados. Verifique se você tem o caminho correto para o diretório de modelos de implantação do ARM do projeto de bot `DeploymentTemplates`, pois precisará dele para atribuir o valor ao arquivo de modelo. Escolha a opção que funciona melhor para você:

* [Implantar por meio do modelo ARM com um novo grupo de recursos](#deploy-via-arm-template-with-new-resource-group)
* [Implantar por meio do modelo ARM com um grupo de recursos existente](#deploy-via-arm-template-with-existing-resource-group)

> [!NOTE]
> Os bots do Python não podem ser implantados em um grupo de recursos que contém serviços/bots do Windows.  Vários bots do Python podem ser implantados no mesmo grupo de recursos, mas criam outros serviços (LUIS, P e R etc.) em outro grupo de recursos.

##### <a name="deploy-via-arm-template-with-new-resource-group"></a>**Implantar por meio do modelo ARM com um novo grupo de recursos**

[!INCLUDE [ARM with new resource group](../includes/deploy/snippet-ARM-new-resource-group.md)]

##### <a name="deploy-via-arm-template-with-existing-resource-group"></a>**Implantar por meio do modelo ARM com um grupo de recursos existente**

[!INCLUDE [ARM with existing resource group](../includes/deploy/snippet-ARM-existing-resource-group.md)]

### <a name="5-prepare-your-code-for-deployment"></a>5. Preparar seu código para implantação

#### <a name="retrieve-or-create-necessary-iiskudu-files"></a>**Recuperar ou criar os arquivos necessários do IIS/do Kudu**

[!INCLUDE [retrieve or create IIS/Kudu files](../includes/deploy/snippet-IIS-Kudu-files.md)]

#### <a name="zip-up-the-code-directory-manually"></a>**Compactar o diretório de código manualmente**

[!INCLUDE [zip up code](../includes/deploy/snippet-zip-code.md)]

## <a name="deploy-bot-to-azure"></a>Implantar bot no Azure

[!INCLUDE [deploy code to Azure](../includes/deploy/snippet-deploy-code-to-az.md)]

## <a name="test-in-web-chat"></a>Testar no Webchat

[!INCLUDE [test in web chat](../includes/deploy/snippet-test-in-web-chat.md)]

## <a name="additional-resources"></a>Recursos adicionais

[!INCLUDE [additional resources snippet](../includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Use o QnA Maker no seu bot para responder a perguntas](bot-builder-tutorial-add-qna.md)
