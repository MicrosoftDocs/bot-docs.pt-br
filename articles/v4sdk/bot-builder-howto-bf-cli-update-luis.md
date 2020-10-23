---
title: Atualizar recursos do LUIS usando o bot Framework LUIS comandos da CLI-serviço bot
description: Descrevendo como automatizar o processo de atualização de um aplicativo LUIS existente usando os comandos da CLI do LUIS SDK do bot Framework
keywords: LUIS, bot, entradas, caixas de diálogo adaptáveis, aplicativos LUIS, modelos de LUIS,
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: how-to
ms.service: bot-service
ms.date: 08/31/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4aa50d3621ccbb8b36ac8eb0507c456985e4d35d
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417234"
---
# <a name="update-luis-resources-using-the-bot-framework-luis-cli-commands"></a>Atualizar os recursos do LUIS usando os comandos da CLI do LUIS do Bot Framework

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

A interface de linha de comando do bot Framework (BF CLI) permite automatizar o gerenciamento de recursos do LUIS. Em uma linha de comando ou um script, você pode criar, atualizar e excluir Propriedades LUIS.

Este artigo explica como atualizar um recurso LUIS existente. Para obter informações sobre como começar e como implantar seus recursos do LUIS usando a CLI do BF, consulte como [implantar recursos do Luis usando os comandos da CLI do bot Framework Luis][how-to-deploy-using-luis-cli].

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [modelos de Lu][lu-templates].
- Ter um projeto de bot com `.lu` arquivos.
- Se estiver trabalhando com caixas de diálogo adaptáveis, você deve compreender:
  - [Processamento de idioma natural em caixas de diálogo adaptáveis][natural-language-processing-in-adaptive-dialogs].
  - [Reconhecimento de idioma em caixas de diálogo adaptáveis][language-understanding].
  - como o [reconhecedor Luis][luis-recognizer] é usado.

## <a name="using-the-luis-cli-commands-to-update-the-luis-resources-used-in-your-bot"></a>Usando os comandos da CLI do LUIS para atualizar os recursos do LUIS usados em seu bot

Este artigo descreve as etapas a seguir usadas para atualizar seus recursos de criação de LUIS existentes no Azure usando a CLI do bot Framework. Estas instruções explicam como usar as versões do aplicativo LUIS para fazer backup da versão do aplicativo active LUIS antes de criar uma nova versão ativa.

