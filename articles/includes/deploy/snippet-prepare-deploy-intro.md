---
ms.openlocfilehash: 8f799820eafaf199a765c7c1e0c52b80bd3695b3
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605298"
---
<!--
 Add this include file under the "Prepare for deployment" header in the file "bot-builder-tutorial-deploy-basic-bot.md".
-->

# <a name="c"></a>[C#](#tab/csharp)

Se você está implantando um bot C#, verifique se ele foi [criado no modo de versão](/visualstudio/debugger/how-to-set-debug-and-release-configurations). No Visual Studio, verifique se a configuração da solução está definida como **Versão** e execute uma recompilação limpa da solução antes de continuar. A implantação poderá falhar se a configuração da solução estiver definida como **depurar**.

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
