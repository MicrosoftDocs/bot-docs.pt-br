---
title: Reconhecedores em diálogos adaptáveis
description: Como fornecer o LU (Reconhecimento vocal) para os diálogos adaptáveis usando reconhecedores
keywords: bot, recognizers, adaptive dialogs, Language Understanding, LU
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 05/06/2020
ms.openlocfilehash: b6b575a6855d4cbeb77bc1f3e3c99ed5cbc4aac1
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577438"
---
# <a name="recognizers-in-adaptive-dialogs"></a>Reconhecedores em diálogos adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os _reconhecedores_ fornecem LU (Reconhecimento vocal) para seus diálogos adaptáveis.

Os reconhecedores fornecem a capacidade de entender a entrada do usuário. O diálogo adaptável pode ter um ou mais reconhecedores configurados para transformar a entrada do usuário em constructos que o bot pode processar. Por exemplo, [Reconhecimento vocal](#luis-recognizer) e [perguntas e respostas](#qna-maker-recognizer).

## <a name="prerequisites"></a>Pré-requisitos

* É útil ter uma compreensão geral de [como funcionam os bots][1] no SDK do Bot Framework V4.
* É útil ter uma compreensão geral dos diálogos adaptáveis no SDK do Bot Framework V4. Para obter mais informações, confira a [Introdução aos diálogos adaptáveis][2] e a [Biblioteca de diálogos][3].

## <a name="introduction-to-natural-language-processing-in-adaptive-dialogs"></a>Introdução ao Processamento de Idioma Natural em diálogos adaptáveis

Os reconhecedores, juntamente com os [geradores][4] constituem as funcionalidades de NLP (processamento de idioma natural) em diálogos adaptáveis. NLP é um processo tecnológico que permite que aplicativos de computador, como bots, obtenham o significado de uma entrada do usuário. Para fazer isso, ele tenta identificar informações úteis contidas em conversas interpretando as necessidades dos usuários (intenções) e extrair informações úteis (entidades) de uma frase e responder a ela novamente em uma linguagem que o usuário entenderá.

As funcionalidades de um bot serão muito mínimas sem o NLP; é ele que permite que o bot compreenda as mensagens enviadas pelos usuários e responda a elas de acordo. Quando um usuário envia uma mensagem com "Olá", trata-se dos recursos de processamento de linguagem natural do bot que permite saber que o usuário postou uma saudação padrão que, por sua vez, permite que o bot utilize seus recursos de ia para criar uma resposta adequada. Nesse caso, o bot pode responder com uma saudação.

Sem o NLP, o bot não pode diferenciar de maneira significativa entre quando um usuário insere "Olá" ou "adeus" ou qualquer outra coisa. Para um bot sem NLP, "Olá" e "adeus" não será diferente de qualquer outra cadeia de caracteres agrupada em ordem aleatória. O NLP ajuda a fornecer contexto e significado às entradas do usuário baseadas em texto ou voz, de modo que o bot possa oferecer a melhor resposta.

Um dos desafios mais significativos quando se trata de NLP no bot é o fato de os usuários não fazerem ideia do que dizer ao bot. Embora você possa tentar prever o que os usuários não vão dizer, haverá conversas inesperadas. A boa notícia é que o SDK do Bot Framework fornece as ferramentas necessárias para refinar continuamente as funcionalidades de NLP dos bots.

Os dois componentes principais do NLP em diálogos adaptáveis são os **reconhecedores** (Reconhecimento vocal), que processa e interpreta a _entrada do usuário_ foco deste artigo, e os [**geradores**][4] (geração de linguagem) que produz as _respostas do bot_, que é o processo de produzir frases significativas na forma de idioma natural. Em síntese, é quando o bot responde a um usuário com uma linguagem legível.

> [!TIP]
> Embora seja comum que os usuários se comuniquem com o bot digitando ou falando uma mensagem, o reconhecedor é o subsistema que você pode usar para processar qualquer forma de entrada do usuário, seja falada, digitada, clicada (como ao responder aos [cartões adaptáveis][15]) e, até mesmo, outras modalidades como um reconhecedor de geolocalização ou de foco pode ser usado. A camada do reconhecedor abstrai as complexidades do processamento de entrada do usuário de gatilhos e ações. Dessa forma, os gatilhos e as ações não precisam interpretar os vários tipos de entradas do usuário, mas permitem que os reconhecedores façam isso.

## <a name="language-understanding"></a>Reconhecimento Vocal

O LU **(Reconhecimento vocal)** é o subconjunto do NLP que lida com o modo como o bot processa as entradas do usuário e as converte em algo que ele pode entender e responder de maneira inteligente. Esse processo é obtido por meio da configuração de reconhecedores e do fornecimento de dados de treinamento para o diálogo, de maneira que as **intenções** e as **entidades** contidas na mensagem dos usuários possam ser capturadas. Quando o reconhecedor identifica uma intenção, ele emite um evento `IntentRecognized` contendo essa intenção e o gatilho OnIntent que você definiu para essa intenção será executado e realizará as ações contidas nesse gatilho.

## <a name="core-lu-concepts-in-adaptive-dialogs"></a>Conceitos básicos do LU em diálogos adaptáveis

### <a name="intents"></a>Intenções

As intenções são como você categoriza as intenções esperadas do usuário, conforme expressas nas mensagens dele para o bot. Considere uma intenção como uma representação da ação que o usuário deseja realizar, a finalidade ou a meta expressa na entrada. Tarefas como reservar um voo, pagar uma fatura ou encontrar um artigo de notícias. Você define e nomeia as intenções que correspondem a essas ações. Por exemplo, qualquer bot pode definir uma intenção chamada _Saudação_; um aplicativo de viagem pode criar uma intenção chamada _BookFlight_. As intenções são definidas em um arquivo de modelo de Reconhecimento vocal (.lu); esses arquivos são arquivos de texto com uma extensão .lu e, geralmente, residem no mesmo diretório e têm o mesmo nome do diálogo.  Por exemplo, o diálogo raiz conterá um arquivo de modelo de Reconhecimento vocal chamado **RootDialog.lu**

Este é um exemplo de um arquivo .lu simples que captura uma intenção de **Saudação** simples com uma lista de exemplos de enunciados que capturam diferentes maneiras pelas quais um usuário pode expressar essa intenção. Use um caractere `-`, `+`ou `*` para indicar as listas. Não há suporte para listas numeradas.

```lu
# Greeting
- Hi
- Hello
- How are you?
```

`#<intent-name>` descreve uma nova seção de definição de intenção no arquivo de modelo LU. Cada linha após a definição da intenção são exemplos de enunciados que descrevem essa intenção. Crie várias definições de intenção no arquivo .lu. Cada seção é identificada pela notação `#<intent-name>`. As linhas em branco são ignoradas durante a análise do arquivo.

### <a name="utterances"></a>Declarações

Os enunciados (_frases de gatilho_) são entradas de usuários e, como tal, podem conter um número quase infinito de variações potenciais. Como os enunciados nem sempre são bem formados, você precisará fornecer vários exemplos de enunciados para intenções específicas que, de fato, treinam os bots para reconhecer intenções de diferentes enunciados. Ao fazer isso, os bots terão alguma "inteligência" para entender os idiomas humanos.

> [!TIP]
> Os enunciados também são conhecidos como _frases de gatilho_, porque são _frases_ _enunciadas_ por um usuário que podem acionar um _gatilho_ `OnIntent`.

### <a name="entities"></a>Entidades

As entidades são uma coleção de objetos, cada um consistindo em dados extraídos de um enunciado que adicionam outras informações esclarecedoras que descrevem a intenção, como lugares, horas e pessoas. As entidades e as intenções são partes importantes dos dados que são extraídos de um enunciado. Os enunciados em geral incluirão uma intenção e poderão incluir zero ou mais entidades, que fornecem detalhes importantes relacionados à intenção.

As entidades no [formato de arquivo .lu][8] são definidas no formato `{<entityName>=<labelled value>}`, como `{toCity=seattle}` (EntityName é _toCity_ e o valor rotulado é _seattle_).  Por exemplo:

```lu
# BookFlight
- book a flight to {toCity=seattle}
- book a flight from {fromCity=new york} to {toCity=seattle}
```

O exemplo acima mostra a definição de uma intenção `BookFlight` com dois exemplos de enunciados e duas definições de entidade: `toCity` e `fromCity`. Quando o gatilho for disparado, se o reconhecedor escolhido conseguir identificar uma cidade de destino, o nome da cidade será disponibilizado como `@toCity` dentro das ações disparadas ou uma cidade de partida com `@fromCity` como os valores de entidade disponíveis. Os valores de entidade podem ser usados diretamente em expressões e modelos LG ou armazenados em uma propriedade na [memória][10] para uso posterior.

<!--TODO P1:  Need to discuss recognizers in the context of recognition results. There is intent recognizer, entity recognizer, there can be other types of recognizers as well but a recognizer gets 3 property bags to fill in - intents[], entities[], properties[].
https://github.com/MicrosoftDocs/bot-docs-pr/pull/2123#discussion_r423237812
-->

<!--TODO P2: Need to have a more in depth discussion of intents & entities. -->

<!--TODO P2: Need a link to the samples, and in each section specify which sample demonstrates that recognizer. -->

<!--TODO P1: Need to document the EntityRecognizers:
    AgeEntityRecognizer.cs
    ConfirmationEntityRecognizer.cs
    CurrencyEntityRecognizer.cs
    DateTimeEntityRecognizer.cs
    DimensionEntityRecognizer.cs
    EmailEntityRecognizer.cs
    EntityRecognizer.cs
    EntityRecognizerSet.cs
    GuidEntityRecognizer.cs
    HashtagEntityRecognizer.cs
    IpEntityRecognizer.cs
    MentionEntityRecognizer.cs
    NumberEntityRecognizer.cs
    NumberRangeEntityRecognizer.cs
    OrdinalEntityRecognizer.cs
    PercentageEntityRecognizer.cs
    PhoneNumberEntityRecognizer.cs
    RegExEntityRecognizer.cs
    TemperatureEntityRecognizer.cs
    TextEntity.cs
    TextEntityRecognizer.cs
    UrlEntityRecognizer.cs
-->

## <a name="recognizer-types"></a>Tipos de reconhecedores

O SDK do Bot Framework fornece diversos reconhecedores e a capacidade de criar um reconhecedor próprio.

Os diálogos adaptáveis dão suporte aos seguintes reconhecedores:

* [RegexRecognizer](#regexrecognizer)
* [Reconhecedor do LUIS](#luis-recognizer)
* [Reconhecedor do QnA Maker](#qna-maker-recognizer)
* [Reconhecedor de vários idiomas](#multi-language-recognizer)
* [Conjunto de reconhecedor CrossTrained](#cross-trained-recognizer-set)
* [RecognizerSet](#recognizer-set)

### <a name="regexrecognizer"></a>RegexRecognizer

O _reconhecedor RegEx_ oferece a capacidade de extrair dados de intenção e de entidade de um enunciado com base em padrões de expressão regular.

`RegexRecognizer` consiste principalmente em:

* `Intents`. O objeto `Intents` contém uma lista de objetos `IntentPattern`, e esses `IntentPattern` objetos consistem em uma propriedade `Intent` que é o nome da intenção e uma propriedade `Pattern` que contém uma expressão regular usada para analisar o enunciado a fim de determinar a intenção.
* `Entities`. O objeto `Entities` contém uma lista de objetos `EntityRecognizer`.  O SDK do bot Framework define várias `EntityRecognizer` classes para ajudá-lo a determinar as entidades contidas em um expressão de usuários.

Para obter informações detalhadas e exemplos, consulte a seção [RegexRecognizer](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#regexrecognizer) no guia de referência de reconhecedores em caixas de diálogo adaptáveis.

### <a name="luis-recognizer"></a>Reconhecedor LUIS

O LUIS (Serviço Inteligente de Reconhecimento vocal) é um serviço de API baseado em nuvem que aplica inteligência de machine learning personalizado a um texto de idioma natural de conversa do usuário para prever o significado geral e extrair informações detalhadas relevantes. O reconhecedor LUIS permite que você extraia as intenções e as entidades do enunciado de um usuário com base no aplicativo LUIS definido, que você treina com antecedência.

Para obter informações detalhadas e um exemplo de como criar um reconhecedor LUIS, consulte a seção [Recognizer Luis](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#luis-recognizer) no guia de referência de reconhecedores em caixas de diálogo adaptáveis.

Para obter etapas detalhadas sobre como criar seu aplicativo LUIS e implantar seus modelos do LUIS usando a CLI do bot Framework, consulte [como implantar recursos do Luis usando os comandos da CLI do SDK do bot Framework Luis][how-to-deploy-using-luis-cli].

### <a name="qna-maker-recognizer"></a>Reconhecedor do QnA Maker

O [QnAMaker.ai][13] é um dos [Serviços Cognitivos da Microsoft][14] que permite criar pares de perguntas e respostas sofisticadas com base no conteúdo existente: documentos, URLs, PDFs etc. Use o Reconhecedor do QnA Maker para fazer a integração ao serviço.

Para obter informações detalhadas e um exemplo de como criar um reconhecedor de QnA Maker, consulte a seção [QnA Maker Recognizer](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#qna-maker-recognizer) no guia de referência de reconhecedores em caixas de diálogo adaptáveis.

### <a name="multi-language-recognizer"></a>Reconhecedor de vários idiomas

Ao criar um bot sofisticado multilíngue, normalmente, haverá um reconhecedor vinculado a um idioma e uma localidade específicos. O reconhecedor de vários idiomas permite que você especifique com facilidade o reconhecedor a ser usado com base na propriedade [localidade][5] na atividade de entrada de um usuário.

Para obter informações detalhadas e um exemplo de como criar um reconhecedor em vários idiomas, consulte a seção [reconhecedor de vários idiomas](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#multi-language-recognizer) no guia de referência de reconhecedores em caixas de diálogo adaptáveis.

### <a name="recognizer-set"></a>Conjunto de reconhecedores

Às vezes, talvez seja necessário executar mais de um reconhecedor em cada turno da conversa. O conjunto de reconhecedores faz exatamente isso. Todos os reconhecedores são executados em cada turno da conversa e o resultado é uma união de todos os resultados do reconhecimento.

Para obter informações detalhadas e um exemplo de como criar um conjunto de reconhecedor, consulte a seção [conjunto de reconhecedor](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#multi-language-recognizer) no guia de referência de reconhecedores em caixas de diálogo adaptáveis.

### <a name="cross-trained-recognizer-set"></a>Conjunto de reconhecedores com treinamento cruzado

O conjunto de reconhecedores com treinamento cruzado compara os resultados de reconhecimento de vários reconhecedores para decidir um vencedor. Considerando uma coleção de reconhecedores, o reconhecedor com treinamento cruzado vai:

* Promover o resultado do reconhecimento de um reconhecedor se todos os outros reconhecedores adiarem o reconhecimento de um só reconhecedor. Para adiar o reconhecimento, um reconhecedor pode retornar a intenção `None` ou uma `DeferToRecognizer_recognizerId` explícita como intenção.
* Acionar um evento `OnChooseIntent` para permitir que o código escolha qual resultado de reconhecimento será usado. Os resultados de cada reconhecedor são retornados por meio da propriedade `turn.recognized.candidates`. Isso permite que você escolha o resultado mais apropriado.

#### <a name="more-on-cross-training"></a>Mais sobre o treinamento cruzado

* Para obter uma explicação conceitual abrangente de treinamento cruzado de seu bot, consulte o [treinamento cruzado do bot para usar o artigo Luis e reconhecedores de QnA Maker](bot-builder-concept-cross-train.md) .

* Para obter detalhes técnicos sobre o treinamento cruzado de um bot, consulte a seção _conjunto de reconhecedor com treinamento cruzado_ do [Guia de referência de reconhecedores em caixas de diálogo adaptáveis](../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md#cross-trained-recognizer-set).

* Para obter informações detalhadas sobre como treinar um bot usando um bot existente como exemplo, consulte [criar um bot entre treinados para usar os reconhecedores Luis e QnA Maker](bot-builder-howto-cross-train.md).

## <a name="additional-information"></a>Informações adicionais

* [O que é o LUIS?][6]
* [Reconhecimento vocal][7]
* [Formato de arquivo .lu][8]
* [Expressões adaptáveis][9]
* [Extrair dados de um texto do enunciado com intenções e entidades][11]
* [Adicionar o LU (Reconhecimento vocal) natural ao seu bot][12]
* [Adicionar a LG (geração de linguagem) natural ao seu bot][4]
* Para obter informações mais detalhadas sobre os reconhecedores em caixas de diálogo adaptáveis, incluindo exemplos, consulte o [Guia de referência de reconhecedores em caixas de diálogo adaptáveis][recognizers-ref].

<!-- Footnote-style links -->
[1]:bot-builder-basics.md
[2]:bot-builder-adaptive-dialog-introduction.md
[3]:bot-builder-concept-dialog.md
[4]:bot-builder-concept-adaptive-dialog-generators.md
[5]:https://github.com/microsoft/botbuilder/blob/master/specs/botframework-activity/botframework-activity.md#locale
[6]:/azure/cognitive-services/luis/what-is-luis
[7]:../v4sdk/bot-builder-concept-luis.md
[8]:../file-format/bot-builder-lu-file-format.md
[9]:bot-builder-concept-adaptive-expressions.md
[10]:bot-builder-concept-adaptive-dialog-memory-states.md
[11]:/azure/cognitive-services/luis/luis-concept-data-extraction?tabs=V2
[12]:bot-builder-howto-v4-luis.md
[13]:https://qnamaker.ai
[14]:https://azure.microsoft.com/services/cognitive-services/
[15]:/adaptive-cards
[recognizers-ref]: ../adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md
[how-to-deploy-using-luis-cli]: ../v4sdk/bot-builder-howto-bf-cli-deploy-luis.md
[interruptions]: bot-builder-concept-adaptive-dialog-interruptions.md
