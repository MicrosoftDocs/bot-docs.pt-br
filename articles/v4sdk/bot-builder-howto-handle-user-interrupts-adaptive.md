---
title: Como lidar com interrupções de usuário em caixas de diálogo adaptáveis – serviço bot
description: Saiba como lidar com interrupções de usuário e fluxo de conversa direta na caixa de diálogo adaptável.
keywords: interrupção, interrupções, tópico de alternância, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/27/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 78da57a6ca756176826292aa1b0998dcc839ae64
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417206"
---
# <a name="handle-user-interruptions-in-adaptive-dialogs"></a>Lidar com interrupções do usuário em caixas de diálogo adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O tratamento de interrupções é um aspecto importante de um bot robusto. Os usuários nem sempre seguirão o fluxo de conversa definido. Geralmente, eles precisarão fazer uma pergunta no meio ou cancelar o processo. Este artigo descreve algumas maneiras comuns de lidar com interrupções de usuário no bot.

## <a name="prerequisites"></a>Pré-requisitos

O tratamento de interrupções é um tópico de desenvolvimento de bot avançado. Para obter o máximo deste artigo, você precisará de um bom conhecimento prático dos conceitos básicos do SDK do bot Framework e das caixas de diálogo adaptáveis, bem como um conhecimento de como desenvolver um bot que incorpora caixas de diálogo adaptáveis.  Examine os artigos listados nos dois primeiros marcadores antes de continuar:

