---
ms.openlocfilehash: dc3a05115e6238b90c7e40b57c0822e476aa86f4
ms.sourcegitcommit: eb0e5dec0ecd4e375d33825030b1ba46ff6e032c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791383"
---
## <a name="prerequisites"></a>Pré-requisitos

- Python [3.6](https://www.python.org/downloads/release/python-369/), [3.7](https://www.python.org/downloads/release/python-375/) ou [3.8](https://www.python.org/downloads/release/python-383/)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento da programação assíncrona em Python

## <a name="create-a-bot"></a>Criar um bot

>[!NOTE]
>
> Alguns desenvolvedores podem achar útil criar bots do Python em um [ambiente virtual](https://docs.python.org/3/library/venv.html). As etapas a seguir funcionarão independentemente de você estar desenvolvendo em um ambiente virtual ou em seu computador local. 


1. Abra um terminal. Instale os pacotes necessários executando o comando a seguir:

- `pip install botbuilder-core`
- `pip install asyncio`
- `pip install aiohttp`
- `pip install cookiecutter`

O último pacote, cookiecutter, será usado para gerar o bot. Verifique se o cookiecutter foi instalado corretamente executando `cookiecutter --help`.

2. Para criar sua execução de bot:

```cmd
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Este comando cria um Bot de Eco com base no [modelo de eco](https://github.com/microsoft/BotBuilder-Samples/tree/master/generators/python/app/templates/echo) do Python.

3. Você deverá fornecer o *nome* do bot e uma *descrição*. Nomeie o bot como `echo-bot` e defina a descrição como `A bot that echoes back user response.`, conforme mostrado abaixo:

![Definir nome e descrição](../media/python/quickstart/set-name-description.png)

Copie os últimos quatro dígitos do endereço na última linha (geralmente _3978_), já que você os usará na próxima etapa. Agora você está pronto para iniciar o bot.

## <a name="start-you-bot"></a>Iniciar o bot

1. De um terminal, navegue até a pasta `echo-bot` em que você salvou o bot. Execute `pip install -r requirements.txt` para instalar os pacotes necessários para executar o bot.

2. Depois que os pacotes forem instalados, execute `python app.py` para iniciar o bot. Você saberá que o bot está pronto para teste quando vir a última linha mostrada na captura de tela abaixo:

![bot em execução localmente](../media/python/quickstart/bot-running-locally.png)

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

1. Inicie o Emulador e clique no botão **Abrir Bot**.

2. Depois de clicar no botão, será aberta uma janela em que você define os valores necessários para executar o bot. Use o número que você salvou anteriormente e defina a **URL do Bot** como `http://localhost:<saved number>/api/messages`, conforme mostrado abaixo:

![abrir uma tela de bot](../media/python/quickstart/open-bot.png)

3. Clique no botão **Conectar** e o bot deverá iniciar. Teste o bot digitando qualquer coisa e clicando em *Inserir* conforme mostrado abaixo:

![conectar e testar](../media/python/quickstart/connect-and-start.png)
