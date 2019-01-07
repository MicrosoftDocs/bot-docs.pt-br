---
title: Implantar seu bot usando a CLI do Azure | Microsoft Docs
description: Implante seu bot na nuvem do Azure.
keywords: deploy bot, azure deploy, publish bot, az deploy bot, visual studio deploy bot, msbot publish, msbot clone
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 12/14/2018
ms.openlocfilehash: 19960940a40fa291534bc1f88290bc6a7da109e0
ms.sourcegitcommit: 8c10aa7372754596a3aa7303a3a893dd4939f7e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654331"
---
# <a name="deploy-your-bot-using-azure-cli"></a>Implantar seu bot usando a CLI do Azure

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Depois de ter criado e testado seu bot localmente, é possível implantá-lo no Azure para torná-lo acessível em qualquer lugar. Implantar seu bot no Azure envolve pagar pelos serviços que você usa. O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/en-us/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

Neste artigo, mostraremos como implantar os bots de C# e de JavaScript para o Azure usando a CLI do `az` e do `msbot`. Seria útil ler este artigo antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.


## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalar a versão mais recente da [ferramenta de CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
- Instale a versão mais recente da extensão `botservice` para a ferramenta `az`.
  - Primeiro, remova a versão antiga usando o comando `az extension remove -n botservice`. Em seguida, use o comando `az extension add -n botservice` para instalar a versão mais recente.
