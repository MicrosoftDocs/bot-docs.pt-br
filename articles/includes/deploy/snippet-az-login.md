---
ms.openlocfilehash: c664749bc6ad63d1b0f60e001b2603898e58a9ea
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "70386081"
---
Depois de criar e testar um bot localmente, você poderá implantá-lo no Azure. Abra um prompt de comando para fazer logon no portal do Azure.

```cmd
az login
```
Uma janela do navegador será aberta, permitindo que você entre.

> [!NOTE]
> Se você implantar o bot em uma nuvem que não é do Azure, como US Gov, precisará executar `az cloud set --name <name-of-cloud>` antes de `az login`, em que &lt;name-of-cloud> é o nome de uma nuvem registrada, como `AzureUSGovernment`. Se você quiser voltar para a nuvem pública, poderá executar `az cloud set --name AzureCloud`. 
