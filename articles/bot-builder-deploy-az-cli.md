---
title: Implantar seu bot | Microsoft Docs
description: Implante seu bot na nuvem do Azure.
keywords: implantar bot, implantar bot no azure, publicar bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 02/07/2019
ms.openlocfilehash: b4c3b982bf061b3a24c6d240b05dc40b0cf07816
ms.sourcegitcommit: 8183bcb34cecbc17b356eadc425e9d3212547e27
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55971416"
---
# <a name="deploy-your-bot"></a>Implantar seu bot 

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Depois de ter criado e testado seu bot localmente, é possível implantá-lo no Azure para torná-lo acessível em qualquer lugar. Implantar seu bot no Azure envolve pagar pelos serviços que você usa. O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/en-us/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

Neste artigo, mostraremos como implantar os bots de C# e de JavaScript no Azure. Seria útil ler este artigo antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.

## <a name="prerequisites"></a>Pré-requisitos
- Instale a versão mais recente da ferramenta [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot).
- Um bot [CSharp](./dotnet/bot-builder-dotnet-sdk-quickstart.md) ou [JavaScript](./javascript/bot-builder-javascript-quickstart.md) que você tenha desenvolvido em seu computador local. 

## <a name="create-a-web-app-bot-in-azure"></a>Criar um Bot de Aplicativo Web no Azure
Esta seção é opcional se você já tiver criado um bot no Azure o qual você deseja usar.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Clique no link **Criar recurso** localizado no canto superior esquerdo do portal do Azure e, em seguida, selecione **IA + Machine Learning > bot de Aplicativo Web**.
1. Uma nova folha será aberta com informações sobre o Bot de Aplicativo Web. 
1. Na folha **Serviço de Bot**, forneça as informações necessárias sobre seu bot.
1. Clique em **Criar** para criar o serviço e implantar o bot na nuvem. Esse processo pode levar vários minutos.

## <a name="download-the-source-code"></a>Fazer o download do código-fonte
1. Na seção **Gerenciamento do Bot**, clique em **Compilar**.
1. Clique no link **Baixar o código-fonte do Bot** no painel à direita.
1. Siga os prompts para baixar o código e, em seguida, descompacte a pasta.

## <a name="decrypt-the-bot-file"></a>Descriptografe o arquivo .bot
O código-fonte que você baixou do portal do Azure inclui um arquivo .bot criptografado. Você precisa descriptografá-lo para copiar os valores para o seu arquivo .bot local.  

1. Abra o recurso de Bot do aplicativo Web para o seu bot no portal do Azure.
1. Abra as **Configurações do Aplicativo** do seu bot.
1. Na janela **Configurações do Aplicativo**, role para baixo até **Configurações do aplicativo**.
1. Localize o **botFileSecret** e copie seu valor.

Use `msbot cli` para descriptografar o arquivo.
```
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear
```

## <a name="update-the-bot-file"></a>Atualize o arquivo .bot
Abra o arquivo .bot que você descriptografou. Copie as entradas listadas na seção `services` e adicione-as ao seu arquivo .bot local. Por exemplo: 

```
{
   "type": "endpoint",
   "name": "production",
   "endpoint": "https://<something>.azurewebsites.net/api/messages",
   "appId": "<App Id>",
   "appPassword": "<App Password>",
   "id": "2
}
```

Salve o arquivo.
 
## <a name="setup-a-repository"></a>Configurar um repositório
Crie um repositório git usando o seu provedor de controle do código-fonte git preferido. Confirme o código no repositório.
 
## <a name="update-app-settings-in-azure"></a>Atualizar configurações do aplicativo no Azure
1. Abra o recurso de **Bot do aplicativo Web** para o seu bot no portal do Azure.
1. Abra as **Configurações do Aplicativo** do seu bot.
1. Na janela **Configurações do Aplicativo**, role para baixo até **Configurações do aplicativo**.
1. Localize o **botFileSecret** e o exclua.
1. Atualize o nome do arquivo bot para corresponder ao arquivo inserido no repositório.
1. Salve as alterações.


