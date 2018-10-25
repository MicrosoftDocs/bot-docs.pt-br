---
title: Criar bots com a CLI do Azure
description: As ferramentas do Construtor de Bot permitem que você gerencie seus recursos de bot diretamente na linha de comando
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: tools
ms.date: 08/31/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b614b11852516ec8dd426d210aacc85a0f39c813
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999408"
---
# <a name="create-bots-with-azure-cli"></a>Criar bots com a CLI do Azure

[!INCLUDE [pre-release-label](./includes/pre-release-label-v3.md)]

Neste tutorial, você aprenderá como: 
- Criar um novo bot usando a CLI do Azure 
- Baixar uma cópia local para desenvolvimento
- Usar a nova ferramenta MSBot para armazenar todas as suas informações de recurso do bot
- Gerenciar, criar ou atualizar modelos de LUIS e QnA com LUDown
- Conectar-se aos serviços do LUIS ou do QnA Maker por meio da CLI
- Implantar seu bot no Azure da CLI

## <a name="prerequisites"></a>Pré-requisitos

Para usar essas ferramentas da linha de comando, você precisará do Node.js instalado em seu computador: 
- [Node.js (v8.5 ou superior)](https://nodejs.org/en/)

## <a name="1-install-tools"></a>1. Instalar ferramentas
1. [Instale](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) a versão mais recente da CLI do Azure.
2. [Instale](https://aka.ms/botbuilder-tools-readme) as Ferramentas do Bot Builder.

Agora você pode gerenciar bots usando a CLI do Azure como qualquer outro recurso do Azure.

>[!TIP]
> A extensão de Bot do Azure atualmente suporta apenas bots v3.
  
3. Faça logon na CLI do Azure executando os seguintes comandos.

```azurecli
az login
```
Uma janela do navegador será aberta, permitindo que você entre. Após a entrada, você verá a seguinte mensagem:

![Logon do dispositivo MS](media/bot-builder-tools/az-browser-login.png)

E, na janela da linha de comando, você verá as seguintes informações:

![Comando de logon do Azure](media/bot-builder-tools/az-login-command.png)

## <a name="2-create-a-new-bot-from-azure-cli"></a>2. Criar um novo bot na CLI do Azure

Usar a CLI do Azure para criar novos bots totalmente a partir da linha de comando. 

```azurecli
az bot [command]
```
|Comandos|  |
|----|----|
| create      |adicionar um recurso|
| excluir     |clonar um recurso|
| baixar   | baixar o código-fonte do bot|
| Publicar   |publicar em um serviço de bot existente|
| mostrar |mostrar recursos de bot existente.|
| atualizar| Atualizar um serviço de bot existente|

Para criar um novo bot da CLI, você precisa selecionar um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) existente, ou criar um novo. 

```azurecli
az bot create --resource-group "my-resource-group" --name "my-bot-name" --kind "my-resource-type" --version v3 --description "description-of-my-bot"
```
Os valores permitidos para `--kind` são: `function, registration, webapp` e para `--version` são `v3, v4`.  Depois de uma solicitação bem-sucedida, você verá a mensagem de confirmação.
```
Obtained msa app id and password. Provisioning bot now.
```

> [!TIP]
> Se você receber uma mensagem de erro de que **não foi possível encontrar o grupo de recursos**, você talvez precise configurar sua [assinatura](https://docs.microsoft.com/en-us/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) na CLI do Azure. A assinatura do Azure deve corresponder à inserida quando você criou o grupo de recursos. Para configurá-la, insira o seguinte.
> ```azurecli
> az account set --subscription "your-subscription-name"
> ```
> Para exibir uma lista de assinaturas para sua conta, insira o comando a seguir.
> ```azurecli
> az account list
> ```

Por padrão, um bot .NET novo será criado. Você pode especificar qual plataforma SDK, especificando a linguagem usando o argumento **-- lang**. Atualmente, o pacote de extensão do bot dá suporte a C# e SDKs de bot do Node.js. Por exemplo, para **criar um bot de Node.js**:

```azurecli
az bot create --resource-group "my-resource-group" --name "my-bot-name" --kind "my-resource-type" --description "description-of-my-bot" --lang Node 
```
Seu novo bot de eco será provisionado para o grupo de recursos no Azure; para testá-lo, selecione simplesmente **testar no Webchat** no cabeçalho de gerenciamento de bot do modo de exibição do bot de aplicativo web. 

![Bot de eco do Azure](media/bot-builder-tools/az-echo-bot.png) 

## <a name="3-download-the-bot-locally"></a>3. Baixar o bot localmente

Há duas maneiras de baixar o código-fonte:
- No portal do Azure.
- Usando a nova CLI do Azure.

Para baixar o código-fonte de bot do [portal do Azure](http://portal.azure.com), simplesmente selecione seu recurso de bot e selecione **Build** em gerenciamento do bot. Há várias opções diferentes disponíveis para gerenciar ou recuperar o código-fonte do seu bot localmente.

![Baixe o bot do portal do Azure](media/bot-builder-tools/az-portal-manage-code.png)

Para baixar seu código-fonte de bot usando a CLI, digite o comando a seguir. Seu bot será baixado localmente.

```azurecli
az bot download --name "my-bot-name" --resource-group "my-resource-group"
```

![Comando de download da CLI](media/bot-builder-tools/cli-bot-download-command.png)

## <a name="4-store-your-bot-information-with-msbot"></a>4. Armazenar as informações de bot no MSBot

A nova ferramenta MSBot permite que você crie um arquivo **.bot**, que armazena metadados sobre os diferentes serviços consumidos pelo bot, em um só local. Esse arquivo também permite que o bot se conecte a esses serviços por meio da CLI. As ferramentas de MSBot dão suporte a vários comandos. Confira o arquivo [leiame](https://aka.ms/botbuilder-tools-msbot-readme) para obter detalhes. 

Para instalar o MSBot, execute:

```shell
npm install -g msbot
```

Para criar um arquivo de bot, na CLI, insira **msbot init** seguido pelo nome do bot e o ponto de extremidade de URL de destino, por exemplo:

```shell
msbot init --name name-of-my-bot --endpoint http://localhost:bot-port-number/api/messages
```

Para conectar o bot a um serviço, na CLI, insira **msbot connect** seguido pelo serviço apropriado:

```shell
msbot connect service-type
```

| Tipo de serviço | DESCRIÇÃO |
| ------ | ----------- |
| azure  |conectar o bot a um registro do Serviço de Bot do Azure|
|endpoint| conectar o bot a um ponto de extremidade, como localhost|
|luis     | conectar o bot a um aplicativo LUIS |
| qna     |conectar o bot a uma base de dados de conhecimento do QnA|
|help [cmd]  |exibir a ajuda do [cmd]|

Consulte o arquivo [leiame](https://aka.ms/botbuilder-tools-msbot-readme) para obter uma lista completa de serviços com suporte.

### <a name="connect-your-bot-to-abs-with-the-bot-file"></a>Conectar o bot aos ABS com o arquivo .bot

Com a ferramenta MSBot instalada, você pode conectar com facilidade o bot a um grupo de recursos existente no Serviço de Bot do Azure executando o comando az bot **show**.

```azurecli
az bot show -n my-bot-name -g my-resource-group --msbot | msbot connect azure --stdin
```

Isso usará o ponto de extremidade atual, a appID da MSA e a senha do grupo de recursos de destino e atualizará as informações adequadamente no arquivo .bot.


## <a name="5-manage-update-or-create-luis-and-qna-services-with--new-botbuilder-tools"></a>5. Gerenciar, atualizar ou criar serviços do LUIS e do QnA com novas ferramentas do construtor de bot

As [ferramentas do construtor de bot](https://aka.ms/botbuilder-tools) são um novo conjunto de ferramentas que permite que você gerencie e interaja com os recursos de bot diretamente na linha de comando.

>[!TIP]
> Cada ferramenta do construtor de bot inclui um comando de ajuda global, acessível na linha de comando com a inserção de **-h** ou **--help**. Esse comando está disponível a qualquer momento em qualquer ação, que fornecerá uma exibição útil das opções disponíveis para você, juntamente com as descrições.

### <a name="ludown"></a>LUDown

O [LUDown](https://aka.ms/botbuilder-ludown) permite que você descreva e crie componentes de linguagem avançados para os bots usando arquivos **.lu**. O novo arquivo .lu é um tipo de formato markdown que a ferramenta LUDown consome e gera arquivos .json específicos do serviço de destino. No momento, você pode usar arquivos .lu para criar um aplicativo [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) ou uma base de dados de conhecimento do [QnA](https://qnamaker.ai/Documentation/CreateKb), usando formatos diferentes para cada um. O LUDown está disponível como um módulo npm e pode ser usado com a instalação global no computador:

```shell
npm install -g ludown
```

A ferramenta LUDown pode ser usada para criar modelos .json para o LUIS e o QnA.  

### <a name="creating-a-luis-application-with-ludown"></a>Criando um aplicativo LUIS com o LUDown

Você pode definir [intenções](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) e [entidades](https://docs.microsoft.com/azure/cognitive-services/luis/add-entities) para um aplicativo LUIS exatamente como faria no portal do LUIS.

`# \<intent-name\>` descreve uma nova seção de definição de intenção. As linhas subsequentes contêm [declarações](https://docs.microsoft.com/azure/cognitive-services/luis/add-example-utterances) que descrevem essa intenção.

Por exemplo, você pode criar várias intenções do LUIS em um único arquivo .lu da seguinte maneira: 

```ludown
# Greeting
Hi
Hello
Good morning
Good evening

# Help
help
I need help
please help
```

### <a name="qna-pairs-with-ludown"></a>Pares do QnA com o LUDown

O formato de arquivo .lu também dá suporte aos pares do QnA usando a seguinte notação: 

  ```ludown
  > This is a comment. QnA definitions have the general format:
  ### ? this-is-the-question-string
  - this-is-an-alternate-form-of-the-same-question
  - this-is-another-one
    ```markdown
    this-is-the-answer
    ```
  ```
A ferramenta LUDown separará automaticamente as perguntas e as respostas em um arquivo JSON do QnA Maker que você pode usar em seguida para criar a base de dados de conhecimento [QnaMaker.ai](http://qnamaker.ai).

  ```ludown
  ### ? How do I change the default message for QnA Maker?
    ```markdown
    You can change the default message if you use the QnAMakerDialog. 
    See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details. 
    ```
  ```

Você também pode adicionar várias perguntas à mesma resposta apenas adicionando novas linhas de variações de perguntas para uma única resposta. 

  ```ludown
  ### ? What is your name?
  - What should I call you?
    ```markdown
    I'm the echoBot! Nice to meet you.
    ```
  ```

### <a name="generating-json-models-with-ludown"></a>Gerando modelos .json com o LUDown

Depois de definir os componentes de linguagem do LUIS ou do QnA no formato .lu, você pode publicá-los em um arquivo .json do LUIS, .json do QnA ou .tsv do QnA. Quando for executada, a ferramenta LUDown procurará arquivos .lu no mesmo diretório de trabalho para análise. Como a ferramenta LUDown pode ser direcionada ao LUIS ou ao QnA com arquivos .lu, basta especificarmos para qual serviço de linguagem eles deverão ser gerados, usando o comando geral **ludown parse <Service> -- in <luFile>**. 

Em nosso diretório de trabalho de exemplo, temos dois arquivos .lu para analisar: 'luis-sample.lu' para criar um modelo do LUIS e 'qna-sample.lu' para criar uma base de dados de conhecimento do QnA.


#### <a name="generate-luis-json-models"></a>Gerar modelos .json do LUIS

**luis-sample.lu** 
```ludown
# Greeting
- Hi
- Hello
- Good morning
- Good evening
```

Para gerar um modelo do LUIS usando o LUDown, no diretório de trabalho atual, basta inserir o seguinte:

```shell
ludown parse ToLuis --in ludown-file-name.lu
```

#### <a name="generate-qna-knowledge-base-json"></a>Gerar um .json para a Base de Dados de Conhecimento do QnA

**qna-sample.lu**
  ```ludown
  > This is a sample ludown file for QnA Maker.

  ### ? How do I change the default message
    ```markdown
    You can change the default message if you use the QnAMakerDialog. 
    See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details. 
    ```
  ```

Da mesma forma, para criar uma base de dados de conhecimento do QnA, você só precisa alterar o destino da análise. 

```shell
ludown parse ToQna --in ludown-file-name.lu
```

Os arquivos JSON resultantes podem ser consumidos pelo LUIS e pelo QnA por meio de seus respectivos portais ou por meio das novas ferramentas da CLI. 

## <a name="6-connect-to-luis-an-qna-maker-services-from-the-cli"></a>6. Conectar-se aos serviços do LUIS ou do QnA Maker por meio da CLI

### <a name="connect-to-luis-from-the-cli"></a>Conectar-se ao LUIS por meio da CLI 

Incluída no novo conjunto de ferramentas está uma [extensão do LUIS](https://aka.ms/botbuilder-luis-cli), que permite que você gerencie seus recursos do LUIS de forma independente. Ela está disponível como um módulo npm que pode ser baixado:

```shell
npm install -g luis-apis
```
O uso do comando básico para a ferramenta do LUIS na CLI é:

```shell
luis action-name resource-name arguments-list
```
Para conectar o bot ao LUIS, você precisará criar um arquivo **.luisrc**. Esse é um arquivo de configuração que provisiona a appID e a senha do LUIS no ponto de extremidade de serviço quando o aplicativo faz chamadas de saída. Crie esse arquivo executando **luis init** da seguinte maneira:

```shell
luis init
```
No terminal, você deverá inserir a chave de criação, a região e a appID do LUIS antes que a ferramenta gere o arquivo.  

![LUIS init](media/bot-builder-tools/luis-init.png) 


Depois que esse arquivo for gerado, o aplicativo poderá consumir o arquivo .json do LUIS (gerado por meio do LUDown) usando o comando da CLI a seguir: 

```shell
luis import application --in luis-app.json | msbot connect luis --stdin
```

### <a name="connect-to-qna-from-the-cli"></a>Conectar-se ao QnA por meio da CLI

Incluída no novo conjunto de ferramentas está uma [extensão do QnA](https://aka.ms/botbuilder-tools-qnaMaker), que permite que você gerencie seus recursos do LUIS de forma independente. Ela está disponível como um módulo npm que pode ser baixado:

```shell
npm install -g qnamaker
```
Com a ferramenta QnA Maker, você pode criar, atualizar, publicar, excluir e treinar sua base de dados de conhecimento. Para começar, você precisa criar um arquivo **.qnamakerrc** para habilitar o ponto de extremidade no serviço. Crie esse arquivo com facilidade executando **qnamaker init** e seguindo os prompts e juntamente com a ID da base de dados de conhecimento do QnA Maker. 

```shell
qnamaker init 
```
![QnA Maker init](media/bot-builder-tools/qnamaker-init.png)

Depois que o arquivo .qnamakerrc for gerado, você poderá se conectar à base de dados de conhecimento do QnA para consumir o arquivo .json/.tsv da base de dados de conhecimento (gerado por meio do LUDown) usando o comando a seguir:

```shell
qnamaker create --in qnaKB.json --msbot | msbot connect qna --stdin
```

## <a name="7-publish-to-azure-from-the-cli"></a>7. Publicar no Azure da CLI

Depois de fazer alterações ao código-fonte do seu bot, você pode publicar perfeitamente suas alterações, executando o seguinte:

```azurecli
az bot publish --name "my-bot-name" --resource-group "my-resource-group"
```

## <a name="references"></a>Referências
- [Ferramentas do Bot Builder](https://aka.ms/botbuilder-tools-readme)
- [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
