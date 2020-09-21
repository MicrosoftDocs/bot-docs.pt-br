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
ms.openlocfilehash: 7189da26d5a0d9e4a2d54d4ebf404e2cbcde31da
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824930"
---
# <a name="deploy-luis-resources-using-the-bot-framework-luis-cli-commands"></a>Implantar recursos do LUIS usando os comandos da CLI do bot Framework LUIS

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

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
1. [Instalar a CLI do SDK do bot Framework](#install-the-bot-framework-sdk-cli)
1. [Criar seu modelo LUIS](#create-your-luis-model)
1. [Criar seu aplicativo LUIS](#create-your-luis-application)
1. [Obter a appId do seu aplicativo LUIS](#get-the-appid-from-your-luis-app)
1. [Treine seu aplicativo LUIS](#train-your-luis-application)
1. [Publicar seu aplicativo LUIS](#publish-your-luis-application)
1. [Gerar código-fonte](#generate-source-code)

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
- **Localização**.   Esta é a região do Azure que contém o recurso de criação do LUIS. Você seleciona essa seleção ao criar o recurso de criação de LUIS.

   ![A folha chaves e ponto de extremidade no Azure](./media/adaptive-dialogs/keys-and-endpoint-example.png)

> [!TIP]
>
> Há dois tipos de chaves de assinatura associadas a um recurso LUIS, dependendo do tipo de recurso LUIS ao qual você está se referindo. Um recurso de criação de LUIS tem uma chave de criação e um recurso de previsão LUIS tem uma chave de previsão, ambas são chaves de assinatura e ambas podem ser encontradas na folha _chaves e ponto de extremidade_ .

Para obter mais informações, consulte [criar recursos de Luis][luis-how-to-azure-subscription].

## <a name="install-the-bot-framework-sdk-cli"></a>Instalar a CLI do SDK do bot Framework

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

![Chaves e ponto de extremidade](./media/adaptive-dialogs/keys-and-endpoint-myluisapp.png)

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

## <a name="additional-information"></a>Informações adicionais

- [Atualizando seus modelos do LUIS][how-to-update-using-luis-cli]

<!----------------------------------------------------------------------------------------------------------------------------->
[cognitive-services-overview]: /azure/cognitive-services/Welcome
[create-cognitive-services]: https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne
[luis-recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md#luis-recognizer
[natural-language-processing-in-adaptive-dialogs]: bot-builder-concept-adaptive-dialog-recognizers.md#introduction-to-natural-language-processing-in-adaptive-dialogs
[language-understanding]: bot-builder-concept-adaptive-dialog-recognizers.md#language-understanding
[lu-templates]: ../file-format/bot-builder-lu-file-format.md
[luis-how-to-azure-subscription]: /azure/cognitive-services/luis/luis-how-to-azure-subscription
[bf-cli-overview]: bf-cli-overview.md

[bf-luisapplicationimport]: https://aka.ms/botframework-cli-luis#bf-luisapplicationimport
[bf-luisapplicationcreate]: https://aka.ms/botframework-cli-luis#bf-luisapplicationcreate
[bf-luisapplicationshow]: https://aka.ms/botframework-cli-luis#bf-luisapplicationshow
[bf-luistrainrun]: https://aka.ms/botframework-cli-luis#bf-luistrainrun
[luisapplicationpublish]: https://aka.ms/botframework-cli-luis#bf-luisapplicationpublish
[bf-luisgeneratecs]: https://aka.ms/botframework-cli-luis#bf-luisgeneratecs
[bf-luisgeneratets]: https://aka.ms/botframework-cli-luis#bf-luisgeneratets

[luis-how-to-add-intents]: /azure/cognitive-services/LUIS/luis-how-to-add-intents
[luis-how-to-start-new-app]: /azure/cognitive-services/LUIS/luis-how-to-start-new-app
[luis-how-to-train]: /azure/cognitive-services/LUIS/luis-how-to-train
[luis-concept-test]: /azure/cognitive-services/LUIS/luis-concept-test
[test-an-utterance]: /azure/cognitive-services/LUIS/luis-interactive-test#test-an-utterance
[luis-interactive-test]: /azure/cognitive-services/LUIS/luis-interactive-test
[luis-how-to-publish-app]: /azure/cognitive-services/LUIS/luis-how-to-publish-app

[how-to-update-using-luis-cli]: bot-builder-howto-bf-cli-update-luis.md

<!----------------------------------------------------------------------------------------------------------------------------->
