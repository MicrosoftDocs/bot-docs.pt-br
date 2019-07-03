---
ms.openlocfilehash: 117f95799df0abbe957000d4979b10f05baf262c
ms.sourcegitcommit: a295a90eac461f8b96770dd902ba44919acf33fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67405530"
---
Antes de iniciar a implantação, verifique se você tem a versão mais recente da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e da [CLI do dotnet](https://dotnet.microsoft.com/download). Se você não tiver a CLI do dotnet, instale-a usando a opção Tempo de Execução do .Net Core no link fornecido acima. 

### <a name="login-to-azure-cli-and-set-your-subscription"></a>Faça logon na CLI do Azure e configure sua assinatura
Você já criou e testou um bot localmente, e agora deseja implantá-lo no Azure. Abra um prompt de comando para fazer logon no portal do Azure.

```cmd
az login
```
### <a name="set-the-subscription"></a>Definir a assinatura

Defina a assinatura padrão que será usada.

```cmd
az account set --subscription "<azure-subscription>"
```

Se você não tiver certeza de qual assinatura usar para implantar o bot, poderá exibir a lista de assinaturas de sua conta usando o comando `az account list`.

Navegue até a pasta do bot.
`cd <local-bot-folder>`

### <a name="create-a-web-app-bot-in-azure"></a>Criar um Bot de Aplicativo Web no Azure 

Se ainda não tiver um, crie um grupo de recursos no qual publicar seu bot local:

```cmd
az group create --name <resource-group-name> --location <geographic-location> --verbose
```

| Opção     | DESCRIÇÃO |
|:-----------|:---|
| Nome     | Um nome exclusivo para o grupo de recursos. NÃO inclua espaços ou sublinhados no nome. |
| location | Localização geográfica usada para criar o grupo de recursos. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. Use `az account list-locations` para obter uma lista de locais. |

Em seguida, crie o recurso de bot no qual você publicará seu bot. Isso provisionará os recursos necessários no Azure e criará um aplicativo Web do bot que você substituirá pelo seu bot local. 

Antes de continuar, leia as instruções que se aplicam a você com base no tipo de conta de email usada para fazer logon no Azure.

#### <a name="msa-email-account"></a>Conta de email MSA
Se você estiver usando uma conta de email MSA, será preciso criar a ID do aplicativo e a senha de aplicativo no Portal de Registro de Aplicativos para uso com o comando `az bot create`.
1. Acesse o [**Portal de Registro de Aplicativos**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
1. Clique em **Adicionar um aplicativo** para registrar seu aplicativo, crie a **ID do aplicativo** e utilize a opção **Gerar nova senha**. Se você já tiver um aplicativo e uma senha, mas não se lembra da senha, você precisará gerar uma nova senha na seção Segredos do aplicativo.
1. Salve a ID do aplicativo e a nova senha que você acabou de gerar, para que você possa usá-las com o comando `az bot create`.  

```cmd
az bot create --kind webapp --name <bot-name-in-azure> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name> --appid "<application-id>" --password "<application-password>" --verbose
```

| Opção | DESCRIÇÃO |
|:---|:---|
| Nome | Um nome exclusivo que é usado para implantar o bot no Azure. Ele pode ser o mesmo nome do seu bot local. NÃO inclua espaços ou sublinhados no nome. |
| location | Localização geográfica usada para criar recursos do serviço do bot. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. |
| resource-group | Nome do grupo de recursos no qual o bot será criado. Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| appid | A ID da conta Microsoft (ID da MSA) a ser usada com o bot. |
| Senha | A senha da conta Microsoft (MSA) para o bot. |

#### <a name="business-or-school-account"></a>Conta comercial ou de estudante

```cmd
az bot create --kind webapp --name <bot-name-in-azure> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name>
```
| Opção | DESCRIÇÃO |
|:---|:---|
| Nome | Um nome exclusivo que é usado para implantar o bot no Azure. Ele pode ser o mesmo nome do seu bot local. NÃO inclua espaços ou sublinhados no nome. |
| location | Localização geográfica usada para criar recursos do serviço do bot. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. |
| lang | O idioma a ser usado para criar o bot: `Csharp` ou `Node`; o padrão é `Csharp`. |
| resource-group | Nome do grupo de recursos no qual o bot será criado. Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

#### <a name="update-appsettingsjson-or-env-file"></a>Atualizar o arquivo appsettings.json ou .env
Depois que o bot é criado, você deve ver as seguintes informações exibidas na janela do console: 

```JSON
{
  "appId": "as234-345b-4def-9047-a8a44b4s",
  "appPassword": "34$#w%^$%23@334343",
  "endpoint": "https://mybot.azurewebsites.net/api/messages",
  "id": "mybot",
  "name": "mybot",
  "resourceGroup": "botresourcegroup",
  "serviceName": "mybot",
  "subscriptionId": "234532-8720-5632-a3e2-a1qw234",
  "tenantId": "32f955bf-33f1-43af-3ab-23d009defs47",
  "type": "abs"
}
```

Você precisará copiar os valores `appId` e `appPassword` e colá-los no arquivo appsettings.json ou .env. Por exemplo:

```JSON
{
  MicrosoftAppId: "as234-345b-4def-9047-a8a44b4s",
  MicrosoftAppPassword: "34$#w%^$%23@334343"
}
```
Observe que se o arquivo appsettings.json ou .env tiver chaves adicionais para outros serviços provisionados para o bot, essas entradas não deverão ser excluídas.

Salve o arquivo.

Em seguida, dependendo da linguagem de programação (**C#** ou **JS**) usada para criar o bot, siga as etapas que se aplicam a você.

**Bot C#:** 

Abra um prompt de comando e navegue até a pasta do projeto. Execute os comandos a seguir na linha de comando.

| Tarefa | Comando |
|:-----|:--------|
| 1. Restaurar as dependências do projeto | `dotnet restore`|
| 2. Compilar o projeto     | `dotnet build` |
| 3. Compactar arquivos de projeto | Use um utilitário para compactar os arquivos de projeto. Vá para a pasta que contém o arquivo .csproj e selecione todos os arquivos e pastas nesse nível para criar a pasta compactada. |
| 4. Definir a configuração da implantação do build | `az webapp config appsettings set --resource-group <resource-group-name> --name <bot-name> --settings SCM_DO_BUILD_DEPLOYMENT=false`|
| 5. Definir os argumentos do gerador de script | `az webapp config appsettings set --resource-group <resource-group-name> --name <bot-name> --settings SCM_SCRIPT_GENERATOR_ARGS="--aspNetCore mybot.csproj"`|

**Bot JS:**
1. Baixe o web.config [aqui](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps) e salve-o em sua pasta de projeto. 
1. Edite o arquivo e substitua todas as ocorrências de "server.js" por "index.js". 
1. Salve o arquivo.

Abra um prompt de comando e navegue até a pasta do projeto. Execute os comandos a seguir na linha de comando.

| Tarefa | Comando |
|:-----|:--------|
| 1. Instalar os módulos de nó | `npm install` |
| 2. Compactar arquivos de projeto | Use um utilitário para compactar os arquivos de projeto. Vá para a pasta que contém o arquivo .csproj e selecione todos os arquivos e pastas nesse nível para criar a pasta compactada. |
| 3. Definir a configuração da implantação do build | `az webapp config appsettings set --resource-group <resource-group-name> --name <bot-name> --settings SCM_DO_BUILD_DEPLOYMENT=false`|