## <a name="deploy-using-azure-deployment-center"></a>Implantar usando a Central de Implantação do Azure
Agora, você precisa conectar seu repositório git aos Serviços de Aplicativo do Azure. Siga as instruções no tópico [configurar a implantação contínua](https://docs.microsoft.com/en-us/azure/app-service/deploy-continuous-deployment). Observe que é recomendável compilar usando `App Service Kudu build server`.

## <a name="test-your-deployment"></a>Testar a implantação
Aguarde alguns segundos após uma implantação bem-sucedida e, opcionalmente, reinicie o aplicativo Web para limpar qualquer cache. Volte para a folha de Bot do Aplicativo Web e teste usando o Webchat fornecido no portal do Azure.

## <a name="additional-resources"></a>Recursos adicionais
- [Como investigar problemas comuns com implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)

<!--

## Prerequisites

[!INCLUDE [prerequisite snippet](~/includes/deploy/snippet-prerequisite.md)]


## Deploy JavaScript and C# bots using az cli

You've already created and tested a bot locally, and now you want to deploy it to Azure. These steps assume that you have created the required Azure resources.

[!INCLUDE [az login snippet](~/includes/deploy/snippet-az-login.md)]

### Create a Web App Bot

If you don't already have a resource group to which to publish your bot, create one:

[!INCLUDE [az create group snippet](~/includes/deploy/snippet-az-create-group.md)]

[!INCLUDE [az create web app snippet](~/includes/deploy/snippet-create-web-app.md)]

Before proceeding, read the instructions that apply to you based on the type of email account you use to log in to Azure.

#### MSA email account

If you are using an [MSA](https://en.wikipedia.org/wiki/Microsoft_account) email account, you will need to create the app ID and app password on the Application Registration Portal to use with `az bot create` command.

[!INCLUDE [create bot msa snippet](~/includes/deploy/snippet-create-bot-msa.md)]

#### Business or school account

[!INCLUDE [create bot snippet](~/includes/deploy/snippet-create-bot.md)]

### Download the bot from Azure

Next, download the bot you just created. 
[!INCLUDE [download bot snippet](~/includes/deploy/snippet-download-bot.md)]

### Decrypt the downloaded .bot file and use in your project

The sensitive information in the .bot file is encrypted.

[!INCLUDE [decrypt bot snippet](~/includes/deploy/snippet-decrypt-bot.md)]

### Update the .bot file

If your bot uses LUIS, QnA Maker, or Dispatch services, you will need to add references to them to your .bot file. Otherwise, you can skip this step.

1. Open your bot in the BotFramework Emulator, using the new .bot file. The bot does not need to be running locally.
1. In the **BOT EXPLORER** panel, expand the **SERVICES** section.
1. To add references to LUIS apps, click the plus-sign (+) to the right of **SERVICES**.
   1. Select **Add Language Understanding (LUIS)**.
   1. If it prompts you to log into your Azure account, do so.
   1. It presents a list of LUIS applications you have access to. Select the ones for your bot.
1. To add references to a QnA Maker knowledge base, click the plus-sign (+) to the right of **SERVICES**.
   1. Select **Add QnA Maker**.
   1. If it prompts you to log into your Azure account, do so.
   1. It presents a list of knowledge bases you have access to. Select the ones for your bot.
1. To add references to Dispatch models, click the plus-sign (+) to the right of **SERVICES**.
   1. Select **Add Dispatch**.
   1. If it prompts you to log into your Azure account, do so.
   1. It presents a list of Dispatch models you have access to. Select the ones for your bot.

### Test your bot locally

At this point, your bot should work the same way it did with the old .bot file. Make sure that it works as expected with the new .bot file.

### Publish your bot to Azure

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish.md)]


[!INCLUDE [clear encryption snippet](~/includes/deploy/snippet-clear-encryption.md)]

## Additional resources

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## Next steps
> [!div class="nextstepaction"]
> [Set up continous deployment](bot-service-build-continuous-deployment.md)

-->
