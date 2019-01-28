---
title: Gerenciar os bots usando as ferramentas de CLI
description: As Ferramentas do Bot Framework permitem que você gerencie seus recursos de bot diretamente na linha de comando
keywords: modelos de botbuilder, ludown, qna, luis, msbot, manage, cli, .bot, bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: tools
ms.date: 11/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 07df43111f3b2e57dcf0140f291a771e749de563
ms.sourcegitcommit: c6ce4c42fc56ce1e12b45358d2c747fb77eb74e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453850"
---
# <a name="manage-bots-using-cli-tools"></a>Gerenciar os bots usando as ferramentas de CLI

As Ferramentas do Bot Framework abrangem o fluxo de trabalho de desenvolvimento de bot de ponta a ponta, que inclui a fase de planejamento, criação, teste, publicação, conexão e avaliação. Vamos ver como essas ferramentas podem ajudá-lo em cada fase do ciclo de desenvolvimento.

## <a name="plan"></a>Plano

### <a name="create-mock-conversations-using-chatdown"></a>Criar conversas fictícias usando o Chatdown

O Chatdown é um gerador de transcrição que consome um arquivo .chat para gerar transcrições fictícias. Os arquivos de transcrição fictícia gerados são enviados para stdout.

Um bom bot, assim como qualquer aplicativo ou site bem-sucedido, começa mostrando transparência sobre os cenários com suporte. A criação de modelos de conversas entre o usuário e o bot é útil para:

