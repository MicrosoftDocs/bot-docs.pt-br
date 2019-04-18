---
title: Gerenciar recursos com um arquivo .bot | Microsoft Docs
description: Descreve a finalidade e uso do arquivo de bot.
keywords: arquivo de bot, .bot, arquivo .bot, recursos do bot, gerenciar recursos do bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/30/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 14552c55da4b1f9b581b81917496de179e92762b
ms.sourcegitcommit: 721bb09f10524b0cb3961d7131966f57501734b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58811498"
---
# <a name="manage-bot-resources"></a>Gerenciar recursos do bot

Em geral, os bots consomem serviços diferentes, como [LUIS.ai](https://luis.ai) ou [QnaMaker.ai](https://qnamaker.ai). Quando estiver desenvolvendo um bot, você precisará ser capaz de rastrear todos eles. Você pode usar vários métodos, como appsettings.json, web.config ou .env. 

> [!IMPORTANT]
> Antes do lançamento do Bot Framework SDK 4.3, oferecíamos o arquivo .bot como um mecanismo para gerenciar recursos. No entanto, daqui em diante, é recomendável que você use o arquivo appsettings.json ou .env para gerenciar esses recursos. Os bots que usam o arquivo .bot continuarão funcionando por enquanto, mesmo que o arquivo .bot tenha sido **_preterido_**. Se você estava usando um arquivo .bot para gerenciar recursos, siga as etapas que se aplicam para migrar as configurações. 

## <a name="migrating-settings-from-bot-file"></a>Migrar as configurações do arquivo .bot
As seções a seguir abordam como migrar as configurações do arquivo .bot. Siga o cenário que se aplica a você.

**Cenário 1: Bot local com um arquivo .bot**

Nesse cenário, você tem um bot local que usa um arquivo .bot, mas o _bot não foi migrado_ para o portal do Azure. Siga as etapas abaixo para migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env.

- Se o arquivo .bot estiver criptografado, você precisará descriptografá-lo usando o seguinte comando:

```cli
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear` command.
```

- Abra o arquivo .bot descriptografado, copie os valores e adicione-os ao arquivo appsettings.json ou .env.
- Atualize o código para que ele leia as configurações do arquivo appsettings.json ou .env.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No método `ConfigureServices`, use o objeto de configuração fornecido pelo ASP.NET Core, por exemplo: 

**Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

No JavaScript, referencie às variáveis .env fora do objeto `process.env`, por exemplo:
   
**index.js**

```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```
---

Se necessário, provisione recursos e conecte-os ao seu bot usando o arquivo appsettings.json ou .env.

**Cenário 2: bot implantado no Azure com um arquivo .bot**

Nesse cenário, você já implantou um bot no portal do Azure usando o arquivo .bot e agora deseja migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env.

- Baixe o código do bot no portal do Azure. Ao baixar o código, você será solicitado a incluir o arquivo appsettings.json ou .env que terá seu MicrosoftAppId e MicrosoftAppPassword e outras configurações nele. 
- Abra o arquivo _baixado_ appsettings.json ou .env e copie as configurações dele para seu arquivo appsettings.json ou .env _local_. Não se esqueça de remover as entradas botSecret e botFilePath do arquivo appsettings.json ou .env local.
- Atualize o código para que ele leia as configurações do arquivo appsettings.json ou .env.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
No método `ConfigureServices`, use o objeto de configuração fornecido pelo ASP.NET Core, por exemplo: 

**Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
No JavaScript, referencie às variáveis .env fora do objeto `process.env`, por exemplo:
   
**index.js**

```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```
---

Também será necessário remover `botFilePath` e `botFileSecret` na seção **Configurações de aplicativo** no **portal do Azure**.

_Se necessário_, provisione recursos e conecte-os ao seu bot usando o arquivo appsettings.json ou .env.

**Cenário 3: para bots que usam o arquivo appsettings.json ou .env**

Esse cenário aborda o caso em que você está começando a desenvolver bots usando o SDK 4.3 do zero e não tem arquivos .bot existentes para migrar. Todas as configurações que você deseja usar em seu bot estão no arquivo appsettings.json ou .env, conforme mostrado abaixo:

```JSON
{
  "MicrosoftAppId": "<your-AppId>",
  "MicrosoftAppPassword": "<your-AppPwd>"
}
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para ler as configurações acima em seu código C#, você usará o objeto de configuração fornecido pelo ASP.NET Core, por exemplo: **Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
No JavaScript, referencie às variáveis .env fora do objeto `process.env`, por exemplo: **index.js**
```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```

---

Se necessário, provisione recursos e conecte-os ao seu bot usando o arquivo appsettings.json ou .env.


## <a name="faq"></a>Perguntas frequentes
**P:** Quero criar um novo bot V4 no portal do Azure. Como a experiência do portal do Azure mudou com a remoção do arquivo .bot?

**R:** Ao criar um bot no portal do Azure, o arquivo .bot não será criado. Você pode usar a seção **Configurações de aplicativo** no **portal do Azure** para localizar as IDs/chaves. Ao baixar o código, essas configurações serão armazenadas no arquivo appsettings.json ou .env para você. Em seu bot, você pode atualizar o código para ler as configurações antes de fazer a chamada ao serviço individual. Depois de atualizar seu código de bot, você pode usar o az bot publish para implantar seu bot.

**P:** E quanto aos bots V3?

**R:** O cenário para o bot V3 é semelhante ao cenário dos bots V4 sem um arquivo de bot. O processo de implantação continuará funcionando. 

## <a name="additional-resources"></a>Recursos adicionais
- Para saber como implantar o bot, confira o tópico [Implantação](../bot-builder-deploy-az-cli.md).
- Para proteger as chaves e os segredos, é recomendável usar o Azure Key Vault. O Azure Key Vault é uma ferramenta para armazenar e acessar segredos com segurança, como o ponto de extremidade e chaves de criação do seu bot. Ele fornece uma solução de [Gerenciamento de chaves](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) e torna mais fácil a criação e o controle de suas chaves de criptografia, assim você tem um controle rígido dos seus segredos.


<!--

# Manage resources with a .bot file

Bots usually consume lots of different services, such as [LUIS.ai](https://luis.ai) or [QnaMaker.ai](https://qnamaker.ai). When you are developing a bot, there is no uniform place to store the metadata about the services that are in use.  This prevents us from building tooling that looks at a bot as a whole.

To address this problem, we have created a **.bot file** to act as the place to bring all service references together in one place to 
enable tooling.  For example, the Bot Framework Emulator ([V4](https://aka.ms/Emulator-wiki-getting-started)) uses a  .bot file to create a unified view over the connected services your bot consumes.  

With a .bot file, you can register services like:

* **Localhost** local debugger endpoints
* [**Azure Bot Service**](https://azure.microsoft.com/en-us/services/bot-service/) Azure Bot Service registrations.
* [**LUIS.AI**](https://www.luis.ai/) LUIS gives your bot the ability to communicate with people using natural language.. 
* [**QnA Maker**](https://qnamaker.ai/) Build, train and publish a simple question and answer bot based on FAQ URLs, structured documents or editorial content in minutes.
* [**Dispatch**](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) models for dispatching across multiple services.
* [**Azure Application Insights**](https://azure.microsoft.com/en-us/services/application-insights/) for insights and bot analytics.
* [**Azure Blob Storage**](https://azure.microsoft.com/en-us/services/storage/blobs/) for bot state persistence. 
* [**Azure Cosmos DB**](https://azure.microsoft.com/en-us/services/cosmos-db/) - globally distributed, multi-model database service to persist bot state.

Apart from these, your bot might rely on other custom services. You can leverage the [generic service](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/add-services.md) capability to connect a generic service configuration.

## When is a .bot file created? 
- If you create a bot using [Azure Bot Service](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryResultsListBlade/selectedSubMenuItemId/%7B%22menuItemId%22%3A%22gallery%2FCognitiveServices_MP%2FBotService%22%2C%22resourceGroupId%22%3A%22%22%2C%22resourceGroupLocation%22%3A%22%22%2C%22dontDiscardJourney%22%3Afalse%2C%22launchingContext%22%3A%7B%22source%22%3A%5B%22GalleryFeaturedMenuItemPart%22%5D%2C%22menuItemId%22%3A%22CognitiveServices_MP%22%2C%22subMenuItemId%22%3A%22BotService%22%7D%7D), a .bot file is automatically created for you with list of connected services provisioned. The .bot is encrypted by default.
- If you create a bot using Bot Framework V4 SDK [Template](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4) for Visual Studio or using Bot Builder [Yeoman Generator](https://www.npmjs.com/package/generator-botbuilder), a .bot file is automatically created. No connected services are provisioned in this flow and the bot file is not encrypted.
- If you are starting with [BotBuilder-samples](https://github.com/Microsoft/botbuilder-samples), every sample for Bot Framework V4 SDK includes a .bot file and the .bot file is not encrypted. 
- You can also create a bot file using the [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md) tool.

## What does a bot file look like? 
Take a look at a sample [.bot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/sample-bot-file.json) file.
To learn about encrypting and decrypting the .bot file, see [Bot Secrets](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/bot-file-encryption.md).

## Why do I need a .bot file?

A .bot file is **not** a requirement to build bots with Bot Framework SDK. You can continue to use appsettings.json, web.config, env, 
keyvault or any mechanism you see fit to keep track of service references and keys that your bot depends on. However, to test
the bot using the Emulator, you'll need a .bot file. The good news is that Emulator can create a .bot file for testing. To do that, 
start the Emulator, click on the **create a new bot configuration** link on the Welcome page. In the dialog box that appears, type a **Bot name** and an **Endpoint URL**. Then connect.

The advantages of using .bot file are:
- Provides a standard way of storing resources regardless of the language/platform you use.   
- Bot Framework Emulator and CLI tools rely on and work great with tracking connected services in a consistent format (in a .bot file) 
- Elegant tooling solutions around services creation and management is harder without a well defined schema (.bot file).  


## Using .bot file in your Bot Framework SDK bot

You can use the .bot file to get service configuration information in your bot's code. The BotFramework-Configuration library available 
for [C#](https://www.nuget.org/packages/Microsoft.Bot.Configuration) and [JS](https://www.npmjs.com/package/botframework-config) helps you load a bot file and supports several methods to query and get the appropriate service configuration information.

## Additional resources
Refer to [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md) readme file for more information on using a bot file.

-->

