---
title: Implantar seu bot | Microsoft Docs
description: Implante seu bot na nuvem do Azure.
keywords: implantar bot, implantar bot no azure, publicar bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 04/12/2019
ms.openlocfilehash: 4532fe55705524573de55017e633289255a20ab9
ms.sourcegitcommit: 721bb09f10524b0cb3961d7131966f57501734b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59508213"
---
# <a name="deploy-your-bot"></a>Implantar seu bot

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Depois de ter criado e testado seu bot localmente, é possível implantá-lo no Azure para torná-lo acessível em qualquer lugar. Implantar seu bot no Azure envolve pagar pelos serviços que você usa. O artigo [gerenciamento de cobrança e custos](https://docs.microsoft.com/en-us/azure/billing/) ajuda você a entender sua cobrança do Azure, monitorar o uso e os custos, e gerenciar sua conta e assinaturas.

Neste artigo, mostraremos como implantar os bots de C# e de JavaScript no Azure. Seria útil ler este artigo antes de seguir as etapas, para que você entenda tudo que está relacionado à implantação de um bot.

## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](http://portal.azure.com) antes de começar.
- Um bot [**CSharp**](./dotnet/bot-builder-dotnet-sdk-quickstart.md) ou [**JavaScript**](./javascript/bot-builder-javascript-quickstart.md) que você tenha desenvolvido em seu computador local.

## <a name="1-prepare-for-deployment"></a>1. Preparar para a implantação
O processo de implantação requer um Bot de aplicativo Web de destino no Azure para que seu bot local possa ser implantado nele. O Bot de aplicativo Web de destino e os recursos que são provisionados com ele no Azure são usados pelo seu bot local na implantação. Isso é necessário porque seu bot local não tem todos os recursos do Azure exigidos provisionados. Quando você cria um bot de aplicativo Web de destino, os seguintes recursos são provisionados para você:
-   Bot de aplicativo Web - você usará este bot para implantar seu bot local.
-   Plano do Serviço de Aplicativo - fornece os recursos que um aplicativo do Serviço de Aplicativo precisa para ser executado.
-   Serviço de Aplicativo - serviço para hospedar aplicativos Web
-   Conta de Armazenamento - contém todos os objetos de dados do Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos.

Durante a criação do Bot de aplicativo Web de destino, a ID do aplicativo e a senha também são geradas para seu bot. No Azure, a ID do aplicativo e a senha oferecem suporte para a [autenticação e autorização do serviço](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization). Você recuperará algumas dessas informações para usar em seu código de bot local. 

> [!IMPORTANT]
> A linguagem de programação do modelo de bot usado no portal do Azure deve corresponder à linguagem de programação na qual seu bot está gravado.

Se você já tiver criado um bot no Azure que gostaria de usar, a criação de um novo bot de aplicativo Web será opcional.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Clique no link **Criar recurso** localizado no canto superior esquerdo do portal do Azure e, em seguida, selecione **IA + Machine Learning > bot de Aplicativo Web**.
1. Uma nova folha será aberta com informações sobre o Bot de Aplicativo Web. 
1. Na folha **Serviço de Bot**, forneça as informações necessárias sobre seu bot.
1. Clique em **Criar** para criar o serviço e implantar o bot na nuvem. Esse processo pode levar vários minutos.

### <a name="download-the-source-code"></a>Fazer o download do código-fonte
Depois de criar o Bot de aplicativo Web de destino, será preciso baixar do código do bot no portal do Azure em seu computador local. O motivo para baixar o código é obter as referências de serviço (por exemplo, MicrosoftAppID, MicrosoftAppPassword, LUIS ou QnA) que estão no arquivo appsettings.json ou .env. 

1. Na seção **Gerenciamento do Bot**, clique em **Compilar**.
1. Clique no link **Baixar o código-fonte do Bot** no painel à direita.
1. Siga os prompts para baixar o código e, em seguida, descompacte a pasta.
    1. [!INCLUDE [download keys snippet](~/includes/snippet-abs-key-download.md)]

### <a name="update-your-local-appsettingsjson-or-env-file"></a>Atualizar seu arquivo appsettings.json ou .env local

Abra o arquivo appsettings.json ou .env que você baixou. Copie **todas** as entradas listadas nele e adicione-as ao seu arquivo appsettings.json ou .env _local_. Resolva quaisquer entradas de serviço duplicadas ou IDs de serviço duplicadas. Mantenha todas as referências de serviço adicionais das quais seu bot depende.

Salve o arquivo.

### <a name="update-local-bot-code"></a>Atualizar o código do bot local
Atualize o arquivo local Startup.cs ou index.js para usar o arquivo appsettings.json ou .env em vez de o arquivo .bot. O arquivo .bot foi preterido e estamos trabalhando para atualizar os modelos VSIX, geradores do Yeoman, amostras e documentos restantes para que todos usem o arquivo appsettings.json ou .env em vez de o arquivo .bot. Enquanto isso, você precisará fazer alterações no código do bot. 

Atualize o código para que ele leia as configurações do arquivo appsettings.json ou .env. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
No método `ConfigureServices`, use o objeto de configuração fornecido pelo ASP.NET Core, por exemplo: 

**Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```

# <a name="jstabjs"></a>[JS](#tab/js)

No JavaScript, referencie às variáveis .env fora do objeto `process.env`, por exemplo:
   
**index.js**

```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```
---

- Salve o arquivo e teste seu bot.

### <a name="setup-a-repository"></a>Configurar um repositório

Para oferecer suporte para a implantação contínua, crie um repositório git usando seu provedor de controle de origem git preferido. Confirme o código no repositório.

Verifique se a raiz do repositório tem os arquivos corretos, conforme descrito na seção [preparar seu repositório](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment#prepare-your-repository).

### <a name="update-app-settings-in-azure"></a>Atualizar configurações do aplicativo no Azure
O bot local não usa um arquivo .bot criptografado, a não ser que o portal do Azure _esteja_ configurado para usar um arquivo .bot criptografado. Você pode resolver isso removendo o **botFileSecret** armazenado nas configurações de bot do Azure.
1. Abra o recurso de **Bot do aplicativo Web** para o seu bot no portal do Azure.
1. Abra as **Configurações do Aplicativo** do seu bot.
1. Na janela **Configurações do Aplicativo**, role para baixo até **Configurações do aplicativo**.
1. Verifique se seu bot tem as entradas **botFileSecret** e **botFilePath**. Em caso positivo, exclua-as.
1. Salve as alterações.

## <a name="2-deploy-using-azure-deployment-center"></a>2. Implantar usando a Central de Implantação do Azure

Agora é preciso carregar seu código de bot no Azure. Siga as instruções no tópico [Implantação contínua do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment).

Observe que é recomendável compilar usando `App Service Kudu build server`.

Depois de configurar a implantação contínua, as alterações que você confirmar em seu repositório serão publicadas. No entanto, se adicionar serviços ao bot, você precisará adicionar entradas ao arquivo .bot para esses.

## <a name="3-test-your-deployment"></a>3. Testar a implantação

Aguarde alguns segundos após uma implantação bem-sucedida e, opcionalmente, reinicie o aplicativo Web para limpar entradas no cache. Volte para a folha de Bot do Aplicativo Web e teste usando o Webchat fornecido no portal do Azure.

## <a name="additional-resources"></a>Recursos adicionais
- [Como investigar problemas comuns com implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)