- Instale a versão mais recente da ferramenta [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot).
- Instale a versão mais recente do [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Instale e configure o [ngrok](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-%28ngrok%29).
- Conhecimento do arquivo [.bot](v4sdk/bot-file-basics.md).

## <a name="deploy-javascript-and-c-bots-using-az-cli"></a>Implantar os bots de JavaScript e C# usando a CLI do Azure

Você já criou e testou um bot localmente, e agora deseja implantá-lo no Azure. Essas etapas pressupõem que você tenha criado os recursos do Azure necessários.

Abra um prompt de comando para fazer logon no portal do Azure.

```cmd
az login
```

Uma janela do navegador será aberta, permitindo que você entre.

### <a name="set-the-subscription"></a>Definir a assinatura

Defina a assinatura padrão que será usada.

```cmd
az account set --subscription "<azure-subscription>"
```

Se você não tiver certeza de qual assinatura usar para implantar o bot, você pode exibir a lista de `subscriptions` para sua conta usando o comando `az account list`.

Navegue até a pasta do bot.

```cmd
cd <local-bot-folder>
```

### <a name="create-a-web-app-bot"></a>Criar um bot de aplicativo Web

Crie o recurso de bot no qual você publicará seu bot.

Antes de continuar, leia as instruções que se aplicam a você com base no tipo de conta de email usada para fazer logon no Azure.

#### <a name="msa-email-account"></a>Conta de email MSA

Se você estiver usando uma conta de email [MSA](https://en.wikipedia.org/wiki/Microsoft_account), será preciso criar a ID do aplicativo e a senha de aplicativo no Portal de Registro de Aplicativos para usar com o comando `az bot create`.

1. Acesse o [**Portal de Registro de Aplicativos**](https://apps.dev.microsoft.com/).
1. Clique em **Adicionar um aplicativo** para registrar seu aplicativo, crie a **ID do aplicativo** e utilize a opção **Gerar nova senha**. Se você já tiver um aplicativo e uma senha, mas não se lembra da senha, você precisará gerar uma nova senha na seção Segredos do aplicativo.
1. Salve a ID do aplicativo e a nova senha que você acabou de gerar, para que você possa usá-las com o comando `az bot create`.  

```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name> --appid "<application-id>" --password "<application-password>" --verbose
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | Um nome exclusivo que é usado para implantar o bot no Azure. Ele pode ser o mesmo nome do seu bot local. NÃO inclua espaços ou sublinhados no nome. |
| --location | Localização geográfica usada para criar recursos do serviço do bot. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. |
| --lang | O idioma a ser usado para criar o bot: `Csharp` ou `Node`; o padrão é `Csharp`. |
| --resource-group | Nome do grupo de recursos no qual o bot será criado. Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --appid | A ID da conta Microsoft (ID da MSA) a ser usada com o bot. |
| --password | A senha da conta Microsoft (MSA) para o bot. |

#### <a name="business-or-school-account"></a>Conta comercial ou de estudante

```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name>
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | Um nome exclusivo que é usado para implantar o bot no Azure. Ele pode ser o mesmo nome do seu bot local. NÃO inclua espaços ou sublinhados no nome. |
| --location | Localização geográfica usada para criar recursos do serviço do bot. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. |
| --lang | O idioma a ser usado para criar o bot: `Csharp` ou `Node`; o padrão é `Csharp`. |
| --resource-group | Nome do grupo de recursos no qual o bot será criado. Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

### <a name="download-the-bot-from-azure"></a>Baixar o bot a partir do Azure

Em seguida, baixe o bot que você acabou de criar. Esse comando criará um subdiretório no save-path; no entanto, o caminho especificado já deve existir.

```cmd
az bot download --name <bot-resource-name> --resource-group <resource-group-name> --save-path "<path>"
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | O nome do bot no Azure. |
| --resource-group | Nome do grupo de recursos no qual o bot está localizado. |
| --save-path | Um diretório existente para onde o código do bot será baixado. |

### <a name="decrypt-the-downloaded-bot-file"></a>Descriptografar o arquivo .bot baixado

As informações confidenciais no arquivo .bot são criptografadas.

Obtenha a chave de criptografia.

1. Faça logon no [Portal do Azure](http://portal.azure.com/).
1. Abra o recurso de Bot do aplicativo Web para o seu bot.
1. Abra as **Configurações de Aplicativo** do seu bot.
1. Na janela **Configurações do Aplicativo**, role para baixo até **Configurações do aplicativo**.
1. Localize o **botFileSecret** e copie seu valor.

Descriptografe o arquivo .bot.

```cmd
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --bot | O caminho relativo para o arquivo .bot baixado. |
| --secret | A chave de criptografia. |

### <a name="use-the-downloaded-bot-file-in-your-project"></a>Usar o arquivo .bot baixado no seu projeto

Copie o arquivo .bot descriptografado para o diretório que contém seu projeto de bot local.

Atualize seu bot para usar esse novo arquivo .bot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em **appsettings.json**, atualize a propriedade **botFilePath** para apontar para o novo arquivo .bot.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Em **.env**, atualize a propriedade **botFilePath** para apontar para o novo arquivo .bot.

---

### <a name="update-the-bot-file"></a>Atualize o arquivo .bot

Se o seu bot usar os serviços Expedição, QnA Maker ou LUIS, você precisará adicionar referências a eles no seu arquivo .bot. Caso contrário, ignore essa etapa.

1. Abra o seu bot no Bot Framework Emulator, usando o novo arquivo .bot. O bot não precisa estar em execução localmente.
1. No painel **GERENCIADOR DO BOT**, expanda a seção **SERVIÇOS**.
1. Para adicionar referências aos aplicativos LUIS, clique no sinal de adição (+) à direita de **SERVIÇOS**.
   1. Selecione **Adicionar Reconhecimento Vocal (LUIS)**.
   1. Caso você seja solicitado a fazer logon na sua conta do Azure, faça-o.
   1. Ela apresenta uma lista de aplicativos LUIS aos quais você tem acesso. Selecione aqueles para o seu bot.
1. Para adicionar referências a uma base de dados de conhecimento do QnA Maker, clique no sinal de adição (+) à direita de **SERVIÇOS**.
   1. Selecione **Adicionar QnA Maker**.
   1. Caso você seja solicitado a fazer logon na sua conta do Azure, faça-o.
   1. Ela apresenta uma lista de bases de conhecimento às quais você tem acesso. Selecione aquelas para o seu bot.
1. Para adicionar referências aos modelos de Expedição, clique no sinal de adição (+) à direita de **SERVIÇOS**.
   1. Selecione **Adicionar Expedição**.
   1. Caso você seja solicitado a fazer logon na sua conta do Azure, faça-o.
   1. Ela apresenta uma lista de modelos de Expedição aos quais você tem acesso. Selecione aqueles para o seu bot.

### <a name="test-your-bot-locally"></a>Testar o bot localmente

Neste ponto, seu bot deve funcionar da mesma forma que funcionou com o arquivo .bot antigo. Certifique-se de que ele funcione conforme o esperado com o novo arquivo .bot.

### <a name="publish-your-bot-to-azure"></a>Publicar seu bot no Azure

<!-- TODO: re-encrypt your .bot file? -->

Publique seu bot local no Azure. Esta etapa pode demorar um pouco.

```cmd
az bot publish --name <bot-resource-name> --proj-file "<project-file-name>" --resource-group <resource-group-name> --code-dir <directory-path> --verbose --version v4
```

<!-- Question: What should --proj-file be for a Node project? -->

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | O nome do recurso do bot no Azure. |
| --proj-file | O nome do arquivo de projeto de inicialização (sem o .csproj) que precisa ser publicado. Por exemplo:  EnterpriseBot. |
| --resource-group | Nome do grupo de recursos. |
| --code-dir | O diretório de onde será carregado o código do bot. |

Ao concluir com uma mensagem de “Implantação bem-sucedida!”, seu bot está implantado no Azure.

<!-- TODO: If we tell them to re-encrypt, this step is not necessary. -->

Apague a configuração da chave de criptografia.

1. Faça logon no [Portal do Azure](http://portal.azure.com/).
1. Abra o recurso de Bot do aplicativo Web para o seu bot.
1. Abra as **Configurações do Aplicativo** do seu bot.
1. Na janela **Configurações do Aplicativo**, role para baixo até **Configurações do aplicativo**.
1. Localize o **botFileSecret** e o exclua.

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
