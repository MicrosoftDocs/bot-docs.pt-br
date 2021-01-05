---
ms.openlocfilehash: 4ed9c6d6b9abccb3c9cda05000aa7ce5392eb0bd
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759138"
---
<!--
 Add this include file under the "Prepare for deployment" header in the file "bot-builder-tutorial-deploy-basic-bot.md".
-->

# <a name="c"></a>[C#](#tab/csharp)

Se você está implantando um bot C#, verifique se ele foi [criado no modo de versão](https://aka.ms/visualstudio-set-debug-release-configurations). No Visual Studio, verifique se a configuração da solução está definida como **Versão** e execute uma recompilação limpa da solução antes de continuar. A implantação poderá falhar se a configuração da solução estiver definida como **depurar**.

Quando você cria um bot usando um [modelo do Visual Studio](../../dotnet/bot-builder-dotnet-sdk-quickstart.md), o código-fonte gerado inclui uma `DeploymentTemplates` pasta que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos o `appsettings.json` arquivo de configuração para gerenciar os recursos de bot. Para obter informações sobre como migrar configurações do arquivo. bot para um arquivo de configuração, consulte [Managing bot Resources](../../v4sdk/bot-file-basics.md).

[!INCLUDE [about .NET Core versions in the templates](../vsix-templates-versions.md)]

# <a name="javascripttypescript"></a>[JavaScript/TypeScript](#tab/javascript+typescript)

Quando você cria um bot usando um [modelo Yeoman](../../javascript/bot-builder-javascript-quickstart.md), o código-fonte gerado inclui uma `deploymentTemplates` pasta que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos o `appsettings.json` arquivo de configuração para gerenciar os recursos de bot. Para obter informações sobre como migrar configurações do arquivo. bot para um arquivo de configuração, consulte [Managing bot Resources](../../v4sdk/bot-file-basics.md).

# <a name="python"></a>[Python](#tab/python)

Quando você cria um bot usando um [modelo CookieCutter](../../python/bot-builder-python-quickstart.md) , o código-fonte gerado inclui uma `deploymentTemplates` pasta que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos o `.env` arquivo para gerenciar os recursos de bot. Para obter informações sobre como migrar configurações do arquivo. bot para um arquivo de configuração, consulte [Managing bot Resources](../../v4sdk/bot-file-basics.md).

---
