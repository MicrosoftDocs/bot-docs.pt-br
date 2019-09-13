---
ms.openlocfilehash: 029275eae6f7b0b4448613ede898575eb491a56f
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386029"
---
Ao usar um grupo de recursos existente, você pode empregar um Plano do Serviço de Aplicativo existente ou criar um novo. As etapas para as duas opções estão listadas abaixo. 

**Opção 1: Plano do Serviço de Aplicativo existente** 

Nesse caso, estamos usando o Plano do Serviço de Aplicativo existente, mas criando um Aplicativo Web e um Registro de Canais de Bot. 

> [!NOTE]
> Este comando define a ID e o nome de exibição do bot. O parâmetro `botId` deve ser globalmente exclusivo e será usado como a ID imutável do bot. O nome de exibição do bot é mutável.

```cmd
az group deployment create --name "<name-of-deployment>" --resource-group "<name-of-resource-group>" --template-file "template-with-preexisting-rg.json" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" newWebAppName="<name-of-web-app>" existingAppServicePlan="<name-of-app-service-plan>" appServicePlanLocation="<location>"
```

**Opção 2: Plano do Serviço de Aplicativo novo**

Nesse caso, estamos criando o Plano do Serviço de Aplicativo, o Aplicativo Web e o Registro de Canais de Bot. 

```cmd
az group deployment create --name "<name-of-deployment>" --resource-group "<name-of-resource-group>" --template-file "template-with-preexisting-rg.json" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" newWebAppName="<name-of-web-app>" newAppServicePlanName="<name-of-app-service-plan>" appServicePlanLocation="<location>"
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| Nome | Nome amigável para a implantação. |
| resource-group | Nome do grupo de recursos do Azure. |
| arquivo de modelo | O caminho para o modelo ARM. Você pode usar o arquivo `template-with-preexisting-rg.json` fornecido na pasta `deploymentTemplates` do projeto. |
| location |Local. Valores de: `az account list-locations`. Você pode configurar o local padrão usando `az configure --defaults location=<location>`. |
| parâmetros | Forneça os valores dos parâmetros de implantação. O valor `appId` que você obteve na execução do comando `az ad app create`. `appSecret` é a senha que você forneceu na etapa anterior. O parâmetro `botId` deve ser globalmente exclusivo e será usado como a ID imutável do bot. Ele também será usado para configurar o nome de exibição do bot, que é mutável. `newWebAppName` é o nome do Aplicativo Web que você está criando. `newAppServicePlanName` é o nome do Plano do Serviço de Aplicativo. `newAppServicePlanLocation` é o local do Plano do Serviço de Aplicativo. |
