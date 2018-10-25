---
title: Personalização de Bot do Enterprise | Microsoft Docs
description: Saiba como personalizar o Bot criado pelo Modelo do Enterprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b9c8a0bc04cfcf96f6c81b624464e9698eab1699
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998944"
---
# <a name="enterprise-bot-template---customize-your-bot"></a>Modelo de Bot do Enterprise: personalizar seu Bot

> [!NOTE]
> Este tópico aplica-se à versão v4 do SDK. 

## <a name="net"></a>.NET
Depois de ter implantado e testado se o Modelo de Bot do Enterprise funciona de ponta a ponta conforme listado nas instruções [aqui](bot-builder-enterprise-template-deployment.md), você poderá personalizar o bot facilmente segundo seu cenário e suas necessidades. O objetivo do modelo é fornecer uma base sólida sobre a qual desenvolver sua experiência de conversação.

## <a name="project-structure"></a>Estrutura do Projeto

A estrutura de pastas do Bot é mostrada abaixo e representa nossa recomendação de melhor prática para estruturar seu projeto de Bot e processar as mensagens de entrada.

    | - YourBot.bot         // The .bot file containing all of your Bot configuration including dependencies
    | - README.md           // README file containing links to documentation
    | - Program.cs          // Default Program.cs file
    | - Startup.cs          // Core Bot Initialisation including Bot Configuration LUIS, Dispatcher, etc. 
    | - <BOTNAME>State.cs   // The Root State class for your Bot
    | - appsettings.json    // References above .bot file for Configuration information. App Insights key
    | - CognitiveModels     
        | - LUIS            // .LU file containing base conversational intents (Greeting, Help, Cancel)
        | - QnA             // .LU file containing example QnA items
    | - DeploymentScripts   // msbot clone recipe for deployment
    | - Dialogs             // All Bot dialogs sit under this folder
        | - Main            // Root Dialog for all messages
            | - MainDialog.cs       // Dialog Logic
            | - MainResponses.cs    // Dialog responses
            | - Resources           // Adaptive Card JSON, Resource File
        | - Onboarding
            | - OnboardingDialog.cs       // Onboarding dialog Logic
            | - OnboardingResponses.cs    // Onboarding dialog responses
            | - OnboardingState.cs        // Localised dialog state
            | - Resources                 Resource File
        | - Cancel
        | - Escalate
        | - Signin
    | - Middleware          // Telemetry, Content Moderator
    | - ServiceClients      // SDK libraries, example GraphClient provided for Auth example
   
## <a name="update-introduction-message"></a>Atualizar Mensagem de Apresentação

