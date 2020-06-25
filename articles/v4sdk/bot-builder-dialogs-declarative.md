---
title: Criar um bot usando diálogos adaptáveis declarativos
description: Saiba como criar um bot que incorpora os adaptáveis usando a abordagem declarativa no SDK do Bot Framework.
keywords: declarativo, adaptável
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/25/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2f276feb23caae812902fc7477beb9cb9128db4c
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081208"
---
# <a name="create-a-bot-using-declarative-adaptive-dialogs"></a>Criar um bot usando diálogos adaptáveis declarativos  

[!INCLUDE[applies-to](../includes/applies-to.md)]

Este artigo mostra como criar um bot que incorpora um **Diálogo adaptável** usando a abordagem declarativa.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot][concept-basics], [gerenciamento de estado][concept-state] e [biblioteca de diálogos][concept-dialogs].
- Conhecimento de [diálogos adaptáveis][concept-adaptive] e de [diálogos declarativos][concept-declarative].
- Conhecimento dos conceitos básicos de como [Criar um bot usando diálogos adaptáveis][how-to-adaptive]. Este artigo se baseia nesse conhecimento.
- Uma cópia da amostra **EchoBot** declarativa na versão prévia do [**C#** ][cs-sample] ou do [**JavaScript**][js-sample].

### <a name="preliminary-steps-to-add-a-declarative-adaptive-dialog-to-a-bot"></a>Etapas preliminares para adicionar um diálogo adaptável declarativo a um bot

Você precisa seguir essas etapas para carregar e usar um diálogo declarativo em um bot.

1. Atualize todos os pacotes NuGet do Bot Builder para a versão 4.9.x.
1. Crie os arquivos declarativos.
1. Adicione os pacotes e as referências necessários ao seu projeto de bot.
1. Registre os componentes declarativos do Bot Framework para diálogos adaptáveis.
1. Use um gerenciador de diálogos no código do bot para iniciar ou continuar o diálogo raiz em cada turno.
1. Crie um gerenciador de recursos no código de bot para carregar os recursos declarativos.

<!--
1. Update all Bot Builder NuGet packages to version 4.9.x.
1. Add the `Microsoft.Bot.Builder.Dialogs.Adaptive` package to your bot project.
1. Add the `Microsoft.Bot.Builder.Dialogs.Declarative` package to your bot project.
1. Create the declarative files.
1. Add references to declarative components.
1. Register the bot frameworks declarative components for adaptive dialogs.
1. Use a dialog manager in the bot code to start or continue the root dialog each turn.
1. Create a resource explorer in the bot code to load the declarative resources.
-->

## <a name="about-the-sample"></a>Sobre o exemplo

Esta amostra, o bot de eco declarativo do Diálogo Adaptável do Bot Framework, demonstra o uso de um [diálogo adaptável][concept-adaptive] definido de modo declarativo que aceita a entrada do usuário e a ecoa de volta.

## <a name="create-the-declarative-files"></a>Criar os arquivos declarativos

Os arquivos de diálogo declarativos são arquivos JSON independentes de linguagem que declaram os elementos de um diálogo, o que significa que eles são os mesmos, independentemente da linguagem usada para criar o bot. Normalmente, eles têm uma extensão de `.dialog`. A amostra do **EchoBot** contém apenas um diálogo adaptável com um gatilho para manipular o evento `UnknownIntent`, que quando disparado envia uma mensagem ao usuário que ecoa o que ele disse: _"You said '${turn.activity.text}'"_ .

> [!TIP]
> [O Bot Framework Composer](https://aka.ms/composer) é uma ferramenta de desenvolvimento integrada que os desenvolvedores e as equipes multidisciplinares podem usar para criar bots. Os bots criados pelo Bot Framework Composer são criados usando a abordagem declarativa.

O arquivo declarativo ([C#][main.dialog] | [JavaScript][echo.dialog]) da amostra do **EchoBot**:

```json
{
  "$schema": "../app.schema",
  "$kind": "Microsoft.AdaptiveDialog",
  "triggers": [
    {
      "$kind": "Microsoft.OnUnknownIntent",
      "actions": [
        {
          "$kind": "Microsoft.SendActivity",
          "activity":  "You said '${turn.activity.text}'"
        }
      ]
    }
  ]
}
```

> [!NOTE]
> Geralmente, é uma boa prática manter os seus arquivos declarativos em uma subpasta chamada `dialogs`, no entanto, eles podem estar em qualquer lugar. Você os carrega no bot usando o método `ResourceExplorer.GetResources()`.

### <a name="creating-the-schema-file"></a>Criando o arquivo de esquema

O arquivo de esquema referenciado pela palavra-chave `"$schema` contém os esquemas de todos os componentes consumidos pelo bot. Para obter informações sobre a palavra-chave `"$schema` e o arquivo de esquema referenciado em arquivos `.dialog`, consulte o artigo [Usando ativos declarativos em diálogos adaptáveis](bot-builder-concept-adaptive-dialog-declarative.md#declarative-files).

Para criar o arquivo de esquema referenciado no arquivo `.dialog`, você precisará da [CLI do BF (Interface de Linha de Comando do Bot Framework)][bf-cli]. Se ela ainda não estiver instalada, você poderá instalar a CLI do BF na linha de comando:

```cmd
npm i -g @microsoft/botframework-cli
```

# <a name="c"></a>[C#](#tab/csharp)

Usando a CLI do BF (Interface de Linha de Comando do Bot Framework) na linha de comando, no diretório raiz do seu projeto execute `bf dialog:merge <filename.csproj>`:

```cmd
bf dialog:merge EchoBot.csproj
```

Isso cria um arquivo chamado App.Schema no mesmo diretório em que o comando foi executado.

<!--
'$ bf dialog:merge *.csproj',
'$ bf dialog:merge libraries/*.schema -o app.schema'
-->

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Usando a CLI do BF (Interface de Linha de Comando do Bot Framework) na linha de comando, no diretório raiz do seu projeto execute `bf dialog:merge <projectFilename>`:

```cmd
bf dialog:merge package.json
```

Isso cria um arquivo chamado App.Schema no mesmo diretório em que o comando foi executado.

---

> [!TIP]
> Se a palavra-chave `"$schema` estiver ausente ou apontar para um arquivo inválido ou inexistente, você não obterá avisos ou erros, e isso não afetará a funcionalidade do bot durante a execução. No entanto, um arquivo App.Schema válido é necessário para que ferramentas de _Conclusão de código inteligente_, como [IntelliSense][intelliSense], funcionem com qualquer um dos ativos declarativos.

## <a name="add-references-to-declarative-components"></a>Adicionar referências a componentes declarativos

# <a name="c"></a>[C#](#tab/csharp)

O declarativo funciona apenas com diálogos adaptáveis. Para habilitar o adaptável no seu bot, instale o pacote NuGet **Microsoft.Bot.Builder.Dialogs.Adaptive** e, em seguida, crie as seguintes referências no seu código em **Startup.cs**:

[!code-CSharp[Startup.cs-Using-Statements](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/Startup.cs?range=4-17&highlight=7-9)]

E também no **EchoBot.cs**:

[!code-CSharp[EchoBot.cs-Using-Statements](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/echoBot.cs?range=4-10&highlight=6-7)]

<!--
```CS
using Microsoft.Bot.Builder.Dialogs.Adaptive;
using Microsoft.Bot.Builder.Dialogs.Declarative.Resources;
```
-->

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O declarativo funciona apenas com diálogos adaptáveis. Para usar os diálogos adaptáveis, o projeto precisa instalar o pacote npm **botbuilder-dialogs-adaptive**. Depois de instalado, você pode habilitar o declarativo criando as seguintes referências no seu código em **index.js**:

<!--[!code-JavaScript[AdaptiveDialogComponentRegistration](~/../botbuilder-samples/blob/master/experimental/adaptive-dialog/javascript_nodejs/20.echo-bot-declarative/index.js?range=4-10&highlight=4-5)]-->

```JavaScript
const { ResourceExplorer } = require('botbuilder-dialogs-declarative');
const { AdaptiveDialogComponentRegistration, LanguageGeneratorMiddleWare } = require('botbuilder-dialogs-adaptive');
const { DialogManager } = require('botbuilder-dialogs');
```

> [!TIP]
> `require()` não faz parte da API JavaScript padrão. Mas, no `Node.js`, ele é uma função interna usada para carregar módulos. Você pode encontrar mais informações na seção [Módulos][nodejs-modules] dos documentos do Node.js.

---

## <a name="register-declarative-components-for-adaptive-dialogs"></a>Registrar os componentes declarativos dos diálogos adaptáveis

# <a name="c"></a>[C#](#tab/csharp)

[!code-CSharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/Startup.cs?range=30-67&highlight=6-16,33-34)]

<!--
```CS
ComponentRegistration.Add(new DialogsComponentRegistration());
ComponentRegistration.Add(new DeclarativeComponentRegistration());
var resourceExplorer = new ResourceExplorer().LoadProject(this.HostingEnvironment.ContentRootPath);
services.AddSingleton(resourceExplorer);
```
-->

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--[!code-JavaScript[AdaptiveDialogComponentRegistration](~/../botbuilder-samples/blob/master/experimental/adaptive-dialog/javascript_nodejs/20.echo-bot-declarative/index.js?range=20-22)]-->

```JavaScript
const resourceExplorer = new ResourceExplorer().addFolder(__dirname, true, true);
resourceExplorer.addComponent(new AdaptiveDialogComponentRegistration(resourceExplorer));
```

---

## <a name="create-the-dialog-declaratively"></a>Criar o diálogo de modo declarativo

Os diálogos declarativos não são arquivos de código típicos. O gerenciador de recursos pode interpretar o recurso e gerar uma instância do diálogo descrito. Use um gerenciador de recursos para carregá-los no tempo de execução.

# <a name="c"></a>[C#](#tab/csharp)

Crie propriedades para o `ResourceExplorer` e o `DialogManager` que o seu bot usará. Use a injeção de dependência para definir o gerenciador de recursos no construtor de `EchoBot`.

[!code-CSharp[EchoBot-Constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/EchoBot.cs?range=14-23&highlight=4-5,7,10)]

<!--
```Csharp
    public class EchoBot : ActivityHandler
    {
        private IStatePropertyAccessor<DialogState> dialogStateAccessor;
        private readonly ResourceExplorer resourceExplorer;
        private DialogManager dialogManager;

        public EchoBot(ConversationState conversationState, ResourceExplorer resourceExplorer)
        {
            this.dialogStateAccessor = conversationState.CreateProperty<DialogState>("RootDialogState");
            this.resourceExplorer = resourceExplorer;
            ...
```
-->

Opcionalmente, você pode definir o diálogo a ser atualizado com as novas configurações sempre que o arquivo `.dialog` subjacente for alterado usando o método `resourceExplorer.Changed`:

[!code-CSharp[resourceExplorer.Changed](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/EchoBot.cs?range=25-32)]

<!--
```Csharp
// auto reload dialogs when file changes
this.resourceExplorer.Changed += (e, resources) =>
{
    if (resources.Any(resource => resource.Id.EndsWith == ".dialog"))
    {
        Task.Run(() => this.LoadRootDialogAsync());
    }
};
```
-->

Por fim, você cria um recurso de diálogo declarativo que você carrega no gerenciador de diálogos como faria com qualquer outro diálogo adaptável.

[!code-CSharp[CreateDeclarativeDialog](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/EchoBot.cs?range=42-45)]

<!--
```Csharp
var resource = this.resourceExplorer.GetResource("main.dialog");
dialogManager = new DialogManager(resourceExplorer.LoadType<AdaptiveDialog>(resource));
dialogManager.UseResourceExplorer(resourceExplorer);
dialogManager.UseLanguageGeneration();
```
-->

O método `GetResource` do gerenciador de recursos lê o arquivo declarativo em um objeto de recurso e o método `LoadType` converte o recurso em um objeto `AdaptiveDialog`. Aqui está o arquivo **main.dialog**.
Em seguida, você pode criar um gerenciador de diálogos como faria com qualquer outro diálogo adaptável. O diálogo está sendo lido e criado do arquivo declarativo em vez de ser definido no código.

O método `UseResourceExplorer` do gerenciador de diálogos registra o gerenciador de recursos para que o gerenciador de diálogos possa usá-lo posteriormente, conforme necessário. O método `UseLanguageGeneration` informa ao gerenciador de diálogos qual gerador de linguagem usar.

Nesse caso, como nenhum arquivo de modelo de LG (geração de linguagem) é fornecido e esse projeto não inclui um arquivo de LG padrão **main.lg**, o gerenciador de diálogos usará o gerador de linguagem padrão, sem modelos predefinidos. No entanto, o bot de eco inclui um modelo de LG embutido usado no diálogo. Ele é definido em **main.dialog**.

[!code-json[main.dialog](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/Dialogs/main.dialog?range=1-15&highlight=10)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--[!code-JavaScript[AdaptiveDialogComponentRegistration](~/../botbuilder-samples/blob/master/experimental/adaptive-dialog/javascript_nodejs/20.echo-bot-declarative/index.js?range=72-82)]-->

```javascript
let myBot;

const loadRootDialog = () => {
    console.log('(Re)Loading dialogs...');
    // Load root dialog
    let rootDialogResource = resourceExplorer.getResource('echo.dialog');
    myBot = new DialogManager();
    myBot.userState = userState;
    myBot.conversationState = conversationState;
    myBot.rootDialog = resourceExplorer.loadType(rootDialogResource);
}

loadRootDialog();
```

O método `GetResource` do gerenciador de recursos lê o arquivo declarativo em um objeto de recurso e o método `LoadType` converte o recurso em um objeto `AdaptiveDialog`. Aqui está o arquivo **echo.dialog**.
Em seguida, você pode criar um gerenciador de diálogos como faria com qualquer outro diálogo adaptável. O diálogo está sendo lido e criado do arquivo declarativo em vez de ser definido no código.
<!--
The dialog manager's `UseResourceExplorer` method registers the resource explorer so that the dialog manager can make use of it later, as necessary. The `UseLanguageGeneration` method tells the dialog manager which language generator to use.

In this case, since no language generation (LG) template file is provided, and this project does not include a **main.lg** default LG file, the dialog manager will use the default language generator, without any predefined templates. However, the echo bot includes an in-line LG template used in the dialog. It is defined in **main.dialog**.
-->

Opcionalmente, você pode definir o diálogo a ser atualizado com as novas configurações sempre que o arquivo `.dialog` subjacente for alterado usando o método `resourceExplorer.Changed`:

<!--[!code-JavaScript[AdaptiveDialogComponentRegistration](~/../botbuilder-samples/blob/master/experimental/adaptive-dialog/javascript_nodejs/20.echo-bot-declarative/index.js?range=111-117)]-->

```javascript
const handleResourceChange = (resources) => {
    if (Array.isArray(resources)) {
        if((resources || []).find(r => r.resourceId.endsWith('.dialog')) !== undefined) loadRootDialog();
    } else {
        if (resources.resourceId && resources.resourceId.endsWith('.dialog')) loadRootDialog()
    }
};
```

---

## <a name="test-the-bot"></a>Testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o Emulador, conecte-se ao seu bot e envie mensagens conforme mostrado abaixo.

![Execução de amostra do exemplo de código do diálogo declarativo do EchoBot](../media/emulator-v4/EchoBot-declarative.png)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md
[concept-adaptive]: bot-builder-adaptive-dialog-introduction.md
[concept-declarative]: bot-builder-concept-adaptive-dialog-declarative.md
[how-to-adaptive]: bot-builder-dialogs-adaptive.md

[bf-cli]: https://aka.ms/bf-cli-overview

[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative

[js-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/javascript_nodejs/20.echo-bot-declarative

[main.dialog]: https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/20.EchoBot-declarative/Dialogs/main.dialog

[echo.dialog]: https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/javascript_nodejs/20.echo-bot-declarative/dialogs/echo.dialog

[nodejs-modules]: https://nodejs.org/api/modules.html#modules_modules

[intelliSense]: https://aka.ms/intellisense-in-visual-studio
