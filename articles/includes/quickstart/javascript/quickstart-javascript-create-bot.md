---
ms.openlocfilehash: 13c4e58897a0d3168a2315dcae5da59c7bc8946d
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94597441"
---
<!-- Include under "Create a bot" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-javascript-quickstart.md -->

1. Use o gerador para criar um bot de eco.

   ```bash
   yo botbuilder
   ```

   Yeoman pede-lhe alguma informação para criar o seu bot. Neste tutorial, use os valores padrão.

   ```text
   ? What's the name of your bot? my-chat-bot
   ? What will your bot do? Demonstrate the core capabilities of the Microsoft Bot Framework
   ? What programming language do you want to use? JavaScript
   ? Which template would you like to start with? Echo Bot - https://aka.ms/bot-template-echo
   ? Looking good.  Shall I go ahead and create your new bot? Yes
   ```

Graças ao modelo, seu projeto contém todo o código necessário para criar o bot neste início rápido. Você não precisa de nenhum código adicional para testar o bot.

> [!NOTE]
> Se você optar por criar um bot `Core`, será necessário um modelo de linguagem LUIS. (Você pode criar um modelo de linguagem em [luis.ai](https://www.luis.ai)). Após a criação do modelo, atualize o arquivo de configuração.
