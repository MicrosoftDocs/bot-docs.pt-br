---
title: Formato de arquivo .qna – Serviço de Bot
description: Referência do formato de arquivo .qna
keywords: qna file format, reference, qnamaker
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0a55b15b7d5198d854b364f6515a745644b97ba0
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566476"
---
# <a name="qna-file-format"></a>Formato de arquivo .qna

[!INCLUDE[applies-to](../includes/applies-to.md)]

Os arquivos .qna contêm definições baseadas em texto Markdown para os conceitos do [QnAmaker.ai](http://qnamaker.ai). Este artigo aborda os vários conceitos expressos por meio do formato de arquivo .qna.

<!--
Supported concepts:

- [Comments](#Adding-comments)
- [QnA pairs](#Question-and-Answer-pairs)
- [Filters](#QnAMaker-Filters)
- [QnA Alterations](#QnA-Maker-alterations)
- [File references](#QnA-Maker-pdf-file-ingestion)
- [References](#External-references)
- [Model description](#Model-description)
- [Multi-turn content](#Multi-turn-content)-->

## <a name="adding-comments"></a>Como adicionar comentários

Você pode adicionar comentários ao documento .qna prefixando o comentário com **>** .

```.qna
> This is a comment and will be ignored
```

## <a name="question-and-answer-pairs"></a>Pares de perguntas e respostas

O arquivo .qna (e o analisador) dão suporte a definições de perguntas e respostas.

Este é um exemplo de definição de pergunta e resposta básica.

```.qna
    # ? Question
    [list of question variations]
    ```.qna
    Answer
    ```
```

Este é um exemplo de definições de perguntas e respostas.


```.qna
> # QnA Definitions
### ? who is the ceo?
    ```
    You can change the default message if you use the QnAMakerDialog.
    See [this link](https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle) for details.
    ```


### ? How do I programmatically update my KB?
    ```
    You can use our REST apis to manage your KB.
    \#1. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa
    ```
```

Observe que o identificador de tipo `markdown` para uma `answer` é opcional.

### <a name="multiple-questions"></a>Várias perguntas

Você pode adicionar várias perguntas à mesma resposta simplesmente adicionando variações às perguntas.

```.qna
### ? Who is your ceo?
- get me your ceo info
    ```.qna
        Vishwac
    ```
```

## <a name="qnamaker-filters"></a>Filtros do QnA Maker

Os filtros do QnA Maker são pares chave-valor simples que podem ser usados para restringir os resultados da pesquisa, aumentar as respostas e armazenar o contexto.

Use a notação a seguir para adicionar filtros.

```.qna
***Filters:***
- name = value
- name = value
```

Este é um exemplo de como um filtro poderá ser usado.

```.qna
### ? Where can I get coffee?
- I need coffee

**Filters:**
- location = seattle

    ```.qna
    You can get coffee in our Seattle store at 1 pike place, Seattle, WA
    ```

### ? Where can I get coffee?
- I need coffee

**Filters:**
- location = portland

    ```.qna
    You can get coffee in our Portland store at 52 marine drive, Portland, OR
    ```
```

## <a name="qna-maker-alterations"></a>Alterações do QnA Maker

O QnA Maker dá suporte a [alterações de palavras](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices#use-synonyms) como uma forma de aprimorar a probabilidade de determinada consulta do usuário ser respondida com uma resposta apropriada. Use esse recurso para adicionar sinônimos a palavras-chave que usam outro formato.

Descreva as listas de alterações/sinônimos de palavras em arquivos .qna usando a notação a seguir.
```.qna
$<synonym word>:qna-alteration=
- <list of synonyms>
```

Aqui está um exemplo:
```.qna
$botframework : qna-alterations=
- bot framework
- Microsoft bot framework
```

## <a name="qna-maker-pdf-file-ingestion"></a>Ingestão de arquivos PDF do QnA Maker

O QnA Maker também dá suporte à ingestão de arquivos PDF durante a criação da KB. Você pode adicionar arquivos para ingestão do QnA Maker usando o esquema de referência de URL. Se o tipo de conteúdo do URI não for texto nem HTML, o analisador o adicionará à coleção de arquivos para ingestão do QnA Maker.

```.qna
[SurfaceManual.pdf](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)
```

## <a name="external-references"></a>Referências externas

Há suporte para referências externas no arquivo .qna e uso da sintaxe de link Markdown.

### <a name="reference-another-qna-file"></a>Referenciar outro arquivo .qna

Referência a outro arquivo .qna por meio de `\[link name](\<.qna file name\>)`. As referências podem ser um caminho absoluto ou um caminho relativo do arquivo .qna contido.

### <a name="reference-to-a-folder-containing-qna-files"></a>Referência a uma pasta contendo arquivos .qna

Há suporte para a referência a uma pasta com outros arquivos .qna por meio do seguinte:

- `\[link name](\<.qna file path\>/*)` procura os arquivos .qna no caminho absoluto ou relativo especificado.
- `\[link name](\<.qna file path\>/**)` procura recursivamente os arquivos .qna no caminho absoluto ou relativo especificado, incluindo as subpastas.

### <a name="reference-a-url"></a>Referenciar uma URL

Referencie uma URL para ingestão do QnA Maker durante a criação da KB por meio de `\[link name](\<URL\>)`.

### <a name="reference-from-a-specific-file"></a>Referência de um arquivo específico

Adicione também referências aos enunciados definidos em um arquivo específico em uma seção de Intenção ou como pares de QnA.

- `[link name](<.lu file path>#<INTENT-NAME>)` localiza todos os enunciados encontrados em <NOME-DA-INTENÇÃO> no arquivo .lu e os adiciona à lista de perguntas em que a referência é especificada.
- `[link name](\<.lu file path>#*utterances*)` localiza todos os enunciados no arquivo .lu e os adiciona à lista de perguntas em que a referência é especificada.
- `\[link name](\<.qna file path\>#?)` localiza as perguntas de todos os pares de P e R definidos no arquivo .qna e os adiciona à lista de enunciados em que essa referência é especificada.
- `\[link name](\<.qna folder\>/*#?)` localiza todas as perguntas de todos os arquivos .qna na pasta especificada e as adiciona à lista de enunciados em que essa referência é especificada.

Este é um exemplo dessas referências.

```.qna
> QnA URL reference
[QnaURL](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs)

> Include all content in ./kb1.qna
[KB1](./kb1.qna)

> Look for all .qna files under a path
[ChitChat](./chitchat/*)

> Recursively look for .qna files under a path including sub-folders.
[Chit chat](../chitchat/resources/**)
```

## <a name="model-description"></a>Descrição do modelo

Inclua informações de configuração do aplicativo LUIS ou da KB do QnA Maker no arquivo .qna para ajudar a instruir o analisador a processar o conteúdo do LU corretamente.

Veja a seguir como adicionar informações de configuração.

```.qna
> !# @\<property> = \<value>
> !# @\<scope>-\<property> = \<value>
> !# @\<scope>-\<property> = \<semicolon-delimited-key-value-pairs>
```

Observe que todas as informações passadas explicitamente por meio de argumentos da CLI substituirão as informações do arquivo .qna.

```.qna
> Parser instruction - this is optional; unless specified, parser will default to the latest version.
> !# @version = 1.0

> QnA Maker KB description
> !# @kb.name = my qna maker kb name

> Source for a specific QnA pair
> !# @qna.pair.source = <source value>
```

## <a name="multiturn-content"></a>Conteúdo de vários turnos

O conteúdo de vários turnos é representado no formato .qna por meio da notação de link Markdown. Os links são especificados com a notação a seguir.

```
- [display text](#<ID or question>)
```

Opcionalmente, você pode incluir `context-only` para as solicitações que só estão contextualmente disponíveis para essa pergunta. Leia a seção sobre [como adicionar um par de perguntas e respostas existente como uma solicitação de acompanhamento][1] para saber mais sobre o uso de `context`.

```.qna
- [tell me a joke](#?joke) `context-only`
```

### <a name="follow-up-prompts"></a>Solicitações de acompanhamento

Os desenvolvedores têm duas opções para criar solicitações de acompanhamento: usando uma pergunta como uma solicitação de acompanhamento diretamente ou atribuindo uma ID explícita a um par de P e R.

### <a name="use-a-question-directly"></a>Usar uma pergunta diretamente

1. O primeiro par de P e R que tenha o texto de link como uma `question` será adicionado como a solicitação. Se você precisar de um controle mais explícito, use [IDs](#question-and-answer-pairs).
2. Quando estiver usando uma pergunta diretamente, use a convenção Markdown e substitua espaços por hifens (por exemplo, use `#?when-is-the-portland-store-open` em vez de `#?when is the portland store open`). O analisador fará o melhor para localizar o link.

```.qna
# ?store hours
    ```
        Most our stores are open M-F 9AM-10PM
    ```
**Prompts:**
- [Seattle store](#?seattle)
- [Portland store](#?when-is-the-portland-store-open)

# ?seattle
    ```
        Seattle store is open M-F 9AM-10PM
    ```

# ?when is the portland store open
- portland store hours
    ```
        Portland store is open 24x7.
    ```
```

Observe que o link não será realmente renderizado como um link clicável na maioria dos renderizadores Markdown.

### <a name="assign-an-explicit-id-to-a-qna-pair"></a>Atribuir uma ID explícita a um par de P e R

Atribua IDs a cada solicitação com um número. Veja no exemplo abaixo que um valor numérico diferente foi atribuído à solicitação de cada loja.

```.qna
# ?store hours
    ```
        Most our stores are open M-F 9AM-10PM
    ```
**Prompts:**
- [Seattle store](#1)
- [Portland store](#2)

<a id = "1"></a>

# ?seattle
    ```
        Seattle store is open M-F 9AM-10PM
    ```

<a id = "2"></a>

# ?when is the portland store open
- portland store hours
    ```
        Portland store is open 24x7.
    ```
```

## <a name="additional-resources"></a>Recursos adicionais
- Confira [Formato de arquivo .lu](bot-builder-lu-file-format.md) para obter informações sobre o formato de arquivo .lu.

[1]:https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/multiturn-conversation#add-a-new-question-and-answer-pair-as-a-follow-up-prompt
