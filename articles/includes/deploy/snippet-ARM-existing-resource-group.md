---
ms.openlocfilehash: d84a8ab18b92dfa10d7e591ffc89d38713cfe84c
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717432"
---
Nesta etapa, você cria um serviço de aplicativo de bot que define a fase de implantação para o bot. Ao usar um grupo de recursos existente, você pode empregar um plano do serviço de aplicativo existente ou criar um novo. Escolha a opção que funciona melhor para você:

- [**Opção 1: Plano do Serviço de Aplicativo existente**](#option-1-existing-app-service-plan)
- [**Opção 2: Novo Plano do Serviço de Aplicativo**](#option-2-new-app-service-plan)

> [!NOTE]
> A conclusão dessa etapa pode levar alguns minutos.

##### <a name="option-1-existing-app-service-plan"></a>**Opção 1: Plano do Serviço de Aplicativo existente**

Nesse caso, estamos usando o Plano do Serviço de Aplicativo existente, mas criando um Aplicativo Web e um Registro de Canais de Bot.

O comando abaixo define a ID e o nome de exibição do bot. O parâmetro `botId` deve ser globalmente exclusivo e será usado como a ID imutável do bot. O nome de exibição do bot é mutável.

```azurecli
az deployment group create --resource-group "<name-of-resource-group>" --template-file "<path-to-template-with-preexisting-rg.json>" --parameters appId="<app-id-from-previous-step>" appSecret="<password-from-previous-step>" botId="<id or bot-app-service-name>" newWebAppName="<bot-app-service-name>" existingAppServicePlan="<name-of-app-service-plan>" appServicePlanLocation="<region-location-name>" --name "<bot-app-service-name>"
```

##### <a name="option-2-new-app-service-plan"></a>**Opção 2: Plano do Serviço de Aplicativo novo**

Nesse caso, estamos criando o Plano do Serviço de Aplicativo, o Aplicativo Web e o Registro de Canais de Bot.

```azurecli
az deployment group create --resource-group "<name-of-resource-group>" --template-file "<path-to-template-with-preexisting-rg.json>" --parameters appId="<app-id-from-previous-step>" appSecret="<password-from-previous-step>" botId="<id or bot-app-service-name>" newWebAppName="<bot-app-service-name>" newAppServicePlanName="<name-of-app-service-plan>" appServicePlanLocation="<region-location-name>" --name "<bot-app-service-name>"
```

| Opção   | Descrição |
|:---------|:------------|
| name | O nome da implantação.|
| resource-group | Nome do grupo de recursos do Azure. |
| arquivo de modelo | O caminho para o modelo ARM. Normalmente, o arquivo `template-with-preexisting-rg.json` é fornecido na pasta `deploymentTemplates` do projeto. Este é um caminho para um arquivo de modelo existente. Pode ser um caminho absoluto ou relativo ao diretório atual. Todos os modelos de bot geram arquivos de modelo ARM.|
| local |Local. Valores de: `az account list-locations`. Você pode configurar o local padrão usando `az configure --defaults location=<location>`. |
| parâmetros | Parâmetros de implantação, fornecidos como uma lista de pares chave=valor. Insira os seguintes valores de parâmetro:

- `appId` – O valor `appId` gerado pela etapa [3.1 Criar o registro de aplicativo](../../bot-builder-deploy-az-cli.md#3-create-the-application-registration).
- `appSecret` – A senha fornecida na etapa [3.1 Criar o registro de aplicativo](../../bot-builder-deploy-az-cli.md#3-create-the-application-registration).
- `botId` – um nome para o recurso de Registro de Canais do Bot a ser criado. Deve ser globalmente exclusivo. É usado como a ID do bot imutável. Também é usado como o nome de exibição padrão, que é mutável.
- `newWebAppName` – um nome para o serviço de aplicativo de bot.
- `newAppServicePlanName` – um nome para o recurso do plano de serviço de aplicativo a ser criado.
- `appServicePlanLocation` – o local do plano do serviço de aplicativo.
