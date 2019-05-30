---
title: Tutorial sobre como criar e implantar um bot básico | Microsoft Docs
description: Saiba como criar um bot básico e, em seguida, implantá-lo no Azure.
keywords: bot de eco, implantar, azure, tutorial
author: Ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7c9bdbbf0ad41bc678476237ef6b6d1d0f872426
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66214236"
---
# <a name="tutorial-create-and-deploy-a-basic-bot"></a>Tutorial: Criar e implantar um bot básico

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Este tutorial orienta você pela criação de um bot básico com o SDK do Bot Framework e sua implantação no Azure. Se você já tiver criado um bot básico e executado-o localmente, avance para a seção [Implantar seu bot](#deploy-your-bot).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um bot de eco básico
> * Executar e interagir com ele localmente
> * Publicar seu bot

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet quickstart](~/includes/quickstart-dotnet.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript quickstart](~/includes/quickstart-javascript.md)]

---

## <a name="deploy-your-bot"></a>Implantar seu bot

### <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- O bot acima, em execução no seu computador local.
- A versão mais recente da [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest).

### <a name="1-prepare-for-deployment"></a>1. Preparar para a implantação
Quando você criar um bot usando modelos do Visual Studio ou do Yeoman, o código-fonte gerado conterá uma pasta `deploymentTemplates` com modelos ARM. O processo de implantação documentado aqui usa o modelo ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure. 

#### <a name="login-to-azure"></a>Logon no Azure

Você já criou e testou um bot localmente, e agora deseja implantá-lo no Azure. Abra um prompt de comando para fazer logon no portal do Azure.

```cmd
az login
```
Uma janela do navegador será aberta, permitindo que você entre.

#### <a name="set-the-subscription"></a>Definir a assinatura
Defina a assinatura padrão que será usada.

```cmd
az account set --subscription "<azure-subscription>"
```

Se você não tiver certeza de qual assinatura usar para implantar o bot, poderá exibir a lista de assinaturas de sua conta usando o comando `az account list`. Navegue até a pasta do bot.

#### <a name="create-an-app-registration"></a>Crie um registro de aplicativo
Registrar o aplicativo significa que você poderá usar o Microsoft Azure AD para autenticar usuários e solicitar acesso a recursos de usuários. Seu bot precisará de um aplicativo registrado no Azure que o permita acessar o Bot Framework Service para enviar e receber mensagens autenticadas. Para criar o registro de um aplicativo por meio da CLI do Azure, execute o seguinte comando:

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| nome de exibição | O nome de exibição do aplicativo. |
| Senha | Senha do aplicativo, também conhecida como 'segredo do cliente'. A senha deve ter pelo menos 16 caracteres, sendo pelo menos um caractere alfabético maiúsculo ou minúsculo e pelo menos um caractere especial. |
| disponível para outros locatários| O aplicativo pode ser usado a partir de qualquer locatário do Microsoft Azure AD.  Valores permitidos: false, true. True por padrão. Isso habilitará seu bot para trabalhar com os canais do Serviço de Bot do Azure|

O comando acima gera JSON com a chave `appId`, salva o valor dessa chave para a implantação do ARM, onde ela será usada para o parâmetro `appId`. A senha fornecida será usada ao parâmetro `appSecret`.

Você pode implantar seu bot em um novo grupo de recursos ou em um grupo de recursos existente. Escolha a opção que funcione melhor para você.

# <a name="deploy-via-arm-template-with-new-resource-grouptabnewrg"></a>[Implantar por meio do modelo ARM (com **novo** Grupo de Recursos)](#tab/newrg)

#### <a name="create-azure-resources"></a>Criar recursos do Azure

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

# <a name="deploy-via-arm-template-with-existing--resource-grouptaberg"></a>[Implantar por meio do modelo ARM (com Grupo de Recursos **existente**)](#tab/erg)

#### <a name="create-azure-resources"></a>Criar recursos do Azure

Ao usar um grupo de recursos existente, você pode empregar um Plano do Serviço de Aplicativo existente ou criar um novo. As etapas para as duas opções estão listadas abaixo. 

**Opção 1: Plano do Serviço de Aplicativo existente** 

Nesse caso, estamos usando o Plano do Serviço de Aplicativo existente, mas criando um Aplicativo Web e um Registro de Canais de Bot novos. 

