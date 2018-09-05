---
title: Criar bots com modelos do Construtor de Bot
description: As ferramentas do Construtor de Bot permitem que você gerencie seus recursos de bot diretamente na linha de comando
keywords: modelos do Construtor de Bot, Node.js, Python, Java, .Net, Ludown, Yeoman
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/25/2018
ms.openlocfilehash: 8004389aba58b5cf79f1559b3ce65d1d66c5358c
ms.sourcegitcommit: 44f100a588ffda19c275b118f4f97029f12d1449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2018
ms.locfileid: "42928334"
---
# <a name="create-bots-with-botbuilder-templates"></a>Criar bots com modelos do Construtor de Bot

> [!NOTE]
> Este tópico aplica-se às versões v3 e v4 do SDK. Veja as observações adicionais abaixo.

Os modelos agora estão disponíveis para criar bots em cada uma das plataformas do SDK do Construtor de Bot: 

- Node.js
- Python
- Java
- .NET

## <a name="prerequisites"></a>Pré-requisitos

Os modelos do Node.js, do Java e do Python criam um bot de eco simples e todos eles são disponibilizados por meio do [Yeoman](http://yeoman.io/).

- [Node.js](https://nodejs.org/en/) v 8.5 ou superior
- [Yeoman](http://yeoman.io/)

Se você ainda não instalou o Yeoman, ele precisa ser instalado globalmente.

```shell
npm install -g yo
```

## <a name="nodejs-python-java-templates"></a>Modelos do Node.js, do Python e do Java
Na linha de comando, execute cd em uma nova pasta de sua escolha. Há duas versões disponíveis dos modelos do Node.js do Construtor de Bot, direcionados para as versões **v3** e **v4** do SDK, respectivamente. Os modelos do Python e do Java só estão disponíveis na v4. 

Para instalar o gerador de modelos do **Construtor de Bot do Node.js v3**:

```shell
npm install generator-botbuilder
```

Para instalar o gerador de modelos do **Construtor de Bot do Node.js v4**:
```shell
npm install generator-botbuilder@preview
```

Os modelos do Python e do Java **só estão disponíveis na v4**. 

Para o **Python**:
```shell
npm install generator-botbuilder-python
```

Para o **Java**:
```shell
npm install generator-botbuilder-java
```

Depois que um gerador for instalado, basta executar o comando **yo** na CLI para exibir uma lista dos geradores disponíveis no Yeoman.

![Interface do Yeoman](media/botbuilder-templates/yeoman-generator-botbuilder.png)

Alterne para um diretório de trabalho de sua escolha e selecione o gerador a ser usado. Você deverá escolher várias opções para criar o bot, como nome e descrição. Quando todos os prompts forem concluídos, o modelo de bot de eco será criado na mesma pasta de trabalho.

![Modelo do Node.js no Yeoman](media/botbuilder-templates/new-template-node.png)


## <a name="net"></a>.NET

Dois modelos estão disponíveis para o .NET, direcionado para as versões **v3** e **v4** do SDK, respectivamente. Ambos estão disponíveis como pacotes [VSIX](https://docs.microsoft.com/en-us/visualstudio/extensibility/anatomy-of-a-vsix-package). Para baixá-los, clique em um dos links a seguir no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

- [Modelo de Construtor de Bot V3](https://marketplace.visualstudio.com/items?itemName=BotBuilder.BotBuilderV3)
- [Modelo de Construtor de Bot V4](https://aka.ms/Ylcwxk)

#### <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2015 ou superior](https://www.visualstudio.com/downloads/)
- [Conta do Azure](https://azure.microsoft.com/en-us/free/)

### <a name="install-the-templates"></a>Instalar os modelos

No diretório salvo, basta abrir o pacote VSIX e o modelo do construtor de bot será instalado no Visual Studio e disponibilizado na próxima vez que você abri-lo.

![Pacote VSIX](media/botbuilder-templates/botbuilder-vsix-template.png)

Para criar um projeto de bot usando o modelo, basta abrir o Visual Studio e selecionar **Arquivo** > **Novo** > **Projeto** e, no Visual C#, selecionar **Bot Framework** > Aplicativo Simples de Bot de Eco. Isso criará um projeto de bot de eco localmente que você poderá editar como desejar. 

![Modelo de bot do .NET](media/botbuilder-templates/new-template-dotnet.png)

## <a name="store-your-bot-information-with-msbot"></a>Armazenar as informações de bot no MSBot

A nova ferramenta [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/MSBot) permite que você crie um arquivo **.bot**, que armazena metadados sobre os diferentes serviços consumidos pelo bot, em um só local. Esse arquivo também permite que o bot se conecte a esses serviços por meio da CLI. A ferramenta está disponível como um módulo npm. Para instalá-la, execute:

```shell
npm install -g msbot 
```

Para criar um arquivo de bot, na CLI, insira **msbot init** seguido pelo nome do bot e o ponto de extremidade de URL de destino, por exemplo:

```shell
msbot init --name TestBot --endpoint http://localhost:9499/api/messages
```
Para conectar o bot a um serviço, na CLI, insira **msbot connect** seguido pelo serviço apropriado:

```shell
msbot connect [Service]
```

| Serviço | DESCRIÇÃO |
| ------ | ----------- |
| azure  |conectar o bot a um registro do Serviço de Bot do Azure|
|localhost| conectar o bot a um ponto de extremidade do localhost|
|luis     | conectar o bot a um aplicativo LUIS |
| qna     |conectar o bot a uma base de dados de conhecimento do QnA|
|help [cmd]  |exibir a ajuda do [cmd]|

### <a name="connect-your-bot-to-abs-with-the-bot-file"></a>Conectar o bot aos ABS com o arquivo .bot

Com a ferramenta MSBot instalada, você pode conectar com facilidade o bot a um grupo de recursos existente no Serviço de Bot do Azure executando o comando az bot **show**. 

```azurecli
az bot show -n <botname> -g <resourcegroup> --msbot | msbot connect azure --stdin
```

Isso usará o ponto de extremidade atual, a appID da MSA e a senha do grupo de recursos de destino e atualizará as informações adequadamente no arquivo .bot. 


## <a name="manage-update-or-create-luis-and-qna-services-with--new-botbuilder-tools"></a>Gerenciar, atualizar ou criar serviços do LUIS e do QnA com novas ferramentas do construtor de bot

As [ferramentas do construtor de bot](https://github.com/microsoft/botbuilder-tools) são um novo conjunto de ferramentas que permite que você gerencie e interaja com os recursos de bot diretamente na linha de comando. 

>[!TIP]
> Cada ferramenta do construtor de bot inclui um comando de ajuda global, acessível na linha de comando com a inserção de **-h** ou **--help**. Esse comando está disponível a qualquer momento em qualquer ação, que fornecerá uma exibição útil das opções disponíveis para você, juntamente com as descrições 

### <a name="ludown"></a>LUDown
O [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/Ludown) permite que você descreva e crie componentes de linguagem avançados para os bots usando arquivos **.lu**. O novo arquivo .lu é um tipo de formato markdown que a ferramenta LUDown consome e gera arquivos .json específicos do serviço de destino. No momento, você pode usar arquivos .lu para criar um aplicativo [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app) ou uma base de dados de conhecimento do [QnA](https://qnamaker.ai/Documentation/CreateKb), usando formatos diferentes para cada um. O LUDown está disponível como um módulo npm e pode ser usado com a instalação global no computador:

```shell
npm install -g ludown
```
A ferramenta LUDown pode ser usada para criar modelos .json para o LUIS e o QnA.  


### <a name="creating-a-luis-application-with-ludown"></a>Criando um aplicativo LUIS com o LUDown

Você pode definir [intenções](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) e [entidades](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-entities) para um aplicativo LUIS exatamente como faria no portal do LUIS. 

'#\<intent-name\>' descreve uma nova seção de definição de intenção. Depois disso, cada linha lista os [enunciados](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-example-utterances) que descrevem essa intenção.

Por exemplo, você pode criar várias intenções do LUIS em um único arquivo .lu da seguinte maneira: 

```LUDown
# Greeting
- Hi
- Hello
- Good morning
- Good evening

# Help
- help
- I need help
- please help
```

### <a name="qna-pairs-with-ludown"></a>Pares do QnA com o LUDown

O formato de arquivo .lu também dá suporte aos pares do QnA usando a seguinte notação: 

```LUDown
> comment
### ? question ?
  ```markdown
    answer
  ```

A ferramenta LUDown separará automaticamente as perguntas e as respostas em um arquivo JSON do QnA Maker que você pode usar em seguida para criar a base de dados de conhecimento [QnaMaker.ai](http://qnamaker.ai).

```LUDown
### ? How do I change the default message for QnA Maker?
  ```markdown
  You can change the default message if you use the QnAMakerDialog. 
  See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details. 
  ```

Você também pode adicionar várias perguntas à mesma resposta apenas adicionando novas linhas de variações de perguntas para uma única resposta. 

```LUDown
### ? What is your name?
- What should I call you?
  ```markdown
    I'm the echoBot! Nice to meet you.
  ```

### <a name="generating-json-models-with-ludown"></a>Gerando modelos .json com o LUDown

Depois de definir os componentes de linguagem do LUIS ou do QnA no formato .lu, você pode publicá-los em um arquivo .json do LUIS, .json do QnA ou .tsv do QnA. Quando for executada, a ferramenta LUDown procurará arquivos .lu no mesmo diretório de trabalho para análise. Como a ferramenta LUDown pode ser direcionada ao LUIS ou ao QnA com arquivos .lu, basta especificarmos para qual serviço de linguagem eles deverão ser gerados, usando o comando geral **ludown parse <Service> -- in <luFile>**. 

Em nosso diretório de trabalho de exemplo, temos dois arquivos .lu para analisar: '1.lu' para criar um modelo do LUIS e 'qna1.lu' para criar uma base de dados de conhecimento do QnA.

#### <a name="generate-luis-json-models"></a>Gerar modelos .json do LUIS

Para gerar um modelo do LUIS usando o LUDown, no diretório de trabalho atual, basta inserir o seguinte:

```shell
ludown parse ToLuis --in <luFile> 
```

#### <a name="generate-qna-knowledge-base-json"></a>Gerar um .json para a Base de Dados de Conhecimento do QnA

Da mesma forma, para criar uma base de dados de conhecimento do QnA, você só precisa alterar o destino da análise. 

```shell
ludown parse ToQna --in <luFile> 
```

Os arquivos JSON resultantes podem ser consumidos pelo LUIS e pelo QnA por meio de seus respectivos portais ou por meio das novas ferramentas da CLI. 

## <a name="connect-to-luis-an-qna-maker-services-from-the-cli"></a>Conectar-se aos serviços do LUIS ou do QnA Maker por meio da CLI

### <a name="connect-to-luis-from-the-cli"></a>Conectar-se ao LUIS por meio da CLI 

Incluída no novo conjunto de ferramentas está uma [extensão do LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/LUIS), que permite que você gerencie seus recursos do LUIS de forma independente. Ela está disponível como um módulo npm que pode ser baixado:

```shell
npm install -g luis-apis
```
O uso do comando básico para a ferramenta do LUIS na CLI é:

```shell
luis <action> <resource> <args...>
```
Para conectar o bot ao LUIS, você precisará criar um arquivo **.luisrc**. Esse é um arquivo de configuração que provisiona a appID e a senha do LUIS no ponto de extremidade de serviço quando o aplicativo faz chamadas de saída. Crie esse arquivo executando **luis init** da seguinte maneira:

```shell
luis init
```
No terminal, você deverá inserir a chave de criação, a região e a appID do LUIS antes que a ferramenta gere o arquivo.  

![LUIS init](media/bot-builder-tools/luis-init.png) 


Depois que esse arquivo for gerado, o aplicativo poderá consumir o arquivo .json do LUIS (gerado por meio do LUDown) usando o comando da CLI a seguir.

```shell
luis import application --in luis-app.json | msbot connect luis --stdin
```

### <a name="connect-to-qna-from-the-cli"></a>Conectar-se ao QnA por meio da CLI

Incluída no novo conjunto de ferramentas está uma [extensão do QnA](https://github.com/Microsoft/botbuilder-tools/tree/master/QnAMaker), que permite que você gerencie seus recursos do LUIS de forma independente. Ela está disponível como um módulo npm que pode ser baixado.

```shell
npm install -g qnamaker
```
Com a ferramenta QnA Maker, você pode criar, atualizar, publicar, excluir e treinar sua base de dados de conhecimento. Para começar, você precisa criar um arquivo **.qnamakerrc** para habilitar o ponto de extremidade no serviço. Crie esse arquivo com facilidade executando **qnamaker init** e seguindo os prompts e provisione a ID da base de dados de conhecimento do QnA Maker. 

```shell
qnamaker init 
```
![QnA Maker init](media/bot-builder-tools/qnamaker-init.png)

Depois que o arquivo .qnamakerrc for gerado, você poderá se conectar à base de dados de conhecimento do QnA para consumir o arquivo .json/.tsv da base de dados de conhecimento (gerado por meio do LUDown) usando o comando a seguir.

```shell
qnamaker create --in qnaKB.json --msbot | msbot connect qna --stdin
```

## <a name="references"></a>Referências
- [Código-fonte das ferramentas do Construtor de Bot](https://github.com/Microsoft/botbuilder-tools)
- [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/MSBot)
- [ChatDown](https://github.com/Microsoft/botbuilder-tools/tree/master/Chatdown)
- [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/Ludown)
- [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
