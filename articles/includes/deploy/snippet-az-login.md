---
ms.openlocfilehash: f8aad539a2d1e415833609f66cd5b398c88206f1
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035781"
---
Abra um prompt de comando para fazer logon no portal do Azure.

```cmd
az login
```

Uma janela do navegador será aberta, permitindo que você entre.

### <a name="set-the-subscription"></a>Definir a assinatura

Defina a assinatura padrão que será usada.

```cmd
az account set --subscription "<azure-subscription>"
```

Se você não tiver certeza de qual assinatura usar para implantar o bot, você pode exibir a lista de `subscriptions` para sua conta usando o comando `az account list`.

Navegue até a pasta do bot.

```cmd
cd <local-bot-folder>
```