---
ms.openlocfilehash: 88732d2d5490962d7a899d936767e7dd148e94c5
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360818"
---
Publique seu bot local no Azure. Esta etapa pode demorar um pouco.

```cmd
az bot publish --name <bot-resource-name> --proj-name "<project-file-name>" --resource-group <resource-group-name> --code-dir <directory-path> --verbose --version v4
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | O nome do recurso do bot no Azure. |
| --proj-name | Para o C#, use o nome do arquivo de projeto de inicialização (sem o .csproj) que precisa ser publicado. Por exemplo: `EnterpriseBot`. Para o Node.js, use o ponto de entrada principal para o bot. Por exemplo, `index.js`. |
| --resource-group | Nome do grupo de recursos. |
| --code-dir | O diretório de onde será carregado o código do bot. |

Ao concluir com uma mensagem de “Implantação bem-sucedida!”, seu bot está implantado no Azure.