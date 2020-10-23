---
title: Implantar recursos do LUIS usando o bot Framework LUIS comandos da CLI-serviço bot
description: Descrevendo como automatizar o processo de implantação de modelos do LUIS usando os comandos da CLI do SDK do bot Framework LUIS
keywords: LUIS, bot, entradas, caixas de diálogo adaptáveis, aplicativos LUIS, modelos de LUIS,
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: how-to
ms.service: bot-service
ms.date: 08/28/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7971677050f82cfdd2f0bcb138c4a26437a5ed6d
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417294"
---
# <a name="deploy-luis-resources-using-the-bot-framework-luis-cli-commands"></a>Implantar os recursos do LUIS usando os comandos da CLI do LUIS do Bot Framework

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

A CLI (interface de linha de comando) do bot Framework permite automatizar o gerenciamento de aplicativos LUIS (LUIS aplicativos). Em uma linha de comando ou um script, você pode criar, atualizar e excluir Propriedades LUIS.

Este artigo explica como implantar um recurso LUIS. Para obter informações sobre como atualizar um recurso de LUIS existente usando a CLI do BF, consulte como [Atualizar recursos do Luis usando os comandos da CLI do bot Framework Luis][how-to-update-using-luis-cli].

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [modelos de Lu][lu-templates].
- Ter um projeto de bot com `.lu` arquivos.
- Se estiver trabalhando com caixas de diálogo adaptáveis, você deve compreender:
  - [Processamento de idioma natural em caixas de diálogo adaptáveis][natural-language-processing-in-adaptive-dialogs].
  - [Reconhecimento de idioma em caixas de diálogo adaptáveis][language-understanding].
  - como o [reconhecedor Luis][luis-recognizer] é usado.

## <a name="using-the-luis-cli-commands-to-enable-luis-in-your-bot"></a>Usando os comandos da CLI do LUIS para habilitar o LUIS em seu bot

Este artigo descreve como executar algumas tarefas comuns usando a CLI do bot Framework.