A mensagem de apresentação usa um [Cartão Adaptável](https://www.adaptivecards.io). Para personalizar a apresentação para o bot, encontre o arquivo JSON na pasta Dialogs/Main/Resources chamada ```Intro.json```. Use o [Visualizador do Cartão Adaptável](http://adaptivecards.io/visualizer) para modificar o cartão e atender às necessidades do Bot.

## <a name="update-bot-responses"></a>Atualizar Respostas do Bot

Cada caixa de diálogo no projeto tem um conjunto de respostas armazenadas em arquivos de recurso de suporte (.resx). Podem ser encontradas dentro da pasta Recursos em cada caixa de diálogo.

É possível alterar as respostas no editor de recursos do Visual Studio, como mostrado abaixo, para ajustar como seu bot responde.

![Personalizando as Respostas do Bot](media/enterprise-template/EnterpriseBot-CustomisingResponses.png)

Essa abordagem dá suporte a respostas em vários idiomas usando a abordagem de localização do arquivo de recurso padrão. Encontre mais informações [aqui.](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-2.1)

## <a name="updating-your-cognitive-models"></a>Atualizando seus Modelos Cognitivos

Há dois Modelos Cognitivos incluídos no Modelo do Enterprise por padrão: um exemplo de perguntas frequentes sobre a Base de Conhecimento do QnAMaker e um modelo do LUIS para intenções gerais (Saudação, Ajuda, Cancelar etc.). Esses modelos podem ser personalizados para atender às suas necessidades. Você também pode adicionar novos modelos de LUIS e bases de conhecimento do QnAMaker para expandir os recursos do bot.

### <a name="updating-an-existing-luis-model"></a>Atualizando um modelo do LUIS existente
Para atualizar um modelo do LUIS existente para o Modelo do Enterprise, execute estas etapas:
1. Faça as alterações no modelo do LUIS no [Portal do LUIS](http://luis.ai) ou use as ferramentas da CLI [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) e [Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS). 
2. Execute o seguinte comando para atualizar o modelo de Expedição e refletir suas alterações (isso garante o roteamento adequado das mensagens):
```shell
    dispatch refresh --bot "YOURBOT.bot" --secret YOURSECRET
```
3. Execute o seguinte comando a partir da raiz do projeto de cada modelo atualizado para atualizar as classes LuisGen associadas: 
```shell
    luis export version --appId [LUIS_APP_ID] --versionId [LUIS_APP_VERSION] --authoringKey [YOUR_LUIS_AUTHORING_KEY] | luisgen --cs [CS_FILE_NAME] -o "\Dialogs\Shared\Resources"
```

### <a name="updating-an-existing-qnamaker-knowledge-base"></a>Atualizando uma Base Conhecimento existente do QnAMaker
Para atualizar uma base de conhecimento do QnAMaker existente, execute as seguintes etapas:
1. Faça alterações na Base de Conhecimento do QnAMaker por meio das ferramentas da CLI [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) e [QnAMaker](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker) ou pelo [Portal do QnAMaker](https://qnamaker.ai).
2. Execute o seguinte comando para atualizar o modelo de Expedição e refletir suas alterações (isso garante o roteamento adequado das mensagens):
```shell
    dispatch refresh --bot "YOURBOT.bot" --secret YOURSECRET
```

### <a name="adding-a-new-luis-model"></a>Adicionando um novo modelo do LUIS

Em cenários em que você deseja adicionar um novo modelo do LUIS ao projeto, é necessário atualizar a configuração do Bot e o Dispatcher para ter certeza de que ele está ciente do modelo adicional. 
1. Crie o modelo do LUIS por meio das ferramentas da CLI LuDown/LUIS ou pelo portal do LUIS.
2. Execute o seguinte comando para conectar o novo aplicativo LUIS ao seu arquivo .bot:
```shell
    msbot connect luis --appId [LUIS_APP_ID] --authoringKey [LUIS_AUTHORING_KEY] --subscriptionKey [LUIS_SUBSCRIPTION_KEY] 
```
3. Adicione esse novo modelo do LUIS ao Dispatcher com o comando a seguir.
```shell
    dispatch add -t luis -id YOUR_LUIS_APPID -bot "YOURBOT.bot" -secret YOURSECRET
```
4. Atualize o modelo de expedição para refletir as alterações do modelo do LUIS com o comando a seguir.
```shell
    dispatch refresh -bot "YOURBOT.bot" -secret YOURSECRET
```

## <a name="adding-a-new-dialog"></a>Adicionando uma nova caixa de diálogo 

Para adicionar uma nova caixa de diálogo ao Bot, você precisa primeiro criar uma nova Pasta em Caixas de Diálogo e fazer com que essa classe derive de `EnterpriseDialog`. É preciso conectar a infraestrutura da Caixa de Diálogo. A caixa de diálogo Integração mostra um exemplo simples que você pode consultar e um fragmento é mostrado abaixo, junto com uma visão geral das etapas.

- Adicionar uma caixa de diálogo de cascata ao construtor
- Definir as etapas da cascata
- Criar as etapas de sua cascata
- Chamar AddDialog passando sua cascata
- Chamar AddDialog passando as solicitações usadas na cascata
- Definir InitialDialogId como a primeira caixa de diálogo que você deseja que o componente execute

```
InitialDialogId = nameof(OnboardingDialog);

var onboarding = new WaterfallStep[]
{
    AskForName,
    AskForEmail,
    AskForLocation,
    FinishOnboardingDialog,
};

AddDialog(new WaterfallDialog(InitialDialogId, onboarding));
AddDialog(new TextPrompt(NamePrompt));
AddDialog(new TextPrompt(EmailPrompt));
AddDialog(new TextPrompt(LocationPrompt));
```

Em seguida, você precisa criar o Gerenciador de Modelos para lidar com as respostas. Crie uma nova classe e derive de TemplateManager; um exemplo é fornecido no arquivo OnboardingResponses.cs e um fragmento é mostrado abaixo.

```
public const string _namePrompt = "namePrompt";
public const string _haveName = "haveName";
public const string _emailPrompt = "emailPrompt";
      
private static LanguageTemplateDictionary _responseTemplates = new LanguageTemplateDictionary
{
    ["default"] = new TemplateIdMap
    {
        {
            _namePrompt,
            (context, data) => OnboardingStrings.NAME_PROMPT
        },
        {
            _haveName,
            (context, data) => string.Format(OnboardingStrings.HAVE_NAME, data.name)
        },
        {
            _emailPrompt,
            (context, data) => OnboardingStrings.EMAIL_PROMPT
        },
```

Para renderizar as respostas, você pode usar uma instância do Gerenciador de Modelos para acessar essas respostas usando `ReplyWith` ou `RenderTemplate` no caso das solicitações. Os exemplos são mostrados abaixo.

```
Prompt = await _responder.RenderTemplate(sc.Context, "en", OnboardingResponses._namePrompt),
await _responder.ReplyWith(sc.Context, OnboardingResponses._haveName, new { name });
```

A última parte da infraestrutura da Caixa de Diálogo é a criação de uma classe State no escopo de sua caixa de diálogo apenas. Crie uma nova classe e verifique se ela deriva de `DialogState`

Depois da caixa de diálogo estar concluída, você precisará adicioná-la ao componente `MainDialog` usando `AddDialog`. Para usar a nova caixa de diálogo, chame `dc.BeginDialogAsync()` de dentro do método `RouteAsync`, disparando com a intenção de LUIS apropriada, se for o caso.

## <a name="conversational-insights-using-powerbi-dashboard-and-application-insights"></a>Insights de conversas usando o painel do PowerBI e o Application Insights
- Para começar a obter insights de conversas, prossiga para [Configurar análise de conversação com o painel do PowerBI](bot-builder-enterprise-template-powerbi.md).
