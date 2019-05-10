---
ms.openlocfilehash: 14d9632ad578014a36b5f13e6dee883e2a6e1722
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035690"
---
1. Acesse o [**Portal de Registro de Aplicativos**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
1. Clique em **Adicionar um aplicativo** para registrar seu aplicativo, crie a **ID do aplicativo** e utilize a opção **Gerar nova senha**. Se você já tiver um aplicativo e uma senha, mas não se lembra da senha, você precisará gerar uma nova senha na seção Segredos do aplicativo.
1. Salve a ID do aplicativo e a nova senha que você acabou de gerar, para que você possa usá-las com o comando `az bot create`.  

```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name> --appid "<application-id>" --password "<application-password>" --verbose
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | Um nome exclusivo que é usado para implantar o bot no Azure. Ele pode ser o mesmo nome do seu bot local. NÃO inclua espaços ou sublinhados no nome. |
| --location | Localização geográfica usada para criar recursos do serviço do bot. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. |
| --lang | O idioma a ser usado para criar o bot: `Csharp` ou `Node`; o padrão é `Csharp`. |
| --resource-group | Nome do grupo de recursos no qual o bot será criado. Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --appid | A ID da conta Microsoft (ID da MSA) a ser usada com o bot. |
| --password | A senha da conta Microsoft (MSA) para o bot. |
