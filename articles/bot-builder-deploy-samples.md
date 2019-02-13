---
title: Implantar bots a partir do repositório botbuilder-samples | Microsoft Docs
description: Implante seu bot na nuvem do Azure.
keywords: deploy bot, azure deploy, publish bot, az deploy bot, visual studio deploy bot, msbot publish, msbot clone
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 01/15/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3ca8ac4bfe14ed20f11a0ab26d8102ac21e60e2b
ms.sourcegitcommit: 32615b88e4758004c8c99e9d564658a700c7d61f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55711950"
---
# <a name="deploy-bots-from-botbuilder-samples-repo"></a>Implantar bots a partir do repositório botbuilder-samples

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Neste artigo, mostraremos como implantar exemplos de C# e JavaScript que estão no repositório [botbuilder-samples](https://github.com/Microsoft/BotBuilder-Samples) no Azure.

As instruções para implantar os bots de exemplo são _diferentes_ da instruções para [implantar um bot que você pode criar com todos os recursos já provisionados no Azure](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp).

> [!IMPORTANT]
> Implantando um bot do repositório [botbuilder-samples](https://github.com/Microsoft/BotBuilder-Samples) no Azure provisionará os serviços do Azure e envolverá a pagar pelos serviços que você usa.
> O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/en-us/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

Seria útil ler este artigo uma vez antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalar o [SDK do .NET Core](https://dotnet.microsoft.com/download) >=v2.2. Use `dotnet --version` para verificar qual versão você tem.
- Instalar a versão mais recente da [ferramenta de CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Use `az --version` para verificar qual versão você tem.
- Instale a versão mais recente da ferramenta [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot).
  - Você precisará da [CLI de LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS#installation) se a operação de clonagem inclui recursos de LUIS ou de expedição.
  - Você precisará da [CLI do QnA Maker](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker#as-a-cli) se a operação de clonagem inclui recursos do QnA Maker.
- Instale o [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Instale e configure o [ngrok](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-%28ngrok%29).
- Conhecimento dos arquivos [.bot](v4sdk/bot-file-basics.md).

O msbot 4.3.2 e posterior exige a CLI do Azure versão 2.0.54 ou posterior. Se você tiver instalado a extensão botservice, remova-a com este comando.

```cmd
az extension remove --name botservice
```

### <a name="c"></a>C\#

 `msbot clone services` não carrega seus arquivos de código para o Azure, apenas a DLL e alguns outros arquivos. Você precisa compilar o exemplo antes de executar esse comando.

### <a name="service-names"></a>Nomes dos serviços

Além de implantar seu bot, o comando `msbot clone services` provisiona todos os serviços associados à sua assinatura escolhida.

Se alguma dessas combinações de nome-serviço já existir na assinatura, o comando gerará um erro e você precisará excluir sua implantação parcial antes de iniciar novamente. Isso inclui os aplicativos LUIS, as bases de conhecimento do QnA Maker e os modelos Dispatch.

## <a name="deploy-javascript-and-c-bots-using-az-cli"></a>Implantar os bots de JavaScript e C# usando a CLI do Azure

Abra um prompt de comando para fazer logon no portal do Azure.

```cmd
az login
```

Uma janela do navegador será aberta, permitindo que você entre.

### <a name="set-the-subscription"></a>Definir a assinatura

Defina a assinatura usando o seguinte comando:

```cmd
az account set --subscription "<azure-subscription>"
```

Se você não tiver certeza de qual assinatura usar para implantar o bot, você pode exibir a lista de `subscriptions` para sua conta usando o comando `az account list`.

Navegue até a pasta do bot.

```cmd
cd <local-bot-folder>
```

### <a name="clone-the-sample"></a>Clonar o exemplo

**Conta de assinatura do Azure**: Antes de continuar, leia as instruções que se aplicam a você com base no tipo de conta de email usada para fazer logon no Azure.

**Criando serviços**: O comando `msbot clone services` criará serviços do Azure para o bot.

1. Ele lista os serviços que ele criará e solicitará que você confirme a operação antes de continuar. Se você recusar, o comando é encerrado sem criar nenhum dos serviços.
1. Você se autentica no Azure antes de continuar.

**Serviços de LUIS**: Se seu bot usa LUIS ou Dispatch, você precisará incluir a chave de criação de LUIS no `msbot clone services` comando.

#### <a name="msa-email-account"></a>Conta de email do MSA

Se você estiver usando uma conta de email [MSA](https://en.wikipedia.org/wiki/Microsoft_account), você precisará criar a appId e appSecret para usar com o comando `msbot clone services`.

- Acesse o [Portal de Registro de Aplicativos](https://apps.dev.microsoft.com/). Clique em **Adicionar um aplicativo** para registrar seu aplicativo, crie a **ID do aplicativo** e utilize a opção **Gerar nova senha**.
> OBSERVAÇÃO: se a senha gerada contiver o caractere "|", ela será rejeitada pelo Azure. Para resolver isso, gere uma nova senha.
- Salve a ID do aplicativo e a nova senha que você acabou de gerar, para que você que possa usá-las com o comando `msbot clone services`.
- Para implantar, use o comando que se aplica ao seu bot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --proj-file "<your.csproj>" --name "<bot-name>" --appId "xxxxxxxx" --appSecret "xxxxxxx" --verbose --luisAuthoringKey <luis-authoring-key>`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>"   --code-dir . --name "<bot-name>" --appId "xxxxxxxx" --appSecret "xxxxxxx" --verbose --luisAuthoringKey <luis-authoring-key>`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

---

#### <a name="business-or-school-account"></a>Conta comercial ou de estudante

Se você estiver usando uma conta de email fornecida por sua empresa ou escola para fazer logon no Azure, você não precisa criar a ID do aplicativo e a senha. Para implantar, use o comando que se aplica ao seu bot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --verbose --proj-file "<your-project-file>" --name "<bot-name>" --luisAuthoringKey <luis-authoring-key>`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --verbose --code-dir . --name "<bot-name>" --luisAuthoringKey <luis-authoring-key>`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

---

#### <a name="save-the-secret-used-to-decrypt-bot-file"></a>Salvar o segredo usado para descriptografar o arquivo .bot

É importante observar que o processo de implantação cria uma _novo arquivo .bot e criptografa-o usando um segredo_. Enquanto o bot está sendo implantado, você verá a seguinte mensagem na linha de comando solicitando que você salve o segredo do arquivo .bot.

`The secret used to decrypt myAzBot.bot is:`
`hT6U1SIPQeXlebNgmhHYxcdseXWBZlmIc815PpK0WWA=`

`NOTE: This secret is not recoverable and you should save it in a safe place according to best security practices.
Copy this secret and use it to open the <file.bot> the first time.`

Salve o segredo do arquivo .bot para uso posterior. O novo arquivo .bot criptografado é usado no portal do Azure com o botFileSecret. Se você precisar alterar o nome ou o segredo do arquivo do bot mais tarde, vá até a seção **Configurações do Serviço de Aplicativo -> Configurações do Aplicativo** no portal. Observe que, no arquivo appsettings.json ou .env, o nome do arquivo do bot é atualizado com o arquivo de bot mais recente que foi criado.  

### <a name="test-your-bot"></a>Testar seu bot

No emulador, use o ponto de extremidade de produção para testar seu aplicativo. Se você deseja testá-lo localmente, verifique se o seu bot está em execução no computador local.

### <a name="to-update-your-bot-code-in-azure"></a>Para atualizar seu código de bot no Azure

NÃO USE o comando `msbot clone services` para atualizar o código de bot no Azure. Você deve usar o comando `az bot publish` conforme mostrado abaixo:

```cmd
az bot publish --name "<your-azure-bot-name>" --proj-name "<your-proj-name>" --resource-group "<azure-resource-group>" --code-dir "<folder>" --verbose --version v4
```

| Argumentos        | DESCRIÇÃO |
|----------------  |-------------|
| `name`      | O nome usado quando o bot foi implantado pela primeira vez no Azure.|
| `proj-name` | Para o C#, use o nome do arquivo de projeto de inicialização (sem o .csproj) que precisa ser publicado. Por exemplo: `EnterpriseBot`. Para o Node.js, use o ponto de entrada principal para o bot. Por exemplo, `index.js`. |
| `resource-group` | O grupo de recursos do Azure usado pelo comando `msbot clone services`.|
| `code-dir`  | Aponta para a pasta do bot local.|

## <a name="additional-resources"></a>Recursos adicionais

Quando você implanta um bot, normalmente esses recursos são criados no portal do Azure:

| Recursos      | DESCRIÇÃO |
|----------------|-------------|
| Bot do aplicativo Web | Um Serviço de Bot do Azure que é implantado em um Serviço de Aplicativo do Azure.|
| [Serviço de Aplicativo](https://docs.microsoft.com/en-us/azure/app-service/)| Permite criar e hospedar aplicativos Web.|
| [Plano do Serviço de Aplicativo](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)| Define um conjunto de recursos de computação para um aplicativo Web ser executado.|
| [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview)| Fornece ferramentas para coletar e analisar a telemetria.|
| [Conta de armazenamento](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction)| Fornece armazenamento na nuvem altamente disponível, seguro, durável, escalonável e redundante.|

Para ver a documentação sobre os comandos `az bot`, consulte o tópico de [referência](https://docs.microsoft.com/en-us/cli/azure/bot?view=azure-cli-latest).

Se você não estiver familiarizado com o grupo de recursos do Azure, consulte este tópico sobre [terminologia](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#terminology).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar implantação contínua](bot-service-build-continuous-deployment.md)