- Delimitar os cenários com suporte pelo bot.
- Permitir a revisão e os comentários por parte dos tomadores de decisões comerciais.
- A definição de um "caminho da felicidade" (bem como outros caminhos) por meio de fluxos de conversação entre um usuário e um formato de arquivo .chat do bot ajuda a criar modelos de conversas entre um usuário e um bot. A ferramenta CLI do Chatdown converte os arquivos .chat em transcrições de conversa (arquivos .transcript) que podem ser exibidas no [Bot Framework Emulator V4](https://github.com/microsoft/botframework-emulator).

Veja um arquivo `.chat` de exemplo:

```markdown
user=Joe
bot=LulaBot

bot: Hi!
user: yo!
bot: [Typing][Delay=3000]
Greetings!
What would you like to do?
* update - You can update your account
* List - You can list your data
* help - you can get help

user: I need the bot framework logo.

bot:
Here you go.
[Attachment=bot-framework.png]
[Attachment=http://yahoo.com/bot-framework.png]
[AttachmentLayout=carousel]

user: thanks
bot:
Here's a form for you
[Attachment=card.json adaptivecard]
```

### <a name="create-a-transcript-file-from-chat-file"></a>Criar um arquivo de transcrição usando o arquivo .chat

O comando Chatdown é semelhante ao seguinte:

```bash
chatdown sample.chat > sample.transcript
```

Isso consumirá `sample.chat` e resultará em `sample.transcript`. Confira [CLI do Chatdown][chatdown] para obter mais informações.

## <a name="build"></a>Compilação

### <a name="create-a-luis-application-with-ludown"></a>Criar um aplicativo LUIS com o LUDown

A ferramenta LUDown pode ser usada para criar modelos .json para o LUIS e o QnA.  
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

### <a name="create-qna-pairs-with-ludown"></a>Criar pares do QnA com o LUDown

O formato de arquivo .lu também dá suporte aos pares do QnA usando a seguinte notação:

~~~LUDown
> comment
### ? question ?
  ```
    answer
  ```
~~~

A ferramenta LUDown separará automaticamente as perguntas e as respostas em um arquivo JSON do QnA Maker que você pode usar em seguida para criar a base de dados de conhecimento [QnaMaker.ai](http://qnamaker.ai).

~~~LUDown
### ? How do I change the default message for QnA Maker?
  ```
  You can change the default message if you use the QnAMakerDialog. 
  See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details.
  ```
~~~

Você também pode adicionar várias perguntas à mesma resposta apenas adicionando novas linhas de variações de perguntas para uma única resposta.

~~~LUDown
### ? What is your name?
- What should I call you?
  ```
    I'm the echoBot! Nice to meet you.
  ```
~~~

### <a name="generate-json-models-with-ludown"></a>Gerar modelos .json com o LUDown

Depois de definir os componentes de linguagem do LUIS ou do QnA no formato .lu, você pode publicá-los em um arquivo .json do LUIS, .json do QnA ou .tsv do QnA. Quando for executada, a ferramenta LUDown procurará arquivos .lu no mesmo diretório de trabalho para análise. Como a ferramenta LUDown pode ser direcionada ao LUIS e ao QnA com arquivos .lu, basta especificar para qual serviço de linguagem eles deverão ser gerados usando o comando geral **ludown parse \<to-service-type> -- in \<lu-file-path>**.

Em nosso diretório de trabalho de exemplo, temos dois arquivos .lu para analisar: '1.lu' para criar um modelo do LUIS e 'qna1.lu' para criar uma base de dados de conhecimento do QnA.

#### <a name="generate-luis-json-models"></a>Gerar modelos .json do LUIS

Para gerar um modelo do LUIS usando o LUDown, no diretório de trabalho atual, basta inserir o seguinte:

```shell
ludown parse ToLuis --in <luFile>
```

#### <a name="generate-qna-knowledge-base"></a>Gerar Base de Dados de Conhecimento do QnA

Da mesma forma, para criar uma base de dados de conhecimento do QnA, você só precisa alterar o destino da análise.

```shell
ludown parse ToQna --in <luFile>
```

Os arquivos JSON resultantes podem ser consumidos pelo LUIS e pelo QnA por meio de seus respectivos portais ou por meio das novas ferramentas da CLI. Consulte o repositório GitHub da [CLI do LUDown][ludown] para saber mais.

### <a name="track-service-references-using-bot-file"></a>Controlar referências de serviço usando o arquivo .bot

A nova ferramenta [MSBot][msbotCli] permite que você crie um arquivo **.bot**, que armazena metadados sobre os diferentes serviços consumidos pelo bot, em um só local. Esse arquivo também permite que o bot se conecte a esses serviços por meio da CLI. A ferramenta está disponível como um módulo npm. Para instalá-la, execute:

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

Para obter a lista de serviços com suporte, consulte o arquivo [Leiame][msbotCli].

### <a name="create-and-manage-luis-applications-using-luis-cli"></a>Criar e gerenciar aplicativos do LUIS usando a CLI do LUIS

Incluída no novo conjunto de ferramentas está uma [extensão do LUIS][luisCli], que permite que você gerencie seus recursos do LUIS de forma independente. Ela está disponível como um módulo npm que pode ser baixado:

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

Depois que esse arquivo for gerado, o aplicativo poderá consumir o arquivo .json do LUIS (gerado por meio do LUDown) usando o comando da CLI a seguir.

```shell
luis import application --in luis-app.json | msbot connect luis --stdin
```
Consulte o repositório GitHub da [CLI do LUIS] [ luisCli] para saber mais.

### <a name="create-qna-maker-kb-using-qna-maker-cli"></a>Criar a Base de Dados de Conhecimento do QnA Maker usando a CLI do QnA Maker

Incluída no novo conjunto de ferramentas está uma [extensão do QnA][qnaCli], que permite que você gerencie seus recursos do LUIS de forma independente. Ela está disponível como um módulo npm que pode ser baixado.

```shell
npm install -g qnamaker
```
Com a ferramenta QnA Maker, você pode criar, atualizar, publicar, excluir e treinar sua base de dados de conhecimento. Você pode usar arquivos gerados por meio do comando [ludown parse toqna](#generate-qna-knowledge-base) para criar/substituir uma base de dados de conhecimento.

```shell
qnamaker create --in qnaKB.json --msbot | msbot connect qna --stdin
```

Consulte o repositório GitHub da [CLI do QnA Maker][qnaCli] para saber mais.

### <a name="create-dispatch-model-using-dispatch-cli"></a>Criar modelo de expedição usando a CLI de Expedição

A Expedição é uma ferramenta para criar e avaliar modelos de LUIS usados para distribuir a intenção em vários módulos de bot, como modelos de LUIS, bases de dados de conhecimento do QnA e outros (adicionados à expedição como um tipo de arquivo).

Use o modelo de expedição quando:

- Seu bot consistir em vários módulos e você precisar de ajuda no roteamento de declarações do usuário para esses módulos e na avaliação da integração do bot.
- For necessário avaliar a qualidade da classificação de intenção de um único modelo de LUIS.
- For necessário criar um modelo de classificação de texto usando arquivos de texto.

Depois de montar o arquivo .bot com os [aplicativos do LUIS][msbotCli-luis] e as [bases de dados de conhecimento do QnA Maker][msbotCli-qna] dos quais seu bot depende, basta criar um modelo de expedição usando: 

```shell
dispatch create -b <YOUR-BOT-FILE> | msbot connect dispatch --stdin
```
Consulte a [CLI de Expedição][dispatchCli] para saber mais.

## <a name="test"></a>Teste

O [Emulador](bot-service-debug-emulator.md) da Estrutura do Bot é um aplicativo de desktop que permite aos desenvolvedores de bot testarem e depurarem seus bots no host local ou executarem remotamente por meio de um túnel.

## <a name="publish"></a>Publicar

Você pode usar a CLI do Azure para criar, baixar e publicar seu bot no Serviço de Bot do Azure.

O msbot 4.3.2 e posterior exige a CLI do Azure versão 2.0.53 ou posterior. Se você tiver instalado a extensão botservice, remova-a com este comando.

```shell
az extension remove --name botservice
```

### <a name="create-azure-bot-service-bot"></a>Criar bot do Serviço de Bot do Azure

Observação: você deve instalar a versão mais recente do `az cli`. Faça a atualização, para que a CLI do AZ possa trabalhar com a ferramenta MSBot.

Fazer logon em sua conta do Azure via

```shell
az login
```

Se você ainda não tiver um grupo de recursos para o qual publicar seu bot, crie um:

```shell
az group create --name <resource-group-name> --location <geographic-location> --verbose
```

| Opção | DESCRIÇÃO |
|:---|:---|
| --nome | Um nome exclusivo para o grupo de recursos. NÃO inclua espaços ou sublinhados no nome. |
| --location | Localização geográfica usada para criar o grupo de recursos. Por exemplo, `eastus`, `westus`, `westus2`, e assim por diante. Use `az account list-locations` para obter uma lista de locais. |

Em seguida, crie o recurso de bot no qual você publicará seu bot.

```shell
az bot create [options]
```

Para criar um bot e atualizar o arquivo .bot com a configuração do bot,  
```shell
az bot create [options] --msbot | msbot connect bot --stdin
```

Se você tiver um bot existente,  
```shell
az bot show [options] --msbot | msbot connect bot --stdin
```

| Opção                            | DESCRIÇÃO                                   |
|-----------------------------------|-----------------------------------------------|
| --kind -k [Obrigatório]              | O tipo do bot.  Valores permitidos: function, registration, webapp.|
| --name -n [Obrigatório]              | O nome do recurso do bot. |
| --appid                           | A ID da conta de msa a ser usada com o bot.   |
| --location -l                     | Local. Você pode configurar o local padrão usando `az configure --defaults location=<location>`.  Padrão: westus.|
| --msbot                           | Mostre a saída como json compatível com um arquivo .bot.  Valores permitidos: false, true.|
| --password -p                     | A senha msa do bot no portal do desenvolvedor. |
| --resource-group -g               | Nome do grupo de recursos. Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`.  Padrão: build2018. |
| --tags                            | Conjunto de marcas a serem adicionadas ao bot. |

### <a name="configure-channels"></a>Configurar canais

Você pode usar a CLI do Azure para gerenciar canais para o bot.

```shell
>az bot -h
Group
   az bot: Manage Bot Services.
    Subgroups:
        directline: Manage Directline Channel on a Bot.
        email     : Manage Email Channel on a Bot.
        facebook  : Manage Facebook Channel on a Bot.
        kik       : Manage Kik Channel on a Bot.
        msteams   : Manage Msteams Channel on a Bot.
        skype     : Manage Skype Channel on a Bot.
        slack     : Manage Slack Channel on a Bot.
        sms       : Manage Sms Channel on a Bot.
        telegram  : Manage Telegram Channel on a Bot.
        webchat   : Manage Webchat Channel on a Bot.

    Commands:
        create    : Create a new Bot Service.
        delete    : Delete an existing Bot Service.
        download  : Download an existing Bot Service.
        publish   : Publish to an existing Bot Service.
        show      : Get an existing Bot Service.
        update    : Update an existing Bot Service.

```

## <a name="additional-information"></a>Informações adicionais

- [Ferramentas do Bot Framework no GitHub][cliTools]
- [Formato de arquivo .lu](https://aka.ms/ludown-file-format)

<!-- Footnote links -->

[cliTools]: https://aka.ms/botbuilder-tools-readme
[azureCli]: https://aka.ms/botbuilder-tools-azureCli
[msbotCli]: https://aka.ms/botbuilder-tools-msbot-readme
[msbotCli-luis]: https://aka.ms/botbuilder-tools-msbot-readme#connecting-to-luis-application
[msbotCli-qna]: https://aka.ms/botbuilder-tools-msbot-readme#connecting-to-qna-maker-knowledge-base
[chatdown]: https://aka.ms/botbuilder-tools-chatdown
[ludown]: https://aka.ms/botbuilder-ludown
[luisCli]: https://aka.ms/botbuilder-luis-cli
[qnaCli]: https://aka.ms/botbuilder-tools-qnaMaker
[dispatchCli]: https://aka.ms/botbuilder-tools-dispatch
