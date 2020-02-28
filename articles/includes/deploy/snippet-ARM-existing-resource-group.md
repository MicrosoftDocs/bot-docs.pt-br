---
ms.openlocfilehash: 2559f424e9f50a760837494f87e4f33731ffb358
ms.sourcegitcommit: 4ddee4f90a07813ce570fdd04c8c354b048e22f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77479240"
---
Nesta etapa, você cria um serviço de aplicativo de bot que define a fase de implantação para o bot. Ao usar um grupo de recursos existente, você pode empregar um plano do serviço de aplicativo existente ou criar um novo. As etapas para as duas opções estão listadas abaixo.

Na saída JSON resultante, copie o valor do campo **id** para usar como o valor para a **id da assinatura do registro** na próxima etapa.

> [!NOTE]
> A conclusão dessa etapa pode levar alguns minutos.

**Opção 1: Plano do Serviço de Aplicativo existente**

Nesse caso, estamos usando o Plano do Serviço de Aplicativo existente, mas criando um Aplicativo Web e um Registro de Canais de Bot.

> [!NOTE]
> Este comando define a ID e o nome de exibição do bot. O parâmetro `botId` deve ser globalmente exclusivo e será usado como a ID imutável do bot. O nome de exibição do bot é mutável.

```cmd
az group deployment create --resource-group "<name-of-resource-group>" --template-file "<path-to-template-with-preexisting-rg.json>" --parameters appId="<app-id-from-previous-step>" appSecret="<password-from-previous-step>" botId="<id or bot-app-service-name>" newWebAppName="<bot-app-service-name>" existingAppServicePlan="<name-of-app-service-plan>" appServicePlanLocation="<region-location-name>" --name "<bot-app-service-name>"
```

**Opção 2: Plano do Serviço de Aplicativo novo**

Nesse caso, estamos criando o Plano do Serviço de Aplicativo, o Aplicativo Web e o Registro de Canais de Bot.

```cmd
az group deployment create --resource-group "<name-of-resource-group>" --template-file "<path-to-template-with-preexisting-rg.json>" --parameters appId="<app-id-from-previous-step>" appSecret="<password-from-previous-step>" botId="<id or bot-app-service-name>" newWebAppName="<bot-app-service-name>" newAppServicePlanName="<name-of-app-service-plan>" appServicePlanLocation="<region-location-name>" --name "<bot-app-service-name>"
```

| Opção   | Descrição |
|:---------|:------------|
| name | O nome de exibição a ser usado para o registro de canais do bot. O padrão é o valor do parâmetro `botId`.|
| resource-group | Nome do grupo de recursos do Azure. |
| arquivo de modelo | O caminho para o modelo ARM. Normalmente, o arquivo `template-with-preexisting-rg.json` é fornecido na pasta `deploymentTemplates` do projeto. Este é um caminho para um arquivo de modelo existente. Pode ser um caminho absoluto ou relativo ao diretório atual. Todos os modelos de bot geram arquivos de modelo ARM.|
| local |Local. Valores de: `az account list-locations`. Você pode configurar o local padrão usando `az configure --defaults location=<location>`. |
| parâmetros | Parâmetros de implantação, fornecidos como uma lista de pares chave=valor. Insira os seguintes valores de parâmetro:

- `appId` – o valor de *id do aplicativo* gerado pela etapa anterior.
- `appSecret` – a senha que você forneceu na etapa anterior.
- `botId` – um nome para o recurso de Registro de Canais do Bot a ser criado. Deve ser globalmente exclusivo. É usado como a ID do bot imutável. Também é usado como o nome de exibição padrão, que é mutável.
- `newWebAppName` – um nome para o serviço de aplicativo de bot.
- `newAppServicePlanName` – um nome para o recurso do plano de serviço de aplicativo a ser criado.
- `newAppServicePlanLocation` – o local do plano do serviço de aplicativo.