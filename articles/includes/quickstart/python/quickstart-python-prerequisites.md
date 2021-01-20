---
ms.openlocfilehash: 4b18be7884dff162bb76dc3765030b8242fdb08b
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605135"
---
<!-- Include under "Prerequisites" header
bot-builder-tutorial-create-basic-bot.md and bot-builder-python-quickstart.md -->

- Python [3.6](https://www.python.org/downloads/release/python-369/), [3.7](https://www.python.org/downloads/release/python-375/) ou [3.8](https://www.python.org/downloads/release/python-383/)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento da programação assíncrona no Python
### <a name="templates"></a>Modelos

1. Instale os pacotes necessários executando o comando a seguir:

    ```cmd
    pip install botbuilder-core
    pip install asyncio
    pip install aiohttp
    pip install cookiecutter==1.7.0
    ```

    O último pacote, cookiecutter, será usado para gerar o bot. Verifique se o cookiecutter foi instalado corretamente executando `cookiecutter --help`.

1. Para criar sua execução de bot:

    ```cmd
    cookiecutter https://github.com/microsoft/BotBuilder-Samples/releases/download/Templates/echo.zip
    ```

    Este comando cria um Bot de Eco com base no [modelo de eco](https://github.com/microsoft/BotBuilder-Samples/tree/master/generators/python/app/templates/echo) do Python.

>[!NOTE]
>
> Alguns desenvolvedores podem achar útil criar bots do Python em um [ambiente virtual](https://docs.python.org/3/library/venv.html). As etapas a seguir funcionarão independentemente de você estar desenvolvendo em um ambiente virtual ou em seu computador local.
