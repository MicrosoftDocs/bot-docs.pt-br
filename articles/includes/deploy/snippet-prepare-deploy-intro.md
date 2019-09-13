---
ms.openlocfilehash: db7c4b447c5a27fe2047cfa41e65fac0d3e3a86c
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386076"
---
Quando você cria um bot usando o [modelo do Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0) ou o [modelo do Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0), o código-fonte gerado inclui uma pasta `deploymentTemplates` que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure. 

> [!NOTE]
> Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos um arquivo appsettings.json ou .env para gerenciar os recursos do bot. Para obter informações sobre como migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env, confira [gerenciando recursos do bot](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0).
