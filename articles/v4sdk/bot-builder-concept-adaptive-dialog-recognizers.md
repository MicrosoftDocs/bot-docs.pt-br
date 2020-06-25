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
ms.openlocfilehash: 7d6a64a6a6638455e233ca29ed40b3c11a7a183c
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073872"
---
# <a name="recognizers-in-adaptive-dialogs"></a>Reconhecedores em diálogos adaptáveis

Os _reconhecedores_ fornecem LU (Reconhecimento vocal) para seus diálogos adaptáveis.

Os reconhecedores fornecem a capacidade de entender a entrada do usuário. O diálogo adaptável pode ter um ou mais reconhecedores configurados para transformar a entrada do usuário em constructos que o bot pode processar. Por exemplo, [Reconhecimento vocal](#luis-recognizer) e [perguntas e respostas](#qna-maker-recognizer).

## <a name="prerequisites"></a>Pré-requisitos

* É útil ter uma compreensão geral de [como funcionam os bots][1] no SDK do Bot Framework V4.
* É útil ter uma compreensão geral dos diálogos adaptáveis no SDK do Bot Framework V4. Para obter mais informações, confira a [Introdução aos diálogos adaptáveis][2] e a [Biblioteca de diálogos][3].

## <a name="introduction-to-natural-language-processing-in-adaptive-dialogs"></a>Introdução ao Processamento de Idioma Natural em diálogos adaptáveis

Os reconhecedores, juntamente com os [geradores][4] constituem as funcionalidades de NLP (processamento de idioma natural) em diálogos adaptáveis. NLP é um processo tecnológico que permite que aplicativos de computador, como bots, obtenham o significado de uma entrada do usuário. Para fazer isso, ele tenta identificar informações úteis contidas em conversas interpretando as necessidades dos usuários (intenções) e extrair informações úteis (entidades) de uma frase e responder a ela novamente em uma linguagem que o usuário entenderá.

As funcionalidades de um bot serão muito mínimas sem o NLP; é ele que permite que o bot compreenda as mensagens enviadas pelos usuários e responda a elas de acordo. Quando um usuário envia uma mensagem com “Olá”, são as funcionalidades de processamento de idioma natural do bot que permitem saber que o usuário postou uma saudação padrão que, por sua vez, permite que o bot utilize as funcionalidades de IA para oferecer uma resposta adequada. Nesse caso, o bot pode responder com uma saudação.

Sem o NLP, o bot não pode diferenciar de maneira significativa quando um usuário insere “Olá” ou “Tchau” ou qualquer outra coisa. Para um bot sem NLP, “Olá” ou “Tchau” não será diferente de nenhuma outra cadeia de caracteres agrupada em ordem aleatória. O NLP ajuda a fornecer contexto e significado às entradas do usuário baseadas em texto ou voz, de modo que o bot possa oferecer a melhor resposta.

Um dos desafios mais significativos quando se trata de NLP no bot é o fato de os usuários não fazerem ideia do que dizer ao bot. Embora você possa tentar prever o que os usuários não vão dizer, haverá conversas inesperadas. A boa notícia é que o SDK do Bot Framework fornece as ferramentas necessárias para refinar continuamente as funcionalidades de NLP dos bots.

Os dois componentes principais do NLP em diálogos adaptáveis são os **reconhecedores** (Reconhecimento vocal), que processa e interpreta a _entrada do usuário_ foco deste artigo, e os [**geradores**][4] (geração de linguagem) que produz as _respostas do bot_, que é o processo de produzir frases significativas na forma de idioma natural. Em síntese, é quando o bot responde a um usuário com uma linguagem legível.

> [!TIP]
> Embora seja comum que os usuários se comuniquem com o bot digitando ou falando uma mensagem, o reconhecedor é o subsistema que você pode usar para processar qualquer forma de entrada do usuário, seja falada, digitada, clicada (como ao responder aos [cartões adaptáveis][21]) e, até mesmo, outras modalidades como um reconhecedor de geolocalização ou de foco pode ser usado. A camada do reconhecedor abstrai as complexidades do processamento de entrada do usuário de gatilhos e ações. Dessa forma, os gatilhos e as ações não precisam interpretar os vários tipos de entradas do usuário, mas permitem que os reconhecedores façam isso.

## <a name="language-understanding"></a>Reconhecimento Vocal

O LU **(Reconhecimento vocal)** é o subconjunto do NLP que lida com o modo como o bot processa as entradas do usuário e as converte em algo que ele pode entender e responder de maneira inteligente. Esse processo é obtido por meio da configuração de reconhecedores e do fornecimento de dados de treinamento para o diálogo, de maneira que as **intenções** e as **entidades** contidas na mensagem dos usuários possam ser capturadas. Quando o reconhecedor identifica uma intenção, ele emite um evento `IntentRecognized` contendo essa intenção e o gatilho OnIntent que você definiu para essa intenção será executado e realizará as ações contidas nesse gatilho.

## <a name="core-lu-concepts-in-adaptive-dialogs"></a>Conceitos básicos do LU em diálogos adaptáveis

### <a name="intents"></a>Intenções

As intenções são como você categoriza as intenções esperadas do usuário, conforme expressas nas mensagens dele para o bot. Considere uma intenção como uma representação da ação que o usuário deseja realizar, a finalidade ou a meta expressa na entrada. Tarefas como reservar um voo, pagar uma fatura ou encontrar um artigo de notícias. Você define e nomeia as intenções que correspondem a essas ações. Por exemplo, qualquer bot pode definir uma intenção chamada _Saudação_; um aplicativo de viagem pode criar uma intenção chamada _BookFlight_. As intenções são definidas em um arquivo de modelo de Reconhecimento vocal (.lu); esses arquivos são arquivos de texto com uma extensão .lu e, geralmente, residem no mesmo diretório e têm o mesmo nome do diálogo.  Por exemplo, o diálogo raiz conterá um arquivo de modelo de Reconhecimento vocal chamado **RootDialog.lu**

Este é um exemplo de um arquivo .lu simples que captura uma intenção de **Saudação** simples com uma lista de exemplos de enunciados que capturam diferentes maneiras pelas quais um usuário pode expressar essa intenção. Use um caractere `-`, `+`ou `*` para indicar as listas. Não há suporte para listas numeradas.  

```dos
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

As entidades no [formato de arquivo .lu][12] são definidas no formato `{<entityName>=<labelled value>}`, como `{toCity=seattle}` (EntityName é _toCity_ e o valor rotulado é _seattle_).  Por exemplo:

```dos
# BookFlight
- book a flight to {toCity=seattle}
- book a flight from {fromCity=new york} to {toCity=seattle}
```

O exemplo acima mostra a definição de uma intenção `BookFlight` com dois exemplos de enunciados e duas definições de entidade: `toCity` e `fromCity`. Quando o gatilho for disparado, se o reconhecedor escolhido conseguir identificar uma cidade de destino, o nome da cidade será disponibilizado como `@toCity` dentro das ações disparadas ou uma cidade de partida com `@fromCity` como os valores de entidade disponíveis. Os valores de entidade podem ser usados diretamente em expressões e modelos LG ou armazenados em uma propriedade na [memória][14] para uso posterior.

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
* `Entities`. O objeto `Entities` contém uma lista de objetos `EntityRecognizer`.  O SDK do Bot Framework define várias classes `EntityRecognizer` para ajudar você a determinar as entidades contidas em um enunciado dos usuários:
  * `AgeEntityRecognizer`
  * `ConfirmationEntityRecognizer`
  * `CurrencyEntityRecognizer`
  * `DateTimeEntityRecognizer`
  * `DimensionEntityRecognizer`
  * `EmailEntityRecognizer`
  * `EntityRecognizer`
  * `EntityRecognizerSet`
  * `GuidEntityRecognizer`
  * `HashtagEntityRecognizer`
  * `IpEntityRecognizer`
  * `MentionEntityRecognizer`
  * `NumberEntityRecognizer`
  * `NumberRangeEntityRecognizer`
  * `OrdinalEntityRecognizer`
  * `PercentageEntityRecognizer`
  * `PhoneNumberEntityRecognizer`
  * `RegExEntityRecognizer`
  * `TemperatureEntityRecognizer`
  * `TextEntity`
  * `TextEntityRecognizer`
  * `UrlEntityRecognizer`

#### <a name="regexrecognizer-code-sample"></a>Exemplo de código do RegexRecognizer

``` C#
var rootDialog = new AdaptiveDialog("rootDialog")
{
    Recognizer = new RegexRecognizer()
    {
        Intents = new List<IntentPattern>()
        {
            new IntentPattern()
            {
                Intent = "AddIntent",
                Pattern = "(?i)(?:add|create) .*(?:to-do|todo|task)(?: )?(?:named (?<title>.*))?"
            },
            new IntentPattern()
            {
                Intent = "HelpIntent",
                Pattern = "(?i)help"
            },
            new IntentPattern()
            {
                Intent = "CancelIntent",
                Pattern = "(?i)cancel|never mind"
            }
        },
        Entities = new List<EntityRecognizer>()
        {
            new ConfirmationEntityRecognizer(),
            new DateTimeEntityRecognizer(),
            new NumberEntityRecognizer()
        }
    }
}
```

> [!TIP]
>
> * O `RegexRecognizer` emitirá uma intenção 'None' quando o enunciado de entrada não corresponder a nenhuma intenção definida. Você pode criar um gatilho `OnIntent` com `Intent = "None"` para lidar com esse cenário.
> * O `RegexRecognizer` é útil para teste e criação rápida de protótipos. Para bots mais sofisticados, recomendamos o uso do reconhecedor LUIS.
> * A [Referência Rápida][7] do RegEx (linguagem de expressão regular) pode ser útil.

### <a name="luis-recognizer"></a>Reconhecedor LUIS

O LUIS (Serviço Inteligente de Reconhecimento vocal) é um serviço de API baseado em nuvem que aplica inteligência de machine learning personalizado a um texto de idioma natural de conversa do usuário para prever o significado geral e extrair informações detalhadas relevantes. O reconhecedor LUIS permite que você extraia as intenções e as entidades do enunciado de um usuário com base no aplicativo LUIS definido, que você treina com antecedência.

Para criar um reconhecedor LUIS:

``` C#
var rootDialog = new AdaptiveDialog("rootDialog")
{
    Recognizer = new LuisAdaptiveRecognizer()
    {
        ApplicationId = "<LUIS-APP-ID>",
        EndpointKey = "<ENDPOINT-KEY>",
        Endpoint = "<ENDPOINT-URI>"
    }
}
```

> [!TIP]
> As seguintes informações ajudarão você a saber mais sobre como incorporar o LU (Reconhecimento vocal) em seu bot usando o LUIS:
>
> * O [LUIS.ai][9] é um serviço baseado em machine learning que permite criar funcionalidades de idioma natural no seu bot.
> * [O que é o LUIS?][10]
> * [Criar um aplicativo LUIS no portal do LUIS][17]
> * [Reconhecimento vocal][11]
> * [Formato de arquivo .lu][12]
> * [Expressões adaptáveis][13]

### <a name="qna-maker-recognizer"></a>Reconhecedor do QnA Maker

O [QnAMaker.ai][18] é um dos [Serviços Cognitivos da Microsoft][19] que permite criar pares de perguntas e respostas sofisticadas com base no conteúdo existente: documentos, URLs, PDFs etc. Use o Reconhecedor do QnA Maker para fazer a integração ao serviço.

> [!NOTE]
> O Reconhecedor do QnA Maker emitirá um `QnAMatch`evento que pode sr processado com um gatilho `OnQnAMatch`.
> Toda a resposta do QnA Maker estará disponível na propriedade `answer`.

```C#
var adaptiveDialog = new AdaptiveDialog()
{
    var recognizer = new QnAMakerRecognizer()
    {
        HostName = configuration["qna:hostname"],
        EndpointKey = configuration["qna:endpointKey"],
        KnowledgeBaseId = configuration["qna:KnowledgeBaseId"],
    }

    Triggers = new List<OnCondition>()
    {
        new OnConversationUpdateActivity()
        {
            Actions = WelcomeUserAction()
        },

        // With QnA Maker set as a recognizer on a dialog, you can use the OnQnAMatch trigger to render the answer.
        new OnQnAMatch()
        {
            Actions = new List<Dialog>()
            {
                new SendActivity()
                {
                    Activity = new ActivityTemplate("Here's what I have from QnA Maker - ${@answer}"),
                }
            }
        }
    }

    // Add adaptiveDialog to the DialogSet.
    AddDialog(adaptiveDialog);
};
```

### <a name="multi-language-recognizer"></a>Reconhecedor de vários idiomas

Ao criar um bot sofisticado multilíngue, normalmente, haverá um reconhecedor vinculado a um idioma e uma localidade específicos. O reconhecedor de vários idiomas permite que você especifique com facilidade o reconhecedor a ser usado com base na propriedade [localidade][8] na atividade de entrada de um usuário.

``` C#
var rootDialog = new AdaptiveDialog("rootDialog")
{
    Recognizer = new MultiLanguageRecognizer()
    {
        Recognizers = new Dictionary<string, Recognizer>()
        {
            {
                "en",
                new RegexRecognizer()
                {
                    Intents = new List<IntentPattern>()
                    {
                        new IntentPattern()
                        {
                            Intent = "AddIntent",
                            Pattern = "(?i)(?:add|create) .*(?:to-do|todo|task)(?: )?(?:named (?<title>.*))?"
                        },
                        new IntentPattern()
                        {
                            Intent = "HelpIntent",
                            Pattern = "(?i)help"
                        },
                        new IntentPattern()
                        {
                            Intent = "CancelIntent",
                            Pattern = "(?i)cancel|never mind"
                        }
                    },
                    Entities = new List<EntityRecognizer>()
                    {
                        new ConfirmationEntityRecognizer(),
                        new DateTimeEntityRecognizer(),
                        new NumberEntityRecognizer()
                    }
                }
            },
            {
                "fr",
                new LuisAdaptiveRecognizer()
                {
                    ApplicationId = "<LUIS-APP-ID>",
                    EndpointKey = "<ENDPOINT-KEY>",
                    Endpoint = "<ENDPOINT-URI>"
                }
            }
        }
    }
};
```

### <a name="recognizer-set"></a>Conjunto de reconhecedores

Às vezes, talvez seja necessário executar mais de um reconhecedor em cada turno da conversa. O conjunto de reconhecedores faz exatamente isso. Todos os reconhecedores são executados em cada turno da conversa e o resultado é uma união de todos os resultados do reconhecimento.

```C#
var adaptiveDialog = new AdaptiveDialog()
{
    Recognizer = new RecognizerSet()
    {
        Recognizers = new List<Recognizer>()
        {
            new ValueRecognizer(),
            new QnAMakerRecognizer()
            {
                KnowledgeBaseId = "<KBID>",
                HostName = "<HostName>",
                EndpointKey = "<Key>"
            }
        }
    }
};
```

### <a name="cross-trained-recognizer-set"></a>Conjunto de reconhecedores com treinamento cruzado

O conjunto de reconhecedores com treinamento cruzado compara os resultados do reconhecimento de mais de um reconhecedor para decidir quem será o vencedor. Considerando uma coleção de reconhecedores, o reconhecedor com treinamento cruzado vai:

* Promover o resultado do reconhecimento de um reconhecedor se todos os outros reconhecedores adiarem o reconhecimento de um só reconhecedor. Para adiar o reconhecimento, um reconhecedor pode retornar a intenção `None` ou uma `DeferToRecognizer_recognizerId` explícita como intenção.
* Acionar um evento `OnChooseIntent` para permitir que o código escolha qual resultado de reconhecimento será usado. Os resultados de cada reconhecedor são retornados por meio da propriedade `turn.recognized.candidates`. Isso permite que você escolha o resultado mais apropriado.

```C#
var adaptiveDialog = new AdaptiveDialog()
{
    Recognizer = new CrossTrainedRecognizerSet()
    {
        Recognizers = new List<Recognizer>()
        {
            new LuisAdaptiveRecognizer()
            {
                Id = "Luis-main-dialog",
                ApplicationId = "<LUIS-APP-ID>",
                EndpointKey = "<ENDPOINT-KEY>",
                Endpoint = "<ENDPOINT-URI>"
            },
            new QnAMakerRecognizer()
            {
                Id = "qna-main-dialog",
                KnowledgeBaseId = "<KBID>",
                HostName = "<HostName>",
                EndpointKey = "<Key>"
            }
        }
    }
};
```

## <a name="additional-information"></a>Informações adicionais

* [O que é o LUIS?][10]
* [Reconhecimento vocal][11]
* [Formato de arquivo .lu][12]
* [Expressões adaptáveis][13]
* [Extrair dados de um texto do enunciado com intenções e entidades][15]
* [Adicionar o LU (Reconhecimento vocal) natural ao seu bot][16]
* [Adicionar a LG (geração de linguagem) natural ao seu bot][4]

[1]:bot-builder-basics.md
[2]:bot-builder-adaptive-dialog-introduction.md
[3]:bot-builder-concept-dialog.md
[4]:bot-builder-concept-adaptive-dialog-generators.md

[7]:https://aka.ms/regular-expression-language-reference
[8]:https://github.com/microsoft/botbuilder/blob/master/specs/botframework-activity/botframework-activity.md#locale
[9]:https://luis.ai
[10]:https://aka.ms/luis-what-is-luis
[11]:https://aka.ms/botbuilder-luis-concept?view=azure-bot-service-4.0
[12]:../file-format/bot-builder-lu-file-format.md
[13]:bot-builder-concept-adaptive-expressions.md
[14]:bot-builder-concept-adaptive-dialog-memory-states.md
[15]:https://aka.ms/luis-concept-data-extraction?tabs=v2
[16]:https://aka.ms/bot-service-add-luis-to-bot
[17]:https://aka.ms/luis-create-new-app-in-luis-portal
[18]:https://qnamaker.ai
[19]:https://azure.microsoft.com/services/cognitive-services/
[20]:https://adaptivecards.io
[21]:https://aka.ms/adaptive-cards-overview
