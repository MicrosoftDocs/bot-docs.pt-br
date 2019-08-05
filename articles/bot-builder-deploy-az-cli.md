---
title: Implantar seu bot | Microsoft Docs
description: Implante seu bot na nuvem do Azure.
keywords: implantar bot, implantar bot no azure, publicar bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.subservice: abs
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ed7c9d7a883a1d1807237b636bbb59d25df60e08
ms.sourcegitcommit: f3fda6791f48ab178721b72d4f4a77c373573e38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68671382"
---
# <a name="deploy-your-bot"></a>Implantar seu bot

[!INCLUDE [applies-to](./includes/applies-to.md)]

Neste artigo, mostraremos como implantar seu bot no Azure. Seria útil ler este artigo antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.

## <a name="prerequisites"></a>Pré-requisitos
- Caso não tenha uma assinatura do Azure, crie uma [conta](https://azure.microsoft.com/free/) antes de começar.
- Um bot CSharp, JavaScript ou TypeScript que você tenha desenvolvido em seu computador local.
- A versão mais recente da [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
- Familiaridade com [modelos ARM e a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="1-prepare-for-deployment"></a>1. Preparar para a implantação
Quando você criar um bot usando modelos do Visual Studio ou do Yeoman, o código-fonte gerado conterá uma pasta `deploymentTemplates` com modelos ARM. O processo de implantação documentado aqui usa o modelo ARM para provisionar os recursos necessários para o bot no Azure usando a CLI do Azure. 

> [!IMPORTANT]
> Com o lançamento do Bot Framework SDK 4.3, _preterimos_ o uso do arquivo .bot em favor do arquivo appsettings.JSON ou. env para gerenciamento de recursos. Para obter informações sobre como migrar as configurações do arquivo .bot para o arquivo appsettings.json ou .env, confira [gerenciando recursos do bot](v4sdk/bot-file-basics.md).

### <a name="login-to-azure"></a>Logon no Azure

Você já criou e testou um bot localmente, e agora deseja implantá-lo no Azure. Abra um prompt de comando para fazer logon no portal do Azure.

```cmd
az login
```
Uma janela do navegador será aberta, permitindo que você entre.

### <a name="set-the-subscription"></a>Definir a assinatura
Defina a assinatura padrão que será usada.

```cmd
az account set --subscription "<azure-subscription>"
```

Se você não tiver certeza de qual assinatura usar para implantar o bot, poderá exibir a lista de assinaturas de sua conta usando o comando `az account list`. Navegue até a pasta do bot.

### <a name="create-an-app-registration"></a>Crie um registro de aplicativo
Registrar o aplicativo significa que você poderá usar o Microsoft Azure AD para autenticar usuários e solicitar acesso a recursos de usuários. Seu bot precisará de um aplicativo registrado no Azure que o permita acessar o Bot Framework Service para enviar e receber mensagens autenticadas. Para criar o registro de um aplicativo por meio da CLI do Azure, execute o seguinte comando:

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| nome de exibição | O nome de exibição do aplicativo. |
| Senha | Senha do aplicativo, também conhecida como 'segredo do cliente'. A senha deve ter pelo menos 16 caracteres e conter pelo menos um caractere alfabético maiúsculo ou minúsculo e pelo menos um caractere especial|
| disponível para outros locatários| O aplicativo poderá ser usado a partir de qualquer locatário do Azure AD. Devemos usar `true` para habilitar o bot para trabalhar com os canais do Serviço de Bot do Azure.|

O comando acima gera JSON com a chave `appId`, salva o valor dessa chave para a implantação do ARM, onde ela será usada para o parâmetro `appId`. A senha fornecida será usada ao parâmetro `appSecret`.

Você pode implantar seu bot em um novo grupo de recursos ou em um grupo de recursos existente. Escolha a opção que funcione melhor para você.

# <a name="deploy-via-arm-template-with-new-resource-grouptabnewrg"></a>[Implantar por meio do modelo ARM (com **novo** Grupo de Recursos)](#tab/newrg)

### <a name="create-azure-resources"></a>Criar recursos do Azure

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

### <a name="create-azure-resources"></a>Criar recursos do Azure

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

### <a name="retrieve-or-create-necessary-iiskudu-files"></a>Recuperar ou criar os arquivos IIS/Kudu necessários

**Para bots C#**

```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

Você deve fornecer o caminho do arquivo .csproj relativo a --code-dir. Isso pode ser feito por meio do argumento --proj-file-path. O comando resolveria --code-dir e --proj-file-path com "./MyBot.csproj"

**Para bots JavaScript**

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

Esse comando buscará um arquivo web.config que é necessário para aplicativos Node.js trabalharem com o IIS nos Serviços de Aplicativos do Azure. Certifique-se de que web.config seja salvo na raiz de seu bot.

**Para bots TypeScript**

```cmd
az bot prepare-deploy --code-dir "." --lang Typescript
```

Esse comando funciona da mesma forma que o JavaScript acima, mas para um bot Typescript.

### <a name="zip-up-the-code-directory-manually"></a>Compacte o diretório de código manualmente

Com o uso da [API de implantação de zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) não configurada para implantar o código de seu bot, o comportamento do Aplicativo Web/Kudu será o seguinte:

_O Kudu assume por padrão que as implantações a partir de arquivos zip estão prontas para serem executadas e não exigem etapas de compilação adicionais durante a implantação, como npm install ou dotnet restore/dotnet publish._

Logo, é importante incluir seu código compilado e todas as dependências necessárias no arquivo zip a ser implantado no Aplicativo Web, caso contrário, seu bot não funcionará como esperado.

> [!IMPORTANT]
> Antes de compactar os arquivos do seu projeto, verifique se você está _na_ pasta correta. 
> - Para bots C#, será a pasta que tiver o arquivo .csproj. 
> - Para bots JS, será a pasta que tiver o arquivo app.js ou index.js. 
>
> Selecione todos os arquivos e compacte-os **nessa pasta**, em seguida, execute o comando ainda nessa pasta.
>
> Se o local da pasta raiz estiver incorreto, o **bot não será executado no portal do Azure**.

## <a name="2-deploy-code-to-azure"></a>2. Implantar código no Azure
Neste ponto, estamos prontos para implantar o código no aplicativo Web do Azure. Execute o seguinte comando na linha de comando para executar a implantação usando o serviço de implantação de zip por push do kudu para um aplicativo web.

```cmd
az webapp deployment source config-zip --resource-group "<new-group-name>" --name "<name-of-web-app>" --src "code.zip" 
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| resource-group | O nome do grupo de recursos no Azure criado anteriormente. |
| Nome | Nome do Aplicativo Web que você usou anteriormente. |
| src  | O caminho para o arquivo compactado que você criou. |

## <a name="3-test-in-web-chat"></a>3. Testar no Webchat

1. Em seu navegador, navegue até o [portal do Azure](https://ms.portal.azure.com).
2. No painel esquerdo, clique em **Grupos de recursos**.
3. No painel direito, pesquise por seu grupo.
4. Clique no nome do grupo.
5. Clique no link do registro de canal de bot.
6. Na folha *Registro de Canal de Bot*, clique em **Testar em Webchat**.
Como alternativa, no painel direito, clique na caixa Teste.

Para obter mais informações sobre o registro de canal, confira [registrar um bot com o Serviço de Bot](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-quickstart-registration?view=azure-bot-service-3.0).

> [!NOTE]
> Uma folha é a superfície na qual as funções de serviço ou os elementos de navegação aparecem quando selecionados.

## <a name="additional-information"></a>Informações adicionais
Implantar seu bot no Azure envolve pagar pelos serviços que você usa. O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Configurar implantação contínua](bot-service-build-continuous-deployment.md)