_Observação: o parâmetro botId deve ser globalmente exclusivo e será usado como a ID imutável do bot. Ele também será usado para configurar o nome de exibição do bot, que é mutável._

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
| resource-group | Nome do grupo de recursos do Azure |
| arquivo de modelo | O caminho para o modelo ARM. Você pode usar o arquivo `template-with-preexisting-rg.json` fornecido na pasta `deploymentTemplates` do projeto. |
| location |Local. Valores de: `az account list-locations`. Você pode configurar o local padrão usando `az configure --defaults location=<location>`. |
| parâmetros | Forneça os valores dos parâmetros de implantação. O valor `appId` que você obteve na execução do comando `az ad app create`. `appSecret` é a senha que você forneceu na etapa anterior. O parâmetro `botId` deve ser globalmente exclusivo e será usado como a ID imutável do bot. Ele também será usado para configurar o nome de exibição do bot, que é mutável. `newWebAppName` é o nome do Aplicativo Web que você está criando. `newAppServicePlanName` é o nome do Plano do Serviço de Aplicativo. `newAppServicePlanLocation` é o local do Plano do Serviço de Aplicativo. |

---

#### <a name="retrieve-or-create-necessary-iiskudu-files"></a>Recuperar ou criar os arquivos IIS/Kudu necessários

**Para bots C#**

```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

Você deve fornecer o caminho do arquivo .csproj relativo a --code-dir. Isso pode ser feito por meio do argumento --proj-file-path. O comando resolveria --code-dir e --proj-file-path com "./MyBot.csproj"

**Para bots JavaScript**

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

Esse comando buscará um arquivo web.config que é necessário para aplicativos Node.js trabalharem com o IIS nos Serviços de Aplicativos do Azure. Certifique-se de que o web.config seja salvo na raiz de seu bot.

#### <a name="zip-up-the-code-directory-manually"></a>Compacte o diretório de código manualmente

Com o uso da [API de implantação de zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) não configurada para implantar o código de seu bot, o comportamento do Aplicativo Web/Kudu será o seguinte:

_O Kudu assume por padrão que as implantações a partir de arquivos zip estão prontas para serem executadas e não exigem etapas de compilação adicionais durante a implantação, como npm install ou dotnet restore/dotnet publish._

Logo, é importante incluir seu código compilado e todas as dependências necessárias no arquivo zip a ser implantado no Aplicativo Web, caso contrário, seu bot não funcionará como esperado.

> [!IMPORTANT]
> Antes de compactar os arquivos do seu projeto, verifique se você está _na_ pasta correta. 
> - Para bots C#, será a pasta que tiver o arquivo .csproj. 
> - Para bots JS, será a pasta que tiver o arquivo app.js ou index.js. 
>
> Selecione todos os arquivos e compacte-os nessa pasta; em seguida, execute o comando ainda nessa pasta.
>
> Se o local da pasta raiz estiver incorreto, o **bot não será executado no portal do Azure**.

### <a name="2-deploy-code-to-azure"></a>2. Implantar código no Azure
Neste ponto, estamos prontos para implantar o código no aplicativo Web do Azure. Execute o seguinte comando na linha de comando para executar a implantação usando o serviço de implantação de zip por push do kudu para um aplicativo web.

```cmd
az webapp deployment source config-zip --resource-group "<new-group-name>" --name "<name-of-web-app>" --src "code.zip" 
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| resource-group | O nome do grupo de recursos no Azure criado anteriormente. |
| Nome | Nome do Aplicativo Web que você usou anteriormente. |
| src  | O caminho para o arquivo compactado que você criou. |

### <a name="3-test-in-web-chat"></a>3. Testar no Webchat
- No portal do Azure, vá para a folha do bot do seu Aplicativo Web.
- Na seção **Gerenciamento de Bot**, clique em **Testar no Webchat**. O Serviço de Bot do Azure carregará o controle de Webchat e se conectará ao bot.
- Aguarde alguns segundos após uma implantação bem-sucedida e, opcionalmente, reinicie o aplicativo Web para limpar entradas no cache. Volte para a folha de Bot do Aplicativo Web e teste usando o Webchat fornecido no portal do Azure.

## <a name="additional-resources"></a>Recursos adicionais

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Use o QnA Maker no seu bot para responder a perguntas](bot-builder-tutorial-add-qna.md)
