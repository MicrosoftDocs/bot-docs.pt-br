---
title: Como depurar um serviço bot-bot
description: Saiba como usar o emulador do bot Framework para depurar bots. Consulte como definir pontos de interrupção em IDEs e como trocar mensagens com bots durante a depuração.
author: v-ducvo
ms.author: kamrani
keywords: SDK do Bot Framework, depurar o bot, testar o bot, emulador do bot, emulador
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/17/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2901265f96897cffac58b09635c41c7e10be2cc5
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456770"
---
# <a name="debugging-a-bot"></a>Depurando um bot

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como depurar seu bot usando um ambiente de desenvolvimento integrado (IDE), como o Visual Studio ou Visual Studio Code e o emulador de Bot Framework. Embora você possa usar esses métodos para depurar qualquer bot localmente, este artigo usa um bot em [C#](~/dotnet/bot-builder-dotnet-sdk-quickstart.md), em [Javascript](~/javascript/bot-builder-javascript-quickstart.md) ou em [Python](~/python/bot-builder-python-quickstart.md) criado no guia de início rápido.

> [!NOTE]
> Neste artigo, usamos o Bot Framework Emulator para enviar e receber mensagens do bot durante a depuração. Se você estiver procurando outras maneiras de depurar o bot usando o Bot Framework Emulator, leia o artigo [Depurar com o Bot Framework Emulator](bot-service-debug-emulator.md).

## <a name="prerequisites"></a>Pré-requisitos

- Baixe e instale o [emulador do Bot Framework](https://aka.ms/Emulator-wiki-getting-started).
- Baixe e instale o [Visual Studio Code](https://code.visualstudio.com) ou o [Visual Studio](https://www.visualstudio.com/downloads) (Community Edition ou superior).

<!-- ### Debug a JavaScript bot using command-line and Emulator

To run a JavaScript bot using the command line and testing the bot with the Emulator, do the following:
1. From the command line, change directory to your bot project directory.
1. Start the bot by running the command **node app.js**.
1. Start the Emulator and connect to the bot's endpoint (e.g.: **http://localhost:3978/api/messages**). If this is the first time you are running
the bot then click **File > New Bot** and follow the instructions on screen. Otherwise, click **File > Open Bot** to open an existing bot.
Since this bot is running locally on your computer, you can leave the **MicrosoftAppId** and **MicrosoftAppPassword** fields blank.
For more information, see [Debug with the Emulator](bot-service-debug-emulator.md).
1. From the Emulator, send your bot a message (e.g.: send the message "Hi").
1. Use the **Inspector** and **Log** panels on the right side of the Emulator window to debug your bot. For example, clicking on any of the messages bubble (e.g.: the "Hi" message bubble in the screenshot below) will show you the detail of that message in the **Inspector** panel. You can use it to view requests and responses as messages are exchanged between the Emulator and the bot. Alternatively, you can click on any of the linked text in the **Log** panel to view the details in the **Inspector** panel.

   ![Inspector panel on the Emulator](~/media/bot-service-debug-bot/emulator_inspector.png) -->

## <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [csharp vscode](~/includes/bot-service-debug-bot/csharp-vscode.md)]

[!INCLUDE [csharp visual studio](~/includes/bot-service-debug-bot/csharp-vs.md)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript vscode](~/includes/bot-service-debug-bot/js-vscode.md)]

## <a name="python"></a>[Python](#tab/python)

[!INCLUDE [python vscode](~/includes/bot-service-debug-bot/python-vscode.md)]

---

## <a name="additional-resources"></a>Recursos adicionais

- Confira [solucionar problemas gerais](bot-service-troubleshoot-bot-configuration.md) e outros artigos de solução de problemas nesta seção.
- Veja como [Depurar com o Emulador](bot-service-debug-emulator.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Depure seu bot usando arquivos de transcrição](v4sdk/bot-builder-debug-transcript.md).