1. [Criar seu recurso de criação do LUIS no Azure](#create-your-luis-authoring-resource-in-azure)
1. [Instalar a CLI do bot Framework](#install-the-bot-framework-cli)
1. [Criar seu modelo LUIS](#create-your-luis-model)
1. [Criar seu aplicativo LUIS](#create-your-luis-application)
1. [Obter a appId do seu aplicativo LUIS](#get-the-appid-from-your-luis-app)
1. [Treine seu aplicativo LUIS](#train-your-luis-application)
1. [Publicar seu aplicativo LUIS](#publish-your-luis-application)
1. [Gerar código-fonte](#generate-source-code)
1. [Criar e treinar um aplicativo LUIS e, em seguida, publicá-lo usando o comando de compilação](#create-and-train-a-luis-app-then-publish-it-using-the-build-command)

Depois que o idioma do seu projeto de bot entender os recursos foram criados, você estará pronto para seguir as etapas descritas neste artigo para fazer com que o LUIS funcione.

## <a name="create-your-luis-authoring-resource-in-azure"></a>Criar seu recurso de criação do LUIS no Azure

O recurso de criação do LUIS é um recurso de [Serviços cognitivas do Azure][cognitive-services-overview] que você cria usando a página [criar serviços cognitivas][create-cognitive-services] do Azure. Você pode considerar isso como um contêiner para seus aplicativos LUIS e o modelo, ou modelos dos quais esses aplicativos LUIS são compostos. O recurso de criação do LUIS fornece uma maneira segura de criar seus recursos do LUIS.  Você precisa disso para poder executar as atividades envolvidas, como criar, atualizar, treinar e publicar um aplicativo LUIS. O grupo LUIS da CLI do bot Framework fornece os comandos necessários para realizar essas tarefas.

1. Vá para a página [criar serviços cognitivas][create-cognitive-services] do Azure.  
2. Na seção **criar opções** , selecione **criação** para criar um recurso de criação de Luis.

   ![Definir opções de criação para criar imagem](./media/adaptive-dialogs/create-options-authoring.png)

3. Insira valores para cada um dos campos e, em seguida, selecione o botão **revisar + criar** .

   ![Definir a imagem criar serviços cognitivas](./media/adaptive-dialogs/create-cognitive-services.png)

    > [!NOTE]
    > Ao inserir o **grupo de recursos** e o **nome**, tenha em mente que você não poderá alterar esses valores posteriormente. Observe também que o valor que você atribui para o **nome** será parte da **URL do ponto de extremidade**.

4. Examine os valores para garantir que estejam corretos e, em seguida, selecione o botão **criar** .

O recurso de criação do LUIS inclui informações que o bot usará para acessar seu aplicativo LUIS:

- **Chaves**. Elas são chamadas de _chaves de assinatura_, às vezes chamadas de chaves de _criação_ ao se referir a chaves no recurso de criação de Luis. Eles são gerados automaticamente. Você precisará de uma chave de criação ao referenciar o recurso de criação do LUIS para qualquer ação, como ao criar seu aplicativo LUIS e os modelos que serão detalhados neste artigo. Você pode encontrar as chaves na folha **chaves e ponto de extremidade** em seu recurso de criação do Luis.
- **Ponto de extremidade**. Isso é gerado automaticamente usando o nome do recurso de criação de LUIS que você fornece ao criá-lo. Ele tem o seguinte formato: `https://<luis-resource-name>.cognitiveservices.azure.com/` . Ao fazer referência ao recurso de criação do LUIS para qualquer ação, como ao criar seu aplicativo LUIS e os modelos que serão detalhados neste artigo. Você pode encontrar a chave na folha **chaves e ponto de extremidade** em seu recurso de criação do Luis.
- **Local**.   Esta é a região do Azure que contém o recurso de criação do LUIS. Você seleciona essa seleção ao criar o recurso de criação de LUIS.

   ![A folha chaves e ponto de extremidade no Azure](./media/adaptive-dialogs/keys-and-endpoint-example.png)

> [!TIP]
>
> Há dois tipos de chaves de assinatura associadas a um recurso LUIS, dependendo do tipo de recurso LUIS ao qual você está se referindo. Um recurso de criação de LUIS tem uma chave de criação e um recurso de previsão LUIS tem uma chave de previsão, ambas são chaves de assinatura e ambas podem ser encontradas na folha _chaves e ponto de extremidade_ .

Para obter mais informações, consulte [criar recursos de Luis][luis-how-to-azure-subscription].

## <a name="install-the-bot-framework-cli"></a>Instalar a CLI do bot Framework

[!INCLUDE [applies-to-v4](../includes/install-bf-cli.md)]

## <a name="create-your-luis-model"></a>Criar seu modelo LUIS

Depois de criar todos os arquivos individuais `.lu` necessários em seu projeto, você pode combiná-los para criar seu modelo Luis usando o `luis:convert` comando. Isso resulta em um arquivo JSON que você fará referência ao criar seu aplicativo LUIS hospedado nos serviços cognitivas do Azure no _recurso de criação do Luis_ que você criou anteriormente.

``` cli
bf luis:convert -i <input-folder-name> -o <output-file-name> -r
```

No exemplo a seguir, o comando é executado em uma linha de comando no diretório raiz do seu projeto. Ele pesquisará todos os `.lu` arquivos no diretório de _caixas de diálogo_ e, por causa da `-r` opção, todos os seus subdiretórios. Ele salvará um arquivo chamado LUISModel.jsno diretório de _saída_ .

``` cli
bf luis:convert -i dialogs -o .\output\LUISModel.json -r
```

## <a name="create-your-luis-application"></a>Criar seu aplicativo LUIS

O _recurso de criação de Luis_ que você perviously criou consiste em suas chaves de criação e ponto de extremidade, valores que você precisa ao criar seu aplicativo Luis (aplicativo Luis). Você pode ter vários aplicativos LUIS associados a um único recurso de criação de LUIS, cada aplicativo LUIS terá seu próprio, `appId` que será fornecido a você como parte do processo de criação. Você precisará que, `appId` ao se referir a este aplicativo Luis no futuro. O aplicativo LUIS fornecerá o bot com todas as funcionalidades fornecidas pelo LUIS, combinadas com os dados específicos do aplicativo que você fornece em seu modelo de LUIS que você criou anteriormente a partir de seus arquivos de projetos `.lu` .

Para criar seu aplicativo LUIS:

``` cli
luis:application:import --in <luis-model-json-file> --endpoint <endpoint> --subscriptionKey <subscription-key> --name <name> --versionId <initial-version-id>
```

O exemplo acima mostra o `luis:application:import` comando com suas opções necessárias. Para obter mais detalhes e ver todas as opções disponíveis para esse comando, consulte a seção [BF Luis: Application: Import][bf-luisapplicationimport] do Leiame da CLI do Luis.

Para criar um aplicativo LUIS sem incluir um modelo LUIS, consulte o comando [BF Luis: aplicativo: criar][bf-luisapplicationcreate] .

![Chaves e Ponto de Extremidade](./media/adaptive-dialogs/keys-and-endpoint-myluisapp.png)

Na imagem acima, você usaria o comando a seguir para criar seu aplicativo LUIS, supondo que o arquivo JSON do modelo LUIS esteja em um subdiretório chamado _output_:

`luis:application:import --in .\output\LUISModel.json --endpoint https://LUIS-Authoring-Resource.cognitiveservices.azure.com/ --subscriptionKey xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx --name LUISApplication --versionId 0.1`

> [!NOTE]
>
> As chaves na imagem acima não são chaves válidas. Eles são mostrados aqui apenas para fins de demonstração.
> Essas chaves de criação oferecem uma maneira segura de acessar seus recursos de criação do LUIS. Não compartilhe suas chaves.

## <a name="get-the-appid-from-your-luis-app"></a>Obter a appId do seu aplicativo LUIS

Você precisará do `appId` que é retornado quando o `luis:application:import` comando for concluído com êxito, você também precisará do `versionId` que você forneceu ao criar o aplicativo Luis. Em cenários em que você não tem essas informações, ou se estiver criando scripts para automatizar esse processo, você pode usar o comando [Luis: Application: show][bf-luisapplicationshow] para obter essas informações.

``` cli
bf luis:application:show --appId <application-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

## <a name="train-your-luis-application"></a>Treine seu aplicativo LUIS

O treinamento é o processo de ensinar seu aplicativo LUIS a melhorar seu conhecimento em linguagem natural. Você precisa treinar seu aplicativo LUIS depois de ter feito todas as atualizações para o modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou declarações. Para obter informações adicionais, consulte o artigo [treinar a versão ativa do aplicativo Luis][luis-how-to-train] no documentos do Luis.

Para treinar seu aplicativo LUIS, use o `luis:train:run` comando:

```cli
bf luis:train:run --appId <application-id> --versionId <version-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Isso permite que você automatize o que normalmente faria no site do LUIS:

![Treinar o aplicativo LUIS](./media/adaptive-dialogs/train.png)

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Train: execute][bf-luistrainrun] na CLI do BF Luis Leiame.

> [!TIP]
>
> Depois de treinar seu aplicativo LUIS, você deve [testá][luis-concept-test] -lo com o declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente. Esse teste pode ser feito manualmente no site do LUIS, para obter mais informações, consulte o artigo [testar um expressão][test-an-utterance].

## <a name="publish-your-luis-application"></a>Publicar seu aplicativo LUIS

Quando você terminar de criar, treinar e testar seu aplicativo LUIS ativo, disponibilize-o para o aplicativo cliente publicando-o no ponto de extremidade. Você pode fazer isso usando o `luis:application:publish` comando.

```cli
bf luis:application:publish --appId <application-id> --versionId <version-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Application: publish][luisapplicationpublish] na CLI do BF Luis Leiame.

Para obter informações sobre como publicar um aplicativo LUIS, consulte [publicar seu aplicativo ativo e treinado em um ponto de extremidade de preparo ou de produção][luis-how-to-publish-app].

## <a name="generate-source-code"></a>Gerar código-fonte

[!INCLUDE [applies-to-v4](../includes/generate-source-code-luis-cli.md)]

## <a name="create-and-train-a-luis-app-then-publish-it-using-the-build-command"></a>Criar e treinar um aplicativo LUIS e, em seguida, publicá-lo usando o comando de compilação

É útil entender como o processo de implantação de um aplicativo LUIS funciona e, depois de concluir este artigo até este ponto, você deve ter uma melhor compreensão dos processos envolvidos na criação do modelo LUIS, usando esse modelo para criar um aplicativo LUIS em um recurso de serviços cognitivas do Azure e, em seguida, treiná-lo e publicá-lo usando os comandos da CLI do bot Framework.

Esses comandos oferecem muita flexibilidade ao adaptar os scripts às suas necessidades específicas.
Você pode usar o `luis:build` comando para criar ou atualizar e, em seguida, treinar e publicar um aplicativo Luis.
No entanto, o uso do `luis:build` comando fornece menos opções para controlar o processo.

Para cada `.lu` arquivo, incluindo `.lu` arquivos para cada localidade, o comando de compilação combina todas as seguintes ações em um único comando:

1. Cria um modelo LUIS para [cada localidade](#lu-and-multiple-language-variations) encontrada usando seus `.lu` arquivos existentes.
1. Usando esse modelo, ele cria um novo aplicativo LUIS no recurso de serviços cognitivas do Azure especificado, caso não exista nenhum, caso contrário, ele atualizará o aplicativo LUIS existente.
1. Ao atualizar um aplicativo LUIS existente, ele incrementará automaticamente o versionID e, opcionalmente, excluirá a versão antiga.
1. Treina o aplicativo LUIS novo ou atualizado e, em seguida, o publica.
1. Se você incluir a `--dialog` opção, ela produzirá os `.dialog` arquivos de definição que podem ser usados pelo [reconhecedor Luis][luis-recognizer] ao desenvolver usando a [abordagem declarativa][declarative]. Isso é explicado na seção [arquivo de diálogo](#the-dialog-file) .

## <a name="how-to-use-the-build-command"></a>Como usar o comando de Build

O comando de compilação LUIS com seus parâmetros obrigatórios:

``` cli
bf luis:build --in <input-file-or-folder> --out <output-file-or-folder> --authoringKey <subscription-key> --region <authoring-region>
```

O `luis:build` comando criará todos os ativos de que você precisa em seus `.lu` arquivos locais. Ao usar a `--in` opção, o `luis:build` criará um aplicativo Luis para cada `.lu` arquivo encontrado para cada localidade.

### <a name="required-luisbuild-parameters"></a>Luis obrigatório: parâmetros de compilação

- `in`: O diretório, incluindo subdiretórios, que serão pesquisados em busca de arquivos. Lu.
- `out`: O diretório no qual salvar os arquivos de saída. Isso inclui todos os arquivos do Recognizer, bem como o arquivo de configurações. Se você omitir a `--out` opção, nenhum arquivo será salvo em disco e somente as chaves de criação e o ponto de extremidade do arquivo de configurações serão gravados no console do.
- `botName`: O nome do bot. Isso será usado como o prefixo para o nome dos aplicativos LUIS gerados.
- `authoringKey`: O mesmo valor que o subscriptionKey usado em todos os comandos anteriores discutidos neste artigo.
- `region`: Define a região para publicar seus aplicativos LUIS.

Para obter informações sobre as opções adicionais, consulte [BF Luis: Build][bf-luisbuild] no Leiame da CLI do BF.

Como alternativa, você pode incluir esses requisitos, bem como qualquer um dos outros parâmetros em um arquivo de configuração e consultá-los usando a `--luConfig` opção.

### <a name="luis-build-configuration-file"></a>Arquivo de configuração de compilação LUIS

Veja a seguir um exemplo de **luconfig.jsno** arquivo que você pode referenciar usando a `--luConfig` opção.

```json
{
    "in": "dialogs",
    "out": "generated",
    "botName":"MyProject",
    "authoringKey":"<your-32-digit-subscription-key>",
    "region": "westus",
    "schema": "app.schema",
    "defaultCulture":"en-us",
    "deleteOldVersion": true,
    "dialog": "multiLanguage",
    "fallbackLocale": "en-us",
    "force": true,  
    "suffix": "<value-to-replace-username>"
}
```

Além disso, há uma `models` seção na **luconfig.jsno** arquivo que é útil quando você precisa controlar quais `.lu` arquivos em seu projeto correspondem a um aplicativo Luis. Isso é especialmente útil se você estiver aproveitando referências externas em seus `.lu` arquivos, portanto, nem todo `.lu` arquivo único é tratado como um aplicativo Luis. Cada arquivo que você listar na `models` seção se tornará um aplicativo Luis e todos os `.lu` arquivos não listados se tornarão parte do aplicativo Luis criado a partir de qualquer `.lu` arquivo que faça referência a ele. Os arquivos referenciados em vários `.lu` arquivos serão duplicados em cada aplicativo Luis que faz referência a ele. Qualquer `.lu` arquivo não explicitamente listado na seção modelos do **luconfig.jsno** arquivo ou referenciado em um dos `.lu` arquivos listados será ignorado.

O exemplo a seguir mostra uma seção de _modelos_ de exemplo do **luconfig.jsno** arquivo:

```json
    // Each model is a LUIS application.
    "models": [
        // Each line is to an lu file and corresponds to a LUIS application.
        // relative paths here are relative to the luconfig.json file itself.
        "./dialog/AddToDoDialog/AddToDoDialog.lu",
        "./dialog/Common/Common.lu",
        "./dialog/DeleteToDoDialog/DeleteToDoDialog.lu",
        "./dialog/GetUserProfileDialog/GetUserProfileDialog.lu",
        "./dialog/RootDialog/RootDialog.lu",
        "./dialog/ViewToDoDialog/ViewToDoDialog.lu"
    ]
```

Depois que esse arquivo de configuração é criado, tudo o que você precisa fazer é fazer referência a ele no `luis:build` comando. Por exemplo:

``` cli
bf luis:build --luConfig luconfig.json
```

### <a name="lu-and-multiple-language-variations"></a>Variações de LU e várias linguagens

Cada [arquivo. Lu][lu-templates] pode ter várias variações de idioma e o `luis:build` comando criará um aplicativo Luis para cada idioma com suporte.

O padrão para o `.lu` nome do arquivo, quando as localidades adicionais são usadas, é o seguinte:

`<file-name>.<locale>.lu`

Por exemplo:

```
RootDialog.en-us.lu
RootDialog.fr-fr.lu
RootDialog.de-de.lu
etc.
```

No exemplo acima, cada um dos arquivos terá `.lu` seu próprio modelo específico de linguagem exclusivo criado para ele, resultando em um aplicativo Luis para cada um dos três `.lu` arquivos.

> [!TIP]
>
> - Como alternativa para incluir a localidade no nome do arquivo, você pode incluí-lo no `.lu` próprio arquivo como parte de suas informações de configuração. Isso é útil quando você precisa de mais flexibilidade com a Convenção de nomenclatura de arquivo. Por exemplo, no `.lu` arquivo para o idioma francês, você adicionaria: `> !# @app.culture = fr-fr` . Consulte a [Descrição do modelo][model-description] na referência de formato de [arquivo. Lu][lu-templates] para obter mais informações.
>
> - Se nenhuma localidade puder ser determinada a partir do nome do arquivo e não estiver incluída nas `.lu` informações de configuração dos arquivos, o valor especificado na opção compilar comandos `--defaultCulture` será usado. Se a `--defaultCulture` opção for omitida, a localidade será definida como `en-us` .

### <a name="luis-applications-created"></a>Aplicativos LUIS criados

Cada aplicativo LUIS criado em seu nome será nomeado usando uma combinação do `botName` valor que você fornece ao executar o `luis:build` comando, o nome de usuário da pessoa conectada e o nome do `.lu` arquivo, incluindo a localidade.

Os nomes de aplicativos LUIS usarão este formato:  `{botName}{(suffix)}-{file-name}-{locale}.lu`

Por exemplo, se seu botName for _MyProject_ e seu nome de usuário for _YuuriTanaka_, e o nome do arquivo for _getaddresses_ , os nomes dos seus aplicativos Luis seriam os seguintes:

```
MyProject(YuuriTanaka)-GetAddresss.en-us.lu
MyProject(YuuriTanaka)-GetAddresss.fr-fr.lu
MyProject(YuuriTanaka)-GetAddresss.de-de.lu
```

O mesmo nome de aplicativo LUIS será usado em cada região do Azure, com pontos de extremidade internos a ele.

> [!TIP]
>
> Incluir o nome de usuário como parte do nome do aplicativo LUIS permite que vários desenvolvedores trabalhem de forma independente. Esse valor é gerado automaticamente, usando o nome de usuário da pessoa que fez logon; no entanto, você pode substituir isso usando a `--suffix` opção.

### <a name="the-settings-file-generated-using-the-build-command"></a>O arquivo de configurações gerado usando o comando de compilação

Todos os `.lu` arquivos de cada localidade resultarão em um aplicativo Luis e a saída do `luis:build` comando incluirá um arquivo de configurações que contém uma lista de cada ID de aplicativo do Luis que foi criada para cada localidade.

Por exemplo, se o nome de usuário conectado for _YuuriTanaka_ e você estiver direcionando a região de criação **westus**, seu nome de arquivo será:

**luis.settings.YuuriTanaka.westus.jsem**

Arquivo de configurações de exemplo:

```json
{
    "luis": {
        "RootDialog_en_us_lu": "<LUIS-App-ID-for-en-us-locale>",
        "RootDialog_fr_fr_lu": "<LUIS-App-ID-for-fr-fr-locale>"
    }
}
```

### <a name="the-dialog-file"></a>O arquivo de diálogo

Quando você incluir a `--dialog` opção, um `.dialog` arquivo será gerado para cada um de seus `.lu` arquivos, um para cada localidade. 

> [!IMPORTANT]
>
> A `--schema` opção é usada em conjunto com a `--dialog` opção. A inclusão da `--schema` opção garantirá que cada arquivo de caixa de diálogo criado terá uma referência ao arquivo de esquema raiz de projetos. Esse arquivo de esquema contém os esquemas de todos os componentes consumidos pelo bot. Cada consumidor de arquivos declarativos, incluindo [Composer] [Composer], precisa de um arquivo de esquema. Se o seu projeto não tiver um arquivo de esquema, você poderá gerar um usando o `dialog:merge` comando. Será necessário executar esse comando antes de executar o `luis:build` comando. Para obter informações adicionais, consulte o artigo sobre como [usar ativos declarativos em caixas de diálogo adaptáveis][dialog-merge-command].

Os parâmetros de caixa de diálogo Luis: Build e Schema:

- **caixa de diálogo**. Há dois valores válidos para a opção de caixa de diálogo `multiLanguage` e `crosstrained` .
- **esquema**. Isso usa um caminho relativo e o nome de arquivo apontando para o arquivo de esquema do bot.

 Esses arquivos serão gravados no diretório especificado na `out` opção. Por exemplo:

```
RootDialog.en-us.lu.dialog <-- LuisRecognizer for en-us locale
RootDialog.fr-fr.lu.dialog <-- LuisRecognizer for fr-fr locale
RootDialog.lu.dialog       <-- MultiLanguageRecognizer configured to use all locales
```

Aqui está um exemplo do arquivo _MultiLanguageRecognizer_ :

```json
{
    "$schema": "app.schema",
    "$kind": "Microsoft.MultiLanguageRecognizer",
    "id": "lu_RootDialog",
    "recognizers": {
        "en-us": "RootDialog.en-us.lu",
        "fr-fr": "RootDialog.fr-fr.lu",
        "": "RootDialog.en-us.lu"
    }
}
```

Você usará esses arquivos se estiver usando a abordagem declarativa para desenvolver o bot, e precisará adicionar uma referência a esse reconhecedor em seu arquivo de caixas de diálogo adaptáveis `.dialog` . No exemplo a seguir, o `"recognizer": "RootDialog.lu"` está procurando o reconhecedor que está definido no arquivo **RootDialog. Lu. Dialog**:

 ![Como fazer referência a um reconhecedor em um arquivo. Dialog](./media/adaptive-dialogs/how-to-reference-the-lu-recognizer-in-dialog-file.png)

Consulte [usando ativos declarativos em caixas de diálogo adaptáveis][declarative] para obter mais informações.

## <a name="additional-information"></a>Informações adicionais

- [Atualizando seus modelos do LUIS][how-to-update-using-luis-cli]

<!----------------------------------------------------------------------------------------------------->
[cognitive-services-overview]: /azure/cognitive-services/Welcome
[create-cognitive-services]: https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne
[luis-recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md#luis-recognizer
[natural-language-processing-in-adaptive-dialogs]: bot-builder-concept-adaptive-dialog-recognizers.md#introduction-to-natural-language-processing-in-adaptive-dialogs
[language-understanding]: bot-builder-concept-adaptive-dialog-recognizers.md#language-understanding
[lu-templates]: ../file-format/bot-builder-lu-file-format.md
[model-description]: ../file-format/bot-builder-lu-file-format.md#model-description
[luis-how-to-azure-subscription]: /azure/cognitive-services/luis/luis-how-to-azure-subscription
[bf-cli-overview]: bf-cli-overview.md

[bf-luisapplicationimport]: https://aka.ms/botframework-cli#bf-luisapplicationimport
[bf-luisapplicationcreate]: https://aka.ms/botframework-cli#bf-luisapplicationcreate
[bf-luisapplicationshow]: https://aka.ms/botframework-cli#bf-luisapplicationshow
[bf-luistrainrun]: https://aka.ms/botframework-cli#bf-luistrainrun
[luisapplicationpublish]: https://aka.ms/botframework-cli#bf-luisapplicationpublish
[bf-luisgeneratecs]: https://aka.ms/botframework-cli#bf-luisgeneratecs
[bf-luisgeneratets]: https://aka.ms/botframework-cli#bf-luisgeneratets
[bf-luisbuild]: https://aka.ms/botframework-cli#bf-luisbuild
[declarative]: bot-builder-concept-adaptive-dialog-declarative.md
[dialog-merge-command]: bot-builder-concept-adaptive-dialog-declarative.md#the-merge-command

[luis-how-to-add-intents]: /azure/cognitive-services/LUIS/luis-how-to-add-intents
[luis-how-to-start-new-app]: /azure/cognitive-services/LUIS/luis-how-to-start-new-app
[luis-how-to-train]: /azure/cognitive-services/LUIS/luis-how-to-train
[luis-concept-test]: /azure/cognitive-services/LUIS/luis-concept-test
[test-an-utterance]: /azure/cognitive-services/LUIS/luis-interactive-test#test-an-utterance
[luis-interactive-test]: /azure/cognitive-services/LUIS/luis-interactive-test
[luis-how-to-publish-app]: /azure/cognitive-services/LUIS/luis-how-to-publish-app

[ToDoBotWithLUISAndQnAMakerSample]: https://aka.ms/csharp-adaptive-dialog-08-todo-bot-luis-qnamaker-sample

[how-to-update-using-luis-cli]: bot-builder-howto-bf-cli-update-luis.md

<!----------------------------------------------------------------------------------------------------------------------------->
