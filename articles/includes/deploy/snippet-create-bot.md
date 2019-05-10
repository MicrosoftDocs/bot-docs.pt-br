---
ms.openlocfilehash: f95c8a37b1207a26dab0a714b86412a9dba2dcb4
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035762"
---
```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name>
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | Um nome exclusivo que é usado para implantar o bot no Azure. Ele pode ser o mesmo nome do seu bot local. NÃO inclua espaços ou sublinhados no nome. |
| --location | Localização geográfica usada para criar recursos do serviço do bot. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. |
| --lang | O idioma a ser usado para criar o bot: `Csharp` ou `Node`; o padrão é `Csharp`. |
| --resource-group | Nome do grupo de recursos no qual o bot será criado. Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |