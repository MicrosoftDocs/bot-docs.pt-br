---
title: Gerenciar recursos do bot – Serviço de Bot
description: Descreve a finalidade e uso do arquivo de bot.
keywords: arquivo de bot, .bot, arquivo .bot, msbot, recursos do bot, gerenciar recursos do bot
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 65b7b3c3da9fb04d1d086c5304439b53bbc30cf7
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75791138"
---
# <a name="manage-bot-resources"></a>Gerenciar recursos do bot

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Em geral, os bots consomem serviços diferentes, como [LUIS.ai](https://luis.ai) ou [QnaMaker.ai](https://qnamaker.ai). Quando estiver desenvolvendo um bot, você precisará ser capaz de rastrear todos eles. Você pode usar vários métodos, como appsettings.json, web.config ou .env. 

> [!IMPORTANT]
> Antes do lançamento do Bot Framework SDK 4.3, oferecíamos o arquivo .bot como um mecanismo para gerenciar recursos. No entanto, daqui em diante, é recomendável que você use o arquivo appsettings.json ou .env para gerenciar esses recursos. Os bots que usam o arquivo .bot continuarão funcionando por enquanto, mesmo que o arquivo .bot tenha sido **_preterido_** . Se você estava usando um arquivo .bot para gerenciar recursos, siga as etapas que se aplicam para migrar as configurações. 

## <a name="migrating-settings-from-bot-file"></a>Migrar as configurações do arquivo .bot
As seções a seguir abordam como migrar as configurações do arquivo .bot. Siga o cenário que se aplica a você.

**Cenário #1: Bot local com um arquivo .bot**

Nesse cenário, você tem um bot local que usa um arquivo .bot, mas o _bot não foi migrado_ para o portal do Azure. Siga as etapas abaixo para migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env.

- Se o arquivo .bot estiver criptografado, você precisará descriptografá-lo usando o seguinte comando:

```cli
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear
```

- Abra o arquivo .bot descriptografado, copie os valores e adicione-os ao arquivo appsettings.json ou .env.
- Atualize o código para que ele leia as configurações do arquivo appsettings.json ou .env.

# <a name="c"></a>[C#](#tab/csharp)

No método `ConfigureServices`, use o objeto de configuração fornecido pelo ASP.NET Core, por exemplo: 

**Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```
# <a name="javascript"></a>[JavaScript](#tab/js)

No JavaScript, referencie às variáveis .env fora do objeto `process.env`, por exemplo:
   
**index.js**

```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```
---

*Se necessário*, provisione recursos e conecte-os ao seu bot usando o arquivo appsettings.json ou .env.

**Cenário 2: bot implantado no Azure com um arquivo .bot**

Nesse cenário, você já implantou um bot no portal do Azure usando o arquivo .bot e agora deseja migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env.

- Baixe o código do bot no portal do Azure. Ao baixar o código, você será solicitado a incluir o arquivo appsettings.json ou .env que terá seu MicrosoftAppId e MicrosoftAppPassword e outras configurações nele. 
- Abra o arquivo _baixado_ appsettings.json ou .env e copie as configurações dele para seu arquivo appsettings.json ou .env _local_. Não se esqueça de remover as entradas botSecret e botFilePath do arquivo appsettings.json ou .env local.
- Atualize o código para que ele leia as configurações do arquivo appsettings.json ou .env.

# <a name="c"></a>[C#](#tab/csharp)
No método `ConfigureServices`, use o objeto de configuração fornecido pelo ASP.NET Core, por exemplo: 

**Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```
# <a name="javascript"></a>[JavaScript](#tab/js)
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

*Se necessário*, provisione recursos e conecte-os ao seu bot usando o arquivo appsettings.json ou .env.

**Cenário 3: para bots que usam o arquivo appsettings.json ou .env**

Esse cenário aborda o caso em que você está começando a desenvolver bots usando o SDK 4.3 do zero e não tem arquivos .bot existentes para migrar. Todas as configurações que você deseja usar em seu bot estão no arquivo appsettings.json ou .env, conforme mostrado abaixo:

```JSON
{
  "MicrosoftAppId": "<your-AppId>",
  "MicrosoftAppPassword": "<your-AppPwd>"
}
```

# <a name="c"></a>[C#](#tab/csharp)

Para ler as configurações acima em seu código C#, você usará o objeto de configuração fornecido pelo ASP.NET Core, por exemplo: **Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```

# <a name="javascript"></a>[JavaScript](#tab/js)
No JavaScript, referencie às variáveis .env fora do objeto `process.env`, por exemplo: **index.js**
```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```

---

Se necessário, provisione recursos e conecte-os ao seu bot usando o arquivo appsettings.json ou .env.

## <a name="additional-resources"></a>Recursos adicionais
- Para saber como implantar o bot, confira o tópico [Implantação](../bot-builder-deploy-az-cli.md).
- Saiba como usar o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) para proteger e gerenciar as chaves e segredos criptográficos usados por aplicativos e serviços de nuvem.
