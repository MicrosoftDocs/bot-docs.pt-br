---
title: Tutorial sobre como criar e implantar um bot básico | Microsoft Docs
description: Saiba como criar um bot básico e, em seguida, implantá-lo no Azure.
keywords: bot de eco, implantar, azure, tutorial
author: Ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 1/9/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7927ab97dc88657a198c8f1d8e56bcb1ddf0fabe
ms.sourcegitcommit: b2245df2f0a18c5a66a836ab24a573fd70c7d272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568233"
---
# <a name="tutorial-create-and-deploy-a-basic-bot"></a>Tutorial: Criar e implantar um bot básico

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Este tutorial orienta você pela criação de um bot básico com o SDK do Bot Framework e sua implantação no Azure. Se você já tiver criado um bot básico e executado-o localmente, avance para a seção [Implantar seu bot](#deploy-your-bot).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um bot de eco básico
> * Executar e interagir com ele localmente
> * Publicar seu bot

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet quickstart](~/includes/quickstart-dotnet.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript quickstart](~/includes/quickstart-javascript.md)]

---

## <a name="deploy-your-bot"></a>Implantar seu bot

Agora, implante seu bot no Azure.

### <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [prerequisite snippet](~/includes/deploy/snippet-prerequisite.md)]

### <a name="login-to-azure-cli-and-set-your-subscription"></a>Faça logon na CLI do Azure e configure sua assinatura

Você já criou e testou um bot localmente, e agora deseja implantá-lo no Azure.

[!INCLUDE [az login snippet](~/includes/deploy/snippet-az-login.md)]

### <a name="create-a-web-app-bot"></a>Criar um bot de aplicativo Web

Se você ainda não tiver um grupo de recursos para o qual publicar seu bot, crie um:

[!INCLUDE [az create group snippet](~/includes/deploy/snippet-az-create-group.md)]

[!INCLUDE [az create web app snippet](~/includes/deploy/snippet-create-web-app.md)]

Antes de continuar, leia as instruções que se aplicam a você com base no tipo de conta de email usada para fazer logon no Azure.

#### <a name="msa-email-account"></a>Conta de email MSA

Se você estiver usando uma conta de email [MSA](https://en.wikipedia.org/wiki/Microsoft_account), será preciso criar a ID do aplicativo e a senha de aplicativo no Portal de Registro de Aplicativos para usar com o comando `az bot create`.

[!INCLUDE [create bot msa snippet](~/includes/deploy/snippet-create-bot-msa.md)]

#### <a name="business-or-school-account"></a>Conta comercial ou de estudante

[!INCLUDE [create bot snippet](~/includes/deploy/snippet-create-bot.md)]

### <a name="download-the-bot-from-azure"></a>Baixar o bot a partir do Azure

Em seguida, baixe o bot que você acabou de criar. 
[!INCLUDE [download bot snippet](~/includes/deploy/snippet-download-bot.md)]

[!INCLUDE [download keys snippet](~/includes/snippet-abs-key-download.md)]

### <a name="decrypt-the-downloaded-bot-file-and-use-in-your-project"></a>Descriptografar o arquivo .bot baixado e usá-lo em seu projeto

As informações confidenciais no arquivo .bot são criptografadas, portanto, vamos descriptografá-la para podermos usá-la facilmente. 

Primeiro, navegue até o diretório de bot baixado.

[!INCLUDE [decrypt bot snippet](~/includes/deploy/snippet-decrypt-bot.md)]

### <a name="test-your-bot-locally"></a>Testar o bot localmente

Neste ponto, seu bot deve funcionar da mesma forma que funcionou com o arquivo `.bot` antigo. Verifique se ele funciona conforme o esperado com o novo arquivo `.bot`.

No emulador, já deve ser possível ver um ponto de extremidade de *Produção*. Caso não o veja, é provável que você ainda esteja usando o arquivo `.bot` antigo.

### <a name="publish-your-bot-to-azure"></a>Publicar seu bot no Azure

<!-- TODO: re-encrypt your .bot file? -->

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish.md)]

<!-- TODO: If we tell them to re-encrypt, this step is not necessary. -->

[!INCLUDE [clear encryption snippet](~/includes/deploy/snippet-clear-encryption.md)]

Agora, você já deve conseguir testar seu bot em Webchat.

## <a name="additional-resources"></a>Recursos adicionais

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Adicionar serviços ao seu bot](bot-builder-tutorial-add-qna.md)

