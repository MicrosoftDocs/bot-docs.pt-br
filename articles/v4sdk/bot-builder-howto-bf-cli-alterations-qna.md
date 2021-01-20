---
title: Criar sinônimos para seu QnA Maker KB usando o serviço de bot de alterações
description: 'Crie listas personalizadas de sinônimos para sua base de dados de conhecimento QnA Maker usando o comando qnamaker: alteráte da CLI do bot Framework.'
keywords: 'QnA Maker, base de dados de conhecimento, KB, CLI BF, qnamaker, sinônimos, alterações, qnamaker: alterações, bot, caixas de diálogo adaptáveis'
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: how-to
ms.service: bot-service
ms.date: 09/12/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ea6db290707649e348a7cfd4d0ab63ae5978aff0
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577388"
---
# <a name="create-customized-lists-of-synonyms-for-your-qna-maker-knowledge-base"></a>Criar listas personalizadas de sinônimos para sua base de dados de conhecimento QnA Maker

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

A CLI do bot Framework permite automatizar o gerenciamento de QnA Maker base de dados de conhecimento. Ele permite criar, atualizar e excluir uma base de dados de conhecimento QnA Maker (KB) da linha de comando ou de um script. Ele também permite que você crie uma lista de sinônimos que se aplicam ao seu KB. Este artigo explica como criar sinônimos para seu QnA Maker KB usando o comando de _alterações_ da CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Um QnA Maker KB existente. Se você ainda não tiver um QnA Maker KB, poderá criar um usando as etapas descritas em [implantar QnA Maker base de dados de conhecimento usando o artigo comandos da CLI do bot Framework qnamaker][deploy-qna-maker-knowledge-base-using-bf-cli-qnamaker] .
- QnA Maker arquivo de inicialização. Se você ainda não tiver um arquivo de inicialização QnA Maker, poderá criar um usando as etapas descritas em [criar seu arquivo de inicialização de QnA Maker][qnamaker-init-file] na [base de dados de conhecimento implantar QnA Maker usando o artigo de comandos da CLI do bot Framework qnamaker][deploy-qna-maker-knowledge-base-using-bf-cli-qnamaker] .

## <a name="an-introduction-to-alterations-in-qna-maker"></a>Uma introdução às alterações no QnA Maker

O comando de alterações permite que você importe listas personalizadas de sinônimos para seu QnA Maker KB. As alterações são uma lista de palavras que têm o mesmo significado. Por exemplo, um sinônimo para a palavra "Gift" poderia ser a palavra "presente".

As alterações também podem ser muito úteis para abreviações. Por exemplo, "GDPR" é um termo amplamente usado, mas algumas pessoas podem chamá-lo de "AVG", que é a abreviação holandesa. As empresas geralmente têm sua própria lista exclusiva de abreviações que se referem a diferentes recursos ou componentes oferecidos por seus produtos.

Embora QnA Maker já tenha sua própria lista interna de sinônimos pretreinados comuns em várias linguagens, muitas empresas ainda podem se beneficiar da existência de sinônimos adicionais.

As alterações também podem ajudar a melhorar a qualidade da sua base de conhecimento, reduzindo o número de pares de perguntas/respostas, bem como o tempo necessário para treiná-lo.

> [!TIP]
>
> Não é possível criar listas personalizadas de sinônimos usando o portal QnA Maker, no entanto, ele está disponível usando a CLI do bot Framework.

## <a name="install-the-bot-framework-sdk-cli"></a>Instalar a CLI do SDK do bot Framework

