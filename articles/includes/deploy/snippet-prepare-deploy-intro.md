---
ms.openlocfilehash: 83ed1e7a28eb3cf417f5f404a1b33f0620e855ea
ms.sourcegitcommit: 4ddee4f90a07813ce570fdd04c8c354b048e22f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77479234"
---
Quando você cria um bot usando o [modelo do Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0), o [modelo do Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0) ou o [modelo do Cookiecutter](https://docs.microsoft.com/azure/bot-service/python/bot-builder-python-quickstart?view=azure-bot-service-4.0), o código-fonte gerado inclui uma pasta `deploymentTemplates` que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

> [!NOTE]
> Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos um arquivo appsettings.json ou .env para gerenciar os recursos do bot. Para obter informações sobre como migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env, confira [gerenciando recursos do bot](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0).

### <a name="bot-ready-to-deploy"></a>Bot pronto para implantação

Este artigo pressupõe que você tenha um bot pronto para implantação e o **caminho** para o projeto relacionado. Você precisa do caminho para acessar os modelos de implantação e também para criar um arquivo *zip* a ser implantado.

Para obter informações sobre como criar um bot de eco simples, confira a [amostra de CSharp](~/dotnet/bot-builder-dotnet-sdk-quickstart.md), a [amostra de JavaScript](~/javascript/bot-builder-javascript-quickstart.md) ou a [amostra de Python](~/python/bot-builder-python-quickstart.md) de início rápido.

Você também pode usar um dos exemplos fornecidos no repositório de [Amostras do Bot Framework](https://github.com/Microsoft/BotBuilder-Samples/blob/master/README.md).