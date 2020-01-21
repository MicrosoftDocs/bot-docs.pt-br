---
title: Implantar o bot – Serviço de Bot
description: Implantar seu bot na nuvem do Azure
keywords: implantar bot, implantar bot no azure, publicar bot
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 08/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1c5c4a1d09b1108d5ccbfbb8736efa4304688613
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75788944"
---
# <a name="deploy-your-bot"></a>Implantar seu bot

[!INCLUDE [applies-to](./includes/applies-to.md)]

Neste artigo, mostraremos como implantar um bot básico no Azure. Explicaremos como preparar seu bot para implantação, implantá-lo no Azure e testá-lo no Webchat. Seria útil ler este artigo antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.

## <a name="prerequisites"></a>Prerequisites
[!INCLUDE [deploy prerequisite](~/includes/deploy/snippet-prerequisite.md)]
<!-- - A subscription to [Microsoft Azure](https://azure.microsoft.com/free/)
- A C#, JavaScript, or TypeScript bot that you have developed on your local machine
- Latest version of the [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- Familiarity with [Azure CLI and ARM templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) -->

## <a name="prepare-for-deployment"></a>Preparar para a implantação
[!INCLUDE [deploy prepare intro](~/includes/deploy/snippet-prepare-deploy-intro.md)]
<!-- When you create a bot using [Visual Studio template](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0) or [Yeoman template](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0), the source code generated contains a `deploymentTemplates` folder with ARM templates. The deployment process documented here uses the ARM template to provision required resources for the bot in Azure by using the Azure CLI. 

> [!NOTE]
> With the release of Bot Framework SDK 4.3, we have _deprecated_ the use of .bot file in favor of appsettings.json or .env file for managing resources. For information on migrating settings from the .bot file to appsettings.json or .env file, see [managing bot resources](v4sdk/bot-file-basics.md). -->

### <a name="1-login-to-azure"></a>1. Logon no Azure
[!INCLUDE [deploy az login](~/includes/deploy/snippet-az-login.md)]
<!-- You've already created and tested a bot locally, and now you want to deploy it to Azure. Open a command prompt to log in to the Azure portal.

```cmd
az login
```
A browser window will open, allowing you to sign in.

> [!NOTE]
> If you deploy your bot to a non-Azure cloud such as US Gov, you need to run `az cloud set --name <name-of-cloud>` before `az login`, where &lt;name-of-cloud> is the name of a registered cloud, such as `AzureUSGovernment`. If you want to go back to public cloud, you can run `az cloud set --name AzureCloud`.  -->


### <a name="2-set-the-subscription"></a>2. Definir a assinatura
[!INCLUDE [deploy az subscription](~/includes/deploy/snippet-az-set-subscription.md)]
<!-- Set the default subscription to use.

```cmd
az account set --subscription "<azure-subscription>"
```

If you are not sure which subscription to use for deploying the bot, you can view the list of subscriptions for your account by using `az account list` command. Navigate to the bot folder. -->

### <a name="3-create-an-app-registration"></a>3. Crie um registro de aplicativo
[!INCLUDE [deploy create app registration](~/includes/deploy/snippet-create-app-registration.md)]
<!-- Registering the application means that you can use Azure AD to authenticate users and request access to user resources. Your bot requires a Registered app in Azure that provides the bot access to the Bot Framework Service for sending and receiving authenticated messages. To create register an app via the Azure CLI, perform the following command:

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Option   | Description |
|:---------|:------------|
| display-name | The display name of the application. |
| password | App password, aka 'client secret'. The password must be at least 16 characters long, contain at least 1 upper or lower case alphabetical character, and contain at least 1 special character.|
| available-to-other-tenants| The application can be used from any Azure AD tenants. This must be `true` to enable your bot to work with the Azure Bot Service channels.|

The above command outputs JSON with the key `appId`, save the value of this key for the ARM deployment, where it will be used for the `appId` parameter. The password provided will be used for the `appSecret` parameter.

> [!NOTE] 
> If you would like to use an existing App registration you can use the command:
> ``` cmd
> az bot create --kind webapp --resource-group "<name-of-resource-group>" --name "<name-of-web-app>" --appid "<existing-app-id>" --password "<existing-app-password>" --lang <Javascript|Csharp>
> ``` -->

### <a name="4-deploy-via-arm-template"></a>4. Implantar por meio do modelo do Resource Manager
Você pode implantar seu bot em um novo grupo de recursos ou em um grupo de recursos existente. Escolha a opção que funcione melhor para você.

> [!NOTE]
> Os bots do Python não podem ser implantados em um grupo de recursos que contém serviços/bots do Windows.  Vários bots do Python podem ser implantados no mesmo grupo de recursos, mas criam outros serviços (LUIS, P e R etc.) em outro grupo de recursos.
>

#### <a name="deploy-via-arm-template-with-new-resource-group"></a>**Implantar por meio do modelo ARM (com **novo** Grupo de Recursos)**
<!-- ##### Create Azure resources -->
[!INCLUDE [ARM with new resourece group](~/includes/deploy/snippet-ARM-new-resource-group.md)]
<!-- You'll create a new resource group in Azure and then use the ARM template to create the resources specified in it. In this case, we are providing App Service Plan, Web App, and Bot Channels Registration.

```cmd
az deployment create --name "<name-of-deployment>" --template-file "template-with-new-rg.json" --location "location-name" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" botSku=F0 newAppServicePlanName="<name-of-app-service-plan>" newWebAppName="<name-of-web-app>" groupName="<new-group-name>" groupLocation="<location>" newAppServicePlanLocation="<location>"
```

| Option   | Description |
|:---------|:------------|
| name | Friendly name for the deployment. |
| template-file | The path to the ARM template. You can use `template-with-new-rg.json` file provided in the `deploymentTemplates` folder of the project. |
| location |Location. Values from: `az account list-locations`. You can configure the default location using `az configure --defaults location=<location>`. |
| parameters | Provide deployment parameter values. `appId` value you got from running the `az ad app create` command. `appSecret` is the password you provided in the previous step. The `botId` parameter should be globally unique and is used as the immutable bot ID. It is also used to configure the display name of the bot, which is mutable. `botSku` is the pricing tier and can be F0 (Free) or S1 (Standard). `newAppServicePlanName` is the name of App Service Plan. `newWebAppName` is the name of the Web App you are creating. `groupName` is the name of the Azure resource group you are creating. `groupLocation` is the location of the Azure resource group. `newAppServicePlanLocation` is the location of the App Service Plan. | -->

#### <a name="deploy-via-arm-template-with-existing--resource-group"></a>**Implantar por meio do modelo ARM (com Grupo de Recursos **existente**)**

[!INCLUDE [ARM with existing resourece group](~/includes/deploy/snippet-ARM-existing-resource-group.md)]
<!-- When using an existing resource group, you can either use an existing App Service Plan or create a new one. Steps for both options are listed below. 

**Option 1: Existing App Service Plan** 

In this case, we are using existing App Service Plan, but creating new a Web App and Bot Channels Registration. 

> [!NOTE]
> The botId parameter should be globally unique and is used as the immutable bot ID. Also used to configure the displayName of the bot, which is mutable.

```cmd
az group deployment create --name "<name-of-deployment>" --resource-group "<name-of-resource-group>" --template-file "template-with-preexisting-rg.json" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" newWebAppName="<name-of-web-app>" existingAppServicePlan="<name-of-app-service-plan>" appServicePlanLocation="<location>"
```

**Option 2: New App Service Plan**

In this case, we are creating App Service Plan, Web App, and Bot Channels Registration. 

```cmd
az group deployment create --name "<name-of-deployment>" --resource-group "<name-of-resource-group>" --template-file "template-with-preexisting-rg.json" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" newWebAppName="<name-of-web-app>" newAppServicePlanName="<name-of-app-service-plan>" appServicePlanLocation="<location>"
```

| Option   | Description |
|:---------|:------------|
| name | Friendly name for the deployment. |
| resource-group | Name of the azure resource group |
| template-file | The path to the ARM template. You can use `template-with-preexisting-rg.json` file provided in the `deploymentTemplates` folder of the project. |
| location |Location. Values from: `az account list-locations`. You can configure the default location using `az configure --defaults location=<location>`. |
| parameters | Provide deployment parameter values. `appId` value you got from running the `az ad app create` command. `appSecret` is the password you provided in the previous step. The `botId` parameter should be globally unique and is used as the immutable bot ID. It is also used to configure the display name of the bot, which is mutable. `newWebAppName` is the name of the Web App you are creating. `newAppServicePlanName` is the name of App Service Plan. `newAppServicePlanLocation` is the location of the App Service Plan. | -->

---

### <a name="5-prepare-your-code-for-deployment"></a>5. Preparar seu código para implantação
#### <a name="51-retrieve-or-create-necessary-iiskudu-files"></a>5.1 Recuperar ou criar os arquivos IIS/Kudu necessários
[!INCLUDE [retrieve or create IIS/Kudu files](~/includes/deploy/snippet-IIS-Kudu-files.md)]

<!-- ##### [C#](#tab/csharp) -->

<!-- ```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

You must provide the path to the .csproj file relative to --code-dir. This can be performed via the --proj-file-path argument. The command would resolve --code-dir and --proj-file-path to "./MyBot.csproj"

##### [JavaScript](#tab/javascript)

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

This command will fetch a web.config which is needed for Node.js apps to work with IIS on Azure App Services. Make sure web.config is saved to the root of your bot.

##### [TypeScript](#tab/typescript)

```cmd
az bot prepare-deploy --code-dir "." --lang Typescript
```

This command works similarly to JavaScript above, but for a Typescript bot.

---

> [!NOTE]
> After executing the above-mentioned command, you should be able to see a `.deployment` file in your bot project folder. -->

#### <a name="52-zip-up-the-code-directory-manually"></a>5.2 Compactar o diretório de código manualmente
[!INCLUDE [zip up code](~/includes/deploy/snippet-zip-code.md)]
<!-- When using the non-configured [zip deploy API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) to deploy your bot's code, Web App/Kudu's behavior is as follows:

_Kudu assumes by default that deployments from zip files are ready to run and do not require additional build steps during deployment, such as npm install or dotnet restore/dotnet publish._

As such, it is important to include your built code and with all necessary dependencies in the zip file being deployed to the Web App, otherwise your bot will not work as intended.

> [!IMPORTANT]
> Before zipping your project files, make sure that you are _in_ the correct folder. 
> - For C# bots, it is the folder that has the .csproj file. 
> - For JS bots, it is the folder that has the app.js or index.js file. 
>
>**Within** the project folder, select all the files and zip them up, then run the command while still in the folder. 
>
> If your root folder location is incorrect, the **bot will fail to run in the Azure portal**. -->

## <a name="deploy-code-to-azure"></a>Implantar código no Azure
[!INCLUDE [deploy code to Azure](~/includes/deploy/snippet-deploy-code-to-az.md)]
<!-- At this point we are ready to deploy the code to the Azure Web App. Run the following command from the command line to perform deployment using the kudu zip push deployment for a web app. -->
<!-- 
```cmd
az webapp deployment source config-zip --resource-group "<new-group-name>" --name "<name-of-web-app>" --src "code.zip" 
```

| Option   | Description |
|:---------|:------------|
| resource-group | Resource group name in Azure that you created earlier. |
| name | Name of the Web App you used earlier. |
| src  | The path to the zipped file you created. | -->

## <a name="test-in-web-chat"></a>Testar no Webchat
[!INCLUDE [test in web chat](~/includes/deploy/snippet-test-in-web-chat.md)]
<!-- 1. In your browser, navigate to the [Azure portal](https://ms.portal.azure.com).
2. In the left panel, click **Resource groups**.
3. In the right panel, search for your group.
4. Click on your group name.
5. Click the link of your Bot Channel Registration.
6. In the *Bot Channel Registration blade*, click **Test in Web Chat**.
Alternatively, in the right panel, click the Test box.

For more information about channel registration, see [Register a bot with Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart-registration?view=azure-bot-service-3.0).

> [!NOTE]
> A blade is the surface on which service functions or navigation elements appear when selected. -->

## <a name="additional-information"></a>Informações adicionais
Implantar seu bot no Azure envolve pagar pelos serviços que você usa. O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Configurar a implantação contínua](bot-service-build-continuous-deployment.md)
