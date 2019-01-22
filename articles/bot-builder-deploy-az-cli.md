---
title: Implantar seu bot usando a CLI do Azure | Microsoft Docs
description: Implante seu bot na nuvem do Azure.
keywords: deploy bot, azure deploy, publish bot, az deploy bot, visual studio deploy bot, msbot publish, msbot clone
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 01/07/2019
ms.openlocfilehash: 3ebc13cf9e2d111d716d081c36f125d28a441811
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360736"
---
# <a name="deploy-your-bot-using-azure-cli"></a>Implantar seu bot usando a CLI do Azure

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Depois de ter criado e testado seu bot localmente, é possível implantá-lo no Azure para torná-lo acessível em qualquer lugar. Implantar seu bot no Azure envolve pagar pelos serviços que você usa. O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/en-us/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

Neste artigo, mostraremos como implantar os bots de C# e de JavaScript para o Azure usando a CLI do `az` e do `msbot`. Seria útil ler este artigo antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [prerequisite snippet](~/includes/deploy/snippet-prerequisite.md)]


## <a name="deploy-javascript-and-c-bots-using-az-cli"></a>Implantar os bots de JavaScript e C# usando a CLI do Azure

Você já criou e testou um bot localmente, e agora deseja implantá-lo no Azure. Essas etapas pressupõem que você tenha criado os recursos do Azure necessários.

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

### <a name="decrypt-the-downloaded-bot-file-and-use-in-your-project"></a>Descriptografar o arquivo .bot baixado e usá-lo em seu projeto

As informações confidenciais no arquivo .bot são criptografadas.

[!INCLUDE [decrypt bot snippet](~/includes/deploy/snippet-decrypt-bot.md)]

### <a name="update-the-bot-file"></a>Atualize o arquivo .bot

Se o seu bot usar os serviços Expedição, QnA Maker ou LUIS, você precisará adicionar referências a eles no seu arquivo .bot. Caso contrário, ignore essa etapa.

1. Abra o seu bot no Bot Framework Emulator, usando o novo arquivo .bot. O bot não precisa estar em execução localmente.
1. No painel **GERENCIADOR DO BOT**, expanda a seção **SERVIÇOS**.
1. Para adicionar referências aos aplicativos LUIS, clique no sinal de adição (+) à direita de **SERVIÇOS**.
   1. Selecione **Adicionar Reconhecimento Vocal (LUIS)**.
   1. Caso você seja solicitado a fazer logon na sua conta do Azure, faça-o.
   1. Ela apresenta uma lista de aplicativos LUIS aos quais você tem acesso. Selecione aqueles para o seu bot.
1. Para adicionar referências a uma base de dados de conhecimento do QnA Maker, clique no sinal de adição (+) à direita de **SERVIÇOS**.
   1. Selecione **Adicionar QnA Maker**.
   1. Caso você seja solicitado a fazer logon na sua conta do Azure, faça-o.
   1. Ela apresenta uma lista de bases de conhecimento às quais você tem acesso. Selecione aquelas para o seu bot.
1. Para adicionar referências aos modelos de Expedição, clique no sinal de adição (+) à direita de **SERVIÇOS**.
   1. Selecione **Adicionar Expedição**.
   1. Caso você seja solicitado a fazer logon na sua conta do Azure, faça-o.
   1. Ela apresenta uma lista de modelos de Expedição aos quais você tem acesso. Selecione aqueles para o seu bot.

### <a name="test-your-bot-locally"></a>Testar o bot localmente

Neste ponto, seu bot deve funcionar da mesma forma que funcionou com o arquivo .bot antigo. Certifique-se de que ele funcione conforme o esperado com o novo arquivo .bot.

### <a name="publish-your-bot-to-azure"></a>Publicar seu bot no Azure

<!-- TODO: re-encrypt your .bot file? -->

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish.md)]

<!-- TODO: If we tell them to re-encrypt, this step is not necessary. -->

[!INCLUDE [clear encryption snippet](~/includes/deploy/snippet-clear-encryption.md)]

## <a name="additional-resources"></a>Recursos adicionais

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Configurar implantação contínua](bot-service-build-continuous-deployment.md)
