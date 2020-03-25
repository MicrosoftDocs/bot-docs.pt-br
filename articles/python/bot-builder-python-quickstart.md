---
title: Criar um bot usando o SDK do Bot Framework para Python | Microsoft Docs
description: Crie rapidamente um bot usando o SDK do Bot Framework para Python.
keywords: início rápido, sdk do bot framework, introdução
author: emgrol
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/10/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: eb03a40f0044866679d0967fe7eaac30b5bc3143
ms.sourcegitcommit: 772b9278d95e4b6dd4afccf4a9803f11a4b09e42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2020
ms.locfileid: "80117718"
---
# <a name="create-a-bot-with-the-bot-framework-sdk-for-python"></a>Criar um bot com o SDK do Bot Framework para Python

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Este guia de início rápido oferece orientações sobre como criar um bot usando o modelo de Python Echo Bot e testá-lo com o Bot Framework Emulator.

## <a name="prerequisites"></a>Pré-requisitos
- Python [3.6](https://www.python.org/downloads/release/python-369/) ou [3.7](https://www.python.org/downloads/release/python-375/)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- [git](https://git-scm.com/)
- conhecimento da programação assíncrona em Python

## <a name="create-a-bot"></a>Criar um bot
1. Abra um terminal e navegue até a pasta em que o bot será salvo localmente. Instale os pacotes necessários executando o comando a seguir:
- `pip install botbuilder-core`
- `pip install asyncio`
- `pip install cookiecutter`

O último pacote, cookiecutter, será usado para gerar o bot. Verifique se o cookiecutter foi instalado corretamente executando `cookiecutter --help`.

2. Para criar sua execução de bot:

```cmd
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Este comando cria um Bot de Eco com base no [modelo de eco](https://github.com/microsoft/BotBuilder-Samples/tree/master/generators/python/app/templates/echo) do Python.

3. Em seguida, você deverá fornecer o *nome* do bot e uma *descrição*. Nomeie o bot como `echo-bot` e defina a descrição como `A bot that echoes back user response.`, conforme mostrado abaixo:

![Definir nome e descrição](../media/python/quickstart/set-name-description.png)

Copie os últimos quatro dígitos no endereço na última linha (geralmente 3978), já que você vai usá-los na próxima etapa. Agora você está pronto para iniciar o bot.

## <a name="start-your-bot"></a>Inicie seu bot
1. De um terminal, navegue até a pasta `echo-bot` em que você salvou o bot. Execute `pip install -r requirements.txt` para instalar os pacotes necessários para executar o bot.

2. Depois que os pacotes forem instalados, execute `python app.py` para iniciar o bot. Você saberá que o bot está pronto para teste quando você vir a última linha mostrada na captura de tela abaixo:

![bot em execução localmente](../media/python/quickstart/bot-running-locally.png)

## <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot
1. Inicie o Emulador e clique no botão **Abrir Bot**.

2. Depois de clicar no botão, será aberta uma janela em que você define os valores necessários para executar o bot. Use o número que você salvou anteriormente e defina a **URL do Bot** como `http://localhost:<saved number>/api/messages`, conforme mostrado abaixo:

![abrir uma tela de bot](../media/python/quickstart/open-bot.png)

3. Clique no botão **Conectar** e o bot deverá iniciar. Teste o bot digitando qualquer coisa e clicando em *Inserir* conforme mostrado abaixo:

![conectar e testar](../media/python/quickstart/connect-and-start.png)

## <a name="additional-resources"></a>Recursos adicionais
Confira [túnel (ngrok)](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-(ngrok)) para saber como se conectar a um bot hospedado remotamente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar seu bot no Azure](../bot-builder-deploy-az-cli.md)

