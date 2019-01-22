---
ms.openlocfilehash: eac6abae509d92ea4714bc01221f180ea575950f
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360819"
---
Obtenha a chave de criptografia.

1. Faça logon no [Portal do Azure](http://portal.azure.com/).
1. Abra o recurso de Bot do aplicativo Web para o seu bot.
1. Abra as **Configurações do Aplicativo** do seu bot.
1. Na janela **Configurações do Aplicativo**, role para baixo até **Configurações do aplicativo**.
1. Localize o **botFileSecret** e copie seu valor.

Descriptografe o arquivo .bot.

```cmd
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --bot | O caminho relativo para o arquivo .bot baixado. |
| --secret | A chave de criptografia. |

Copie o arquivo `.bot` descriptografado para o diretório que contém seu projeto de bot local, atualize seu bot para usar esse novo arquivo `.bot` e remova seu arquivo `.bot` antigo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em **appsettings.json**, atualize a propriedade **botFilePath** para apontar para o novo arquivo `.bot` que você adicionou ao seu diretório local.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Em **.env**, atualize a propriedade **botFilePath** para apontar para o novo arquivo `.bot` que você adicionou ao seu diretório local.

---

Depois que seu bot tiver sido atualizado, exclua o diretório temporário do bot baixado.