---
ms.openlocfilehash: 266cdc2bbeeb140e4b601c1bf0fb3fa8eb085dda
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360830"
---
- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalar a versão mais recente da [ferramenta de CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
- Instale a versão mais recente da ferramenta [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot).
- Instale a versão mais recente do [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Instale e configure o [ngrok](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-%28ngrok%29).
- Conhecimento do arquivo [.bot](~/v4sdk/bot-file-basics.md).

O msbot 4.3.2 e posterior exige a CLI do Azure versão 2.0.54 ou posterior. Se você tiver instalado a extensão botservice, remova-a com este comando.

```cmd
az extension remove --name botservice
```