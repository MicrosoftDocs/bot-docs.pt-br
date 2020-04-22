---
title: Usar vários modelos de LUIS e QnA – Serviço de Bot
description: Aprenda a usar o criador LUIS e QnA no seu bot.
keywords: Luis, QnA, ferramenta Dispatch, vários serviços, rotear intenções
author: diberry
ms.author: diberry
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/27/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 48cc24fd3e78d39388bb600477f3d23e03a89fd9
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81395649"
---
# <a name="use-multiple-luis-and-qna-models"></a>Usar vários modelos de LUIS e QnA

[!INCLUDE[applies-to](../includes/applies-to.md)]

Se um bot usar vários modelos do LUIS e bases de dados de conhecimento do QnA Maker, você poderá usar a ferramenta Dispatch para determinar qual modelo do LUIS ou base de dados de conhecimento do QnA Maker tem a melhor correspondência com a entrada do usuário. A ferramenta Dispatch faz isso criando um aplicativo LUIS individual a fim de encaminhar a entrada do usuário para o modelo correto. Para obter mais informações sobre o Dispatch, incluindo os comandos da CLI, consulte o arquivo [LEIAME][dispatch-readme].

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot](bot-builder-basics.md), [LUIS][howto-luis] e [QnA Maker][howto-qna].
- [Ferramenta de expedição](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
- Uma cópia do **NLP com Dispatch** do repositório de códigos de [Exemplo de C#][cs-sample], [Exemplo de JS][js-sample] ou [Exemplo de Python][python-sample].
- Uma conta [luis.ai](https://www.luis.ai/) para publicar aplicativos LUIS.
- Uma conta do [QnA Maker](https://www.qnamaker.ai/) para publicar a base de conhecimento do QnA.

## <a name="about-this-sample"></a>Sobre este exemplo

Este exemplo é baseado em um conjunto predefinido de aplicativos LUIS e QnA Maker.

## <a name="c"></a>[C#](#tab/cs)

![Fluxo de lógica de exemplo de código](./media/tutorial-dispatch/dispatch-logic-flow.png)

`OnMessageActivityAsync` é chamado em cada entrada do usuário recebida. Esse módulo localiza a intenção do usuário com a maior pontuação e transmite o resultado a `DispatchToTopIntentAsync`. DispatchToTopIntentAsync, por sua vez, chama o manipulador de aplicativo apropriado

- `ProcessSampleQnAAsync`: para perguntas frequentes de bot.
- `ProcessWeatherAsync`: para consultas de previsão do tempo.
- `ProcessHomeAutomationAsync`: para comandos de iluminação doméstica.

## <a name="javascript"></a>[JavaScript](#tab/js)

![Fluxo de lógica de exemplo de código](./media/tutorial-dispatch/dispatch-logic-flow-js.png)

`onMessage` é chamado em cada entrada do usuário recebida. Esse módulo localiza a intenção do usuário com a maior pontuação e transmite o resultado a `dispatchToTopIntentAsync`. dispatchToTopIntentAsync, por sua vez, chama o manipulador de aplicativo apropriado

- `processSampleQnA`: para perguntas frequentes de bot.
- `processWeather`: para consultas de previsão do tempo.
- `processHomeAutomation`: para comandos de iluminação doméstica.

## <a name="python"></a>[Python](#tab/python)

![Fluxo de lógica de exemplo de código](./media/tutorial-dispatch/dispatch-logic-flow-python.png)

`on_message_activity` é chamado em cada entrada do usuário recebida. Esse módulo localiza a intenção do usuário com a maior pontuação e transmite o resultado a `_dispatch_to_top_intent`. _dispatch_to_top_intent, por sua vez, chama o manipulador de aplicativo apropriado

- `_process_sample_qna`: para perguntas frequentes de bot.
- `_process_weather`: para consultas de previsão do tempo.
- `_process_home_automation`: para comandos de iluminação doméstica.

---

O manipulador chama o serviço LUIS ou QnA Maker e retorna o resultado gerado para o usuário.

## <a name="create-luis-apps-and-qna-knowledge-base"></a>Criar aplicativos LUIS e base de dados de conhecimento do QnA

Antes de criar o modelo do Dispatch, você precisará ter seus aplicativos LUIS e bases de dados de conhecimento do QnA criados e publicados. Neste artigo, publicaremos os seguintes modelos que foram incluídos no exemplo _NLP com Dispatch_ na pasta `\CognitiveModels`:

| Nome | Descrição |
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

8. Uma vez concluído, _Treine_ e _Publique_ os aplicativos de **Automação residencial** e de **Previsão do tempo** do LUIS repetindo as etapas acima para o arquivo 'Weather.json'.

### <a name="create-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker

O primeiro passo para definir uma base de dados de conhecimento do QnA Maker é configurar um serviço do QnA Maker no Azure. Para fazer isso, siga as instruções passo a passo encontradas [aqui](https://aka.ms/create-qna-maker).

Depois que o serviço QnA Maker é criado no Azure, você precisa registrar a _Chave 1_ dos Serviços Cognitivos fornecida por seu serviço QnA Maker. Ela será usada como \<azure-qna-service-key1> ao adicionar o aplicativo do QnA Maker ao seu aplicativo Dispatch.

Saiba mais sobre os [dois tipos diferentes de chaves](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure#types-of-keys-in-qna-maker) usadas com o QnA Maker.

As etapas a seguir fornecem essa chave:

![Selecionar Serviço Cognitivo](./media/tutorial-dispatch/select-qna-cognitive-service.png)

1. No portal do Azure, selecione seu serviço cognitivo do QnA Maker.

    ![Selecionar chaves do Serviço Cognitivo](./media/tutorial-dispatch/select-cognitive-service-keys.png)

1. Selecione o ícone Chaves encontrado sob a seção _Gerenciamento de Recursos_, no menu à esquerda.

    ![Selecionar chave 1 do Serviço Cognitivo](./media/tutorial-dispatch/select-cognitive-service-key1.png)

1. Copie o valor da _Chave 1_ para a área de transferência e salve-a localmente. Ela será usada posteriormente no valor de chave (-k) \<azure-qna-service-key1> ao adicionar p aplicativo do QnA Maker ao aplicativo Dispatch.

1. Agora entre no [portal da Web do QnA Maker](https://qnamaker.ai).

1. Na etapa 2, selecione o seguinte:

    - Sua conta do Azure AD.
    - Seu nome da assinatura do Azure.
    - O nome que você criou para seu serviço QnA Maker. (Se o seu serviço QnA do Azure não for exibido inicialmente nessa lista suspensa, tente atualizar a página).

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

    A cadeia de caracteres da URL completa do seu nome do host será semelhante a "https://<id-do-host>.azure.net/qnamaker". Esses valores serão usados posteriormente no seu arquivo `appsettings.json` ou `.env`.

## <a name="dispatch-app-needs-read-access-to-existing-apps"></a>O aplicativo Dispatch precisa ter acesso de leitura aos aplicativos existentes

A ferramenta Dispatch precisa de acesso de criação para ler os aplicativos existentes do LUIS e do QnA Maker para criar um aplicativo do LUIS pai que expede para os aplicativos do LUIS e do QnA Maker. Esse acesso é fornecido as IDs do aplicativo e as chaves de criação.

### <a name="service-authoring-keys"></a>Chaves de criação de serviço

A **chave de criação** é usada apenas para criar e editar os modelos. É necessário ter uma ID e uma chave para cada um dos dois aplicativos do LUIS e o aplicativo do QnA Maker.

|Aplicativo|Localização das informações|
|--|--|
|LUIS|**ID do aplicativo** – encontrada no [portal do LUIS](https://www.luis.ai) para cada aplicativo: Gerenciar -> Informações do Aplicativo<br>**Chave de criação** – encontrada no portal do LUIS, no canto superior direito, selecione seu próprio Usuário e, em seguida, Configurações.|
|QnA Maker| **ID do aplicativo** – encontrada no [portal do QnA Maker](https://http://qnamaker.ai), na página de Configurações, após você publicar o aplicativo. Esta é a ID encontrada na primeira parte do comando POST após a base de dados de conhecimento. Um exemplo de onde encontrar a ID do aplicativo é `POST /knowledgebases/<APP-ID>/generateAnswer`.<br>**Chave de criação** – encontrada no portal do Azure, para o recurso do QnA Maker, em **Chaves**. Você precisa apenas de uma das chaves.|

A chave de criação não é usada para obter uma pontuação de previsão nem uma pontuação de confiança do aplicativo publicado. Você precisa das chaves do ponto de extremidade para esta ação. As **[chaves do ponto de extremidade](#service-endpoint-keys)** são encontradas e usadas posteriormente neste tutorial.

Saiba mais sobre os [dois tipos diferentes de chaves](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure#types-of-keys-in-qna-maker) usadas com o QnA Maker.

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

Esses serviços precisam ser publicados com os nomes corretos para que o bot seja executado corretamente.
O bot precisa de informações sobre os serviços publicados para poder acessar esses serviços.

### <a name="service-endpoint-keys"></a>Chaves do ponto de extremidade de serviço

O bot precisa dos pontos de extremidade de previsão de consulta para os três aplicativos do LUIS (Dispatch, Clima e HomeAutomation) e única base de dados de conhecimento do QnA Maker. Use a tabela a seguir para encontrar as chaves de pontos de extremidade:

|Aplicativo|Localização da chave do ponto de extremidade de consulta|
|--|--|
|LUIS|No portal do LUIS, para cada aplicativo do LUIS, na seção Gerenciar, selecione **Configurações de chaves e ponto de extremidade** para localizar as chaves associadas a cada aplicativo. Se você estiver seguindo este tutorial, a chave do ponto de extremidade será a mesma chave que o `<your-luis-authoring-key>`. A chave de criação permite 1000 ocorrências de ponto de extremidade e, em seguida, expira.|
|QnA Maker|No portal do QnA Maker, para a base de dados de conhecimento, em Gerenciar configurações, use o valor de chave mostrado nas configurações do Postman para o cabeçalho **Autorização**, sem o texto de `EndpointKey`.|

Esses valores são usados em **appsettings.json** para C# e no arquivo **.env** para JavaScript.

## <a name="c"></a>[C#](#tab/cs)

### <a name="installing-packages"></a>Instalar pacotes

Antes de executar esse aplicativo pela primeira vez, verifique se vários pacotes NuGet estão instalados:

- **Microsoft.Bot.Builder**
- **Microsoft.Bot.Builder.AI.Luis**
- **Microsoft.Bot.Builder.AI.QnA**

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

## <a name="javascript"></a>[JavaScript](#tab/js)

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

**.env** [!code-file[EmptyEnv](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/.env?range=1-10)]

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

## <a name="python"></a>[Python](#tab/python)

### <a name="installing-packages"></a>Instalar pacotes

Antes de executar esse aplicativo pela primeira vez, você precisará instalar vários pacotes PyPI.

```powershell
pip install azure
pip install botbuilder-core
pip install botbuilder-ai
```

### <a name="manually-update-your-configpy-file"></a>Atualizar manualmente o arquivo config.py
Depois que todos os aplicativos de serviço forem criados, as informações para cada um precisarão ser adicionadas ao arquivo “config.py”. O código do [Exemplo de Python][python-sample] inicial contém um arquivo config.py vazio.

**config.py**

[!code-python[config.py](~/../botbuilder-samples/samples/python/14.nlp-with-dispatch/config.py?range=10-24)]

Para cada uma das entidades mostradas abaixo, adicione os valores que você registrou anteriormente nas instruções:

```python
APP_ID = os.environ.get("MicrosoftAppId", "")
APP_PASSWORD = os.environ.get("MicrosoftAppPassword", "")

QNA_KNOWLEDGEBASE_ID = os.environ.get("QnAKnowledgebaseId", "<knowledge-base-id>")
QNA_ENDPOINT_KEY = os.environ.get("QnAEndpointKey", "<qna-maker-resource-key>")
QNA_ENDPOINT_HOST = os.environ.get("QnAEndpointHostName", "<your-hostname>")

LUIS_APP_ID = os.environ.get("LuisAppId", "<app-id-for-dispatch-app>")
LUIS_API_KEY = os.environ.get("LuisAPIKey", "<your-luis-endpoint-key>")
# LUIS endpoint host name, ie "westus.api.cognitive.microsoft.com"
LUIS_API_HOST_NAME = os.environ.get("LuisAPIHostName", "<your-dispatch-app-region>")
```

Quando todas as alterações tiverem sido concluídas, salve esse arquivo.

---

### <a name="connect-to-the-services-from-your-bot"></a>Conectar-se aos serviços do seu bot

Para se conectar aos serviços Dispatch, LUIS e QnA Maker, seu bot obtém informações do arquivo de configurações por pull.

## <a name="c"></a>[C#](#tab/cs)

Em **BotServices.cs**, as informações contidas no arquivo de configuração _appsettings.json_ são usadas para conectar o bot de expedição aos serviços `Dispatch` e `SampleQnA`. Os construtores usam os valores fornecidos para se conectarem a esses serviços.

**BotServices.cs**

[!code-csharp[ReadConfigurationInfo](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/BotServices.cs?range=10-47)]

## <a name="javascript"></a>[JavaScript](#tab/js)

Em **dispatchBot.js**, as informações contidas no arquivo de configuração _.env_ são usadas para conectar o bot de expedição aos serviços _LuisRecognizer(Dispatch)_ e  _QnA Maker_. Os construtores usam os valores fornecidos para se conectarem a esses serviços.

**bots/dispatchBot.js**

[!code-javascript[ReadConfigurationInfo](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=11-26)]

## <a name="python"></a>[Python](#tab/python)

Em **dispatch_bot.py**, as informações contidas no arquivo de configuração _config.py_ são usadas para conectar o bot de expedição aos serviços _QnAMaker_ e _LuisRecognizer_. Os construtores usam os valores fornecidos para se conectarem a esses serviços.

**bots/dispatch_bot.py**

[!code-python[ReadConfigurationInfo](~/../botbuilder-samples/samples/python/14.nlp-with-dispatch/bots/dispatch_bot.py?range=14-34)]

---

> [!NOTE]
> Por padrão, o parâmetro `includeApiResults` é definido como false, o que significa que o reconhecedor retornará apenas informações básicas sobre entidades/intenções. Se você precisar da resposta completa do LUIS (como o `ConnectedServiceResult` usado posteriormente neste tutorial), defina esse parâmetro como true. Isso adicionará a resposta completa do serviço LUIS à coleção de propriedades no `RecognizerResult`.

### <a name="call-the-services-from-your-bot"></a>Chamar os serviços do seu bot

Para cada entrada do usuário, a lógica do bot verifica a entrada com base no modelo do Dispatch combinado, localiza a principal intenção retornada e usa essas informações para chamar o serviço apropriado para a entrada.

## <a name="c"></a>[C#](#tab/cs)

No arquivo **DispatchBot.cs**, sempre que o método `OnMessageActivityAsync` é chamado, verificamos a mensagem da entrada do usuário com base no modelo do Dispatch. Em seguida, transmitimos `topIntent` e `recognizerResult` do modelo do Dispatch para o método correto para chamar o serviço e retornar o resultado.

**bots\DispatchBot.cs**

[!code-csharp[OnMessageActivity](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/bots/DispatchBot.cs?range=26-36)]

## <a name="javascript"></a>[JavaScript](#tab/js)

No método **dispatchBot.js** `onMessage`, verificamos a mensagem de entrada do usuário com base no modelo do Dispatch, encontramos a _topIntent_ e a transmitimos chamando _dispatchToTopIntentAsync_.

[!code-javascript[onMessage](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=31-44)]

## <a name="python"></a>[Python](#tab/python)

No arquivo **dispatch_bot.py**, sempre que o método `on_message_activity` é chamado, verificamos a mensagem da entrada do usuário com base no modelo do Dispatch. Em seguida, transmitimos `top_intent` e `recognize_result` do modelo do Dispatch para o método correto para chamar o serviço e retornar o resultado.

**bots/dispatch_bot.py**

[!code-python[on_message](~/../botbuilder-samples/samples/python/14.nlp-with-dispatch/bots/dispatch_bot.py?range=46-54)]

---

### <a name="work-with-the-recognition-results"></a>Trabalhar com os resultados de reconhecimento

## <a name="c"></a>[C#](#tab/cs)

Quando o modelo produz um resultado, ele indica qual serviço pode processar mais adequadamente a declaração. O código neste bot encaminha a solicitação para o serviço correspondente e, então, resume a resposta do serviço chamado. Dependendo da _intenção_ retornada pelo Dispatch, esse código usa a intenção retornada para encaminhar para o modelo LUIS ou serviço QnA correto.

**bots\DispatchBot.cs**

[!code-csharp[DispatchToTop](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/bots/DispatchBot.cs?range=51-69)]

Se o método `ProcessHomeAutomationAsync` ou `ProcessWeatherAsync` for invocado, ele transmite os resultados do modelo de expedição em _luisResult.ConnectedServiceResult_. O método especificado fornece comentários do usuário mostrando a principal intenção do modelo de expedição, além de uma lista classificada de todas as intenções e entidades que foram detectadas.

Se o método `q_sample-qna` for invocado, ele usará a entrada do usuário contida em turnContext para gerar uma resposta da base de dados de conhecimento e exibirá o resultado ao usuário.

## <a name="javascript"></a>[JavaScript](#tab/js)

Quando o modelo produz um resultado, ele indica qual serviço pode processar mais adequadamente a declaração. O código neste exemplo usa a _topIntent_ reconhecida para mostrar como encaminhar a solicitação para o serviço correspondente.

**bots/dispatchBot.js** [!code-javascript[dispatchToTopIntentAsync](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=61-77)]

Se o método `processHomeAutomation` ou `processWeather` for invocado, ele transmitirá os resultados do modelo de expedição em _recognizerResult.luisResult_. O método especificado fornece comentários do usuário mostrando a principal intenção do modelo de expedição, além de uma lista classificada de todas as intenções e entidades que foram detectadas.

Se o método `q_sample-qna` for invocado, ele usará a entrada do usuário contida em turnContext para gerar uma resposta da base de dados de conhecimento e exibirá o resultado ao usuário.

## <a name="python"></a>[Python](#tab/python)

Quando o modelo produz um resultado, ele indica qual serviço pode processar mais adequadamente a declaração. O código neste exemplo usa a _intenção_ principal reconhecida para mostrar como encaminhar a solicitação para o serviço correspondente.

**bots\dispatch_bot.py**

[!code-python[dispatch top intent](~/../botbuilder-samples/samples/python/14.nlp-with-dispatch/bots/dispatch_bot.py?range=56-70)]

Se os métodos `_process_home_automation` ou `_process_weather` forem invocados, eles passarão os resultados do modelo de expedição dentro de _recognizer_result.properties["luisResult"]_ . O método especificado fornece comentários do usuário mostrando a principal intenção do modelo de expedição, além de uma lista classificada de todas as intenções e entidades que foram detectadas.

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

Depois que seu bot estiver em execução, é possível melhorar o desempenho do bot removendo declarações semelhantes ou sobrepostas entre os aplicativos expedidos.
<!--For example, let's say that in the `Home Automation` LUIS app requests like "turn my lights on" map to a "TurnOnLights" intent, but requests like "Why won't my lights turn on?" map to a "None" intent so that they can be passed on to QnA Maker. These two utterances are too close for the dispatch LUIS app to determine if the correct child app is the LUIS app or the QnA Maker app.

When you combine the LUIS app and the QnA Maker app using dispatch, you need to do _one_ of the following:

- Remove the "None" intent from the child `Home Automation` LUIS app, and instead add the utterances from that intent to the "None" intent in the dispatcher app.
- Add logic in your bot to pass the messages that match the Dispatch LUIS app's "None" intent on to the QnA maker service. Compare the score of the Dispatch LUIS app's score and the score of the QnA Maker app. Use the highest score. This effectively removes QnA Maker from the Dispatch cycle.

Either of the above two actions will reduce the number of times that your bot responds back to your users with the message, 'Couldn't find an answer.'
-->
Você pode usar a ferramenta de linha de comando [Dispatch][dispatch-readme] para testar e avaliar seu modelo de expedição.

### <a name="to-update-or-create-a-new-luis-model"></a>Para atualizar ou criar um novo modelo do LUIS

Este exemplo é baseado em um modelo predefinido do LUIS. Confira [aqui](https://aka.ms/create-luis-model#updating-your-cognitive-models) mais informações para ajudá-lo a atualizar esse modelo ou criar um novo modelo do LUIS.

Depois de atualizar os modelos subjacentes (QnA ou LUIS), execute `dispatch refresh` para atualizar seu aplicativo de Expedição do LUIS. `dispatch refresh` é basicamente o mesmo comando que `dispatch create`, exceto pelo fato de que nenhuma nova ID do aplicativo do LUIS é criada.

Observe que os enunciados que foram adicionados diretamente no LUIS não serão mantidos ao executar `dispatch refresh`. Para manter esses enunciados extras no aplicativo de expedição, adicione-os em um arquivo de texto (um enunciado por linha) e, em seguida, adicione o arquivo à Expedição executando o comando:

```powershell
dispatch add -t file -f <file path> --intentName <target intent name, ie l_General>
```

Quando o arquivo com enunciados extras for adicionado à Expedição, os enunciados permanecerão após cada atualização.

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

<!-- Foot-note style links -->



[howto-luis]: bot-builder-howto-v4-luis.md
[howto-qna]: bot-builder-howto-qna.md

[cs-sample]: https://aka.ms/dispatch-sample-cs
[js-sample]: https://aka.ms/dispatch-sample-js
[python-sample]: https://aka.ms/dispatch-sample-python

[dispatch-readme]: https://aka.ms/dispatch-command-line-tool
<!--[dispatch-evaluate]: https://aka.ms/dispatch-command-line-tool#evaluating-your-dispatch-model-->
