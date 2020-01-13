---
ms.openlocfilehash: 3cb29b395019e720d5bc2ef7475c48f6c5757206
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491172"
---
Quando você cria um bot usando o [modelo do Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0), o [modelo do Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0) ou o [modelo do Cookiecutter](https://docs.microsoft.com/azure/bot-service/python/bot-builder-python-quickstart?view=azure-bot-service-4.0), o código-fonte gerado inclui uma pasta `deploymentTemplates` que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure. 

> [!NOTE]
> Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos um arquivo appsettings.json ou .env para gerenciar os recursos do bot. Para obter informações sobre como migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env, confira [gerenciando recursos do bot](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0).
