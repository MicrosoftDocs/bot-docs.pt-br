---
ms.openlocfilehash: 867e65b25878f810e3247eb3cace95f4d31e11db
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360825"
---
Use um diretório temporário fora do diretório de seu projeto atual. 

Esse comando criará um subdiretório no save-path; no entanto, o caminho especificado já deve existir.

```cmd
az bot download --name <bot-resource-name> --resource-group <resource-group-name> --save-path "<path>"
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | O nome do bot no Azure. |
| --resource-group | Nome do grupo de recursos no qual o bot está localizado. |
| --save-path | Um diretório existente para onde o código do bot será baixado. |