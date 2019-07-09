---
title: Usar vários modelos de LUIS e QnA | Microsoft Docs
description: Aprenda a usar o criador LUIS e QnA no seu bot.
keywords: Luis, QnA, ferramenta Dispatch, vários serviços, rotear intenções
author: diberry
ms.author: diberry
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c72f978e927f05f430ec94cf747016f4ebc15c5d
ms.sourcegitcommit: 0e6c49964b96c1ac8485ba7afe0daae04b671138
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492007"
---
# <a name="use-multiple-luis-and-qna-models"></a>Usar vários modelos de LUIS e QnA

[!INCLUDE[applies-to](../includes/applies-to.md)]

Se um bot usar vários modelos do LUIS e bases de dados de conhecimento do QnA Maker, você poderá usar a ferramenta Dispatch para determinar qual modelo do LUIS ou base de dados de conhecimento do QnA Maker tem a melhor correspondência com a entrada do usuário. A ferramenta Dispatch faz isso criando um aplicativo LUIS individual a fim de encaminhar a entrada do usuário para o modelo correto. Para obter mais informações sobre o Dispatch, incluindo os comandos da CLI, consulte o arquivo [LEIAME][dispatch-readme].

## <a name="prerequisites"></a>Pré-requisitos
- Conhecimento de [conceitos básicos do bot](bot-builder-basics.md), [LUIS][howto-luis], and [QnA Maker][howto-qna]. 
- [Ferramenta de expedição](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
- Uma cópia do **NLP com Dispatch** do repositório de códigos or [JS Sample][js-sample] de [exemplo de C#][cs-sample].
- Uma conta [luis.ai](https://www.luis.ai/) para publicar aplicativos LUIS.
- Uma conta do [QnA Maker](https://www.qnamaker.ai/) para publicar a base de conhecimento do QnA.

## <a name="about-this-sample"></a>Sobre este exemplo

Este exemplo é baseado em um conjunto predefinido de aplicativos LUIS e QnA Maker.

## <a name="ctabcs"></a>[C#](#tab/cs)

![Fluxo de lógica de exemplo de código](./media/tutorial-dispatch/dispatch-logic-flow.png)

`OnMessageActivityAsync` é chamado em cada entrada do usuário recebida. Esse módulo localiza a intenção do usuário com a maior pontuação e transmite o resultado a `DispatchToTopIntentAsync`. DispatchToTopIntentAsync, por sua vez, chama o manipulador de aplicativo apropriado

- `ProcessSampleQnAAsync`: para perguntas frequentes de bot.
- `ProcessWeatherAsync`: para consultas de previsão do tempo.
- `ProcessHomeAutomationAsync`: para comandos de iluminação doméstica.

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

![Fluxo de lógica de exemplo de código](./media/tutorial-dispatch/dispatch-logic-flow-js.png)

`onMessage` é chamado em cada entrada do usuário recebida. Esse módulo localiza a intenção do usuário com a maior pontuação e transmite o resultado a `dispatchToTopIntentAsync`. dispatchToTopIntentAsync, por sua vez, chama o manipulador de aplicativo apropriado

- `processSampleQnA`: para perguntas frequentes de bot.
- `processWeather`: para consultas de previsão do tempo.
- `processHomeAutomation`: para comandos de iluminação doméstica.

---

O manipulador chama o serviço LUIS ou QnA Maker e retorna o resultado gerado para o usuário.

## <a name="create-luis-apps-and-qna-knowledge-base"></a>Criar aplicativos LUIS e base de dados de conhecimento do QnA
Antes de criar o modelo do Dispatch, você precisará ter seus aplicativos LUIS e bases de dados de conhecimento do QnA criados e publicados. Neste artigo, publicaremos os seguintes modelos que foram incluídos no exemplo _NLP com Dispatch_ na pasta `\CognitiveModels`: 

| NOME | DESCRIÇÃO |
|------|------|
| HomeAutomation | Um aplicativo LUIS que reconhece uma intenção de automação residencial com dados de entidade associados.|
| Clima | Um aplicativo LUIS que reconhece intenções relacionadas ao clima com os dados de localização.|
| QnAMaker  | Uma base de dados de conhecimento do QnA Maker que fornece respostas a perguntas simples sobre o bot. |

### <a name="create-luis-apps"></a>Criar aplicativos LUIS
1. Faça logon no [portal da Web do LUIS](https://www.luis.ai/). Na seção _Meus aplicativos_, selecione a guia _Importar novo aplicativo_. A caixa de diálogo a seguir será exibida:

    ![Importar arquivo json do LUIS](./media/tutorial-dispatch/import-new-luis-app.png)

2. Selecione o botão _Escolher arquivo de aplicativo_, navegue até a pasta CognitiveModel do código de exemplo e selecione o arquivo 'HomeAutomation.json'. Deixe o campo de nome opcional em branco. 

3. Selecione _Concluído_.

4. Depois que o LUIS abrir seu aplicativo de automação residencial, selecione o botão _Treinar_. Isso treinará seu aplicativo usando o conjunto de declarações que você acabou de importar usando o arquivo “home-automation.json”.

5. Quando o treinamento estiver concluído, selecione o botão _Publicar_. A caixa de diálogo a seguir será exibida:

    ![Publicar aplicativo LUIS](./media/tutorial-dispatch/publish-luis-app.png)

6. Escolha o ambiente de “produção” e, em seguida, selecione o botão _Publicar_.

7. Depois que seu novo aplicativo LUIS tiver sido publicado, selecione a guia _GERENCIAR_. Na página de informações do aplicativo, registre os valores `Application ID` como "_app-id-for-app_" e `Display name` como "_name-of-app_". Na página 'Chave e Pontos de Extremidade', registre os valores `Authoring Key` como "_your-luis-authoring-key_" e `Region` como "_your-region_". Esses valores serão usados posteriormente no seu arquivo “appsetting.json”.

8. Uma vez concluído, _Treine_ e _Publique_ o aplicativo de previsão do tempo LUIS e o aplicativo de expedição LUIS repetindo as etapas acima para o arquivo 'Weather.json'.

### <a name="create-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker

O primeiro passo para definir uma base de dados de conhecimento do QnA Maker é configurar um serviço do QnA Maker no Azure. Para fazer isso, siga as instruções passo a passo encontradas [aqui](https://aka.ms/create-qna-maker).

Depois que o serviço QnA Maker é criado no Azure, você precisa registrar a _Chave 1_ dos Serviços Cognitivos fornecida por seu serviço QnA Maker. Ela será usada como \<azure-qna-service-key1> ao adicionar o aplicativo do QnA Maker ao seu aplicativo Dispatch. As etapas a seguir fornecem essa chave:
    
![Selecionar Serviço Cognitivo](./media/tutorial-dispatch/select-qna-cognitive-service.png)

1. No portal do Azure, selecione seu serviço cognitivo do QnA Maker.

    ![Selecionar chaves do Serviço Cognitivo](./media/tutorial-dispatch/select-cognitive-service-keys.png)

1. Selecione o ícone Chaves encontrado sob a seção _Gerenciamento de Recursos_, no menu à esquerda.

    ![Selecionar chave 1 do Serviço Cognitivo](./media/tutorial-dispatch/select-cognitive-service-key1.png)

1. Copie o valor da _Chave 1_ para a área de transferência e salve-a localmente. Ela será usada posteriormente no valor de chave (-k) \<azure-qna-service-key1> ao adicionar p aplicativo do QnA Maker ao aplicativo Dispatch.

1. Agora entre no [portal da Web do QnA Maker](https://qnamaker.ai). 

1. Na etapa 2, selecione o seguinte:

    * Sua conta do Azure AD.
    * Seu nome da assinatura do Azure.
    * O nome que você criou para seu serviço QnA Maker. (Se o seu serviço QnA do Azure não for exibido inicialmente nessa lista suspensa, tente atualizar a página).

    ![Criar QnA Etapa 2](./media/tutorial-dispatch/create-qna-step-2.png) 
     

1. Na etapa 3, forneça um nome para sua base de dados de conhecimento do QnA Maker. Para este exemplo, use o nome 'sample-qna'.

    ![Crie QnA Etapa 3](./media/tutorial-dispatch/create-qna-step-3.png)

1. Na etapa 4, selecione a opção _+ Adicionar Arquivo_, navegue até a pasta CognitiveModel do código de exemplo e selecione o arquivo 'QnAMaker.tsv'. Há uma seleção adicional para adicionar uma personalidade de _Bate-papo_ à sua base de dados de conhecimento, mas nosso exemplo não inclui essa opção.

    ![Criar QnA Etapa 4](./media/tutorial-dispatch/create-qna-step-4.png)

1. Na etapa 5, selecione _Criar sua base de dados de conhecimento_.

1. Depois que a base de dados de conhecimento for criada com base no arquivo carregado, selecione _Salvar e treinar_ e, quando terminar, selecione a guia _PUBLICAR_ e publique o aplicativo.

1. Depois que seu aplicativo do QnA Maker for publicado, selecione a guia _CONFIGURAÇÕES_ e role para baixo até os “Detalhes da implantação”. Registre os seguintes valores da solicitação HTTP de exemplo, _Carteiro_.

    ```text
    POST /knowledge bases/<knowledge-base-id>/generateAnswer
    Host: <your-hostname>  // NOTE - this is a URL.
    Authorization: EndpointKey <qna-maker-resource-key>
    ```
    
    A cadeia de caracteres da URL completa do seu nome do host será semelhante a "https://< >.azure.net/qnamaker". Esses valores serão usados posteriormente no seu arquivo `appsettings.json` ou `.env`.

## <a name="dispatch-app-needs-read-access-to-existing-apps"></a>O aplicativo Dispatch precisa ter acesso de leitura aos aplicativos existentes

A ferramenta Dispatch precisa de acesso de criação para ler os aplicativos existentes do LUIS e do QnA Maker para criar um aplicativo do LUIS pai que expede para os aplicativos do LUIS e do QnA Maker. Esse acesso é fornecido as IDs do aplicativo e as chaves de criação. É necessário ter uma ID e uma chave para cada um dos dois aplicativos do LUIS e o aplicativo do QnA Maker.

|Aplicativo|Localização das informações|
|--|--|
|LUIS|ID do aplicativo – encontrada no [portal do LUIS](https://www.luis.ai) para cada aplicativo; Gerenciar -> Informações do Aplicativo<br>Chave de criação – encontrada no portal do LUIS, no canto superior direito, selecione seu próprio Usuário e, em seguida, Configurações.|
|QnA Maker| ID do aplicativo – encontrada no [portal do QnA Maker](https://http://qnamaker.ai), na página de Configurações, após você publicar o aplicativo. Esta é a ID encontrada na primeira parte do comando POST após a base de dados de conhecimento. Um exemplo de onde encontrar a ID do aplicativo é `POST /knowledgebases/{APP-ID}/generateAnswer`.<br>Chave de criação – encontrada no portal do Azure, para o recurso do QnA Maker, nas **Chaves**. Você precisa apenas de uma das chaves.|

## <a name="create-the-dispatch-model"></a>Criar o modelo de expedição

A interface da CLI para a ferramenta Dispatch cria o modelo para expedição para o aplicativo correto do LUIS ou do QnA Maker.

1. Abra um prompt de comando ou janela de terminal e alterne para o diretório **CognitiveModels**
1. Verifique se você tem a versão atual do npm e a ferramenta Dispatch.

    ```cmd
    npm i -g npm
    npm i -g botdispatch
    ```

1. Use `dispatch init` para inicializar a criação de um arquivo `.dispatch` para seu modelo do Dispatch. Crie-o usando um nome de arquivo que você consiga reconhecer depois.

    ```cmd
    dispatch init -n <filename-to-create> --luisAuthoringKey "<your-luis-authoring-key>" --luisAuthoringRegion <your-region>
    ```

1. Use `dispatch add` para adicionar seus aplicativos do LUIS e bases de dados de conhecimento do QnA Maker ao arquivo `.dispatch`.

    ```cmd
    dispatch add -t luis -i "<app-id-for-weather-app>" -n "<name-of-weather-app>" -v <app-version-number> -k "<your-luis-authoring-key>" --intentName l_Weather
    dispatch add -t luis -i "<app-id-for-home-automation-app>" -n "<name-of-home-automation-app>" -v <app-version-number> -k "<your-luis-authoring-key>" --intentName l_HomeAutomation
    dispatch add -t qna -i "<knowledge-base-id>" -n "<knowledge-base-name>" -k "<azure-qna-service-key1>" --intentName q_sample-qna
    ```

1. Use `dispatch create` para gerar um modelo do Dispatch baseado no arquivo `.dispatch`.

    ```cmd
    dispatch create
    ```

1. Publique o aplicativo Dispatch do LUIS, que acaba de ser criado.

## <a name="use-the-dispatch-luis-app"></a>Usar o aplicativo Dispatch do LUIS

O aplicativo do LUIS gerado define as intenções para cada um dos aplicativos filho e bases de dados de conhecimento, bem como uma intenção _none_ para quando o enunciado não tem uma opção adequada.

- `l_HomeAutomation`
- `l_Weather`
- `None`
- `q_sample-qna`

Esses serviços precisam ser publicados com os nomes corretos para que o bot seja executado corretamente. O bot precisa de informações sobre os serviços publicados para poder acessar esses serviços.

O bot precisa dos pontos de extremidade de previsão de consulta para os três aplicativos do LUIS (Dispatch, Clima e HomeAutomation) e única base de dados de conhecimento do QnA Maker. Use a tabela a seguir para encontrar as chaves de pontos de extremidade:

|Aplicativo|Localização da chave do ponto de extremidade de consulta|
|--|--|
|LUIS|No portal do LUIS, para cada aplicativo do LUIS, na seção Gerenciar, selecione **Configurações de chaves e ponto de extremidade** para localizar as chaves associadas a cada aplicativo. Se você estiver seguindo este tutorial, a chave do ponto de extremidade será a mesma chave que o `<your-luis-authoring-key>`. A chave de criação permite 1000 ocorrências de ponto de extremidade e, em seguida, expira.|
|QnA Maker|No portal do QnA Maker, para a base de dados de conhecimento, em Gerenciar configurações, use o valor de chave mostrado nas configurações do Postman para o cabeçalho **Autorização**, sem o texto de `EndpointKey `.|

Esses valores são usados em **appsettings.json** para C# e no arquivo **.env** para JavaScript.

## <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="installing-packages"></a>Instalar pacotes

Antes de executar esse aplicativo pela primeira vez, verifique se estes pacotes NuGet estão instalados:

**Microsoft.Bot.Builder**

**Microsoft.Bot.Builder.AI.Luis**

**Microsoft.Bot.Builder.AI.QnA**

### <a name="manually-update-your-appsettingsjson-file"></a>Atualizar manualmente o arquivo appsettings.json

Depois que todos os aplicativos de serviço forem criados, as informações para cada um precisarão ser adicionadas ao arquivo “appsettings.json”. O código inicial do [Exemplo de C#][cs-sample] contém um arquivo appsettings.json vazio:

**appsettings.json**  
[!code-json[AppSettings](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/AppSettings.json?range=8-17)]

Para cada uma das entidades mostradas abaixo, adicione os valores que você registrou anteriormente nas instruções:

**appsettings.json**
```json
"MicrosoftAppId": "",
"MicrosoftAppPassword": "",
  
"QnAKnowledgebaseId": "<knowledge-base-id>",
"QnAEndpointKey": "<qna-maker-resource-key>",
"QnAEndpointHostName": "<your-hostname>",

"LuisAppId": "<app-id-for-dispatch-app>",
"LuisAPIKey": "<your-luis-endpoint-key>",
"LuisAPIHostName": "<your-dispatch-app-region>",
```
Quando todas as alterações tiverem sido concluídas, salve esse arquivo.

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="installing-packages"></a>Instalar pacotes

Antes de executar esse aplicativo pela primeira vez, você precisará instalar vários pacotes npm.

```powershell
npm install --save botbuilder
npm install --save botbuilder-ai
```
Para usar o arquivo de configuração .env, o bot precisará da inclusão de mais um pacote:

```powershell
npm install --save dotenv
```

### <a name="manually-update-your-env-file"></a>Atualizar manualmente o arquivo .env

Depois que todos os aplicativos de serviço forem criados, as informações para cada um precisarão ser adicionadas ao arquivo “.env”. O código inicial do [Exemplo de JavaScript][js-sample] contém um arquivo .env vazio. 

**.env**  
[!code-file[EmptyEnv](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/.env?range=1-10)]

Adicione os valores de conexão de serviço conforme mostrado abaixo:

**.env**
```file
MicrosoftAppId=""
MicrosoftAppPassword=""

QnAKnowledgebaseId="<knowledge-base-id>"
QnAEndpointKey="<qna-maker-resource-key>"
QnAEndpointHostName="<your-hostname>"

LuisAppId=<app-id-for-dispatch-app>
LuisAPIKey=<your-luis-endpoint-key>
LuisAPIHostName=<your-dispatch-app-region>

```
Quando todas as alterações tiverem sido realizadas, salve esse arquivo.

---

### <a name="connect-to-the-services-from-your-bot"></a>Conectar-se aos serviços do seu bot

Para se conectar aos serviços LUIS, QnA Maker e Dispatch, seu bot recupera informações do arquivo de configurações (o arquivo `appsettings.json` ou `.env`).

## <a name="ctabcs"></a>[C#](#tab/cs)

Em **BotServices.cs**, as informações contidas no arquivo de configuração _appsettings.json_ são usadas para conectar o bot de expedição aos serviços `Dispatch` e `SampleQnA`. Os construtores usam os valores fornecidos para se conectarem a esses serviços.

**BotServices.cs**  
[!code-csharp[ReadConfigurationInfo](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/BotServices.cs?range=14-30)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Em **dispatchBot.js**, as informações contidas no arquivo de configuração _.env_ são usadas para conectar o bot de expedição aos serviços _LuisRecognizer(Dispatch)_ e  _QnA Maker_. Os construtores usam os valores fornecidos para se conectarem a esses serviços.

**dispatchBot.js**  
[!code-javascript[ReadConfigurationInfo](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=18-31)]

---

### <a name="call-the-services-from-your-bot"></a>Chamar os serviços do seu bot

Para cada entrada do usuário, a lógica do bot verifica a entrada com base no modelo do Dispatch combinado, localiza a principal intenção retornada e usa essas informações para chamar o serviço apropriado para a entrada.

## <a name="ctabcs"></a>[C#](#tab/cs)

No arquivo **DispatchBot.cs**, sempre que o método `OnMessageActivityAsync` é chamado, verificamos a mensagem da entrada do usuário com base no modelo do Dispatch. Em seguida, transmitimos `topIntent` e `recognizerResult` do modelo do Dispatch para o método correto para chamar o serviço e retornar o resultado.

**DispatchBot.cs**  
[!code-csharp[OnMessageActivity](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/bots/DispatchBot.cs?range=26-36)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

No método **dispatchBot.js** `onMessage`, verificamos a mensagem de entrada do usuário com base no modelo do Dispatch, encontramos a _topIntent_ e a transmitimos chamando  _dispatchToTopIntentAsync_.

**dispatchBot.js**  

[!code-javascript[OnMessageActivity](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=37-50)]

---

### <a name="work-with-the-recognition-results"></a>Trabalhar com os resultados de reconhecimento

## <a name="ctabcs"></a>[C#](#tab/cs)

Quando o modelo produz um resultado, ele indica qual serviço pode processar mais adequadamente a declaração. O código neste bot encaminha a solicitação para o serviço correspondente e, então, resume a resposta do serviço chamado. Dependendo da _intenção_ retornada pelo Dispatch, esse código usa a intenção retornada para encaminhar para o modelo LUIS ou serviço QnA correto.

**DispatchBot.cs**  
[!code-csharp[DispatchToTop](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/bots/DispatchBot.cs?range=51-69)]

Se o método `ProcessHomeAutomationAsync` ou `ProcessWeatherAsync` for invocado, ele transmite os resultados do modelo de expedição em _luisResult.ConnectedServiceResult_. O método especificado fornece comentários do usuário mostrando a principal intenção do modelo de expedição, além de uma lista classificada de todas as intenções e entidades que foram detectadas.

Se o método `q_sample-qna` for invocado, ele usará a entrada do usuário contida em turnContext para gerar uma resposta da base de dados de conhecimento e exibirá o resultado ao usuário.

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Quando o modelo produz um resultado, ele indica qual serviço pode processar mais adequadamente a declaração. O código neste exemplo usa a _topIntent_ reconhecida para mostrar como encaminhar a solicitação para o serviço correspondente.

**DispatchBot.cs**  
[!code-javascript[DispatchToTop](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=67-83)]

Se o método `processHomeAutomation` ou `processWeather` for invocado, ele transmitirá os resultados do modelo de expedição em _recognizerResult.luisResult_. O método especificado fornece comentários do usuário mostrando a principal intenção do modelo de expedição, além de uma lista classificada de todas as intenções e entidades que foram detectadas.

Se o método `q_sample-qna` for invocado, ele usará a entrada do usuário contida em turnContext para gerar uma resposta da base de dados de conhecimento e exibirá o resultado ao usuário.

---

> [!NOTE]
> Se fosse um aplicativo de produção, aqui é onde os métodos LUIS selecionados se conectariam ao serviço especificado, transmitiriam a entrada do usuário e processariam a intenção do LUIS e os dados de entidade retornados.

## <a name="test-your-bot"></a>Testar seu bot

1. Usando o ambiente de desenvolvimento, inicie o código de exemplo. Observe o endereço de _localhost_ mostrado na barra de endereços da janela do navegador aberta por seu aplicativo: "https://localhost:<Port_Number>". 
1. Abre o Bot Framework Emulator e selecione `Create a new bot configuration`. Um arquivo `.bot` permite que você use o _Inspetor_ do emulador de bor para ver o JSON retornado do LUIS e do QnA Maker.
1. Na caixa de diálogo **Nova configuração de bot**, digite o nome do bot e a URL do ponto de extremidade, como `http://localhost:3978/api/messages`. Salve o arquivo na raiz de seu projeto de código de amostra do bot.
1. Abra o arquivo de bot e adicione seções para seus aplicativos do LUIS e do QnA Maker. Use [este arquivo de exemplo](https://github.com/microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/sample-bot-file.json) como modelo para as configurações. Salve as alterações.
1. Selecione o nome do bot na lista **Meus Bots** para acessar o bot em execução. Para sua referência, aqui estão algumas das perguntas e dos comandos cobertos pelos serviços criados para o bot:

    - QnA Maker
      - `hi`, `good morning`
      - `what are you`, `what do you do`
    - LUIS (automação doméstica)
      - `turn on bedroom light`
      - `turn off bedroom light`
      - `make some coffee`
    - LUIS (clima)
      - `whats the weather in redmond washington`
      - `what's the forecast for london`
      - `show me the forecast for nebraska`

## <a name="dispatch-for-user-utterance-to-qna-maker"></a>Expedição do enunciado do usuário para o QnA Maker

1. No emulador de bot, insira o texto `hi` e envie o enunciado. O bot envia essa consulta para o aplicativo Dispatch do LUIS e recebe uma resposta que indica qual aplicativo filho deve receber esse enunciado para processamento adicional. 

1. Selecionando a linha `LUIS Trace` no log, você pode ver a resposta do LUIS no emulador de bot. O resultado no LUIS do aplicativo Dispatch do LUIS é exibido no Inspetor. 

    ```json
    {
      "luisResponse": {
        "entities": [],
        "intents": [
          {
            "intent": "q_sample-qna",
            "score": 0.9489713
          },
          {
            "intent": "l_HomeAutomation",
            "score": 0.0612499453
          },
          {
            "intent": "None",
            "score": 0.008567564
          },
          {
            "intent": "l_Weather",
            "score": 0.0025761195
          }
        ],
        "query": "Hi",
        "topScoringIntent": {
          "intent": "q_sample-qna",
          "score": 0.9489713
        }
      }
    }
    ```
    
    Como o enunciado, `hi`, faz parte da intenção **q_sample-qna** do aplicativo Dispatch do LUIS e é selecionado como o `topScoringIntent`, o bot fará uma segunda solicitação, desta vez para o aplicativo do QnA Maker, com o mesmo enunciado. 

1. Selecione a linha `QnAMaker Trace` no log de emulador de bot. O resultado do QnA Maker é exibido no Inspetor. 

```json
{
    "questions": [
        "hi",
        "greetings",
        "good morning",
        "good evening"
    ],
    "answer": "Hello!",
    "score": 1,
    "id": 96,
    "source": "QnAMaker.tsv",
    "metadata": [],
    "context": {
        "isContextOnly": false,
        "prompts": []
    }
}
```

## <a name="resolving-incorrect-top-intent-from-dispatch"></a>Resolvendo intenção incorreta no Dispatch

Depois que seu bot estiver em execução, é possível melhorar o desempenho do bot removendo declarações semelhantes ou sobrepostas entre os aplicativos expedidos. Por exemplo, vamos supor que no aplicativo do LUIS `Home Automation`, solicitações como "acender minhas luzes" são mapeadas para uma intenção "TurnOnLights", mas solicitações como "Por que minhas luzes não acendem?" são mapeadas para uma intenção "None", para que possam ser passadas para o QnA Maker. Esses dois enunciados são muito próximos para o aplicativo Dispatch do LUIS determinar se o aplicativo filho correto é o aplicativo do LUIS ou o do QnA Maker.

Quando você combina o aplicativo do LUIS e aplicativo do QnA Maker usando o Dispatch, é necessário executar _um_ dos seguintes procedimentos:

* Remova a intenção “None” do aplicativo LUIS `Home Automation` filho e, em vez disso, adicione as declarações dessa intenção à intenção “None” no aplicativo Dispatch.
* Adicione lógica em seu bot para passar as mensagens que correspondem à intenção "None" do aplicativo Dispatch do LUIS para o serviço do QnA Maker. Compare a pontuação do aplicativo Dispatch do LUIS e a pontuação do aplicativo do QnA Maker. Use a pontuação mais alta. Isso remove efetivamente o QnA Maker do ciclo do Dispatch. 

Qualquer uma dessas duas ações acima reduzirá o número de vezes que o seu bot responde aos usuários com a mensagem “Não foi possível encontrar uma resposta”.

### <a name="to-update-or-create-a-new-luis-model"></a>Para atualizar ou criar um novo modelo do LUIS

Este exemplo é baseado em um modelo predefinido do LUIS. Confira [aqui](https://aka.ms/create-luis-model#updating-your-cognitive-models) mais informações para ajudá-lo a atualizar esse modelo ou criar um novo modelo do LUIS.

### <a name="to-delete-resources"></a>Para excluir recursos

Este exemplo cria vários aplicativos e recursos que você pode excluir usando as etapas listadas abaixo, mas você não deve excluir os recursos dos quais *outros aplicativos ou serviços* dependam.

Para excluir recursos do LUIS:

1. Entre no portal [luis.ai](https://www.luis.ai).
1. Acesse a página _Meus Aplicativos_.
1. Selecione os aplicativos criados por este exemplo.
   - `Home Automation`
   - `Weather`
   - `NLP-With-Dispatch-BotDispatch`
1. Clique em _Excluir_ e clique em _OK_ para confirmar.

Para excluir recursos do QnA Maker:

1. Entre no portal [qnamaker.ai](https://www.qnamaker.ai/).
1. Acesse a página _My knowledge bases_ (Minhas bases de conhecimento).
1. Clique no botão de exclusão da base de conhecimento `Sample QnA` e clique em _Delete_ (Excluir) para confirmar.

### <a name="best-practice"></a>Melhor prática

Para melhorar os serviços usados neste exemplo, confira a prática recomendada para [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices) e [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices).


[howto-luis]: bot-builder-howto-v4-luis.md
[howto-qna]: bot-builder-howto-qna.md

[cs-sample]: https://aka.ms/dispatch-sample-cs
[js-sample]: https://aka.ms/dispatch-sample-js

[dispatch-readme]: https://aka.ms/botbuilder-tools-dispatch
