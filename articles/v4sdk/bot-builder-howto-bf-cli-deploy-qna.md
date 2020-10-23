---
title: Implantar QnA Maker base de dados de conhecimento usando o bot Framework QnA Maker comandos da CLI-serviço bot
description: Descrevendo como automatizar o processo de implantação de QnA Maker base de dados de conhecimento usando o bot Framework QnA Maker comandos da CLI
keywords: QnA Maker, base de dados de conhecimento, KB, CLI BF, qnamaker, modelos de QnA Maker, bot, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: how-to
ms.service: bot-service
ms.date: 09/10/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 34f7813711da97c9a68a67a607eb138944b9271f
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417270"
---
# <a name="deploy-qna-maker-knowledge-base-using-the-bot-framework-qnamaker-cli-commands"></a>Implantar QnA Maker base de dados de conhecimento usando os comandos da CLI do bot Framework qnamaker

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

A CLI (interface de linha de comando) do bot Framework permite automatizar o gerenciamento da base de dados de conhecimento do QnA Maker (KB). Ele permite que você crie, atualize e exclua QnA Maker KB da linha de comando ou de um script. Este artigo explica como implantar QnA Maker KB em um QnA Maker KB no Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Noções básicas sobre [QnA Maker][qna-overview].
- Conhecimento do [formato de arquivo. QnA][qna-file-format].
- Ter um projeto de bot com `.qna` arquivos.
- Se estiver trabalhando com caixas de diálogo adaptáveis, você deve compreender:
  - [Processamento de idioma natural em caixas de diálogo adaptáveis][natural-language-processing-in-adaptive-dialogs].
  - como o [reconhecedor de QnA Maker][qna-maker-recognizer] é usado.

## <a name="using-the-qnamaker-cli-commands-to-enable-qna-maker-in-your-bot"></a>Usando os comandos da CLI do qnamaker para habilitar QnA Maker em seu bot

Este artigo descreve como executar algumas tarefas comuns usadas para implantar um QnA Maker KB usando a CLI do bot Framework.

