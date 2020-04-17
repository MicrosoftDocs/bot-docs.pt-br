---
ms.openlocfilehash: 4cee4c59fc7baa4d9aa18b573f7aebce8e99d1bb
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77479236"
---
Nesta etapa, você criará um serviço de aplicativo de bot que define a fase de implantação para o bot. Você usará um modelo ARM, um novo plano de serviço e um novo grupo de recursos.

Na saída JSON resultante, copie o valor numérico do campo **id** para usar como o valor para a **id da assinatura do registro** na próxima etapa.

> [!NOTE]
> A conclusão dessa etapa pode levar alguns minutos.

```cmd
az deployment create --template-file "<path-to-template-with-new-rg.json" --location <region-location-name> --parameters appId="<app-id-from-previous-step>" appSecret="<password-from-previous-step>" botId="<id or bot-app-service-name>" botSku=F0 newAppServicePlanName="<new-service-plan-name>" newWebAppName="<bot-app-service-name>" groupName="<new-group-name>" groupLocation="<region-location-name>" newAppServicePlanLocation="<region-location-name>" --name "<bot-app-service-name>"
```

| Opção   | Descrição |
|:---------|:------------|
| name | O nome de exibição a ser usado para o registro de canais do bot. O padrão é o valor do parâmetro `botId`.|
| arquivo de modelo | O caminho para o modelo ARM. Normalmente, o arquivo `template-with-new-rg.json` é fornecido na pasta `deploymentTemplates` do projeto do bot. Este é um caminho para um arquivo de modelo existente. Pode ser um caminho absoluto ou relativo ao diretório atual. Todos os modelos de bot geram arquivos de modelo ARM.|
| local |Local. Valores de: `az account list-locations`. Você pode configurar o local padrão usando `az configure --defaults location=<location>`. |
| parâmetros | Parâmetros de implantação, fornecidos como uma lista de pares chave=valor. Insira os seguintes valores de parâmetro:

- `appId` – o valor de *id do aplicativo* gerado pela etapa anterior.
- `appSecret` – a senha que você forneceu na etapa anterior.
- `botId` – um nome para o recurso de Registro de Canais do Bot a ser criado. Deve ser globalmente exclusivo. É usado como a ID do bot imutável. Também é usado como o nome de exibição padrão, que é mutável.
- `botSku` – o tipo de preço e pode ser F0 (Gratuito) ou S1 (Standard).
- `newAppServicePlanName` – o nome do novo plano do serviço de aplicativo.
- `newWebAppName` – um nome para o serviço de aplicativo de bot.
- `groupName` – um nome para o novo grupo de recursos.
- `groupLocation` – o local do grupo de recursos do Azure.
- `newAppServicePlanLocation` – o local do plano do serviço de aplicativo.