1. [Instalar a CLI do bot Framework](#install-the-bot-framework-cli)
1. [Obter configurações de seu aplicativo LUIS](#get-settings-from-your-luis-app)
1. [Criar seu modelo LUIS](#create-your-luis-model)
1. [Excluir versão de backup, se existir](#delete-backup-version)
1. [Salvar versão atual como backup](#save-current-version-as-backup)
1. [Importar nova versão do modelo LUIS](#import-new-version-of-luis-model)
1. [Treine seu aplicativo LUIS](#train-your-luis-application)
1. [Publicar seu aplicativo LUIS](#publish-your-luis-application)
1. [Gerar código-fonte](#generate-source-code)

## <a name="install-the-bot-framework-cli"></a>Instalar a CLI do bot Framework

[!INCLUDE [applies-to-v4](../includes/install-bf-cli.md)]

## <a name="get-settings-from-your-luis-app"></a>Obter configurações de seu aplicativo LUIS

Você precisará da _ID do aplicativo Luis_ e da _ID da versão ativa_ para concluir o processo de atualização dos recursos do Luis. Há duas maneiras pelas quais você pode obter essas informações. Esta seção explicará e quando você usará cada uma delas. Independentemente da abordagem usada, você precisará ter a chave de assinatura e o ponto de extremidade com o qual seu aplicativo LUIS está associado. Você pode encontrar essas informações em seu _recurso de criação do Luis_  no Azure, na folha **chaves e ponto de extremidade** .

![Chaves e Ponto de Extremidade](./media/adaptive-dialogs/keys-and-endpoint-myluisapp.png)

Se você souber qual é a ID do aplicativo LUIS, mas precisar obter a ID da versão ativa, poderá usar o `luis:application:show` comando. Isso retornará apenas informações para o aplicativo LUIS especificado.

``` cli
bf luis:application:show --appId <application-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Application: show][bf-luisapplicationshow] na CLI do BF Luis Leiame.

Se você não souber a ID do aplicativo LUIS, poderá usar o comando [Luis: Application: list][bf-luisapplicationlist] para obtê-lo, juntamente com a ID da versão ativa. Esse comando listará todos os aplicativos LUIS que foram criados no recurso de criação de LUIS especificado. A ID do aplicativo LUIS é retornada como `id` e a ID da versão ativa é retornada como `activeVersion` .

``` cli
bf luis:application:list --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Application: list][bf-luisapplicationlist] na CLI do BF Luis Leiame.

## <a name="create-your-luis-model"></a>Criar seu modelo LUIS

Sempre que você fizer atualizações em qualquer um dos `.lu` arquivos individuais usados em seu projeto, será necessário criar um novo modelo Luis usando o `luis:convert` comando. Você usará esse novo modelo para atualizar seu aplicativo LUIS que está hospedado no Azure. Isso permitirá que essas alterações entrem em vigor no bot.

``` cli
bf luis:convert -i <input-folder-name> -o <output-file-name> -r --name <name>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Convert][bf-luisconvert] in the BF CLI Luis README.

<!--
In the example below, the command is run in a command line while in the root directory of your project. It will search for all `.lu` files in the _dialogs_ directory and because of the `-r` option, all of its sub-directories. It will save a file named LUISModel.json in the _output_ directory.

``` cli
bf luis:convert -i dialogs -o .\output\LUISModel.json -r --name LUISModel.json
```
-->

> [!TIP]
>
> A `name` opção não é necessária, no entanto, se você não incluir essa opção, será necessário atualizar manualmente seu JSON do modelo Luis antes de importá-lo ou receberá um erro: `Failed to import app version: Error: Application name cannot be null or empty.`

## <a name="delete-backup-version"></a>Excluir versão de backup

Antes de criar uma nova versão do modelo LUIS, você pode criar um backup da versão ativa. Na próxima vez que você criar uma nova atualização do modelo LUIS, talvez queira excluir o backup antigo antes de criar o novo backup. Use o comando `luis:version:delete` para fazer isso.

``` cli
bf luis:version:delete --appId <application-id> --versionId <version-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Version: delete][bf-luisversiondelete] na CLI do BF Luis Leiame.

> [!IMPORTANT]
>
> Tenha cuidado para não confundir o `luis:application:delete` comando com o `luis:version:delete` comando. O `luis:application:delete` comando excluirá permanentemente o aplicativo Luis, juntamente com todas as versões de todos os modelos do Luis associados a ele. O `luis:version:delete` comando excluirá apenas a versão especificada. Esse comando excluirá uma versão, sem aviso, mesmo que ela seja a única versão do modelo.

## <a name="save-current-version-as-backup"></a>Salvar versão atual como backup

Antes de importar a nova versão do modelo LUIS, você pode fazer backup de sua versão ativa. Isso pode ser feito com o comando `luis:version:rename`. Você precisará da `versionId` versão ativa que obteve na seção anterior [obter configurações de seu aplicativo Luis](#get-settings-from-your-luis-app)e pode definir o `newVersionId` valor como "backup" para especificar que é a sua versão de backup.

``` cli
bf luis:version:rename --appId <application-id> --versionId <version-id> --newVersionId <new-version-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Version: Rename][bf-luisversionrename] in the BF CLI Luis README.

> [!TIP]
>
> Você não está limitado a caracteres numéricos para uma VersionId. Se você nomear sua versão de backup "backup", ele simplificará o processo de exclusão da sua versão de backup.

## <a name="import-new-version-of-luis-model"></a>Importar nova versão do modelo LUIS

Agora você está pronto para importar a nova versão do modelo que você criou na seção [criar seu modelo do Luis](#create-your-luis-model) deste artigo. Você faz isso usando o `luis:version:import` .

Para atualizar seu aplicativo LUIS:

``` cli
luis:version:import --in <luis-model-json-file> --endpoint <endpoint> --subscriptionKey <subscription-key> --appId <app-id> --versionId <version-id>
```

Para obter mais detalhes e ver todas as opções disponíveis para esse comando, consulte a seção [BF Luis: Application: Import][bf-luisapplicationimport] do Leiame da CLI do Luis.

## <a name="train-your-luis-application"></a>Treine seu aplicativo LUIS

O treinamento é o processo de ensinar seu aplicativo LUIS a melhorar seu conhecimento em linguagem natural. Você precisa treinar seu aplicativo LUIS depois de ter feito todas as atualizações para o modelo. Para obter informações adicionais, consulte o artigo [treinar a versão ativa do aplicativo Luis][luis-how-to-train] no documentos do Luis.

Para treinar seu aplicativo LUIS, use o `luis:train:run` comando:

```cli
bf luis:train:run --appId <application-id> --versionId <version-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Train: execute][bf-luistrainrun] na CLI do BF Luis Leiame.

> [!TIP]
>
> Depois de treinar seu aplicativo LUIS, você deve [testá][luis-concept-test] -lo com o declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Se não estiverem, faça atualizações no aplicativo LUIS, importe, treine e teste novamente. Esse teste pode ser feito manualmente no site do LUIS, para obter mais informações, consulte o artigo [testar um expressão][test-an-utterance].

## <a name="publish-your-luis-application"></a>Publicar seu aplicativo LUIS

Quando você terminar de criar, treinar e testar seu aplicativo LUIS ativo, disponibilize-o para o aplicativo cliente publicando-o no ponto de extremidade. Você pode fazer isso usando o `luis:application:publish` comando.

```cli
bf luis:application:publish --appId <application-id> --versionId <version-id> --endpoint <endpoint> --subscriptionKey <subscription-key>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Application: publish][luisapplicationpublish] na CLI do BF Luis Leiame.

Para obter informações sobre como publicar um aplicativo LUIS, consulte [publicar seu aplicativo ativo e treinado em um ponto de extremidade de preparo ou de produção][luis-how-to-publish-app].

## <a name="generate-source-code"></a>Gerar código-fonte

[!INCLUDE [applies-to-v4](../includes/generate-source-code-luis-cli.md)]

<!-------------------------------------------------------------------------------------------------->
[luis-recognizer]: bot-builder-concept-adaptive-dialog-recognizers.md#luis-recognizer
[natural-language-processing-in-adaptive-dialogs]: bot-builder-concept-adaptive-dialog-recognizers.md#introduction-to-natural-language-processing-in-adaptive-dialogs
[language-understanding]: bot-builder-concept-adaptive-dialog-recognizers.md#language-understanding
[lu-templates]: ../file-format/bot-builder-lu-file-format.md
[luis-how-to-azure-subscription]: /azure/cognitive-services/luis/luis-how-to-azure-subscription

[bf-luisapplicationimport]: https://aka.ms/botframework-cli-luis#bf-luisapplicationimport
[bf-luisapplicationcreate]: https://aka.ms/botframework-cli-luis#bf-luisapplicationcreate
[bf-luisapplicationlist]: https://aka.ms/botframework-cli-luis#bf-luisapplicationlist
[bf-luisapplicationshow]: https://aka.ms/botframework-cli-luis#bf-luisapplicationshow
[bf-luistrainrun]: https://aka.ms/botframework-cli-luis#bf-luistrainrun
[luisapplicationpublish]: https://aka.ms/botframework-cli-luis#bf-luisapplicationpublish
[bf-luisgeneratecs]: https://aka.ms/botframework-cli-luis#bf-luisgeneratecs
[bf-luisgeneratets]: https://aka.ms/botframework-cli-luis#bf-luisgeneratets
[bf-luisversionrename]: https://aka.ms/botframework-cli-luis#bf-luisversionrename
[bf-luisversiondelete]:  https://aka.ms/botframework-cli-luis#bf-luisversiondelete
[bf-luisconvert]: https://aka.ms/botframework-cli-luis#bf-luisconvert

[luis-how-to-add-intents]: /azure/cognitive-services/LUIS/luis-how-to-add-intents
[luis-how-to-start-new-app]: /azure/cognitive-services/LUIS/luis-how-to-start-new-app
[luis-how-to-train]: /azure/cognitive-services/LUIS/luis-how-to-train
[luis-concept-test]: /azure/cognitive-services/LUIS/luis-concept-test
[test-an-utterance]: /azure/cognitive-services/LUIS/luis-interactive-test#test-an-utterance
[luis-interactive-test]: /azure/cognitive-services/LUIS/luis-interactive-test
[luis-how-to-publish-app]: /azure/cognitive-services/LUIS/luis-how-to-publish-app

[how-to-deploy-using-luis-cli]: bot-builder-howto-bf-cli-deploy-luis.md
<!-------------------------------------------------------------------------------------------------->
