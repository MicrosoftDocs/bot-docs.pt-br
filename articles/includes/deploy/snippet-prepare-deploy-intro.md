---
ms.openlocfilehash: 4b100eeb728ddd72982389a334486106218ccab1
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94599658"
---
<!--
 Add this include file under the "Prepare for deployment" header in the file "bot-builder-tutorial-deploy-basic-bot.md".
-->

# <a name="c"></a>[C#](#tab/csharp)

Se você está implantando um bot C#, verifique se ele foi [criado no modo de versão](https://aka.ms/visualstudio-set-debug-release-configurations). No Visual Studio, verifique se a configuração da solução está definida como **Versão** e execute uma recompilação limpa da solução antes de continuar. A implantação poderá falhar se a configuração da solução estiver definida como **depurar**.

Quando você cria um bot usando um [modelo do Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0&preserve-view=true), o código-fonte gerado inclui uma `deploymentTemplates` pasta que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos o `appsettings.json` arquivo de configuração para gerenciar os recursos de bot. Para obter informações sobre como migrar configurações do arquivo. bot para um arquivo de configuração, consulte [Managing bot Resources](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0&preserve-view=true).

> [!NOTE]
> As versões .NET Core 2.1 e .NET Core 3.1 dos modelos em C# estão disponíveis.
> Ao criar bots no Visual Studio 2019, você deverá usar os modelos do .NET Core 3.1.\
> Os exemplos de bot atuais usam modelos do .NET Core 3.1. Encontre os exemplos que usam modelos do .NET Core 2.1 na ramificação [4.7-archive](https://github.com/microsoft/BotBuilder-Samples/tree/4.7-archive/samples/csharp_dotnetcore) do repositório BotBuilder-Samples.
> Para saber mais sobre a implantação de bots do .NET Core 3.1 no Azure, confira [Implantar o bot](~/bot-builder-deploy-az-cli.md).

# <a name="javascripttypescript"></a>[JavaScript/TypeScript](#tab/javascript+typescript)

Quando você cria um bot usando um [modelo Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0&preserve-view=true), o código-fonte gerado inclui uma `deploymentTemplates` pasta que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos o `appsettings.json` arquivo de configuração para gerenciar os recursos de bot. Para obter informações sobre como migrar configurações do arquivo. bot para um arquivo de configuração, consulte [Managing bot Resources](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0&preserve-view=true).

# <a name="python"></a>[Python](#tab/python)

Quando você cria um bot usando um [modelo CookieCutter](https://docs.microsoft.com/azure/bot-service/python/bot-builder-python-quickstart?view=azure-bot-service-4.0&preserve-view=true) , o código-fonte gerado inclui uma `deploymentTemplates` pasta que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos o `.env` arquivo para gerenciar os recursos de bot. Para obter informações sobre como migrar configurações do arquivo. bot para um arquivo de configuração, consulte [Managing bot Resources](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0&preserve-view=true).

---