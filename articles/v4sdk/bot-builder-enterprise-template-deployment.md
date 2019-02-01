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
ms.openlocfilehash: c9a462c1ff9a1de8bc7929cb11368191aafd031a
ms.sourcegitcommit: 1ed179ae48bd2e28920a3f1e270e59d15d86fbf7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54892979"
---
# <a name="enterprise-bot-template---deploying-your-bot"></a>Modelo de Bot do Enterprise - Implantação do Bot

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se você atualizou o [.NET Core](https://www.microsoft.com/net/download) para a versão mais recente.

- Verifique se o [Gerenciador de pacotes do nó](https://nodejs.org/en/) está instalado.

- Instale as ferramentas da CLI (interface da linha de comando) do Serviço de Bot do Azure. É importante fazer isso, mesmo que você já tenha usado as ferramentas antes para garantir que tem as versões mais recentes.

```shell
npm install -g ludown luis-apis qnamaker botdispatch msbot chatdown
```

- Instale as Ferramentas de Linha de Comando (CLI) do Azure [aqui](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest). Se você já tiver a ferramenta CLI (Linha de Comando) do Serviço de Bot do Azure instalada, atualize-a para a versão mais recente desinstalando sua versão atual e instalando a nova.

> Com o msbot 4.3.2 e versões posteriores, o único pré-requisito da CLI do AZ é ter uma versão >= 2.0.53 da CLI do AZ. Se você tiver a extensão de botservice também instalada, remova-a por meio do comando “az extension remove --name botservice”.

- Instalar a ferramenta LUISGen

```shell
dotnet tool install -g luisgen
```

- Instalar a ferramenta LUISGen

```shell
dotnet tool install -g luisgen
```

## <a name="configuration"></a>Configuração

- Recuperar sua Chave de Criação LUIS
   - Analise [esta](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) página de documentação do portal correto do LUIS para região que você planeja implantar. Observe que www.luis.ai refere-se à região dos EUA e uma chave de criação recuperada desse portal não funcionará com uma implantação na Europa.
   - Depois de conectado, clique em seu nome no canto superior direito.
   - Escolha Configurações e anote a Chave de Criação para a próxima etapa.

## <a name="deployment"></a>Implantação

> Se você tiver várias assinaturas do Azure e quiser garantir que a implantação escolherá a correta, execute os seguintes comandos antes de prosseguir.

 Siga o processo de logon do navegador em sua Conta do Azure
```shell
az login
az account list
az account set --subscription "YOUR_SUBSCRIPTION_NAME"
```

Os Bots do Modelo do Enterprise exigem as seguintes dependências para as operações de ponta a ponta.
- Aplicativo Web do Azure
- Conta de Armazenamento do Azure (Transcrições)
- Azure Application Insights (Telemetria)
- Azure CosmosDB (Estado)
- Serviços Cognitivos do Azure - Reconhecimento Vocal
- Serviços Cognitivos do Azure - QnA Maker (incluindo o Azure Search e o Aplicativo Web do Azure)
- Serviços Cognitivos do Azure - Content Moderator (etapa manual opcional)

O novo projeto do Bot conta com uma receita de implantação que permite ao comando `msbot clone services` automatizar a implantação de todos os serviços acima em sua assinatura do Azure e garantir que o arquivo .bot em seu projeto seja atualizado com todos os serviços, incluindo as chaves que permitem o funcionamento perfeito do Bot. Ele também tem várias opções de configuração para os seguintes idiomas: chinês, inglês, francês, alemão, italiano e espanhol.

> Depois de implantado, analise os Tipos de Preço dos serviços criados e ajuste-os ao seu cenário.

O README.md no projeto criado contém um exemplo de linha de comando `msbot clone services` atualizado com o nome do Bot criado e uma versão genérica é mostrada abaixo. Atualize a chave de criação da etapa anterior e escolha o local do datacenter do Azure que você deseja usar (por exemplo, westus ou westeurope). Verifique se a chave de criação do LUIS, recuperada na etapa anterior, é a indicada para a região especificada abaixo (por exemplo, westus para luis.ai ou westeurope para eu.luis.ai). Por fim, faça referência à pasta do idioma que você quer usar (por exemplo, `DeploymentScripts\en`).

> **Observação** no comando msbot a seguir, o YOUR-BOT-NAME é usado para criar um nome de serviço do Azure. Os caracteres permitidos para nomes de serviço do Azure são letras minúsculas, números e traços ("-"). Não inclua sublinhados ("_") e outros caracteres não alfabéticos como parte do YOUR-BOT-NAME.

```shell
msbot clone services --name "YOUR-BOT-NAME" --luisAuthoringKey "YOUR_AUTHORING_KEY" --folder "DeploymentScripts\LOCALE_FOLDER" --location "REGION"
```

> Há um problema conhecido com alguns usuários no qual você pode enfrentar o seguinte erro ao executar a implantação `ERROR: Unable to provision MSA id automatically. Please pass them in as parameters and try again`. Nessa situação, navegue até https://apps.dev.microsoft.com e crie manualmente um novo aplicativo ao recuperar o ApplicationID e a Senha/Segredo. Execute o comando de serviços de clone msbot acima, mas forneça dois novos argumentos `appId` e `appSecret` passando os valores que você acabou de recuperar. É necessário escapar caracteres especiais na senha que podem ser interpretados pelo shell como um comando:
>   - Para *prompt de comando do Windows*, coloque o appSecret entre aspas duplas. Por exemplo, os serviços de clone msbt --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipt ***--appSecret "!|%gr%"***
>   - Para *Windows PowerShell, tente passar no appSecret após o argumento --%. Por exemplo, os serviços de clone msbot --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipt ***--% --appSecret "!|%gr%"***
>   - Para *MacOS ou Linux*, coloque o appSecret entre aspas simples. Por exemplo, os serviços de clone msbot --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipt ***--appSecret '!|%gr%'***

A ferramenta msbot descreverá o plano de implantação, incluindo o local e o SKU. Analise antes de prosseguir.

![Confirmação da Implantação](./media/enterprise-template/EnterpriseBot-ConfirmDeployment.png)

>Após a conclusão da implantação, é **de suma importância** anotar o segredo do arquivo .bot fornecido, pois ele será necessário para as etapas posteriores. Você também pode executar `msbot secret --clear --secret YOUR_BOT_SECRET` para remover o segredo de seu arquivo de bot e simplificar o desenvolvimento até que esteja pronto para liberar seu bot para produção. Execute `msbot secret --new` para gerar um novo segredo.

- Atualize seu arquivo `appsettings.json` com o nome do arquivo .bot recém-criado e o segredo do arquivo .bot.
- Execute comando a seguir, recupere o InstrumentationKey da instância do Application Insights e atualize-o no arquivo `appsettings.json`.

`msbot list --bot YOUR_BOT_FILE.bot --secret "YOUR_BOT_SECRET"`

        {
          "botFilePath": ".\\YOUR_BOT_FILE.bot",
          "botFileSecret": "YOUR_BOT_SECRET",
          "ApplicationInsights": {
            "InstrumentationKey": "YOUR_INSTRUMENTATION_KEY"
          }
        }

## <a name="testing"></a>Testando

Quando concluído, execute seu projeto do bot no ambiente de desenvolvimento e abra o Emulador do Bot Framework. No Emulator, escolha Abrir Bot no menu Arquivo e no diretório, navegue até o arquivo .bot.

Em seguida, digite ```hi``` para verificar se tudo está funcionando.

Se houver problemas com o Bot Framework Emulator, primeiro verifique se você tem o Bot Framework Emulator mais recente. Se a sua versão mais antiga do emulador não estiver atualizando corretamente, desinstale e reinstale o emulador.

## <a name="deploy-to-azure"></a>Implantar no Azure

Os testes podem ser realizados localmente de ponta a ponta. Quando você estiver pronto para implantar seu Bot no Azure e realizar mais testes, use o comando a seguir para publicar o código-fonte. Isso pode ser executado sempre que você quiser efetuar o push nas atualizações do código-fonte.

```shell
az bot publish -g YOUR-BOT-NAME -n YOUR-BOT-NAME --proj-file YOUR-BOT-NAME.csproj --sdk-version v4
```

## <a name="enabling-more-scenarios"></a>Como habilitar mais cenários

Seu projeto de Bot oferece recursos adicionais que podem ser habilitados com as seguintes etapas.

### <a name="authentication"></a>Autenticação

Para habilitar a autenticação, siga estas etapas depois de configurar um Nome de Conexão de Autenticação nas Configurações do Bot no Portal do Azure. É possível encontrar mais informações na [documentação](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-authentication?view=azure-bot-service-4.0&tabs=csharp).

Registre `AuthenticationDialog` no construtor MainDialog:
    
`AddDialog(new AuthenticationDialog(_services.AuthConnectionName));`

Adicione o seguinte ao código no local desejado para testar um fluxo de logon simples:
    
`var authResult = await dc.BeginDialogAsync(nameof(AuthenticationDialog));`

### <a name="content-moderation"></a>Moderação de conteúdo

A moderação de conteúdo pode ser usada para identificar a informação de identificação do usuário (PII) e conteúdo adulto nas mensagens enviadas ao bot. Para habilitar essa funcionalidade, acesse o portal do Azure e crie um novo serviço do Content Moderator. Colete a chave de assinatura e região para configurar o arquivo .bot. 

> Essa etapa será automatizada no futuro.

Adicione o seguinte código à parte inferior do método service.AddBot<>() na inicialização para permitir a moderação de conteúdo a cada passo. O resultado da moderação de conteúdo pode ser acessado pelo estado do bot. 
    
```
    // Content Moderation Middleware (analyzes incoming messages for inappropriate content including PII, profanity, etc.)
    var moderatorService = botConfig.Services.Where(s => s.Name == ContentModeratorMiddleware.ServiceName).FirstOrDefault();
    if (moderatorService != null)
    {
        var moderator = moderatorService as GenericService;
        var moderatorKey = moderator.Configuration["subscriptionKey"];
        var moderatorRegion = moderator.Configuration["region"];
        var moderatorMiddleware = new ContentModeratorMiddleware(moderatorKey, moderatorRegion);
        options.Middleware.Add(moderatorMiddleware);
    }
```
Acesse o resultado do middleware chamando de dentro da pilha de caixas de diálogo.
```     
    var cm = dc.Context.TurnState.Get<Microsoft.CognitiveServices.ContentModerator.Models.Screen>(ContentModeratorMiddleware.TextModeratorResultKey);
```

## <a name="customize-your-bot"></a>Personalizar seu Bot

Após verificar que você implantou com sucesso o Bot diretamente, personalize-o para atender seu cenário e necessidades. Prossiga com [Personalizar seu Bot](bot-builder-enterprise-template-customize.md).
