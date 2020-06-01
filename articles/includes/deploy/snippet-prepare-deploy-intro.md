---
ms.openlocfilehash: 633e97cda128aa34f5157e8ccaa45664ed07e582
ms.sourcegitcommit: eb0e5dec0ecd4e375d33825030b1ba46ff6e032c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797148"
---
Quando você cria um bot usando um [modelo do Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0), o [modelo do Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0) ou o [modelo do Cookiecutter](https://docs.microsoft.com/azure/bot-service/python/bot-builder-python-quickstart?view=azure-bot-service-4.0), o código-fonte gerado inclui uma pasta `deploymentTemplates` que contém modelos ARM. O processo de implantação documentado aqui usa um dos modelos ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure.

> [!NOTE]
> Com a versão do SDK do Bot Framework 4.3, _preterimos_ o uso de um arquivo .bot. Em vez disso, usamos um arquivo de configuração, como um arquivo appsettings.json ou .env, para gerenciar os recursos de bot. Para saber mais sobre como migrar as configurações do arquivo .bot para um arquivo de configuração, confira [como gerenciar recursos do bot](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0).\
> As versões .NET Core 2.1 e .NET Core 3.1 dos modelos em C# estão disponíveis.
> Ao criar bots no Visual Studio 2019, você deverá usar os modelos do .NET Core 3.1.\
> Os exemplos de bot atuais usam modelos do .NET Core 3.1. Encontre os exemplos que usam modelos do .NET Core 2.1 na ramificação [4.7-archive](https://github.com/microsoft/BotBuilder-Samples/tree/4.7-archive/samples/csharp_dotnetcore) do repositório BotBuilder-Samples.
> Para saber mais sobre a implantação de bots do .NET Core 3.1 no Azure, confira [Implantar o bot](~/bot-builder-deploy-az-cli.md).

### <a name="bot-ready-to-deploy"></a>Bot pronto para implantação

Este artigo pressupõe que você tenha um bot pronto para implantação. Se você está implantando um bot C#, verifique se ele foi [criado no modo de versão](https://aka.ms/visualstudio-set-debug-release-configurations).

Para obter informações sobre como criar um bot de eco simples, confira a [amostra de C#](~/dotnet/bot-builder-dotnet-sdk-quickstart.md), a [amostra de JavaScript](~/javascript/bot-builder-javascript-quickstart.md) ou a [amostra de Python](~/python/bot-builder-python-quickstart.md) de início rápido. Você também pode usar um dos exemplos fornecidos no repositório de [Amostras do Bot Framework](https://github.com/Microsoft/BotBuilder-Samples/blob/master/README.md).