Se você já tiver instalado a CLI do bot Framework, poderá pular para [usar os comandos da CLI do qnamaker para criar uma lista de sinônimos para sua base de dados de conhecimento QnA Maker](#use-the-qnamaker-cli-commands-to-create-a-list-of-synonyms-for-your-qna-maker-knowledge-base).

[!INCLUDE [applies-to-v4](../includes/install-bf-cli.md)]

## <a name="use-the-qnamaker-cli-commands-to-create-a-list-of-synonyms-for-your-qna-maker-knowledge-base"></a>Use os comandos da CLI do qnamaker para criar uma lista de sinônimos para sua base de dados de conhecimento QnA Maker

Quando você [cria seu modelo de QnA Maker][create-your-qna-maker-model] dois arquivos JSON são criados: o _modelo QnAMaker_, que é nomeado **converted.jsem** e o arquivo de _alterações_ , que é chamado de **alterations_converted.jsem**. Embora o modelo QnAMaker contenha os dados de todos os arquivos. QnA em seu projeto, todos combinados para formar um único arquivo, o arquivo de alterações contém apenas uma lista de alterações vazia, como mostrado abaixo:

```json
{
  "wordAlterations": []
}
```

Você não precisa criar seu modelo de QnA Maker ou KB usando comandos da CLI para usar os comandos de alteração para criar sua lista de sinônimos, mas fazer isso cria o arquivo de alterações do Word.

O arquivo de alterações é um arquivo JSON que contém uma matriz de _wordAlterations_ que consiste em uma matriz de _alterações_, que é uma lista de sinônimos, por exemplo:

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "qnamaker",
        "qna maker"
      ]
    },
    {
      "alterations": [
        "botframework",
        "bot framework"
      ]
    },
    {
      "alterations": [
        "bot framework command line interface",
        "bot framework cli",
        "bf cli"
      ]
    }
  ]
}
```

Depois que o arquivo de alterações for criado, você poderá passá-lo para o `qnamaker:alterations:replace` comando como a `input` propriedade para substituir a lista de alterações vazia criada por padrão ao criar o QnA Maker KB. Você usará o mesmo comando sempre que precisar atualizar a lista existente.

``` cli
bf qnamaker:alterations:replace -i <input-file-name>
```

Se você não tiver um [arquivo de inicialização][qnamaker-init-file], será necessário incluir a chave de assinatura:

```cli
bf qnamaker:alterations:replace -i <input-file-name> --subscriptionKey <Subscription-Key>
```

> [!IMPORTANT]
>
> Você não pode adicionar ou remover itens incrementalmente da lista de alterações no Azure. Quando você executa o comando de substituição de alterações, a lista de alterações no Azure é excluída e substituída pelo arquivo passado.

Para obter informações adicionais sobre como usar esse comando, consulte [`bf qnamaker:alterations:replace`][bf-qnamakeralterationsreplace] na CLI do BF QnA Maker Leiame.

## <a name="download-the-list-of-alterations-in-your-qna-maker-knowledge-base"></a>Baixar a lista de alterações em sua base de dados de conhecimento QnA Maker

Se você precisar ver quais sinônimos estão em seu QnA Maker KB, poderá usar o `qnamaker:alterations:list` comando.

``` cli
bf qnamaker:alterations:list
```

Se você não tiver um [arquivo de inicialização][qnamaker-init-file], será necessário incluir a chave de assinatura:

``` cli
 `bf qnamaker:alterations:list --subscriptionKey <Subscription-Key>`
```

Para obter informações adicionais sobre como usar esse comando, consulte [`bf qnamaker:alterations:list`][bf-qnamakeralterationslist] na CLI do BF QnA Maker Leiame.

## <a name="update-the-list-of-alterations-in-your-qna-maker-knowledge-base"></a>Atualizar a lista de alterações em sua base de dados de conhecimento QnA Maker

Embora não haja nenhum comando para atualizar diretamente uma lista de alterações existente no QnA Maker, você pode [usar o comando de _lista de alterações_](#download-the-list-of-alterations-in-your-qna-maker-knowledge-base) para baixar a lista de alterações, fazer as modificações necessárias e, em seguida, usar essa nova lista substituir sua lista de alterações no Azure.

1. Obter a lista atual de alterações usando o comando `bf qnamaker:alterations:list`

    > [!TIP]
    > Você pode enviar os resultados diretamente para um arquivo usando um comando de tubulação, como o `>` comando dos. O exemplo a seguir criará um arquivo chamado **alterations_converted.jsno** diretório atual:
    >
    > `bf qnamaker:alterations:list >alterations_converted.json`

1. Faça as atualizações desejadas no arquivo JSON e salve essas alterações.
1. Substitua a lista de alterações que está no seu QnA Maker KB usando o comando: `bf qnamaker:alterations:replace -i <input-file-name>` . Se você salvou o arquivo JSON da lista de alterações como **alterations_converted.jsno** diretório atual, o comando será: `bf qnamaker:alterations:replace -i alterations_converted.json`

<!-------------------------------------------------------------------------------------------------->
[deploy-qna-maker-knowledge-base-using-bf-cli-qnamaker]: bot-builder-howto-bf-cli-deploy-qna.md
[create-your-qna-maker-model]: bot-builder-howto-bf-cli-deploy-qna.md#create-your-qna-maker-model
[qnamaker-init-file]: bot-builder-howto-bf-cli-deploy-qna.md#create-your-qna-maker-initialization-file

[bf-qnamakeralterationsreplace]: https://aka.ms/botframework-cli#bf-qnamakeralterationsreplace
[bf-qnamakeralterationslist]: https://aka.ms/botframework-cli#bf-qnamakeralterationslist

[bf-qnamakerconvert]: https://aka.ms/botframework-cli#bf-qnamakerconvert
<!-------------------------------------------------------------------------------------------------->
