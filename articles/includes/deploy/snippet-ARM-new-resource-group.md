---
ms.openlocfilehash: d8f2afba9127c1abf10f5ff2d377b1191709a0ed
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073710"
---
Nesta etapa, você criará um serviço de aplicativo de bot que define a fase de implantação para o bot. Você usará um modelo ARM, um novo plano de serviço e um novo grupo de recursos. Execute o seguinte comando da CLI do Azure para iniciar uma implantação no escopo da assinatura de um arquivo de modelo local. 

```cmd
az deployment sub create --template-file "<path-to-template-with-new-rg.json" --location <region-location-name> --parameters appId="<app-id-from-previous-step>" appSecret="<password-from-previous-step>" botId="<id or bot-app-service-name>" botSku=F0 newAppServicePlanName="<new-service-plan-name>" newWebAppName="<bot-app-service-name>" groupName="<new-group-name>" groupLocation="<region-location-name>" newAppServicePlanLocation="<region-location-name>" --name "<bot-app-service-name>"
```

> [!NOTE]
> A conclusão dessa etapa pode levar alguns minutos.


| Opção   | Descrição |
|:---------|:------------|
| name | O nome de exibição a ser usado para o registro de canais do bot. O padrão é o valor do parâmetro `botId`.|
| arquivo de modelo | O caminho para o modelo ARM. Normalmente, o arquivo `template-with-new-rg.json` é fornecido na pasta `deploymentTemplates` do projeto do bot. Este é um caminho para um arquivo de modelo existente. Pode ser um caminho absoluto ou relativo ao diretório atual. Todos os modelos de bot geram arquivos de modelo ARM.|
| local |Local. Valores de: `az account list-locations`. Você pode configurar o local padrão usando `az configure --defaults location=<location>`. |
| parâmetros | Parâmetros de implantação, fornecidos como uma lista de pares chave=valor. Insira os seguintes valores de parâmetro:

- `appId` – O valor *app id* da saída JSON gerada pela etapa [criar o registro de aplicativo](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp#3-create-the-application-registration).
- `appSecret` – A senha fornecida na etapa [criar o registro de aplicativo](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp#3-create-the-application-registration).
- `botId` – um nome para o recurso de Registro de Canais do Bot a ser criado. Deve ser globalmente exclusivo. É usado como a ID do bot imutável. Também é usado como o nome de exibição padrão, que é mutável.
- `botSku` – o tipo de preço e pode ser F0 (Gratuito) ou S1 (Standard).
- `newAppServicePlanName` – o nome do novo plano do serviço de aplicativo.
- `newWebAppName` – um nome para o serviço de aplicativo de bot.
- `groupName` – um nome para o novo grupo de recursos.
- `groupLocation` – o local do grupo de recursos do Azure.
- `newAppServicePlanLocation` – o local do plano do serviço de aplicativo.