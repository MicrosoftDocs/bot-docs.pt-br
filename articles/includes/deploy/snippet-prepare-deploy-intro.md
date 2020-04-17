---
ms.openlocfilehash: f2eb746a1ca5df60883b11944e278cedc1a04367
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80499850"
---
Quando você cria um bot usando um [modelo do Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0), o [modelo do Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0) ou o [modelo do Cookiecutter](https://docs.microsoft.com/azure/bot-service/python/bot-builder-python-quickstart?view=azure-bot-service-4.0), o código-fonte gerado inclui uma pasta `deploymentTemplates` que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

> [!NOTE]
> Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos um arquivo appsettings.json ou .env para gerenciar os recursos do bot. Para obter informações sobre como migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env, confira [gerenciando recursos do bot](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0).

### <a name="bot-ready-to-deploy"></a>Bot pronto para implantação

Este artigo pressupõe que você tenha um bot pronto para implantação. Se você está implantando um bot C#, verifique se ele foi [criado no modo de versão](https://aka.ms/visualstudio-set-debug-release-configurations).

Para obter informações sobre como criar um bot de eco simples, confira a [amostra de C#](~/dotnet/bot-builder-dotnet-sdk-quickstart.md), a [amostra de JavaScript](~/javascript/bot-builder-javascript-quickstart.md) ou a [amostra de Python](~/python/bot-builder-python-quickstart.md) de início rápido. Você também pode usar um dos exemplos fornecidos no repositório de [Amostras do Bot Framework](https://github.com/Microsoft/BotBuilder-Samples/blob/master/README.md).
