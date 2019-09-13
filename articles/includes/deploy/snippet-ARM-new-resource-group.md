---
ms.openlocfilehash: fd279af81e0c94ac22f54429cb8ae330e5d60661
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70385991"
---
Você criará um novo grupo de recursos no Azure e, em seguida, usará o modelo ARM para criar os recursos especificados nele. Nesse caso, estamos fornecendo o Plano do Serviço de Aplicativo, o Aplicativo Web e o Registro de Canais de Bot.

```cmd
az deployment create --name "<name-of-deployment>" --template-file "template-with-new-rg.json" --location "location-name" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" botSku=F0 newAppServicePlanName="<name-of-app-service-plan>" newWebAppName="<name-of-web-app>" groupName="<new-group-name>" groupLocation="<location>" newAppServicePlanLocation="<location>"
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| Nome | Nome amigável para a implantação. |
| arquivo de modelo | O caminho para o modelo ARM. Você pode usar o arquivo `template-with-new-rg.json` fornecido na pasta `deploymentTemplates` do projeto. |
| location |Local. Valores de: `az account list-locations`. Você pode configurar o local padrão usando `az configure --defaults location=<location>`. |
| parâmetros | Forneça os valores dos parâmetros de implantação. O valor `appId` que você obteve na execução do comando `az ad app create`. `appSecret` é a senha que você forneceu na etapa anterior. O parâmetro `botId` deve ser globalmente exclusivo e será usado como a ID imutável do bot. Ele também será usado para configurar o nome de exibição do bot, que é mutável. `botSku` é o tipo de preço e pode ser F0 (Gratuito) ou S1 (Standard). `newAppServicePlanName` é o nome do Plano do Serviço de Aplicativo. `newWebAppName` é o nome do Aplicativo Web que você está criando. `groupName` é o nome do grupo de recursos do Azure que você está criando. `groupLocation` é o local do grupo de recursos do Azure. `newAppServicePlanLocation` é o local do Plano do Serviço de Aplicativo. |
