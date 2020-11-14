---
title: Criar um bot entre treinados para usar LUIS e QnA Maker Recognizers-serviço bot
description: Descrevendo como criar um bot com treinamento cruzado para usar LUIS e reconhecedores de QnA Maker
keywords: LUIS, QnA Maker, QnA, bot, treinamento cruzado, treinamento cruzado, caixas de diálogo adaptáveis, geração de linguagem, LG, Lu
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: how-to
ms.service: bot-service
ms.date: 10/10/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 798c4ad8f01646875820575db519ed4fd74fc80f
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94618153"
---
# <a name="create-a-bot-cross-trained-to-use-both-luis-and-qna-maker-recognizers"></a>Criar um bot cruzado para usar os reconhecedores LUIS e QnA Maker

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este artigo de instruções descreve as etapas necessárias para criar um bot que integra os recursos de [Luis][luis] e [QnA Maker][qnamaker] juntos em cada uma das suas caixas de diálogo adaptáveis, permitindo que cada uma delas determine a melhor resposta possível ao usuário, usando a [geração de idioma][language-generation], independentemente de qual caixa de diálogo está ativa.

A integração dos recursos de LUIS e de QnA Maker requer o uso do `luis:cross-train` comando ou da `qnamaker:cross-train` CLI para fazer o _treinamento cruzado_ de seus `.lu` arquivos e do `.qna` , bem como o uso do [reconhecedor com treinamento cruzado][cross-trained-recognizer-set-concept] como o reconhecedor de caixas de diálogo adaptáveis.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo orienta você pelas etapas necessárias para criar um bot totalmente funcional usando caixas de diálogo adaptáveis, demonstrado usando o [bot de todo C# com Luis e QnA Maker](#the-todo-bot-with-luis-and-qna-maker-sample) exemplo. Para fazer isso, você precisa entender os vários conceitos envolvidos. Antes de começar, você deve ter uma compreensão básica sólida dos seguintes conceitos:

- [Introdução às caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-introduction.md)
- [Eventos e gatilhos em caixas de diálogo adaptáveis](bot-builder-concept-adaptive-dialog-triggers.md)
- [Ações em diálogos adaptáveis](bot-builder-concept-adaptive-dialog-actions.md)
- [Reconhecedores em caixas de diálogo adaptáveis](bot-builder-concept-adaptive-dialog-recognizers.md)
- [Treinamento cruzado de seus modelos LUIS e QnA Maker](bot-builder-concept-cross-train.md)
- [Como lidar com interrupções em caixas de diálogo adaptáveis](bot-builder-concept-adaptive-dialog-interruptions.md)
- [Formato de arquivo .lu][lu-templates]
- [Formato de arquivo .qna][qna-file-format]

Você também precisará de uma [conta do Azure][create-azure-account].

## <a name="what-is-covered"></a>O que é coberto

Este artigo descreve como criar um bot com treinamento cruzado para usar LUIS e reconhecedores de QnA Maker para responder a um usuário. Isso envolverá as seguintes etapas:

1. Introdução ao [bot de todo com Luis e QnA Maker](#the-todo-bot-with-luis-and-qna-maker-sample) exemplo
1. [Criar seu recurso de criação do LUIS nos serviços cognitivas do Azure](#create-your-luis-authoring-resource-in-azure)
1. [Criar seu recurso de QnA Maker nos serviços cognitivas do Azure](#create-your-qna-maker-resource-in-azure-cognitive-services)
1. [Instalar a CLI do SDK do bot Framework](#install-the-bot-framework-cli)
1. [Gerar modelos de LU com treinamento cruzado](#generate-cross-trained-lu-models)
1. [Criar e publicar aplicativos LUIS usando o comando de Build](#create-and-publish-luis-applications-using-the-build-command)
1. [Criar e publicar QnA Maker bases de dados de conhecimento usando o comando de compilação](#create-and-publish-qna-maker-knowledge-bases-using-the-build-command)
1. [Atualize o arquivo de configuração do projeto para incluir informações de conexão para LUIS e QnA Maker](#update-your-projects-configuration-file-to-include-connection-information-for-luis-and-qna-maker)
1. [Testar o bot usando o Bot Framework Emulator](#testing-the-bot-using-bot-framework-emulator)

## <a name="the-todo-bot-with-luis-and-qna-maker-sample"></a>O bot de todo com LUIS e QnA Maker exemplo

O exemplo de **bot adaptável de todo com Luis e QnA Maker** ( [**C#**][cs-sample-todo-bot]) tem cinco `.lu` arquivos e seis `.qna` arquivos para o reconhecimento da linguagem. Ao criar modelos de reconhecimento de linguagem com treinamento cruzado, você combinará os `.lu` arquivos e de `.qna` forma que o permitirá utilizar os recursos de Luis e de QnA Maker juntos no mesmo reconhecedor, permitindo que você treine o reconhecedor como melhor interpretar e responder à solicitação de um usuário. Depois de executar o comando de treinamento cruzado, você terá novas cópias de cada arquivo, mas elas serão atualizadas para que os `.lu` arquivos contenham as `.qna` informações necessárias e os `.qna` arquivos contenham as `.lu` informações necessárias, habilitando o [conjunto de reconhecedor com treinamento cruzado][crosstrainedrecognizerset-ref-guide] para determinar como lidar melhor a solicitação de usuários.

Se você ainda não fez isso, clone o repositório de amostras mais recente. Você pode usar o seguinte comando git em uma janela do console:
```cmd
git clone https://github.com/Microsoft/botbuilder-samples.git
```

## <a name="create-your-luis-authoring-resource-in-azure"></a>Criar seu recurso de criação do LUIS no Azure

Se você ainda não tiver um recurso de criação de LUIS que deseja usar para isso, você poderá seguir as etapas abaixo para criar um. Para obter uma explicação detalhada desse processo, consulte o artigo [implantar recursos do Luis usando os comandos da CLI do bot Framework Luis][create-your-luis-authoring-resource-in-azure].

1. Vá para a página Azure [Create Luis cognitiva Services][create-cognitive-services-luis] .  
2. Na seção **criar opções** , selecione **criação** para criar um recurso de criação de Luis.

   ![Definir opções de criação para criar imagem](./media/adaptive-dialogs/create-options-authoring.png)

3. Insira valores para cada um dos campos e, em seguida, selecione o botão **revisar + criar** .

   ![Definir a imagem criar serviços cognitivas](./media/adaptive-dialogs/create-cognitive-services-cross-trainsample.png)

4. Examine os valores para garantir que estejam corretos e, em seguida, selecione o botão **criar** .

O recurso de criação do LUIS conterá as informações necessárias ao executar o [comando de compilação](#create-and-publish-luis-applications-using-the-build-command) ou atualizar o [arquivo de configuração](#update-your-projects-configuration-file-to-include-connection-information-for-luis-and-qna-maker)do bot. Depois que o recurso for criado, você poderá encontrar as informações mostradas abaixo na folha **chaves e ponto de extremidade** em seu recurso de criação do Luis. Copie e salve os valores para uso posterior.

- **Chaves**. Qualquer um dos dois valores de chave é usado como a opção _subscriptionKey_ no [comando Build](#create-and-publish-luis-applications-using-the-build-command), bem como o _LuisAPIKey_ no arquivo de [configuração](#update-your-projects-configuration-file-to-include-connection-information-for-luis-and-qna-maker).
- **Ponto de extremidade**. Esse é o valor usado como a opção de _ponto de extremidade_ no [comando de compilação](#create-and-publish-luis-applications-using-the-build-command), bem como o _LuisAPIHostName_ em seu [arquivo de configuração](#update-your-projects-configuration-file-to-include-connection-information-for-luis-and-qna-maker).
- **Localização**.   Esse é o valor usado como a opção _Region_ no [comando Build](#create-and-publish-luis-applications-using-the-build-command).

   ![Chaves e ponto de extremidade para o recurso LUIS no Azure](./media/adaptive-dialogs/keys-and-endpoint-cross-train.png)

## <a name="create-your-qna-maker-resource-in-azure-cognitive-services"></a>Criar seu recurso de QnA Maker nos serviços cognitivas do Azure

Se você ainda não tiver um recurso de QnA Maker, poderá seguir as etapas abaixo para criar um. Para obter uma explicação detalhada desse processo, consulte o artigo [implantar QnA Maker base de dados de conhecimento usando os comandos da CLI do bot Framework qnamaker][create-your-qna-maker-resource-in-azure-cognitive-services].

1. Vá para a página [criar serviços cognitivas do QnA Maker][create-cognitive-services-qnamaker] do Azure.
2. Insira valores para cada um dos campos e, em seguida, selecione o botão **revisar + criar** .

   ![Criar sua base de dados de conhecimento do QnA Maker no Azure](./media/adaptive-dialogs/create-qna-maker-cross-trainsample.png)

3. Examine os valores para garantir que estejam corretos e, em seguida, selecione o botão **criar** .

O recurso de QnA Maker conterá o valor necessário para a opção _subscriptionKey_ usada ao executar o [comando de compilação](#create-and-publish-qna-maker-knowledge-bases-using-the-build-command). Copie e salve o valor para uso posterior.

   ![Chaves e ponto de extremidade para QnA Maker recurso no Azure](./media/adaptive-dialogs/qna-maker-keys-and-endpoint-cross-trainsample.png)

## <a name="install-the-bot-framework-cli"></a>Instalar a CLI do bot Framework

[!INCLUDE [applies-to-v4](../includes/install-bf-cli.md)]

## <a name="generate-cross-trained-lu-models"></a>Gerar modelos de LU com treinamento cruzado

Antes de executar o comando de compilação para criar seus aplicativos LUIS e QnA Maker base de dados de conhecimento nos serviços cognitivas do Azure, você precisa _treinar_ os `.lu` `.qna` arquivos e para incluir as informações exigidas pelo reconhecedor do bot para adiar a entrada do usuário para Luis QnA Maker ou para o processamento. Para entender melhor o conceito de treinamento cruzado, consulte o artigo [treinar o bot para usar Luis e reconhecedores de QnA Maker](bot-builder-concept-cross-train.md) . Para obter detalhes sobre o `cross-train` comando, consulte a seção _conjunto de reconhecedor com treinamento cruzado_ dos [reconhecedores em caixas de diálogo adaptáveis – guia de referência](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#cross-trained-recognizer-set).

#### <a name="cross-train-the-_todo-bot-with-luis-and-qna-maker_-sample"></a>Fazer treinamento cruzado no _bot de todo com o Luis e_ o exemplo de QnA Maker

Para treinar o bot de **todo com Luis e QnA Maker** exemplo:

1. Em um terminal, navegue até `samples\csharp_dotnetcore\adaptive-dialog\08.todo-bot-luis-qnamaker`

1. Crie um diretório no qual armazenar a saída do comando. Este artigo usa _gerado_ como o nome para esse diretório.

   ```cmd
      md generated
   ```

1. Execute o comando `luis:cross-train`.

   ```cmd
      bf luis:cross-train -i dialogs -o generated --config dialogs\DialogLuHierarchy.config.json
   ```

Depois de concluir, você terá versões com treinamento cruzado dos cinco `.lu` arquivos e seis `.qna` arquivos. Ao executar os comandos de compilação nas seções a seguir, aponte para o diretório gerado para os arquivos de entrada.

> [!IMPORTANT]
>
> Você precisa executar o `luis:cross-train` comando ou o `qnamaker:cross-train` comando, não é necessário executar ambos. ao executar `luis:cross-train` , todas as informações necessárias entre os treinamentos serão incluídas nos `.lu` arquivos e resultantes `.qna` . Executar ambos funcionará, mas é desnecessário.

## <a name="create-and-publish-luis-applications-using-the-build-command"></a>Criar e publicar aplicativos LUIS usando o comando de Build

Para cada `.lu` arquivo, incluindo `.lu` arquivos para cada localidade, o comando de compilação combina todas as seguintes ações em um único comando:

1. Cria um modelo LUIS para cada localidade encontrada usando seus `.lu` arquivos existentes.
1. Usando esse modelo, ele cria um novo aplicativo LUIS (ou atualiza um existente) no recurso de serviços cognitivas do Azure especificado.
   - Ao atualizar um aplicativo LUIS existente, ele incrementará automaticamente o versionID e, opcionalmente, excluirá a versão antiga.
1. Treina o aplicativo LUIS novo ou atualizado e, em seguida, o publica.

Para obter uma explicação detalhada sobre como usar o `luis:build` comando, consulte [implantar aplicativos Luis usando os comandos da CLI do bot Framework Luis][luis-build].

> [!IMPORTANT]
>
> Esse comando substituirá o modelo de LUIS anterior, bem como qualquer conteúdo que você possa ter em seus [LUIS Applications] [ https://www.luis.ai/applications ].

### <a name="how-to-use-the-luisbuild-command"></a>Como usar o comando Luis: Build

Aqui está o comando de Build LUIS com os parâmetros necessários:

``` cli
bf luis:build --in <input-file-or-folder> --out <output-file-or-folder> --botName <bot-name> --authoringKey <subscription-key> --region <authoring-region>
```

O `luis:build` comando criará todos os ativos de que você precisa em seus `.lu` arquivos locais.

### <a name="required-luisbuild-parameters"></a>Luis obrigatório: parâmetros de compilação

- `--in`: O diretório que será pesquisado por `.lu` arquivos.
- `--out`: O diretório no qual salvar os arquivos de saída.
- `--botName`: O nome do bot. Isso será usado como o prefixo para o nome dos aplicativos LUIS gerados.
- `--authoringKey`: Este é o subscriptionKey.
- `--region`: Define a região para publicar seus aplicativos LUIS.

Para obter informações sobre as opções adicionais, consulte [BF Luis: Build][bf-luisbuild] no Leiame da CLI do BF.

Como alternativa, você pode incluir esses requisitos, bem como qualquer um dos outros parâmetros, em um arquivo de configuração e consultá-los usando a `--luConfig` opção.

### <a name="luis-build-configuration-file"></a>Arquivo de configuração de compilação LUIS

Veja a seguir um exemplo de **luconfig.jsno** arquivo que você pode referenciar usando a `--luConfig` opção.

```json
{
    "in": "generated",
    "out": "output",
    "botName":"<your-bot-name>",
    "authoringKey":"<your-32-digit-subscription-key>",
    "endpoint":"<your-endpoint>",
    "region": "<your-region-default-is-westus>"
}
```

Depois que esse arquivo de configuração é criado, tudo o que você precisa fazer é fazer referência a ele no `luis:build` comando. Por exemplo:

``` cli
bf luis:build --luConfig luconfig.json
```

Para obter uma explicação detalhada sobre como usar o `luis:build` comando, consulte [implantar aplicativos Luis usando os comandos da CLI do bot Framework Luis][luis-build].

#### <a name="create-the-luis-applications-for-the-_todo-bot-with-luis-and-qna-maker_-sample"></a>Criar os aplicativos LUIS para o _bot de todo com Luis e QnA Maker_ exemplo

Para criar os aplicativos LUIS para o **bot de todo com Luis e QnA Maker** exemplo:

1. Em um terminal, navegue até `samples\csharp_dotnetcore\adaptive-dialog\08.todo-bot-luis-qnamaker`

1. Crie um diretório no qual armazenar a saída do comando. Este artigo usa a _saída_ como o nome para esse diretório.

   ```cmd
      md output
   ```

1. Crie seu **luconfig.jsno** arquivo, conforme mostrado na seção anterior e salve-o no diretório raiz do exemplo: `samples\csharp_dotnetcore\adaptive-dialog\08.todo-bot-luis-qnamaker` . Certifique-se de atualizar as entradas para as opções "botName", "authoringKey" e "Region":

    ```json
    {
        "in": "generated",
        "out": "output",
        "botName":"todo bot with LUIS and QnA Maker",
        "authoringKey":"<your-32-digit-subscription-key>",
         "endpoint":"<your-endpoint>",
        "region": "<your-region-default-is-westus>"
    }
    ```

1. Execute o `luis:build` comando usando o arquivo luConfig.

   ```cmd
      bf luis:build --luConfig luconfig.json
   ```

Depois de concluir, você terá um aplicativo LUIS para cada um dos cinco `.lu` arquivos no [Luis](https://www.luis.ai/conversations/applications):

![LUIS minha lista de aplicativos](./media/adaptive-dialogs/luis-apps-list.png)

> [!TIP]
>
> O `luis:build` comando salvará um arquivo chamado **luis.settings.YourUserName.westus.jsno** diretório de saída. Esse arquivo contém as IDs de aplicativo LUIS que você precisará adicionar ao arquivo de configuração, descrito em uma [seção posterior](#update-your-projects-configuration-file-to-include-connection-information-for-luis-and-qna-maker).

## <a name="create-and-publish-qna-maker-knowledge-bases-using-the-build-command"></a>Criar e publicar QnA Maker bases de dados de conhecimento usando o comando de compilação

O `qnamaker:build` comando combina todas as seguintes ações em um único comando:

1. Cria um modelo de QnA Maker para cada localidade encontrada usando seus `.qna` arquivos existentes.
1. Cria um novo QnA Maker KB se não houver nenhum; caso contrário, ele substituirá o KB existente.
1. Ele treina seu QnA Maker KB e, em seguida, publica-o no ponto de extremidade de produção.

Para obter uma explicação detalhada sobre como usar o `qnamaker:build` comando, consulte [implantar QnA Maker base de dados de conhecimento usando os comandos da CLI do bot Framework qnamaker][qnamaker-build].

> [!TIP]
>
> Se você ainda não tiver feito isso, será necessário [migrar para uma chave de criação de recursos do Azure][luis-migration-authoring]. Caso contrário, o não verá os aplicativos LUIS no [Luis][luis] criados usando o `luis:build` comando.

### <a name="how-to-use-the-qnamakerbuild-command"></a>Como usar o comando qnamaker: Build

Aqui está o comando QnA Maker Build com os parâmetros necessários:

``` cli
bf qnamaker:build --in <input-file-or-folder> --out <folder-to-save-files-to> --subscriptionKey <Subscription-Key> --botName <bot-name>
```

> [!IMPORTANT]
>
> Esse comando substituirá o modelo de QnA Maker anterior, bem como qualquer conteúdo que você possa ter em seu QnA Maker KB, incluindo qualquer conteúdo criado diretamente no [QnA Maker.ai](https://www.qnamaker.ai/).

#### <a name="the-qnamakerbuild-parameters"></a>Os parâmetros qnamaker: Build

- `--in`: O diretório, incluindo subdiretórios, que serão pesquisados para arquivos. QnA.
- `--out`: O diretório no qual salvar os arquivos de saída.
- `--botName`: O nome do bot. Isso será usado para gerar o nome do QnA Maker KB, que é explicado em mais detalhes na base de [dados de conhecimento implantar QnA Maker usando o artigo de comandos da CLI do bot Framework qnamaker][qna-maker-knowledge-bases-created] .
- `--subscriptionKey`: A mesma chave de assinatura que está no seu [arquivo de inicialização][create-your-qna-maker-initialization-file].

Para obter informações sobre parâmetros adicionais, consulte [BF qnamaker: Build][bf-qnamakerbuild] na CLI do BF qnamaker Leiame.

Como alternativa, você pode incluir esses parâmetros necessários em um arquivo de configuração e fornecê-los por meio do `qnaConfig` parâmetro.

#### <a name="the-qnamaker-configuration-file"></a>O arquivo de configuração qnamaker

O arquivo de configuração qnamaker é um arquivo JSON que pode conter qualquer `qnamaker:build` opção válida.

```json
{
  "in": "<location-of-qna-files>",
  "out": "<location-to-save-output-files>",
  "subscriptionKey": "<Enter-subscription-key-here>",
  "botName": "<Enter-botName-here>"
}
```

Depois de criado, tudo o que você precisa fazer é fazer referência a ele em seu `qnamaker:build` comando, por exemplo:

``` cli
bf qnamaker:build --qnaConfig qnaConfig.json
```

Para obter uma explicação detalhada sobre como usar o `qnamaker:build` comando, consulte [implantar QnA Maker base de dados de conhecimento usando os comandos da CLI do bot Framework qnamaker][qnamaker-build].

#### <a name="create-the-qna-maker-knowledge-base-for-the-_todo-bot-with-luis-and-qna-maker_-sample"></a>Crie a base de dados de conhecimento QnA Maker para o _bot de todo com Luis e QnA Maker_ exemplo

Para criar a base de dados de conhecimento QnA Maker para o **bot de todo com Luis e QnA Maker** exemplo:

1. Em um terminal, navegue até `samples\csharp_dotnetcore\adaptive-dialog\08.todo-bot-luis-qnamaker`

1. Se ele ainda não existir, crie um novo diretório chamado _saída_

   ```cmd
      md output
   ```

1. Crie seu **qnaConfig.jsno** arquivo, conforme mostrado na seção anterior e salve-o no diretório raiz do exemplo: `samples\csharp_dotnetcore\adaptive-dialog\08.todo-bot-luis-qnamaker` . Certifique-se de atualizar as entradas para as opções "botName", "subscriptionKey" e "Region":

    ```json
    {
        "in": "generated",
        "out": "output",
        "botName":"<todo-bot-with-LUIS-and-QnA-Maker>",
        "subscriptionKey":"<your-32-digit-subscription-key>",
        "region": "<your-region-default-is-westus>"
    }
    ```


1. Execute o `qnamaker:build` comando usando o arquivo qnaConfig.

   ```cmd
      bf qnamaker:build --qnaConfig qnaConfig.json
   ```

Depois de concluir, você terá uma base de dados de conhecimento QnA Maker com todas as perguntas e respostas dos cinco `.qna` arquivos em [QnA Maker](https://www.qnamaker.ai/Home/MyServices):

![Lista de KB QnA Maker](./media/adaptive-dialogs/qna-maker-kb-list.png)

> [!TIP]
>
> O `qnamaker:build` comando salvará um arquivo chamado **qnamaker. settings. \<your-user-name>.westus.jsno** diretório de saída. Esse arquivo contém a ID da base de dados de conhecimento que você precisará adicionar ao arquivo de configuração, descrito na próxima seção.

## <a name="update-your-projects-configuration-file-to-include-connection-information-for-luis-and-qna-maker"></a>Atualize o arquivo de configuração do projeto para incluir informações de conexão para LUIS e QnA Maker

O arquivo de configuração é nomeado **appsettings.jsem**. O exemplo a seguir mostra o arquivo de configuração para o **bot de todo com Luis e QnA Maker** amostra:

```json
{
    // The first two values are used to connect to your bot channel in Azure.
    // See the "Bot channels registration" section below for more information.
    "MicrosoftAppId": "",
    "MicrosoftAppPassword": "",
    // The next two values are used to connect to your Azure cognitive services LUIS
    // authoring resource. See the "LUIS key and hostname" section below for more information.
    "LuisAPIKey": "",
    "LuisAPIHostName": "",
    // The luis section contains all luis application IDs, created by the luis build
    // command and saved to the specified output directory in the file named:
    //  "luis.settings.<username>.<authoring-region>.json"
    // See "LUIS application IDs" section below for more details.
    "luis": {
        "GetUserProfileDialog_en_us_lu": "",
        "ViewToDoDialog_en_us_lu": "",
        "DeleteToDoDialog_en_us_lu": "",
        "RootDialog_en_us_lu": "",
        "AddToDoDialog_en_us_lu": ""
    },
    // The next two values are used to connect to your Azure cognitive services QnA Maker
    // resource. See "QnA Maker hostname and endpoint key" section below for more details.
    "QnAHostName": "",
    "QnAEndpointKey": "",
    // The qna section contains all QnA Maker knowledge base IDs, created from the .qna files by
    // the qnamaker build command and saved to the specified output directory in the file named:
    //  "qnamaker.settings.<username>.<authoring-region>.json"
    // See "QnA Maker knowledge base IDs" section below for more details.
    "qna": {
        "TodoBotWithLuisAnDQnA_en_us_qna": ""
    }
}
```

### <a name="the-configuration-file-details"></a>Os detalhes do arquivo de configuração

Esta seção explica o **appsettings.jsno** arquivo para o exemplo de bot a ser feito em detalhes.

<!--![The appsettings.json file](./media/appsettings.json.png)-->

#### <a name="bot-channels-registration"></a>Registro de canais do bot

Consulte o artigo de [registro de canais de bot][bot-channels-registration] para obter detalhes sobre como obter os `MicrosoftAppId` `MicrosoftAppPassword` valores e.

> [!NOTE]
>
> Esses valores não são necessários para concluir este artigo.

#### <a name="luis-key-and-hostname"></a>Chave LUIS e nome do host

O * *_LuisAPIKey_* _ é o `subscriptionKey` , e o _*_LuisAPIHostName_*_ é o `ENDPOINT` valor. Ambos os valores são encontrados na folha _chaves e ponto de extremidade_ na página de recursos de criação de Luis de serviços cognitivas do Azure, conforme mostrado na captura de tela abaixo:

![Chaves e ponto de extremidade para o recurso LUIS no Azure. Valores para LuisAPIKey e LuisAPIHostName.](./media/adaptive-dialogs/keys-and-endpoint-cross-train.png)

#### <a name="luis-application-ids"></a>LUIS IDs de aplicativo

A seção Luis contém todas as IDs de aplicativo do Luis usadas pelo bot. Esses valores podem ser encontrados na página de _configurações do aplicativo_ para um aplicativo LUIS no [www.Luis.ai](https://www.luis.ai/), no entanto, eles também estão listados no arquivo de configurações criado pelo `luis:build` comando, salvo no local fornecido como a `--out` opção. Este arquivo de configurações contém uma lista de cada ID de aplicativo LUIS que foi criada para cada localidade. O nome completo desse arquivo JSON é `luis.settings.<username>.<authoring-region>.json` . Por exemplo, se o nome de usuário conectado for _YuuriTanaka_ e você estiver direcionando a criação de região _ * westus * *, seu nome de arquivo será **luis.settings.YuuriTanaka.westus.js**. É aqui que você encontrará todos os valores da seção _Luis_ do seu **appsettings.jsno** arquivo.

#### <a name="qna-maker-hostname-and-endpoint-key"></a>Nome de host e chave de ponto de extremidade QnA Maker

`QnAHostName` é o valor de _host_ e `QnAEndpointKey` é o valor de _EndpointKey_ , ambos encontrados em QnA Maker, acessados selecionando o botão **Exibir código** quando estiver na página _bases de dados de conhecimento_ a seguir, conforme mostrado na captura de tela abaixo:

![QnA Maker código de exibição](./media/qna-maker-view-code.png)

#### <a name="qna-maker-knowledge-base-ids"></a>QnA Maker IDs da base de dados de conhecimento

O `qnamaker:build` comando salvará um arquivo de configurações no local fornecido como a `--out` opção. Esse arquivo contém uma lista de todas as QnA Maker ID da base de dados de conhecimento criada para cada localidade. O nome completo desse arquivo JSON é `qnamaker.settings.<username>.<authoring-region>.json` . Por exemplo, se o nome de usuário conectado for _YuuriTanaka_ e você estiver direcionando a região de criação **westus** , seu nome de arquivo será **qnamaker.settings.YuuriTanaka.westus.js**. É aqui que você encontrará todos os valores da `qna` seção de sua **appsettings.jsno** arquivo.

> [!IMPORTANT]
>
> O arquivo de configurações criado pelo `qnamaker:build` comando conterá uma entrada para cada um dos cinco modelos de QnA Maker, o valor de cada um será a ID do QnA Maker KB criado pelo comando de compilação. Como cada um contém o mesmo valor de ID, use qualquer um deles para o valor da chave "TodoBotWithLuisAndQnA_en_us_qna". Se você substituir esse valor único por todos os cinco valores do arquivo qnamaker. Settings, receberá um erro: "System. Exception: Observação: QnA Maker não está configurado para RootDialog".

## <a name="source-code-updates-for-cross-trained-models"></a>Atualizações de código-fonte para modelos com treinamento cruzado

Não há nenhuma atualização de código-fonte necessária no exemplo de **bot de todo adaptável com Luis e QnA Maker** ( [**C#**][cs-sample-todo-bot]) para aproveitar os modelos de treinamento cruzado, ele foi criado com o treinamento cruzado em mente. Esta seção explicará o código neste exemplo relacionado aos bots utilizando modelos com treinamento cruzado, usando **AddToDoDialog.cs** como exemplo, os mesmos conceitos se aplicam às outras caixas de diálogo adaptáveis neste bot.

### <a name="define-the-recognizer"></a>Definir o reconhecedor

O reconhecedor necessário para trabalhar com modelos que foram treinados em cruz é o [CrossTrainedRecognizerSet][crosstrainedrecognizerset-ref-guide].

Neste bot de exemplo, o reconhecedor é definido chamando um método, passando a `configuration` propriedade como seu parâmetro:

<!-- Line 44 -->

```csharp
Recognizer = CreateCrossTrainedRecognizer(configuration)
```

O método `CreateCrossTrainedRecognizer` cria um `CrossTrainedRecognizerSet` reconhecedor que consiste em uma `Recognizers` lista que contém um Luis e um reconhecedor de QnA Maker. Cada reconhecedor é criado chamando um método para cada:

<!-- Line 318-328 -->

```csharp
private static Recognizer CreateCrossTrainedRecognizer(IConfiguration configuration)
{
   return new CrossTrainedRecognizerSet()
   {
      Recognizers = new List<Recognizer>()
      {
         CreateLuisRecognizer(configuration),
         CreateQnAMakerRecognizer(configuration)
      }
   };
}

```

O `CreateLuisRecognizer` método cria o reconhecedor Luis. Consulte os comentários no trecho de código abaixo para obter explicações de código:
<!-- Line 360-375 -->

```csharp
public static Recognizer CreateLuisRecognizer(IConfiguration Configuration)
{
   // Verify that all required values exist in the configuration file appsettings.json and throw an error
   if (string.IsNullOrEmpty(Configuration["luis:RootDialog_en_us_lu"]) || string.IsNullOrEmpty(Configuration["LuisAPIKey"]) || string.IsNullOrEmpty(Configuration["LuisAPIHostName"]))
   {
      throw new Exception("Your RootDialog LUIS application is not configured. Please see README.MD to set up a LUIS application.");
   }
   return new LuisAdaptiveRecognizer()
   {
      // Get settings from the configuration file appsettings.json
      Endpoint = Configuration["LuisAPIHostName"],
      EndpointKey = Configuration["LuisAPIKey"],
      ApplicationId = Configuration["luis:RootDialog_en_us_lu"],

      // Id needs to be LUIS_<dialogName> for cross-trained recognizer to work.
      Id = $"LUIS_{nameof(RootDialog)}"
   };
}
```

O método `CreateQnAMakerRecognizer` cria um reconhecedor de QnA Maker. Consulte os comentários no trecho de código abaixo para obter explicações de código:

<!-- Line 330-358 -->

```csharp
private static Recognizer CreateQnAMakerRecognizer(IConfiguration configuration)
{
   // Verify that all required values exist in the configuration file appsettings.json
   if (string.IsNullOrEmpty(configuration["qna:TodoBotWithLuisAndQnA_en_us_qna"]) || string.IsNullOrEmpty(configuration["QnAHostName"]) || string.IsNullOrEmpty(configuration["QnAEndpointKey"]))
   {
      throw new Exception("NOTE: QnA Maker is not configured for RootDialog. Please follow instructions in README.md. To enable all capabilities, add 'qnamaker:qnamakerSampleBot_en_us_qna', 'qnamaker:LuisAPIKey' and 'qnamaker:endpointKey' to the appsettings.json file.");
   }

   return new QnAMakerRecognizer()
   {
      // Get settings from the configuration file appsettings.json
      HostName = configuration["QnAHostName"],
      EndpointKey = configuration["QnAEndpointKey"],
      KnowledgeBaseId = configuration["qna:TodoBotWithLuisAndQnA_en_us_qna"],

      // property path that holds qna context
      Context = "dialog.qnaContext",

      // Property path where previous qna id is set. This is required to have multi-turn QnA working.
      QnAId = "turn.qnaIdFromPrompt",

      // Disable Personal Information telemetry logging
      LogPersonalInformation = false,

      // Enable to automatically including dialog name as meta data filter on calls to QnA Maker.
      IncludeDialogNameInMetadata = true,

      // Id needs to be QnA_<dialogName> for cross-trained recognizer to work.
      Id = $"QnA_{nameof(RootDialog)}"
   };
}

```

### <a name="allowing-interruptions"></a>Permitindo interrupções
<!--When your models are not cross trained, and a user utterance-->
Se uma entrada do usuário não resultar em uma correspondência do reconhecedor, antes do treinamento cruzado de seus modelos LUIS e QnA Maker, o bot o enviará automaticamente para o pai de caixas de diálogo ativas, desde que a `AllowInterruptions` propriedade seja avaliada como _true_. Quando você faz o treinamento de seus modelos, a caixa de diálogo ativa se reconhece de outros recursos de manipulação de intenção de caixas de diálogo, portanto, se nenhuma correspondência for retornada, não será necessário consultar a caixa de diálogo pai. Nesse caso, como determinar se a caixa de diálogo ativa deve tratar a resposta dos usuários ou emergi-la para o pai? Considere este cenário usando o  **bot de todo com Luis e QnA Maker** exemplo:

> [!NOTE]
> Você pode acompanhar ao executar o bot usando o emulador. as instruções para fazer isso são fornecidas na próxima seção [testando o bot usando o emulador do bot Framework](#testing-the-bot-using-bot-framework-emulator).

- Ao iniciar o bot, o usuário é saudado com _Olá, é bom atendê-lo! Sou um bot de exemplo. Aqui estão algumas coisas com as quais posso me ajudar_ e, em seguida, prompt com SuggestedActions = **Add Item | Exibir listas | Remover item | Perfil | Cancelar | Ajuda** do
- O usuário seleciona _Exibir listas_

> Neste ponto, o **ViewToDoDialog** se torna a caixa de diálogo adaptável ativa

- O usuário receberá uma solicitação com _qual lista você gostaria de ver?_ e deu estas três opções: **todo | Supermercado | Compras | Todos**
- Em vez de selecionar qualquer uma das opções apresentadas, o usuário insere _remover todo_

O expressão _remover todo_ não pertence a nenhuma intenções no **ViewToDoDialog** , mas como os modelos foram treinados em cruz, Luis retorna uma correspondência. O bot só precisa saber usar o mecanismo de consulta para emergir a solicitação para **RootDialog** onde esse expressão está associado à intenção que resulta na chamada **DeleteToDoDialog** .

O código por trás da lista ao qual o usuário é solicitado no **ViewToDoDialog** :

[!code-csharp[AllowInterruptions](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/08.todo-bot-luis-qnamaker/Dialogs/ViewToDoDialog/ViewToDoDialog.cs?range=55-71&highlight=3-4,6)]
<!--
**The following code snippet is for comparison with the code link above. Remove before merging with main**

```csharp
new TextInput()
{
   Property = "dialog.listType",                       //highlight
   Prompt = new ActivityTemplate("${GetListType()}"),  //highlight
   Value = "=@listType",
   AllowInterruptions = "!@listType && turn.recognized.score >= 0.7",   //highlight
   Validations = new List<BoolExpression>()
   {
      // Verify using expressions that the value is one of todo or shopping or grocery
      "contains(createArray('todo', 'shopping', 'grocery', 'all'), toLower(this.value))",
   },
   OutputFormat = "=toLower(this.value)",
   InvalidPrompt = new ActivityTemplate("${GetListType.Invalid()}"),
   MaxTurnCount = 2,
   DefaultValue = "todo",
   DefaultValueResponse = new ActivityTemplate("${GetListType.DefaultValueResponse()}")
},
```
 -->
- O `Prompt` para isso `TextInput` chama o `GetListType()` modelo em **ViewToDoDialog. LG**.
- O valor retornado da entrada do usuário é salvo em `turn.recognized.entities.listType` . A abreviação de `turn.recognized.entities.listType` é `@listType`
- A expressão para verificações de AllowInterruptions `@listType` , que existirá se o usuário tiver selecionado ou inserido um tipo de lista válido. Se ele não existir, ele verificará se a correspondência retornada por LUIS tem uma pontuação de previsão de 70% ou superior `turn.recognized.score >= 0.7` . Se tiver, isso significa que uma caixa de diálogo pai ou irmã tem uma intenção com uma pontuação de previsão alta. Isso resulta em `AllowInterruptions` avaliação para true e os usuários expressão são passados para a caixa de diálogo pai a ser manipulada. Quando a caixa de diálogo pai manipula esse expressão, ele encontra uma correspondência na `DeleteItem` intenção que resulta na **DeleteToDoDialog**.

> [!NOTE]
>
>O artigo de conceito [cross training Your Luis and QnA Maker Models](bot-builder-concept-cross-train.md) descreve as alterações feitas em seus `.lu` `.qna` arquivos e quando eles são treinados de forma cruzada, e a [tabela de respostas do Recognizer](bot-builder-concept-cross-train.md#recognizer-responses) nesse artigo mostra todas as respostas possíveis do reconhecedor e a ação resultante tomada pelo bot.


## <a name="testing-the-bot-using-bot-framework-emulator"></a>Testar o bot usando o Bot Framework Emulator

## <a name="prerequisites-for-testing-the-bot-using-bot-framework-emulator"></a>Pré-requisitos para testar o bot usando o emulador do bot Framework

- [Visual Studio 2019 ou posterior](https://www.visualstudio.com/downloads) ou [Visual Studio Code](https://code.visualstudio.com/Download)
- [.NET Core 3.1](https://dotnet.microsoft.com/download)
- Uma cópia do [bot todo do C# com Luis e QnA Maker][cs-sample-todo-bot] exemplo.
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)

## <a name="build-and-run-the-bot-locally"></a>Criar e executar o bot localmente

Para executar o bot localmente, execute os comandos mostrados abaixo.

1. Em um terminal, navegue até `samples\csharp_dotnetcore\adaptive-dialog\08.todo-bot-luis-qnamaker`

1. Execute o bot.

   ```cmd
      dotnet run
   ```

Isso vai compilar o aplicativo, implantá-lo no localhost e iniciar o navegador da Web para exibir a página `default.htm` do aplicativo. Neste ponto, o bot deve estar em execução localmente na porta 3978, a menos que a `applicationUrl` configuração em **launchSettings.jsem** tenha sido modificada.

## <a name="start-the-emulator-and-connect-to-your-bot"></a>Iniciar o Emulador e conectar-se ao seu bot

1. Instale o Emulador do Bot Framework.

1. Clique em **Abrir Bot** na guia **Bem-vindo** do Emulador.

1. Insira a URL do bot, que é a URL da porta local, com /api/messages adicionado ao caminho, normalmente `http://localhost:3978/api/messages`.

   <!--This is the same process in the Emulator for all three languages.-->
   ![abrir uma tela de bot](../media/python/quickstart/open-bot.png)

1. E clique em **Conectar**.

Agora você pode interagir com o bot.

<!------------------------------------------------------------------------------------------------------------------>
[create-azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[luis]: https://aka.ms/luis-what-is-luis
[qnamaker]: /azure/cognitive-services/qnamaker/overview/overview
[cs-sample-todo-bot]: https://aka.ms/csharp-adaptive-dialog-08-todo-bot-luis-qnamaker-sample
[lu-templates]: ../file-format/bot-builder-lu-file-format.md
[qna-file-format]: ../file-format/bot-builder-qna-file-format.md
[create-your-luis-authoring-resource-in-azure]: bot-builder-howto-bf-cli-deploy-luis.md#create-your-luis-authoring-resource-in-azure
[create-your-qna-maker-resource-in-azure-cognitive-services]: bot-builder-howto-bf-cli-deploy-qna.md#create-your-qna-maker-resource-in-azure-cognitive-services

[bot-channels-registration]: ../bot-service-quickstart-registration.md

[cross-trained-recognizer-set-concept]: bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set

[crosstrainedrecognizerset-ref-guide]: ../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#cross-trained-recognizer-set

[language-generation]: bot-builder-concept-adaptive-dialog-generators.md
[create-cognitive-services-luis]: https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne
[create-cognitive-services-qnamaker]: https://portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker

[bf-qnamakerbuild]: https://aka.ms/botframework-cli#bf-qnamakerbuild
[qnamaker-build]: bot-builder-howto-bf-cli-deploy-qna.md#create-a-qna-maker-knowledge-base-and-publish-it-to-production-using-the-build-command
[qna-maker-knowledge-bases-created]: bot-builder-howto-bf-cli-deploy-qna.md#qna-maker-knowledge-bases-created
[create-your-qna-maker-initialization-file]: bot-builder-howto-bf-cli-deploy-qna.md#create-your-qna-maker-initialization-file

[bf-luisbuild]: https://aka.ms/botframework-cli#bf-luisbuild
[luis-build]: bot-builder-howto-bf-cli-deploy-luis.md#create-and-train-a-luis-app-then-publish-it-using-the-build-command

[luis-migration-authoring]: /azure/cognitive-services/luis/luis-migration-authoring

<!---------------------------------------------------------------------------------------------------------------->