1. [Criar seu recurso de QnA Maker nos serviços cognitivas do Azure](#create-your-qna-maker-resource-in-azure-cognitive-services)
1. [Instalar a CLI do bot Framework](#install-the-bot-framework-cli)
1. [Criar seu arquivo de inicialização QnA Maker](#create-your-qna-maker-initialization-file)
1. [Criar seu modelo de QnA Maker](#create-your-qna-maker-model)
1. [Criar sua base de dados de conhecimento QnA Maker](#create-your-qna-maker-knowledge-base)
1. [Testar sua base de dados de conhecimento QnA Maker](#test-your-qna-maker-knowledge-base)
1. [Publicar sua base de dados de conhecimento QnA Maker](#publish-your-qna-maker-knowledge-base)
1. [Criar uma base de dados de conhecimento QnA Maker e publicá-la na produção usando o comando de Build](#create-a-qna-maker-knowledge-base-and-publish-it-to-production-using-the-build-command)

Depois que os arquivos KB do seu projeto de bot QnA Maker `.qna` tiverem sido criados, você estará pronto para seguir as etapas descritas neste artigo para criar seu QnA Maker KB. Se você não tiver um projeto com QnA Maker arquivos KB `.qna` , poderá usar o [QnAMaker][qna-maker-sample]. Consulte o Leiame do repositório de exemplo para obter informações sobre [como obter os exemplos][getting-the-samples].

## <a name="create-your-qna-maker-resource-in-azure-cognitive-services"></a>Criar seu recurso de QnA Maker nos serviços cognitivas do Azure

O recurso de QnA Maker é um recurso de [Serviços cognitivas do Azure][cognitive-services-overview] que você cria usando a página [criar serviços cognitivas][create-cognitive-services] do Azure. Isso fornece as chaves de segurança e o ponto de extremidade necessários para acessar seu QnA Maker KB no Azure.

1. Vá para a página [criar serviços cognitivas][create-cognitive-services] do Azure.
2. Insira valores para cada um dos campos e, em seguida, selecione o botão **revisar + criar** .

   ![Criar sua base de dados de conhecimento do QnA Maker no Azure](./media/adaptive-dialogs/create-qna-maker.png)

    > [!NOTE]
    > Ao inserir o **grupo de recursos** e o **nome**, tenha em mente que você não poderá alterar esses valores posteriormente. Observe também que o valor que você atribui para o **nome** será parte da **URL do ponto de extremidade**.

3. Examine os valores para garantir que estejam corretos e, em seguida, selecione o botão **criar** .

O recurso de QnA Maker inclui informações que o bot usará para acessar sua base de dados de conhecimento do QnA Maker:

- **Chaves**. Elas são chamadas de _chaves de assinatura_ e são geradas automaticamente. Você precisará da chave de assinatura ao referenciar o recurso de QnA Maker para qualquer ação, como ao criar ou atualizar seu QnA Maker KB, que será detalhado neste artigo. Você pode encontrar as chaves na folha **chaves e ponto de extremidade** em seu recurso de QnA Maker.
- **Ponto de extremidade**. Isso é gerado automaticamente usando o nome do recurso de QnA Maker que você fornece ao criá-lo. Ele tem o seguinte formato: `https://<qnamaker-resource-name>.cognitiveservices.azure.com/` . Ao fazer referência a seu recurso de QnA Maker para qualquer ação, como ao criar seu QnA Maker KB, que será detalhado neste artigo. Você pode encontrar a chave na folha **chaves e ponto de extremidade** em seu recurso de QnA Maker. 

    > [!TIP]
    >
    > É importante entender a diferença entre esse QnA Maker ponto de extremidade de criação de recursos, que é referenciado em todos os comandos de criação do BF CLI qnamaker e a chave de ponto de extremidade da base de dados de conhecimento da QnA Maker que é referenciada em arquivos de configuração de código-fonte, como appsettings.jsem C# `.env` ou em JavaScript ou `config.py` em Python.

- **Local**. Esta é a região do Azure que contém seu QnA Maker KB. Você seleciona essa seleção ao criar o recurso de QnA Maker.

   ![A folha chaves e ponto de extremidade no Azure](./media/adaptive-dialogs/qna-maker-keys-and-endpoint.png)

## <a name="install-the-bot-framework-cli"></a>Instalar a CLI do bot Framework

Se você já tiver instalado a CLI do bot Framework, poderá pular para [criar seu modelo de QnA Maker](#create-your-qna-maker-model).

[!INCLUDE [applies-to-v4](../includes/install-bf-cli.md)]

## <a name="create-your-qna-maker-initialization-file"></a>Criar seu arquivo de inicialização QnA Maker

A CLI do bot Framework fornece um mecanismo para armazenar todos os valores de parâmetro usados com frequência em um arquivo de inicialização. Depois que esse arquivo for criado, cada `qnamaker` comando executado verificará esse arquivo quanto aos valores necessários se eles não estiverem incluídos na linha de comando. Se você fornecer o valor desse parâmetro, ele substituirá o valor no arquivo init.

Você cria esse arquivo de inicialização usando o `qnamaker:init` comando. Isso criará um arquivo JSON contendo os dados necessários ao executar muitos dos comandos da CLI do QnAMaker BF, incluindo _subscriptionKey_, _kbId_, _endpointKey_ e _hostname_.

> [!NOTE]
>
> Como você ainda não criou seu QnA Maker KB, não será possível fornecer a ID do KB quando solicitado. Isso resultará em um arquivo de inicialização que não tem um valor para `kbId` também `hostname` . Esses valores serão adicionados automaticamente quando você criar seu QnA Maker KB quando você incluir a `--save` opção ao executar o comando.

O comando para criar o arquivo de inicialização qnamaker:

``` cli
bf qnamaker:init
```

Para criar o arquivo de inicialização da CLI do QnA Maker:

1. No seu console, digite `bf qnamaker:init`
1. Você será solicitado a fornecer a chave de assinatura para seu QnA Maker recurso de serviços cognitivas no Azure. Você pode encontrar isso na folha _chaves e ponto de extremidade_ :

    ![Chaves de QnA Maker e ponto de extremidade no Azure](./media/adaptive-dialogs/keys-and-endpoint-qnamaker.png)

1. Em seguida, você será solicitado a fornecer sua ID da base de dados de conhecimento ( `kbId` ). Como você ainda não criou seu QnA Maker KB, insira **nenhum**.
1. Os valores são coletados e gravados na tela para verificação. Se correto, digite `yes` ou apenas pressione a tecla **Enter** .
1. Em seguida, o arquivo é criado e salvo em _C:\Users \\ \<unsername> \AppData\Local \@microsoft\botframework-cli\config.jsem_. Como esse arquivo contém dados confidenciais, eles não são salvos no mesmo diretório que os arquivos de projeto do bot para impedir que ele seja verificado em qualquer local potencialmente desprotegido ao fazer check-in do código-fonte.

> [!TIP]
> Quando você insere um `bf qnamaker` comando da CLI, ele procura automaticamente os valores de _subscriptionKey_, _kbId_, _endpointKey_ e _hostname_ nesse arquivo de inicialização, a menos que você os inclua ao inserir o comando, ponto em que os valores inseridos substituirão os valores do arquivo de inicialização.

Para obter informações adicionais sobre como usar esse comando, consulte [`bf qnamaker:init`][bf-qnamakerinit] na CLI do BF QnA Maker Leiame.

> [!IMPORTANT]
>
> Os comandos explicados neste artigo pressupõem que você tenha um arquivo de inicialização. Se você não tiver um arquivo de inicialização, precisará incluir esses valores ao executar cada comando.

## <a name="create-your-qna-maker-model"></a>Criar seu modelo de QnA Maker

Depois de criar os arquivos individuais `.qna` para o bot, você pode convertê-los em um único _modelo de QnA Maker_ usando o `qnamaker:convert` comando. O modelo de QnA Maker é um arquivo JSON usado para criar um QnA Maker KB.

Para criar seu modelo de QnA Maker:

``` cli
bf qnamaker:convert -i <input-folder-name> -o <output-folder-name> --name <QnA-KB-Name> -r
```

Por exemplo, o comando `bf qnamaker:convert -i dialogs -o output --name MyQnAMakerBot -r` pesquisará recursivamente todos os `.qna` arquivos no diretório _Dialogs__ e quaisquer subdiretórios e os mesclará em um único arquivo chamado **converted.js** no diretório de _saída_ . Esse arquivo JSON conterá todas as informações necessárias para criar um QnA Maker KB, incluindo o nome _MyQnAMakerBot_ , que será o nome da base de dados de conhecimento QnA Maker que existirá no Azure.

Para obter informações adicionais sobre como usar esse comando, consulte [`bf qnamaker:convert`][bf-qnamakerconvert] na CLI do BF QnA Maker Leiame.

## <a name="create-your-qna-maker-knowledge-base"></a>Criar sua base de dados de conhecimento QnA Maker

O _recurso de QnA Maker_ que você perviously criou consiste em duas chaves de assinatura e um ponto de extremidade. Esses são os valores que você precisa ao criar seu QnA Maker KB (QnA Maker KB). Você pode ter vários KBs QnA Maker associados a um único recurso de QnA Maker, cada QnA Maker KB terá sua própria ID, denominada `kbId` . Esse valor será retornado como parte do processo de criação. Você precisará dessa ID quando fizer referência a este QnA Maker KB no futuro. Este QnA Maker KB fornece o bot com todas as funcionalidades fornecidas pelo QnA Maker.

Para criar seu QnA Maker KB:

``` cli
bf qnamaker:kb:create --in <QnA-Maker-model-JSON-file> --name <QnA-Maker-kb-name>
```

> [!NOTE]
>
> - O arquivo de entrada para esse comando é o arquivo criado com a execução do `qnamaker:convert` comando, conforme discutido na etapa anterior. O nome do arquivo é **converted.js** por padrão.
> - A `name` opção é o nome do QnA Maker KB e é opcional se o arquivo JSON do modelo QnA tiver um valor para a propriedade Name; caso contrário, será necessário.

Para obter informações adicionais sobre como usar esse comando, consulte [`bf qnamaker:kb:create`][bf-qnamakerkbcreate] na CLI do BF QnA Maker Leiame.

## <a name="test-your-qna-maker-knowledge-base"></a>Testar sua base de dados de conhecimento QnA Maker

Não há nenhum comando BF CLI qnamaker disponível para testar sua KB, no entanto, você pode [testar a base de dados de conhecimento com perguntas do lote e respostas esperadas][batch-testing].

Se você planeja criar scripts para automatizar esse processo de ponta a ponta, isso permitirá que você inclua testes.

## <a name="publish-your-qna-maker-knowledge-base"></a>Publicar sua base de dados de conhecimento QnA Maker

Os KBs QnA Maker recentemente criados são publicados automaticamente no ponto de extremidade de _teste_ , onde podem ser testados antes de ficarem ativos. Para obter informações gerais sobre como testar seu KB, consulte [testar sua base de dados de conhecimento no QnA Maker][test-knowledge-base].

Depois de testado, você pode usar o `qnamaker:kb:publish` para publicá-lo no ponto de extremidade de _produção_ .

Para publicar sua base de dados de conhecimento do QnA Maker:

``` cli
bf qnamaker:kb:publish
```

Para publicar seu QnA Maker KB, se você não tiver um arquivo de [arquivo de inicialização](#create-your-qna-maker-initialization-file) :

``` cli
bf qnamaker:kb:publish --subscriptionKey <Subscription-Key> --kbId <knowledge-base-id>
```

> [!TIP]
>
> Se você tiver vários QnA Maker KB e quiser publicar um que não seja o apontado no arquivo de inicialização, será necessário fornecer a ID do KB usando a `--kbId` opção:
>
> `bf qnamaker:kb:publish --kbId <knowledge-base-id>`
>
> Você pode encontrar a ID da base de dados de conhecimento em [QnAMaker](https://www.qnamaker.ai/) na seção _detalhes da implantação_ da página _configurações_ :
>
> ![Seção detalhes da implantação da página configurações no QnA Maker](./media/adaptive-dialogs/settings-deployment-details-qnamaker.png)

<!-------------------------------------------------------------------------------------------------------------------------->
## <a name="create-a-qna-maker-knowledge-base-and-publish-it-to-production-using-the-build-command"></a>Criar uma base de dados de conhecimento QnA Maker e publicá-la na produção usando o comando de Build

É útil entender como o processo de implantação de um QnA Maker KB funciona e, depois de concluir este artigo até este ponto, você deve ter uma melhor compreensão dos processos envolvidos para publicar em seu ponto de extremidade de teste ou produção usando os comandos da CLI do bot Framework.

O uso desses comandos oferece flexibilidade ao personalizar scripts para suas necessidades específicas. Se essa flexibilidade não for necessária, há outro comando da CLI do BF que combina a maioria dos comandos discutidos neste artigo em um único comando que você pode usar para criar ou atualizar, em seguida, treinar e publicar um QnA Maker KB no ponto de extremidade de _produção_ , e esse é o `qnamaker:build` comando.

O `build` comando só publica no ponto de extremidade de produção. Você pode usá-lo separadamente dos comandos anteriores discutidos neste artigo, pois eles são usados principalmente durante o processo de desenvolvimento para publicar no ponto de extremidade de teste, onde os testes podem ocorrer antes da publicação para produção.

O comando QnAMaker Build combina todas as seguintes ações em um único comando:

1. Cria um modelo de QnA Maker para [cada localidade](#qna-and-multiple-language-variations) encontrada usando seus `.qna` arquivos existentes.
1. Cria um novo QnA Maker KB se não houver nenhum; caso contrário, ele substituirá o KB existente.
1. Ele treina seu QnA Maker KB e, em seguida, publica-o no ponto de extremidade de produção.
1. Se você incluir o `dialog` parâmetro opcional, ele produzirá os `.dialog` arquivos de definição que podem ser usados pelo [reconhecedor de QnA Maker][qna-maker-recognizer] ao desenvolver usando a [abordagem declarativa][declarative]. Isso é explicado na seção [arquivo de diálogo](#the-dialog-file) .

### <a name="how-to-use-the-build-command"></a>Como usar o comando de Build

O comando de compilação QnAMaker com seus parâmetros obrigatórios:

``` cli
bf qnamaker:build --in <input-file-or-folder> --subscriptionKey <Subscription-Key> --botName <bot-name>
```

> [!IMPORTANT]
>
> Esse comando substituirá o modelo de QnA Maker anterior, bem como qualquer conteúdo que você possa ter em seu QnA Maker KB, incluindo qualquer conteúdo criado diretamente no [QnA Maker.ai](https://www.qnamaker.ai/).

#### <a name="the-qnamakerbuild-parameters"></a>Os parâmetros qnamaker: Build

- `in`: O diretório, incluindo subdiretórios, que serão pesquisados para arquivos. QnA.
- `out`: O diretório no qual salvar os arquivos de saída. Isso inclui todos os arquivos do Recognizer, bem como o arquivo de configurações e, opcionalmente, os arquivos de diálogo. Se você omitir a `out` opção, nenhum arquivo será salvo em disco e somente as chaves de criação e o ponto de extremidade serão gravados no console do.
- `log`: Um valor booliano que determina se um log é criado durante esse processo.
- `botName`: O nome do bot. Isso será usado para gerar o nome do QnA Maker KB, isso é explicado mais detalhadamente na seção [QnA Maker base de dados de conhecimento criada](#qna-maker-knowledge-bases-created) abaixo.
- `subscriptionKey`: A mesma chave de assinatura que está no seu [arquivo de inicialização](#create-your-qna-maker-initialization-file).

Para obter informações sobre parâmetros adicionais, consulte [BF qnamaker: Build][bf-qnamakerbuild] no Leiame da CLI do BF.

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

### <a name="qna-and-multiple-language-variations"></a>QnA e várias variações de idioma

Cada [arquivo. QnA][qna-file-format] pode ter várias variações de idioma, uma para cada idioma com suporte.

O padrão para o `.qna` nome de arquivo quando as variantes de idioma são usadas é o seguinte:

`<file-name>.<locale>.qna`

Por exemplo:

```
example.en-us.qna
example.fr-fr.qna
example.de-de.qna
etc.
```

No exemplo acima, cada um dos `.qna` arquivos resultará em um QnA Maker KB criado para cada um dos idiomas.

> [!TIP]
>
> Se o idioma não puder ser determinado com base no nome do arquivo, o valor especificado no parâmetro CLI `--defaultCulture` será usado. Se o parâmetro da CLI `--defaultCulture` estiver ausente, o idioma usará como padrão `en-us` .

### <a name="qna-maker-knowledge-bases-created"></a>Bases de dados de conhecimento QnA Maker criadas

Para habilitar o cenário em que várias pessoas estão trabalhando com modelos LUIS e precisam ser capazes de trabalhar com eles independentemente um do outro enquanto todos estão vinculados ao mesmo controle do código-fonte, o `qnamaker:build` comando cria a base de dados de conhecimento QnA Maker usando uma combinação do valor que você provou como o `botName` parâmetro seguido pelo nome de usuário no qual você está conectado, depois a localidade seguida por `.qna` .

O nome do QnA Maker KB:

`<botName>(<user-name>).locale.qna`

Por exemplo, se seu botName for _MyProject_ e seu nome de usuário for _YuuriTanaka_, os nomes dos KBS serão os seguintes:

```
MyProject(YuuriTanaka).en-us.qna
MyProject(YuuriTanaka).fr-fr.qna
MyProject(YuuriTanaka).de-de.qna
```

> [!TIP]
>
> Incluir o nome de usuário como parte do nome do KB permite que vários desenvolvedores trabalhem de forma independente. Esse valor é gerado automaticamente, usando o nome de usuário da pessoa que fez logon; no entanto, você pode substituir isso usando a `--suffix` opção.

### <a name="the-settings-file-generated-using-the-build-command"></a>O arquivo de configurações gerado usando o comando de compilação

A saída de `qnamaker:build` inclui um arquivo de configurações que contém o mapeamento para todas as bases de dados de conhecimento QnA Maker, incluindo a ID do KB e o nome do host para cada.

O arquivo de configurações do QnA Maker KB:

`qnamaker.settings.<app-name>.<website-location>.json`

Por exemplo, o arquivo de configurações para a região de destino do usuário _YuuriTanaka_ _é denominado_ :

**qnamaker.settings.YuuriTanaka.westus.jsem**

Arquivo de configurações de exemplo:

```json
{
    "qna": {
        "RootDialog_en_us_qna": "<knowledge-base-ID-for-en-us-locale>",
        "RootDialog_fr_fr_qna": "<knowledge-base-ID-for-fr-fr-locale>",
        "hostname": "https://<app-name>.azurewebsites.net/qnamaker"
    }
}
```

> [!TIP]
>
> A parte do _site-local_ do nome do arquivo, bem _como a parte do nome do_ arquivo e o valor do nome do host no arquivo, ambos vêm dos valores fornecidos ao [criar o recurso de QnA Maker no Azure](#create-your-qna-maker-resource-in-azure-cognitive-services), na seção _detalhes do serviço de aplicativo-para tempo de execução_ .
>
> ![Nome do aplicativo](./media/adaptive-dialogs/qna-app-name.png)

### <a name="the-dialog-file"></a>O arquivo de diálogo

Quando você usar a `--dialog` opção opcional, um arquivo de caixa de diálogo será gerado para todas as variações de idioma de cada um dos seus `.qna` arquivos.

> [!IMPORTANT]
>
> A `--schema` opção é usada em conjunto com a `--dialog` opção. A inclusão da `--schema` opção garantirá que cada arquivo de caixa de diálogo criado terá uma referência ao arquivo de esquema raiz de projetos. Esse arquivo de esquema contém os esquemas de todos os componentes consumidos pelo bot. Cada consumidor de arquivos declarativos, incluindo [Composer] [Composer], precisa de um arquivo de esquema. Se o seu projeto não tiver um arquivo de esquema, você poderá gerar um usando o `dialog:merge` comando. Será necessário executar esse comando antes de executar o `luis:build` comando. Para obter informações adicionais, consulte o artigo sobre como [usar ativos declarativos em caixas de diálogo adaptáveis][dialog-merge-command].

Os parâmetros de caixa de diálogo qnamaker: Build e Schema:

- **caixa de diálogo**. Há dois valores válidos para a opção de caixa de diálogo `multiLanguage` e `crosstrained` .
- **esquema**. Isso usa um caminho relativo e o nome de arquivo apontando para o arquivo de esquema do bot.

 Esses arquivos serão gravados no diretório especificado na `out` opção. Por exemplo:

```
 ./rootDialog/RootDialog.qna.dialog <-- MultiLanguageRecognizer configured to use all of the languages
 ./rootDialog/RootDialog.en-us.qna.dialog <-- QnARecognizer for en-us locale
 ./rootDialog/RootDialog.fr-fr.qna.dialog <-- QnARecognizer for fr-fr locale
```


Aqui está um exemplo do arquivo _MultiLanguageRecognizer_ :

```json
{
    "$schema": "app.schema",
    "$kind": "Microsoft.MultiLanguageRecognizer",
    "id": "QnA_RootDialog",
    "recognizers": {
        "en-us": "RootDialog.en-us.qna",
        "fr-fr": "RootDialog.fr-fr.qna",
        "": "RootDialog.en-us.qna"
    }
}
```

Você usará esses arquivos se estiver usando a abordagem declarativa para desenvolver o bot, e precisará adicionar uma referência a esse reconhecedor em seu arquivo de caixas de diálogo adaptáveis `.dialog` . No exemplo a seguir, o `"recognizer": "RootDialog.qna"` está procurando o reconhecedor que está definido no arquivo **RootDialog. QnA. Dialog**:

 ![Como fazer referência a um reconhecedor em um arquivo. Dialog](./media/adaptive-dialogs/how-to-reference-the-qna-recognizer-in-dialog-file.png)

Consulte [usando ativos declarativos em caixas de diálogo adaptáveis][declarative] para obter mais informações.

## <a name="additional-information"></a>Informações adicionais

- Como [criar listas personalizadas de sinônimos para sua base de dados de conhecimento QnA Maker](bot-builder-howto-bf-cli-alterations-qna.md)

<!-------------------------------------------------------------------------------------------------->
[qna-overview]: /azure/cognitive-services/qnamaker/overview/overview
[cognitive-services-overview]: /azure/cognitive-services/Welcome
[create-cognitive-services]: https://portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker
[qna-maker-recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md#qna-maker-recognizer
[qna-file-format]: ../file-format/bot-builder-qna-file-format.md

[natural-language-processing-in-adaptive-dialogs]: bot-builder-concept-adaptive-dialog-recognizers.md#introduction-to-natural-language-processing-in-adaptive-dialogs

[bf-cli-overview]: bf-cli-overview.md

[bf-qnamakerconvert]: https://aka.ms/botframework-cli#bf-qnamakerconvert
[bf-qnamakerkbcreate]: https://aka.ms/botframework-cli#bf-qnamakerkbcreate
[bf-qnamakerkbpublish]: https://aka.ms/botframework-cli#bf-qnamakerkbpublish
[bf-qnamakerinit]: https://aka.ms/botframework-cli#bf-qnamakerinit
[bf-qnamakerbuild]: https://aka.ms/botframework-cli#bf-qnamakerbuild
[dialog-merge-command]: bot-builder-concept-adaptive-dialog-declarative.md#the-merge-command

[test-knowledge-base]: /azure/cognitive-services/QnAMaker/how-to/test-knowledge-base
[batch-testing]: /azure/cognitive-services/QnAMaker/quickstarts/batch-testing
[declarative]: bot-builder-concept-adaptive-dialog-declarative.md

[qna-maker-sample]: https://aka.ms/csharp-adaptive-dialog-07-qnamaker-sample
[getting-the-samples]: https://aka.ms/botbuilder-samples-repo#getting-the-samples
<!-------------------------------------------------------------------------------------------------->
