---
ms.openlocfilehash: bb7520e8e99ad6326d7d00d8190dae306bf11afa
ms.sourcegitcommit: a47183f5d1c2b2454c4a06c0f292d7c075612cdd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2019
ms.locfileid: "67252594"
---
Publique seu bot local no Azure. Esta etapa pode demorar um pouco.

```cmd
az bot publish --name <bot-resource-name> --proj-file-path "<project-file-name>" --resource-group <resource-group-name> --code-dir <directory-path> --verbose --version v4
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | O nome do recurso do bot no Azure. |
| --proj-file-path | Para o C#, use o nome do arquivo de projeto de inicialização (sem o .csproj) que precisa ser publicado. Por exemplo: `EnterpriseBot`. Para o Node.js, use o ponto de entrada principal para o bot. Por exemplo, `index.js`. |
| --resource-group | Nome do grupo de recursos. |
| --code-dir | O diretório de onde será carregado o código do bot. |

Ao concluir com uma mensagem de “Implantação bem-sucedida!”, seu bot está implantado no Azure.