- Conhecimento das [noções básicas de bot][concept-basics], [Gerenciamento de estado][concept-state]e de [caixas de diálogo][concept-dialogs] conceitos de SDK da estrutura de bot Framework.
- Compreensão dos conceitos de caixa de diálogo adaptáveis abordados na [introdução a caixas de diálogo adaptáveis][adaptive-dialog-introduction], [solicitando entrada do usuário em caixas de diálogo adaptáveis][adaptive-dialog-input] e [lidando com interrupções em artigos de conceito de caixa de diálogo adaptável][adaptive-dialog-interruptions] .
- Uma cópia do `InterruptionsBot` exemplo em [**C#**][cs-sample].  <!-- , [**JavaScript**][js-sample] or [**Python**][python-sample].-->
- Um conhecimento prático de como [criar um bot usando caixas de diálogo adaptáveis](bot-builder-dialogs-adaptive.md) seria útil, no entanto, este artigo explicará o `InterruptionsBot` exemplo em detalhes.

> [!NOTE]
>
> O `InterruptionsBot` exemplo está atualmente disponível apenas em [**C#**][cs-sample].

## <a name="setting-up-luis-to-work-in-your-bot"></a>Configurando o LUIS para trabalhar em seu bot

Esta seção fornece detalhes sobre como criar recursos do LUIS no portal do Azure e, em seguida, configurar e definir o LUIS para trabalhar com este exemplo. Se você já fez isso, pode pular para [o exemplo InterruptionsBot](#the-interruptionsbot-sample).

O que é abordado nesta seção:

1. [Criar recursos do LUIS no portal do Azure](#create-luis-resources-in-the-azure-portal)
1. [Obter sua chave de criação](#get-your-authoring-key)
1. [Use a CLI do bot Framework para conectar seu bot ao recurso LUIS no Azure](#use-the-bot-framework-cli-to-connect-your-bot-to-your-luis-resource-in-azure)
1. [Atualizar o arquivo de configuração](#update-your-configuration-file)


### <a name="create-luis-resources-in-the-azure-portal"></a>Criar recursos do LUIS no portal do Azure

1. Vá para a página [criar serviços cognitivas][CognitiveServicesLUISAllInOne] do Azure.  
2. Na seção **criar opções** , selecione **criação**.

   ![Criar opções para a criação de imagem](./media/adaptive-dialogs/create-options-authoring.png)

3. Insira valores para cada um dos campos e, em seguida, selecione o botão **revisar + criar** .

   ![Criar imagem de serviços cognitivas](./media/adaptive-dialogs/create-cognitive-services.png)

    > [!NOTE]
    > Ao inserir o **grupo de recursos** e o **nome**, tenha em mente que você não poderá alterar esses valores posteriormente. Observe também que o valor que você atribui para o **nome** será parte da **URL do ponto de extremidade**.

4. Examine os valores para garantir que estejam corretos e, em seguida, selecione o botão **criar** .

### <a name="get-your-authoring-key"></a>Obter sua chave de criação

Agora que você criou o recurso LUIS no portal do Azure, você pode obter sua chave de criação.

1. Quando o Azure terminar de criar os recursos do LUIS no portal do Azure, você verá uma notificação **de que sua implantação está concluída** , clique em **ir para o recurso**.

   ![a implantação está completa](./media/adaptive-dialogs/your-deployment-is-complete.png)

2. No painel esquerdo, selecione **chaves e ponto de extremidade**.
3. Copie o valor da **chave 1** , essa é a sua *chave de criação*. Você precisa inserir isso como o valor para:
    -  `LuisAPIKey`: Uma configuração no arquivo de configuração.
    - `--authoringKey`: Uma propriedade do `bf luis:build` comando da CLI que é discutida na seção a seguir.

   ![chaves e imagem do ponto de extremidade](./media/adaptive-dialogs/keys-and-endpoint.png)

4. Copie e salve o **ponto de extremidade**. Você atribuirá esse valor ao `LuisAPIHostName` no arquivo de configuração.

Com esses novos recursos do LUIS no portal do Azure, agora você está pronto para conectar o bot a ele.

### <a name="use-the-bot-framework-cli-to-connect-your-bot-to-your-luis-resource-in-azure"></a>Use a CLI do bot Framework para conectar seu bot ao recurso LUIS no Azure

Esta seção explica como usar a CLI do bot Framework para conectar o bot aos recursos do LUIS no Azure. Isso automatiza as várias tarefas necessárias para criar, atualizar, treinar e publicar aplicativos LUIS para cada arquivo. Lu para o bot. Para usar isso, primeiro você precisa Node.js e a CLI do bot Framework.

1. Se você tiver **Node.js** instalado, verifique se você tem a versão 10,14 ou superior executando o seguinte em um prompt de comando: `npm node.js –version` . Você pode obter a versão mais recente executando o seguinte em um prompt de comando: `npm i -g npm` .

    Se você não o tiver instalado, poderá instalá-lo na página de [ downloadNode.js](https://nodejs.org/download/).

1. Usando Node.js, instale a versão mais recente da CLI do bot Framework na linha de comando.

    ```bash
    npm i -g @microsoft/botframework-cli
    ```

1. Em um prompt de comando, navegue até o diretório raiz do exemplo InterruptionBot, geralmente `..\samples\csharp_dotnetcore\adaptive-dialog\05.interruptions-bot` .

    Agora você está pronto para conectar o bot aos recursos do LUIS no Azure usando a CLI do bot Framework.

1. Em um prompt de comando, no diretório raiz do código-fonte do projeto, execute o seguinte comando:

    ``` bash
    bf luis:build --in Dialogs --out generated --log --botName InterruptionBotSample --authoringKey <your-authoring-key>
    ```

    A seguir estão os **parâmetros BF Luis: Build:**

    - `in`: Esse é o diretório, junto com seus subdiretórios, que serão pesquisados em busca de arquivos. Lu.
    - `out`: Esse é o diretório no qual os arquivos gerados por esse processo são salvos.
    - `log`: Um valor booliano que determina se um log é criado durante esse processo.
    - `botName`: Use o mesmo valor que você usou para **nome** na etapa 3 da seção [criar recursos do Luis na portal do Azure](#setting-up-luis-to-work-in-your-bot) acima.
    - `authoringKey`: Sua [chave de criação](#get-your-authoring-key).

    Executar o `bf luis:build` comando faz várias coisas. Ele carrega todas as informações contidas em todos os arquivos. Lu para o recurso LUIS no Azure e, em seguida, executa o treinamento e a publicação de LUIS necessários.  Tudo o que você precisa fazer agora é atualizar seu arquivo de configuração com as informações geradas como resultado das etapas realizadas anteriormente.

#### <a name="files-generated"></a>Arquivos gerados

Há dois tipos de arquivos gerados durante o `bf luis:build` processo:

1. Um `.json` arquivo que contém as informações necessárias para o bot que você precisa adicionar ao [arquivo de configuração](#update-your-configuration-file). Esse arquivo é nomeado `luis.settings.<youralias>.<region>.json` .
1. `.dialog` arquivos, que são úteis ao usar a forma declarativa de caixas de diálogo adaptáveis.

Você não precisa dos dois arquivos. Dialog gerados para este artigo de instruções.

### <a name="update-your-configuration-file"></a>Atualizar o arquivo de configuração

<!--# [C#](#tab/csharp)-->

Adicione os seguintes valores para **appsettings.jsem**:

``` json
  "luis": {
    "GetUserProfileDialog_en_us_lu": "",
    "RootDialog_en_us_lu": "",
    "LuisAPIKey": "",
    "LuisAPIHostName": ""
  }
```

> [!NOTE]
>
> Os nomes de arquivo gerados podem diferir dependendo das configurações locais do seu computador. No exemplo usado aqui, a configuração de localidade é `en_us` .

- **GetUserProfileDialog_en_us_lu**: obter esse valor do arquivo chamado **Luis. Settings. \<youralias> . \<region> . JSON** que foi gerado como parte da execução do `bf luis:build` comando.

- **RootDialog_en_us_lu**: obter esse valor do arquivo chamado **Luis. Settings. \<youralias> . \<region> . JSON** que foi gerado como parte da execução do `bf luis:build` comando.

- **LuisAPIKey**: essa é a sua [chave de criação](#get-your-authoring-key).

- **LuisAPIHostName**: é o valor da URL do **ponto de extremidade** que você obtém da seção **chaves e ponto de extremidade** no Azure, que será semelhante a: `https://InterruptionBotSample.cognitiveservices.azure.com` .

<!--
# [JavaScript](#tab/javascript)

Add the following values to your **.env** file:

``` JavaScript
getUserProfileDialog_en_us_lu=
rootDialog_en_us_lu=
LuisAPIHostName=
LuisAPIKey=
```

- **GetUserProfileDialog_en_us_lu**: Get this value from the file named **luis.settings.\<youralias>.\<region>.json** that was generated as a part of running the `bf luis:build` command.

- **RootDialog_en_us_lu**: Get this value from the file named **luis.settings.\<youralias>.\<region>.json** that was generated as a part of running the `bf luis:build` command.

- **LuisAPIKey**: This is your [authoring key](#get-your-authoring-key).

- **LuisAPIHostName**: This is the **ENDPOINT** URL value that you get from the **Keys and Endpoint** section in Azure, which will look something like: `https://InterruptionBotSample.cognitiveservices.azure.com`.

---
-->
Agora você deve ser capaz de usar o LUIS em seu bot.

## <a name="the-interruptionsbot-sample"></a>O exemplo de InterruptionsBot

O exemplo usado neste artigo demonstra como criar um bot que usa LUIS e caixas de diálogo adaptáveis para obter conceitos de LU avançados, incluindo a manipulação de interrupções. A qualquer momento durante a conversa com o bot, o usuário pode emitir _ajuda_ ou _Cancelar_ comandos para interromper o fluxo de conversação atual. O exemplo InterruptionsBot tem duas caixas de diálogo adaptáveis e cada caixa de diálogo inclui tratamento de interrupções que é específica para seu fluxo de conversa.

### <a name="rootdialog"></a>RootDialog

O RootDialog é a caixa de diálogo adaptável raiz desse bot. É o pai da única outra caixa de diálogo adaptável neste bot: [UserProfileDialog](#getuserprofiledialog).

#### <a name="rootdialog-recognizer"></a>Reconhecedor RootDialog

A primeira coisa que acontece quando `rootDialog` é criado é definir seu reconhecedor. Neste exemplo, você usará um reconhecedor adaptável LUIS. As instruções para fazer com que o bot funcione usando o reconhecedor LUIS são detalhadas na seção [Configurando Luis para trabalhar em seu bot](#setting-up-luis-to-work-in-your-bot).

Cada caixa de diálogo adaptável tem seu próprio reconhecedor e todas as caixas de diálogo adaptáveis que usam o reconhecedor LUIS podem ter um ou mais `.lu` arquivos. Em geral, esse arquivo recebe o mesmo nome que o nome de arquivo que contém a caixa de diálogo, com a `.lu` extensão File, por exemplo, se o arquivo que hospeda a caixa de diálogo for denominado _RootDialog_, o arquivo. Lu será _RootDialog.Lu_. O arquivo. Lu é usado exclusivamente por essa caixa de diálogo. Para obter mais informações sobre `.lu` arquivos, consulte o [formato de arquivo article. Lu][lu].

No `.lu` arquivo, você define as [intenções][intents], [declarações][utterances] e [entidades][entities] que devem ser usadas nessa caixa de diálogo. Se uma caixa de diálogo adaptável não definir um gatilho para lidar com uma determinada tentativa, mas uma de suas caixas de diálogo adaptáveis pai, o mecanismo de consulta permitirá que a caixa de diálogo pai manipule o expressão. Depois que o processo for concluído, o usuário será devolvido para onde a conversa estava antes da interrupção.

<!--# [C#](#tab/csharp)-->

O reconhecedor é definido chamando o `CreateLuisRecognizer()` método e passando as definições de configuração do bot que incluem os valores armazenados em **appsettings.jsem**, que contêm as configurações de Luis necessárias.

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.cs?range=31-34&highlight=4)]

<!--
```cs
// Create instance of adaptive dialog.
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Recognizer = CreateLuisRecognizer(this.configuration),
    ...
```
-->

O `CreateLuisRecognizer()` método primeiro verifica se os valores necessários existem no arquivo de configuração (appsettings.jsem) e, em caso afirmativo, cria um novo `LuisAdaptiveRecognizer` nome `Recognizer` . O reconhecedor do LUIS agora está pronto.

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.cs?range=99-112)]

<!--
```cs
private static Recognizer CreateLuisRecognizer(IConfiguration configuration)
{
    if (string.IsNullOrEmpty(configuration["luis:RootDialog_en_us_lu"]) || string.IsNullOrEmpty(configuration["luis:LuisAPIKey"]) || string.IsNullOrEmpty(configuration["luis:LuisAPIHostName"]))
    {
        throw new Exception("NOTE: LUIS is not configured for RootDialog. To enable all capabilities, add 'LuisAppId-RootDialog', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.");
    }

    return new LuisAdaptiveRecognizer()
    {
        ApplicationId = configuration["luis:RootDialog_en_us_lu"],
        EndpointKey = configuration["luis:LuisAPIKey"],
        Endpoint = configuration["luis:LuisAPIHostName"]
    };
}
```
-->

<!--# [JavaScript](#tab/javascript)

The recognizer is set by calling the `CreateLuisRecognizer()` method

[!code-js[recognizer](~/../experimental/adaptive-dialog/javascript_nodejs/05.interruptions-bot/dialogs/rootDialog/rootDialog.js?range=19)]

```javascript
recognizer: this.createLuisRecognizer(),
```

The `CreateLuisRecognizer()` method first verifies the required values exist in the configuration (.env) file and if so creates a new `LuisAdaptiveRecognizer` named `recognizer`. Your LUIS recognizer is now ready.

[!code-js[recognizer](~/../experimental/adaptive-dialog/javascript_nodejs/05.interruptions-bot/dialogs/rootDialog/rootDialog.js?range=119-128)]

```javascript
createLuisRecognizer() {
    if (process.env.getUserProfileDialog_en_us_lu === "" || process.env.LuisAPIHostName === "" || process.env.LuisAPIKey === "")
        throw `Sorry, you need to configure your LUIS application and update .env file.`;
    return new LuisAdaptiveRecognizer().configure(
        {
            endpoint: new StringExpression(process.env.LuisAPIHostName),
            endpointKey: new StringExpression(process.env.LuisAPIKey),
            applicationId: new StringExpression(process.env.getUserProfileDialog_en_us_lu)
        }
    );
```
---
-->

#### <a name="rootdialog-generator"></a>Gerador de RootDialog

O gerador requer um arquivo de [geração de linguagem][language-generation] válido (. LG) que define os modelos de geração de idioma que serão usados por essa caixa de diálogo. Esse arquivo recebe o mesmo nome que o nome do arquivo que contém a caixa de diálogo, com a `.lg` extensão de arquivo, por exemplo, se o arquivo que hospeda a caixa de diálogo for denominado _RootDialog_, o arquivo. LG será _RootDialog. LG_. O arquivo. LG é usado exclusivamente por essa caixa de diálogo.

<!--# [C#](#tab/csharp)-->

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.cs?range=29-35&highlight=1,7)]

<!--
```cs
_templates = Templates.ParseFile(Path.Combine(".", "Dialogs", "RootDialog", "RootDialog.lg"));

// Create instance of adaptive dialog.
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Recognizer = CreateLuisRecognizer(this.configuration),
    Generator = new TemplateEngineLanguageGenerator(_templates),
```
-->

<!--# [JavaScript](#tab/javascript)

[!code-csharp[PropertyAssignment](~/../experimental/adaptive-dialog/javascript_nodejs/05.interruptions-bot/dialogs/rootDialog/rootDialog.js?range=16-19&highlight=1,3)]

```javascript
const lgFile = Templates.parseFile(path.join(__dirname, 'getUserProfileDialog.lg'));
const dialog = new AdaptiveDialog(DIALOG_ID).configure({
    generator: new TemplateEngineLanguageGenerator(lgFile),
    recognizer: this.createLuisRecognizer(),
```

---
-->

#### <a name="rootdialog-triggers"></a>Gatilhos RootDialog

Ao executar este bot de exemplo, a primeira coisa que a caixa de diálogo raiz faz depois de configurar seu reconhecedor e gerador é manipular o `conversationUpdate` evento de atividade usando o gatilho [OnConversationUpdateActivity][OnConversationUpdateActivity] . Isso será acionado sempre que um novo usuário ingressar na conversa e chamar as ações retornadas pelo `WelcomeUserActions` método. A primeira é a `SendActivity` ação, usando o `${WelcomeUser()}` modelo, definida no arquivo **RootDialog. LG** . Isso envia uma mensagem de saudação e três ações sugeridas: "perfil", "ajuda" e "Cancelar". Isso mostra ao usuário que a mensagem de saudação exibe as três opções que podem ser selecionadas diretamente. O usuário pode selecionar uma das ações ou digitar qualquer coisa na caixa de entrada. Consulte a dica a seguir para obter uma breve descrição do uso do modelo.

> [!TIP]
> Na explicação acima, `${WelcomeUser()}` é passado como um argumento para a `SendActivity` ação. `WelcomeUser` é definido no arquivo [. LG][lg] de caixas de diálogo raiz e é o nome de um [modelo de resposta estruturado][structured-response-template]
>
> As referências a modelos de resposta estruturados são indicadas por:
>
> - Anexando um parênteses de abertura e fechamento **()** imediatamente após o nome do modelo: `WelcomeUser()`
> - Ao redor dela com chaves **{}** : `{WelcomeUser()}`
> - Prefixando-o com o cifrão **$** : `${WelcomeUser()}`

Em seguida, estão as opções mostradas ao usuário com uma explicação do que acontece quando são selecionadas.

##### <a name="profile"></a>Perfil

Se selecionado:

1. Isso gera um "perfil" expressão do usuário.
1. O reconhecedor da caixa de diálogo raiz reconhece uma intenção "GetUserProfile".
1. O gatilho da caixa `OnIntent` de diálogo raiz para essa intenção é acionado, o que inicia a `userProfileDialog` caixa de diálogo, outra caixa de diálogo adaptável.

A caixa de diálogo perfil de usuário tem seu próprio reconhecedor e gerador e executa seus próprios gatilhos e ações em resposta à entrada do usuário, conforme discutido posteriormente na seção [GetUserProfileDialog](#getuserprofiledialog) .

Aqui está o código do `OnIntent` gatilho, em **RootDialog.cs**, que manipula a intenção _GetUserProfile_ :

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.cs?range=42-52)]

<!--
```cs
new OnIntent()
{
    Intent = "GetUserProfile",
    Actions = new List<Dialog>()
    {
        new BeginDialog()
        {
            Dialog = nameof(GetUserProfileDialog)
        }
    }
},
```
-->

##### <a name="help"></a>Ajuda

Se selecionado:

1. Isso gera um expressão de "ajuda" do usuário.
1. O reconhecedor da caixa de diálogo raiz reconhece uma intenção de "ajuda".
1. O gatilho da caixa `OnIntent` de diálogo raiz para essa intenção é acionado, fazendo com que suas ações sejam executadas.
1. A `SendActivity` ação é chamada passagem `${RootHelp()}` como seu parâmetro. Isso resulta em uma mensagem para o usuário semelhante ao que foi recebido quando a conversa começa. Consulte a _dica_ anterior para obter uma explicação detalhada de como isso funciona.

<!--# [C#](#tab/csharp)-->

Este é o `OnIntent` código de gatilho que manipula a intenção de _ajuda_ no RootDialog.cs:

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.cs?range=53-60)]

<!--
```cs
new OnIntent()
{
    Intent = "Help",
    Actions = new List<Dialog>()
    {
        new SendActivity("${RootHelp()}")
    }
},
```
-->

Aqui está o [modelo de resposta estruturado][structured-response-template] em **RootDialog. LG** que está relacionado à intenção de "ajuda":

[!code-lg[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.lg?range=4-8)]

<!--
```plaintext
# RootHelp
[Activity
    Text = I'm a sample interruptions bot
    SuggestedActions = Profile | Help | Cancel
]
```
-->

##### <a name="cancel"></a>Cancelar

Se selecionado:
1. O bot dispara um `intent` evento com um valor de "Cancelar".
1. O `OnIntent` gatilho contendo a _intenção = "Cancelar"_ é acionado, fazendo com que suas ações sejam executadas.
1. Isso resulta na execução da ação [ConfirmInput][confirm-input] , que exibe a mensagem "tem certeza de que deseja cancelar?"
1. Em seguida, a `IfCondition` ação é executada para responder à  `ConfirmInput` ação.
    1. Se o usuário responde afirmativamente, `turn.confirm` é definido como `true` e o `CancelReadBack` modelo LG é executado, resultando em uma mensagem para o usuário: _certamente, cancelando todas as caixas de diálogo..._ e executa a ação [CancelAllDialogs][cancel-all-dialogs] , fechando todas as caixas de diálogo.
    1. Se o usuário não responder a afirmativamente, `turn.confirm` é definido como `false` e o `Cancelcancelled` modelo LG é executado, resultando em uma mensagem para o usuário: _sem problema_e a conversa continua de onde parou.

Este é o `OnIntent` código de gatilho em RootDialog.cs:

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.cs?range=61-86)]

<!--
```cs
new OnIntent()
{
    Intent = "Cancel",
    Actions = new List<Dialog>()
    {
        new ConfirmInput()
        {
            Property = "turn.confirm",
            AllowInterruptions = false,
            Prompt = new ActivityTemplate("${RootCancelConfirm()}")
        },
        new IfCondition()
        {
            Condition = "turn.confirm == true",
            Actions = new List<Dialog>()
            {
                new SendActivity("${CancelReadBack()}"),
                new CancelAllDialogs()
            },
            ElseActions = new List<Dialog>()
            {
                new SendActivity("${Cancelcancelled()}")
            }
        }
    }
}
```
-->

Aqui estão os três modelos de geração de linguagem em **RootDialog. LG** que são chamados das `ConfirmInput` `IfCondition` ações ou como resultado da intenção de "Cancelar":

[!code-lg[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.lg?range=16-23)]

<!--
```plaintext
# RootCancelConfirm
- Are you sure you want to cancel?

# CancelReadBack
- Sure, cancelling all dialogs...

# Cancelcancelled
- No problem.
```
-->

<!--# [JavaScript](#tab/javascript)
new OnIntent()
{
    Intent = "Cancel",
    Actions = new List<Dialog>()
    {
        new ConfirmInput()
        {
            Property = "turn.confirm",
            AllowInterruptions = false,
            Prompt = new ActivityTemplate("${RootCancelConfirm()}")
        },
        new IfCondition()
        {
            Condition = "turn.confirm == true",
            Actions = new List<Dialog>()
            {
                new SendActivity("${CancelReadBack()}"),
                new CancelAllDialogs()
            },
            ElseActions = new List<Dialog>()
            {
                new SendActivity("${Cancelcancelled()}")
            }
        }
    }
}
```

Here is the LG Template in RootDialog.lg:

[!code-lg[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.lg?range=16-23)]

```plaintext
# RootCancelConfirm
- Are you sure you want to cancel?

# CancelReadBack
- Sure, cancelling all dialogs...

# Cancelcancelled
- No problem.
```
---
-->

### <a name="getuserprofiledialog"></a>GetUserProfileDialog

A caixa de diálogo definida em **GetUserProfileDialog** é denominada `userProfileDialog` . Essa caixa de diálogo é chamada pela caixa de diálogo raiz em resposta ao usuário que insere um expressão, também conhecido como uma _frase de gatilho_, que está associada à `GetUserProfile` intenção definida em **RootDialog.Lu**.

[!code-json[env](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.lu?range=1-5)]

<!--
```plaintext
# GetUserProfile
- Hi
- My name is vishwac
- I'm 36 years old
- Profile
```
-->

A `userProfileDialog` caixa de diálogo é a única caixa de diálogo adaptável filho nesse bot, portanto, todas as interrupções globais vão diretamente para a caixa de diálogo raiz. Uma pesquisa rápida do arquivo **GetUserProfileDialog.Lu** mostrará que não há  _ajuda_ ou _Cancelar_ tentativas definidas. Sem o mecanismo de consulta de caixa de diálogo adaptável, lidar com essas interrupções é `userProfileDialog` muito mais difícil, mas devido ao esforço colocado no SDK do bot Framework, essas interrupções são fáceis de lidar. Qualquer ação definida nesta caixa de diálogo que tenha sua `AllowInterruptions` propriedade definida ou avaliada para `true` permitirá que essas interrupções sejam manipuladas por qualquer uma de suas caixas de diálogo pai, neste caso, a caixa de diálogo raiz.

Há também duas interrupções locais definidas. Elas são definidas como `OnIntent` ações que manipulam as intenções _por motivo_ e _novalor_ . Você pode abrir o arquivo **GetUserProfileDialog.Lu** para exibir, adicionar ou atualizar o declarações associado a essas intenções. Essas intenções não são uma parte típica do fluxo de conversação normal e, como tal, são tratadas como interrupções locais. Depois que essas interrupções forem concluídas, o controle será retornado para a ação que foi interrompida para continuar diretamente o fluxo de conversação onde parou.

#### <a name="getuserprofiledialog-recognizer"></a>Reconhecedor GetUserProfileDialog

Configurar seu reconhecedor é a primeira coisa que acontece quando o `userProfileDialog` é criado. Cada caixa de diálogo configura seu próprio reconhecedor independente de todas as outras caixas de diálogo, e cada caixa de diálogo pode usar o mesmo tipo de reconhecedor, ou cada uma pode usar um tipo diferente de reconhecedor. Cada caixa de diálogo em seu bot pode usar qualquer tipo de reconhecedor definido no SDK do bot Framework, independentemente do que qualquer outra caixa de diálogo estiver usando. Para saber mais sobre os diferentes tipos disponíveis, consulte a seção [tipos de reconhecedor][recognizer-types] do artigo conceito de reconhecedores. <!---  Add ", or a custom recognizer" once there is an article that discusses this.  -->

Como mencionado anteriormente, cada caixa de diálogo adaptável tem seu próprio reconhecedor e o `.lu` arquivo associado a ele está exclusivamente vinculado a essa caixa de diálogo. No `.lu` arquivo, você define as [intenções][intents], [declarações][utterances] e [entidades][entities] que devem ser usadas nessa caixa de diálogo. Se o usuário inserir uma intenção que não esteja definida no arquivo dessa caixa de diálogo `.lu` , o mecanismo de consulta de caixa de diálogo adaptável permitirá que o bot emergisse a intenção do usuário para a caixa de diálogo pai manipular, se puder. Nesse caso, as tentativas de _ajuda_ e _cancelamento_ são definidas na caixa de diálogo raiz, mas o bot ainda pode lidar com essas tentativas de usuário mesmo quando `userProfileDialog` é a caixa de diálogo ativa. Isso é explicado em detalhes em uma seção posterior, intitulado [disparadores GetUserProfileDialog](#getuserprofiledialog-triggers).

Para a maior parte, o código em GetUserProfileDialog usado para definir o reconhecedor é o mesmo que o código usado na [caixa de diálogo raiz](#rootdialog-recognizer), a única diferença é que você precisa referenciar GetUserProfileDialog_en_us_lu para o `ApplicationId ` valor em oposição ao RootDialog_en_us_lu, no `CreateLuisRecognizer` método. Consulte a seção [arquivos gerados](#files-generated) para obter mais informações sobre o arquivo que está sendo referenciado.

[!code-csharp[CreateLuisRecognizer](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/GetUserProfileDialog/GetUserProfileDialog.cs?range=171-186&highlight=10)]

<!--
```csharp
        private static Recognizer CreateLuisRecognizer(IConfiguration configuration)
        {
            if (string.IsNullOrEmpty(configuration["luis:GetUserProfileDialog_en_us_lu"]) || string.IsNullOrEmpty(configuration["luis:LuisAPIKey"]) || string.IsNullOrEmpty(configuration["luis:LuisAPIHostName"]))
            {
                throw new Exception("NOTE: LUIS is not configured for RootDialog. To enable all capabilities, add 'LuisAppId-RootDialog', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.");
            }

            return new LuisAdaptiveRecognizer()
            {
                ApplicationId = configuration["luis:GetUserProfileDialog_en_us_lu"],     //  <------- This line should be highlighted in the above code snippet.
                EndpointKey = configuration["luis:LuisAPIKey"],
                Endpoint = configuration["luis:LuisAPIHostName"]
            };
        }
```
-->

#### <a name="getuserprofiledialog-generator"></a>Gerador de GetUserProfileDialog

O gerador consiste em seu arquivo de modelo LG que está no mesmo diretório que o arquivo que contém o código-fonte da caixa de diálogo adaptável, com o mesmo nome de arquivo e a `.lg` extensão de arquivos, como **GetUserProfileDialog. LG**.<!-- if programming using C# or **getUserProfileDialog.lg** if using JavaScript.-->

<!--# [C#](#tab/csharp)-->

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/GetUserProfileDialog/GetUserProfileDialog.cs?range=30-36&highlight=1,7)]

<!--
```cs
_templates = Templates.ParseFile(Path.Combine(".", "Dialogs", "GetUserProfileDialog", "GetUserProfileDialog.lg"));

// Create instance of adaptive dialog.
var userProfileDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Recognizer = CreateLuisRecognizer(this.configuration),
    Generator = new TemplateEngineLanguageGenerator(_templates),
```
-->

<!--# [JavaScript](#tab/javascript)

[!code-js[PropertyAssignment](~/../experimental/adaptive-dialog/javascript_nodejs/05.interruptions-bot/dialogs/getUserProfileDialog/getUserProfileDialog.js?range=16-18&highlight=1,3)]

```javascript
const lgFile = Templates.parseFile(path.join(__dirname, 'getUserProfileDialog.lg'));
const dialog = new AdaptiveDialog(DIALOG_ID).configure({
    generator: new TemplateEngineLanguageGenerator(lgFile),
```

---
-->

#### <a name="getuserprofiledialog-triggers"></a>Gatilhos GetUserProfileDialog

Quando essa caixa de diálogo é iniciada, seu `OnBeginDialog` gatilho é executado:

- Duas `PropertyAssignment` ações são executadas definindo duas novas propriedades: `user.profile.name` e `user.profile.age` .
- Essas propriedades serão preenchidas se as entidades estiverem presentes, como quando essa caixa de diálogo foi disparada pela frase, "Olá, sou Vishwac". Nesse caso, a `@Personname` entidade seria _Vishwac_.
- Se uma das propriedades for nula, o bot solicitará ao usuário as informações ausentes usando as ações de _entrada de texto_ .
- Ambas as ações de entrada permitem interrupções, usando [expressões adaptáveis][adaptive-expressions].

<!--# [C#](#tab/csharp)-->

[!code-csharp[PropertyAssignment](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/GetUserProfileDialog/GetUserProfileDialog.cs?range=49-55,64-78,82-85,89,93-100,102-106,110,113-114)]

<!--
```CS
new SetProperties()
{
    Assignments = new List<PropertyAssignment>()
    {
        new PropertyAssignment()
        {
            Property = "user.profile.name",
            Value = "=coalesce(dialog.userName, @personName)"
        },
        new PropertyAssignment()
        {
            Property = "user.profile.age",
            Value = "=coalesce(dialog.userAge, @age)"
        }
    }
},
new TextInput()
{
    Property = "user.profile.name",
    Prompt = new ActivityTemplate("${AskFirstName()}"),
    Validations = new List<BoolExpression>()
    {
        "count(this.value) >= 3",
        "count(this.value) <= 50"
    },
    InvalidPrompt = new ActivityTemplate("${AskFirstName.Invalid()}"),

    Value = "=@personName",
    AllowInterruptions = "turn.recognized.score >= 0.3 || !@personName"
},
new TextInput()
{
    Property = "user.profile.age",
    Prompt = new ActivityTemplate("${AskUserAage()}"),
    Validations = new List<BoolExpression>()
    {
        "int(this.value) >= 1",
        "int(this.value) <= 150"
    },
    InvalidPrompt = new ActivityTemplate("${AskUserAge.Invalid()}"),
    UnrecognizedPrompt = new ActivityTemplate("${AskUserAge.Unrecognized()}"),
    Value = "=coalesce(@age.number, @number)",
    AllowInterruptions = "!@age && !@number"
},
```

-->

> [!TIP]
>
> A `AllowInterruptions` propriedade é um `BoolExpression` . Uma expressão booliana é uma propriedade [AdaptiveExpressions][adaptive-expressions] que é um booliano ou uma expressão de cadeia de caracteres que é resolvida para um booliano.  `AllowInterruptions = "turn.recognized.score >= 0.3 || !@personName"` definirá a `AllowInterruptions` propriedade como true se a [Pontuação de previsão][prediction-score] retornada de Luis for maior que 30% ou a entidade [PersonName][person-name] for nula, caso contrário, ela será avaliada como false.

<!--# [JavaScript](#tab/javascript)

[!code-js[PropertyAssignment](~/../experimental/adaptive-dialog/javascript_nodejs/05.interruptions-bot/dialogs/getUserProfileDialog/getUserProfileDialog.js?range=28-29,36-42)]

<!--
new SetProperty().configure({
    property: new StringExpression("user.profile.name"),
    value: new ValueExpression("=coalesce(dialog.userName, @personName)")
}),
new SetProperty().configure(
{
    property: new StringExpression("user.profile.age"),
    value: new ValueExpression("=coalesce(dialog.userAge, @age)")
}),

---
-->

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte ao seu bot e envie mensagens conforme mostrado a seguir.

A captura de tela mostrada abaixo verifica se você pode interromper o fluxo de conversação solicitando ajuda, mesmo que a caixa de diálogo adaptável ativa no momento não contenha o gatilho correspondente.

![exemplo de interrupção de caixa de diálogo de teste adaptável](./media/adaptive-dialogs/test-interruption-bot.png)

<!--## Additional information-->

<!-- Footnote-style links -->

<!-- Prerequisites ----->
[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md
[adaptive-dialog-introduction]: bot-builder-adaptive-dialog-Introduction.md
[adaptive-dialog-input]: bot-builder-concept-adaptive-dialog-Inputs.md
[adaptive-dialog-interruptions]: bot-builder-concept-adaptive-dialog-interruptions.md

<!-- About this sample links ----->
[intents]: bot-builder-concept-adaptive-dialog-recognizers.md#intents
[utterances]: bot-builder-concept-adaptive-dialog-recognizers.md#utterances
[entities]: bot-builder-concept-adaptive-dialog-recognizers.md#entities
[recognizer-types]: bot-builder-concept-adaptive-dialog-recognizers.md#recognizer-types

[OnConversationUpdateActivity]: ../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#onconversationupdateactivity
[confirm-input]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#confirminput
[cancel-all-dialogs]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#cancelalldialogs

[lu]: ../file-format/bot-builder-lu-file-format.md
[lg]: ../file-format/bot-builder-lg-file-format.md
[language-generation]: bot-builder-concept-language-generation.md
[structured-response-template]: ../language-generation/language-generation-structured-response-template.md

[adaptive-expressions]: bot-builder-concept-adaptive-expressions.md
[person-name]: ../file-format/bot-builder-lu-file-format.md#machine-learned-entity
[prediction-score]: https://aka.ms/luis-prediction-score 

<!-- Setting up LUIS ----->
[create-luis-resources-in-azure-portal]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-luis-resources-in-azure-portal
[CognitiveServicesLUISAllInOne]: https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne

<!-- Sample links ----->
[cs-sample]: https://aka.ms/cs-adaptive-interruptions-sample
[js-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/javascript_nodejs/05.interruptions-bot
[python-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/python/05.interruptions-bot
[rootdialog.lu]: https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/05.interruptions-bot/Dialogs/RootDialog/RootDialog.lu
