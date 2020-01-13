---
ms.openlocfilehash: e2a43cf5880da0036415d80d19e59db4c84d2f73
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491766"
---
## <a name="prerequisites"></a>Prerequisites
- Python [3.6](https://www.python.org/downloads/release/python-369/) ou [3.7](https://www.python.org/downloads/release/python-375/)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- [git](https://git-scm.com/)
- conhecimento da programação assíncrona em Python

## <a name="create-a-bot"></a>Criar um bot
1. Abra um terminal e navegue até a pasta em que o bot será salvo localmente. Instale os pacotes necessários executando o comando a seguir:
- `pip install botbuilder-core`
- `pip install asyncio`
- `pip install -r requirements.txt`
- `pip install cookiecutter`

O último pacote, cookiecutter, será usado para gerar o bot. Verifique se o cookiecutter foi instalado corretamente executando `cookiecutter --help`.

2. Para criar sua execução de bot:

```cmd
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Este comando cria um Bot de Eco com base no [modelo de eco](https://github.com/microsoft/BotBuilder-Samples/tree/master/generators/python/app/templates/echo/%7B%7Bcookiecutter.bot_name%7D%7D) do Python.

3. Em seguida, você deverá fornecer o *nome* do bot e uma *descrição*. Nomeie o bot como `echo-bot` e defina a descrição como `A bot that echoes back user response.`, conforme mostrado abaixo:

![Definir nome e descrição](~/media/python/quickstart/set-name-description.png)

Copie os últimos quatro dígitos no endereço na última linha (geralmente 3978), já que você vai usá-los na próxima etapa. Agora você está pronto para iniciar o bot.

## <a name="start-you-bot"></a>Iniciar o bot
1. De um terminal, navegue até a pasta `echo-bot` em que você salvou o bot. Execute `pip install -r requirements.txt` para instalar os pacotes necessários para executar o bot.

2. Depois que os pacotes forem instalados, execute `python app.py` para iniciar o bot. Você saberá que o bot está pronto para teste quando você vir a última linha mostrada na captura de tela abaixo:

![bot em execução localmente](~/media/python/quickstart/bot-running-locally.png)
<!---
Alternatively, you can set the file in an environment variable with set `FLASK_APP=app.py` in Windows and `export FLASK_APP=app.py` in Mac OS/Linux and then run `flask run --host=127.0.0.1 --port=3978`.-->

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
1. Inicie o Emulador e clique no botão **Abrir Bot**.

2. Depois de clicar no botão, será aberta uma janela em que você define os valores necessários para executar o bot. Use o número que você salvou anteriormente e defina a **URL do Bot** como `http://localhost:<saved number>/api/messages`, conforme mostrado abaixo:

![abrir uma tela de bot](~/media/python/quickstart/open-bot.png)

3. Clique no botão **Conectar** e o bot deverá iniciar. Teste o bot digitando qualquer coisa e clicando em *Inserir* conforme mostrado abaixo:

![conectar e testar](~/media/python/quickstart/connect-and-start.png)
