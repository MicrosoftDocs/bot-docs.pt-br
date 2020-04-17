---
title: Tutorial sobre como criar e implantar um bot básico – Serviço de Bot
description: Saiba como criar um bot básico e, em seguida, implantá-lo no Azure.
keywords: bot de eco, implantar, azure, tutorial
author: Ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/23/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3b4e3652f7799fb09219b8536bd98a78e0952223
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80499904"
---
# <a name="tutorial-create-and-deploy-a-basic-bot"></a>Tutorial: Criar e implantar um bot básico

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Este tutorial guia você pela criação de um bot básico com o SDK do Bot Framework e sua implantação no Azure. Se você já tiver criado um bot básico e executado-o localmente, avance para a seção [Implantar seu bot](#deploy-your-bot).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um bot de eco básico
> * Executar e interagir com ele localmente
> * Publicar seu bot

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet quickstart](~/includes/quickstart-dotnet.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript quickstart](~/includes/quickstart-javascript.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [python quickstart](~/includes/quickstart-python.md)]

---

## <a name="deploy-your-bot"></a>Implantar seu bot

### <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [deploy prerequisite](~/includes/deploy/snippet-prerequisite.md)]

### <a name="prepare-for-deployment"></a>Preparar para a implantação

> [!TIP]
> Este procedimento usa um arquivo zip para implantar o bot. No C#, isso poderá falhar se a configuração da solução no build estiver definida como **Depurar**.
> No Visual Studio, verifique se a configuração da solução está definida como **Versão** e execute uma recompilação limpa da solução antes de continuar.

[!INCLUDE [deploy prepare intro](~/includes/deploy/snippet-prepare-deploy-intro.md)]

#### <a name="1-login-to-azure"></a>1. Logon no Azure

[!INCLUDE [deploy az login](~/includes/deploy/snippet-az-login.md)]

#### <a name="2-set-the-subscription"></a>2. Definir a assinatura

[!INCLUDE [deploy az subscription](~/includes/deploy/snippet-az-set-subscription.md)]

#### <a name="3-create-an-app-registration"></a>3. Crie um registro de aplicativo

[!INCLUDE [deploy create app registration](~/includes/deploy/snippet-create-app-registration.md)]

#### <a name="4-deploy-via-arm-template"></a>4. Implantar por meio do modelo do Resource Manager

Você pode implantar seu bot em um novo grupo de recursos ou em um grupo de recursos existente. Escolha a opção que funcione melhor para você.

> [!NOTE]
> Os bots do Python não podem ser implantados em um grupo de recursos que contém serviços/bots do Windows.  Vários bots do Python podem ser implantados no mesmo grupo de recursos, mas criam outros serviços (LUIS, P e R etc.) em outro grupo de recursos.
>

##### <a name="deploy-via-arm-template-with-new-resource-group"></a>**Implantar por meio do modelo ARM com um novo grupo de recursos**

[!INCLUDE [ARM with new resourece group](~/includes/deploy/snippet-ARM-new-resource-group.md)]

##### <a name="deploy-via-arm-template-with-existing-resource-group"></a>**Implantar por meio do modelo ARM com um grupo de recursos existente**

[!INCLUDE [ARM with existing resourece group](~/includes/deploy/snippet-ARM-existing-resource-group.md)]

#### <a name="5-prepare-your-code-for-deployment"></a>5. Preparar seu código para implantação

[!INCLUDE [Work around for .NET Core 3.1 SDK](~/includes/deploy/samples-workaround-3-1.md)]

##### <a name="retrieve-or-create-necessary-iiskudu-files"></a>**Recuperar ou criar os arquivos necessários do IIS/do Kudu**

[!INCLUDE [retrieve or create IIS/Kudu files](~/includes/deploy/snippet-IIS-Kudu-files.md)]

##### <a name="zip-up-the-code-directory-manually"></a>**Compactar o diretório de código manualmente**

[!INCLUDE [zip up code](~/includes/deploy/snippet-zip-code.md)]

### <a name="deploy-code-to-azure"></a>Implantar código no Azure

[!INCLUDE [deploy code to Azure](~/includes/deploy/snippet-deploy-code-to-az.md)]

### <a name="test-in-web-chat"></a>Testar no Webchat

[!INCLUDE [test in web chat](~/includes/deploy/snippet-test-in-web-chat.md)]

## <a name="additional-resources"></a>Recursos adicionais

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Use o QnA Maker no seu bot para responder a perguntas](bot-builder-tutorial-add-qna.md)