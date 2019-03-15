---
title: Implantação do Modelo de Bot do Enterprise | Microsoft Docs
description: Saiba como implantar todos os recursos de suporte do Azure para o Bot do Enterprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e888b2473269cf576fd9edda0d99a30aa6212f7b
ms.sourcegitcommit: b2245df2f0a18c5a66a836ab24a573fd70c7d272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571832"
---
# <a name="enterprise-bot-template---getting-started"></a>Enterprise Bot Template - Introdução

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

## <a name="prerequisites"></a>Pré-requisitos

Instale o seguinte:
- [Enterprise Bot Template VSIX](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4enterprise)
- [SDK do .NET Core](https://www.microsoft.com/net/download) (versão mais recente)
- [Gerenciador de pacotes do Node](https://nodejs.org/en/)
- [Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0) (versão mais recente)
- [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Ferramentas CLI do Serviço de Bot do Azure](https://github.com/Microsoft/botbuilder-tools) (versões mais recentes)
    ```shell
    npm install -g ludown luis-apis qnamaker botdispatch msbot chatdown
    ```
- [LuisGen](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/LUISGen/src/npm/readme.md)
    ```shell
    dotnet tool install -g luisgen
    ```

## <a name="create-your-bot-project"></a>Criar seu projeto de bot
1. No Visual Studio, clique em **Arquivo > Novo Projeto**.
1. Em **Bot**, escolha **Enterprise Bot Template**.

![Arquivar novo modelo do projeto](media/enterprise-template/new_project.jpg)

1. Nomeie seu projeto e clique em **Criar**.
1. Clique com o botão direito do mouse no projeto e clique em **Criar** para restaurar seus pacotes NuGet.

## <a name="deploy-your-bot"></a>Implantar seu bot

Os bots de Modelo do Enterprise exigem os seguintes serviços do Azure para operação de ponta a ponta:
- Aplicativo Web do Azure
- Conta de Armazenamento do Azure (Transcrições)
- Azure Application Insights (Telemetria)
- Azure CosmosDB (armazenamento de estado de conversa)
- Serviços Cognitivos do Azure - Reconhecimento Vocal
- Serviços Cognitivos do Azure - QnA Maker (inclui o Azure Search e o Aplicativo Web do Azure)

As seguintes etapas ajudam a implantar esses serviços utilizando os scripts de implantação fornecidos:

1. Recuperar sua Chave de Criação LUIS.
   - Leia [esta](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) página da documentação para obter o portal LUIS correto para a região da implantação. Observe que www.luis.ai refere-se à região dos EUA e uma chave de criação recuperada desse portal não funcionará com uma implantação na Europa.
   - Depois de conectado, clique em seu nome no canto superior direito.
   - Escolha **Configurações** e anote a Chave de Criação para usar posteriormente.
    
    ![Captura de tela da Chave de Criação LUIS](./media/enterprise-template/luis_authoring_key.jpg)

1. Abra um Prompt de Comando.
1. Faça logon em sua conta do Azure usando a CLI do Azure. A lista de assinaturas às quais você tem acesso está na página [Assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) do Portal do Azure.
    ```shell
    az login
    az account set --subscription "YOUR_SUBSCRIPTION_NAME"
    ```

1. Execute o comando msbot clone services para implantar os serviços e configure um arquivo .bot no projeto. **OBSERVAÇÃO: após a conclusão da implantação, anote o segredo do arquivo do bot mostrado na janela do prompt de comando para usar posteriormente.**

    ```shell
    msbot clone services --name "YOUR-BOT-NAME" --luisAuthoringKey "YOUR_AUTHORING_KEY" --folder "DeploymentScripts\LOCALE_FOLDER" --location "REGION"
    ```

    > **Observações**:
    >- o parâmetro **YOUR-BOT-NAME** deve ser **globalmente exclusivo** e só pode conter letras minúsculas, números e traços ("-").
    >- A região de implantação fornecida nessa etapa deve ser a mesma contida no portal da chave de autorização LUIS (por exemplo, westus para luis.ai ou westeurope para eu.luis.ai).
    >- Há um problema conhecido que alguns usuários podem enfrentar ao provisionar o AppId e a senha da MSA. Se você receber o erro `ERROR: Unable to provision MSA id automatically. Please pass them in as parameters and try again`, acesse [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) e crie manualmente um novo aplicativo, anotando o AppId e a Senha ou Segredo. Execute o comando msbot clone services novamente, mas forneça dois novos argumentos, `--appId` e `--appSecret`, com os valores que você acabou de recuperar. Talvez seja necessário retirar caracteres especiais da senha que possam ser interpretados pelo shell como um comando:
    >   - Para *prompt de comando do Windows*, coloque o appSecret entre aspas duplas. Por exemplo: `msbot clone services --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipe --appSecret "YOUR_APP_SECRET"`
    >   - Para *Windows PowerShell, tente passar no appSecret após o argumento --%. Por exemplo: `msbot clone services --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipe --% --appSecret "YOUR_APP_SECRET"`
    >   - Para *MacOS ou Linux*, coloque o appSecret entre aspas simples. Por exemplo: `msbot clone services --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipe --appSecret 'YOUR_APP_SECRET'`

1. Após a conclusão da implantação, atualize **appsettings.json** com o segredo do arquivo de bot. 
    
    ```
    "botFilePath": "./YOUR_BOT_FILE.bot",
    "botFileSecret": "YOUR_BOT_SECRET",
    ```
1. Execute o comando a seguir e recupere o InstrumentationKey da instância do Application Insights.
    ```
    msbot list --bot YOUR_BOT_FILE.bot --secret "YOUR_BOT_SECRET"
    ```

1. Atualize **appsettings.json** com a chave de instrumentação:

    ```
    "ApplicationInsights": {
        "InstrumentationKey": "YOUR_INSTRUMENTATION_KEY"
    }
    ```

## <a name="test-your-bot"></a>Testar seu bot

Quando concluído, execute o projeto do bot no ambiente de desenvolvimento e abra o **Bot Framework Emulator**. No Emulator, clique em **Arquivo > Abrir Bot** e navegue até o arquivo .bot no diretório.

![Captura de tela do ponto de extremidade de desenvolvimento do Emulator](./media/enterprise-template/development_endpoint.jpg)

Quando a conversa começar, você receberá uma mensagem de apresentação.

Digite ```hi``` para verificar se tudo está funcionando.

## <a name="publish-your-bot"></a>Publicar seu bot

Os testes podem ser realizados localmente de ponta a ponta. Quando você estiver pronto para implantar seu Bot no Azure e realizar mais testes, use o seguinte comando para publicar o código-fonte:

```shell
az bot publish -g YOUR-BOT-NAME -n YOUR-BOT-NAME --proj-name YOUR-BOT-NAME.csproj --version v4
```

## <a name="view-your-bot-analytics"></a>Exibir análise de bot
O Enterprise Bot Template vem com um painel pré-configurado do Power BI que conecta você ao serviço Application Insights para fornecer análise de conversa. Depois de testar o bot localmente, abra esse painel para exibir os dados. 

1. Baixe o painel do Power BI (arquivo .pbit) [aqui](https://github.com/Microsoft/AI/blob/master/solutions/analytics/ConversationalAnalyticsSample_02132019.pbit).
1. Abra o painel no [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Insira a ID de aplicativo do Application Insights (localizada no arquivo .bot).

    ![Onde encontrar a appId do AppInsights no arquivo bot](./media/enterprise-template/appInsights_appId.jpg)

1. Saiba mais sobre os recursos do painel do Power BI [aqui](https://github.com/Microsoft/AI/tree/master/solutions/analytics).

# <a name="next-steps"></a>Próximas etapas

Depois de implantar o bot pronto para uso com êxito, você pode personalizar o bot conforme seu cenário e necessidades. Prossiga com [Personalizar seu Bot](bot-builder-enterprise-template-customize.md).
