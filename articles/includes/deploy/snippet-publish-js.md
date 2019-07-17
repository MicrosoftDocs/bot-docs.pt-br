---
ms.openlocfilehash: 0891b9652154f8ed086cc45ce6018aa0be1a67b8
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230569"
---
Para publicar seu bot JavaScript local no Azure, você primeiro deve criar manualmente um único arquivo compactado que contém todos os arquivos usados para compilar e executar seu bot localmente. Isso inclui todas as bibliotecas npm baixadas na pasta `node_modules`. Ao criar o arquivo zip _verifique se o diretório-raiz usado é o mesmo diretório em que reside o arquivo index.js_.

Depois de criar um arquivo zip contendo todo o código-fonte do bot, abra uma janela do prompt de comando e execute o seguinte comando _Az cli_. 

Esta etapa pode demorar um pouco.

```cmd
az webapp deployment source config-zip --resource-group <resource-group-name> --name <bot-resource-name> --src <directory-path>
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --resource-group | Nome do grupo de recursos no Azure. |
| --nome | O nome do recurso do bot no Azure. |
| --src | O caminho completo do diretório do qual carregar o código do bot compactado. Por exemplo `c:\my-local-repository\this-app-folder\my-zipped-code.zip` |

Assim que isso for concluído com êxito, seu bot será implantado no Azure.
