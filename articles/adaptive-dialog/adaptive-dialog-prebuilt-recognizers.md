---
title: Reconhecedores em caixas de diálogo adaptáveis – guia de referência
description: Descrevendo os reconhecedores predefinidos da caixa de diálogo adaptável
keywords: bot, reconhecedores, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 06/12/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b1bef2f1d9bdd8676d785e6c0312e2a638ccc1d5
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124598"
---
# <a name="recognizers-in-adaptive-dialogs---reference-guide"></a>Reconhecedores em caixas de diálogo adaptáveis – guia de referência

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Os reconhecedores permitem que o bot compreenda a entrada do usuário e uma caixa de diálogo adaptável pode ter um ou mais reconhecedores configurados. Para obter informações adicionais sobre os reconhecedores, consulte o artigo [reconhecedores em caixas de diálogo adaptáveis](../v4sdk/bot-builder-concept-adaptive-dialog-recognizers.md) .

<!--
Adaptive dialogs provide support for the following recognizers:

* [RegexRecognizer](#regexrecognizer)
* [LUIS recognizer](#luis-recognizer)
* [QnA Maker recognizer](#qna-maker-recognizer)
* [Multi-language recognizer](#multi-language-recognizer)
* [RecognizerSet](#recognizer-set)
* [Cross-Trained recognizer set](#cross-trained-recognizer-set)
-->

## <a name="regexrecognizer"></a>RegexRecognizer

O _reconhecedor RegEx_ oferece a capacidade de extrair dados de intenção e de entidade de um enunciado com base em padrões de expressão regular.

`RegexRecognizer`consiste basicamente em:

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

### <a name="regexrecognizer-code-sample"></a>Exemplo de código do RegexRecognizer

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
> * A [Referência Rápida][2] do RegEx (linguagem de expressão regular) pode ser útil.

## <a name="luis-recognizer"></a>Reconhecedor LUIS

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
> * O [LUIS.ai][4] é um serviço baseado em machine learning que permite criar funcionalidades de idioma natural no seu bot.
> * [O que é o LUIS?][5]
> * [Criar um aplicativo LUIS no portal do LUIS][11]
> * [Reconhecimento vocal][6]
> * [Formato de arquivo .lu][7]
> * [Expressões adaptáveis][8]

## <a name="qna-maker-recognizer"></a>Reconhecedor do QnA Maker

O [QnAMaker.ai][12] é um dos [Serviços Cognitivos da Microsoft][13] que permite criar pares de perguntas e respostas sofisticadas com base no conteúdo existente: documentos, URLs, PDFs etc. Use o Reconhecedor do QnA Maker para fazer a integração ao serviço.

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

## <a name="multi-language-recognizer"></a>Reconhecedor de vários idiomas

Ao criar um bot sofisticado multilíngue, normalmente, haverá um reconhecedor vinculado a um idioma e uma localidade específicos. O reconhecedor de vários idiomas permite que você especifique com facilidade o reconhecedor a ser usado com base na propriedade [localidade][3] na atividade de entrada de um usuário.

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

## <a name="recognizer-set"></a>Conjunto de reconhecedores

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

## <a name="cross-trained-recognizer-set"></a>Conjunto de reconhecedores com treinamento cruzado

O conjunto de reconhecedores com treinamento cruzado compara os resultados do reconhecimento de mais de um reconhecedor para decidir quem será o vencedor. Considerando uma coleção de reconhecedores, o reconhecedor com treinamento cruzado vai:

* Promover o resultado do reconhecimento de um reconhecedor se todos os outros reconhecedores adiarem o reconhecimento de um só reconhecedor. Para adiar o reconhecimento, um reconhecedor pode retornar a intenção `None` ou uma `DeferToRecognizer_recognizerId` explícita como intenção.
* Gere um `OnChooseIntent` evento para permitir que seu código escolha qual resultado de reconhecimento usar. Os resultados de cada reconhecedor são retornados por meio da propriedade `turn.recognized.candidates`. Isso permite que você escolha o resultado mais apropriado.

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

* [O que é o LUIS?][5]
* [Reconhecimento vocal][6]
* [Formato de arquivo .lu][7]
* [Expressões adaptáveis][8]
* [Extrair dados de um texto do enunciado com intenções e entidades][9]
* [Adicionar o LU (Reconhecimento vocal) natural ao seu bot][10]
* [Adicionar a LG (geração de linguagem) natural ao seu bot][1]

<!-- Footnote-style links -->
[1]:../v4sdk/bot-builder-concept-adaptive-dialog-generators.md
[2]:https://aka.ms/regular-expression-language-reference
[3]:https://github.com/microsoft/botbuilder/blob/master/specs/botframework-activity/botframework-activity.md#locale
[4]:https://luis.ai
[5]:https://aka.ms/luis-what-is-luis
[6]:https://aka.ms/botbuilder-luis-concept?view=azure-bot-service-4.0
[7]:../file-format/bot-builder-lu-file-format.md
[8]:../v4sdk/bot-builder-concept-adaptive-expressions.md
[9]:https://aka.ms/luis-concept-data-extraction?tabs=v2
[10]:https://aka.ms/bot-service-add-luis-to-bot
[11]:https://aka.ms/luis-create-new-app-in-luis-portal
[12]:https://qnamaker.ai
[13]:https://azure.microsoft.com/services/cognitive-services/
