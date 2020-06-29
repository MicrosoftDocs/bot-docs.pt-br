---
title: Formato de arquivo .lu – Serviço de Bot
description: Referência do formato de arquivo .lu
keywords: file format, reference, language understanding
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1a2a06e944a2e6bfd0277be8e930aa8b2dbaeb60
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566486"
---
# <a name="lu-file-format"></a>Formato de arquivo .lu

[!INCLUDE[applies-to](../includes/applies-to.md)]

Um arquivo .lu contém definições baseadas em texto simples e semelhantes a Markdown para os conceitos do [LUIS][1]. Este artigo aborda os vários conceitos expressos por meio do formato de arquivo .lu.

## <a name="add-comments"></a>Adicionar comentários

Use **>** para criar um comentário. Aqui está um exemplo:

```.lu
> This is a comment and will be ignored.

# Greeting
- hi
- hello
```

## <a name="intent"></a>Intencional

Uma [intenção][2] representa uma ação que o usuário deseja executar. A intenção é uma finalidade ou uma meta expressa na entrada do usuário, como reservar um voo, pagar uma fatura ou localizar um artigo de notícias. Por exemplo, um aplicativo de viagem pode definir uma intenção chamada _BookFlight_. Você define e nomeia as intenções que correspondem a essas ações.

Este é um arquivo .lu que captura uma intenção `Greeting` simples com uma lista de exemplos de enunciados que capturam as maneiras pelas quais os usuários podem expressar essa intenção. Use os caracteres **-** , **+** ou **\*** para indicar as listas. Não há suporte para listas numeradas.

```.lu
# Greeting
- Hi
- Hello
- Good morning
- Good evening
```

`#\<intent-name\>` descreve uma nova seção de definição de intenção. Cada linha após a definição da intenção são exemplos de enunciados que descrevem essa intenção. Você pode adicionar várias definições de intenção a um só arquivo, como no seguinte exemplo:

```.lu
# Greeting
- Hi
- Hello
- Good morning
- Good evening

# Help
- help
- I need help
- please help
```

Cada seção é identificada pela notação `#\<intent name\>`. As linhas em branco são ignoradas durante a análise do arquivo.

## <a name="entity"></a>Entidade

Uma [entidade][3] representa informações detalhadas que são relevantes em um enunciado. Por exemplo, no enunciado _Reservar uma passagem para Paris_, _Paris_ é uma localização.

|Exemplo de enunciado do usuário|entidade|
|--------------------------|----------|
|"Reservar um voo para **Seattle**?"|Seattle|
|"Quando a sua loja é **aberta**?"|Abrir|
|"Agendar uma reunião às **13h** com **Bob** na distribuição"|13h, Bob|

### <a name="definition"></a>Definição

As entidades são declaradas da seguinte maneira:

```.lu
@ <entity-type> <entity-name> [[hasRole[s]] <comma-separated-list-of-roles>] [hasFeature[s] <comma-separated-list-of-features>]
```

As entidades que exigem uma definição, como uma lista e entidades de expressões regulares, são representadas por meio da seguinte notação:

```.lu
@ <entity-name> = <definition>
```

A declaração e a definição também podem ser combinadas em uma só linha:

```.lu
@ <entity-type> <entity-name> [[hasRoles] <comma-separated-list-of-roles>] = <definition>
```

`entity type`, `entity name` e `definition` são obrigatórios; `roles` são opcionais.

Os nomes de entidades com um espaço podem ser encapsulados entre aspas. Observe que os nomes de entidades predefinidos não podem conter espaços entre eles.

Aqui está um exemplo:

```.lu
@ ml "this is a simple entity" role1, role2
@ ml 'this is a simple entity' hasRoles role1, role2
```

### <a name="machine-learned-entity"></a>Entidade com aprendizado de máquina

As [entidades de machine learning][4] aprendem com o contexto em um enunciado. O agrupamento de entidades pai ocorre, independentemente do tipo de entidade. Isso torna significativa a variação do posicionamento nos exemplos de enunciados.

No exemplo abaixo, o `name` da entidade ml é definido como `firstName` e `lastName`.

```.lu
@ ml name firstName, lastName
```

Para qualquer entidade rotulada que não tenha recebido um tipo explicitamente, o analisador usa como padrão um tipo de entidade ml para essa entidade.

```.lu
# getUserName
- my name is {username=vishwac}

> Without an explicit entity definition, 'userName' defaults to 'ml' entity type.
```

Há suporte para os seguintes tipos de [entidade LUIS predefinidos][5]:

- age
- datetimeV2
- dimensão
- email
- geographyV2
- keyPhrase
- money
- número
- ordinal
- ordinalV2
- percentage
- personName
- phonenumber
- temperatura
- url
- DATETIME

Estes são alguns exemplos de entidades predefinidas:

```.lu
@ prebuilt number numOfGuests, age
@ prebuilt datetimeV2 fromDate, toDate
@ prebuilt age userAge
```

Nem todos os tipos de entidades predefinidas estão disponíveis em todas as localidades. Confira [Entidades por cultura no modelo LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-entities) para obter suporte às entidades predefinidas por localidade.

### <a name="list-entity"></a>Entidade de lista

As [entidades de lista][6] representam um conjunto fixo e fechado de palavras relacionadas junto com os respectivos sinônimos. Eles são extraídos com base em uma correspondência exata do texto.

No exemplo abaixo, é definida uma entidade de lista que inclui sinônimos de cores:

```.lu
@ list color favColor, screenColor
@ color =
    - <normalized-value> :
        - <synonym1>
        - <synonym2>
        - ...
    - <normalized-value> :
        - <synonym1>, <synonym2>, ...

> Alternate definition

@ list color favColor, screenColor =
    - <normalized-value> :
        - <synonym1>; <synonym2>; ...
```

Ao usar as entidades de lista, você deverá incluir um valor da lista diretamente no enunciado, não um rótulo de entidade nem qualquer outro valor.

### <a name="composite-entity"></a>Entidade composta

Uma [entidade composta][7] é constituída de outras entidades, como entidades predefinidas, simples, de expressão regular e de lista. As entidades separadas formam uma entidade inteira.

Este é um exemplo de uma entidade composta simples:

```.lu
@ composite deviceTemperature from, to
@ deviceTemperature =
    - child1, child2

> Alternate definition

@ composite deviceTemperature from, to = [child1, child2]
```

Esta é uma definição de exemplo mais complexa:

```.lu
# setThermostat
> This utterance labels ‘thermostat to 72’ as composite entity deviceTemperature
    - Please set {deviceTemperature = thermostat to 72}
> This is an example utterance that labels ‘owen’ as customDevice (ml entity) and wraps ‘owen to 72’ with the ‘deviceTemperature’ composite entity
    - Set {deviceTemperature = {customDevice = owen} to 72}

> Define a composite entity ‘deviceTemperature’ that has device (list entity), customDevice (ml entity), temperature (pre-built entity) as children

@ composite deviceTemperature = [device, customDevice, temperature]

@ list device =
    - thermostat :
        - Thermostat
        - Heater
        - AC
        - Air conditioner
    - refrigerator :
        - Fridge
        - Cooler

@ ml customDevice

@ prebuilt temperature
```

### <a name="regular-expression-entity"></a>Entidade de expressão regular

Uma [entidade de expressão regular][8] extrai uma entidade com base em um padrão de expressão regular fornecido por você.

Este é um exemplo de uma definição de entidade de expressão regular simples.

```.lu
> from, to are roles to hrf-number.
@ regex hrf-number from, to
@ hrf-number = /hrf-[0-9]{6}/

> Alternate definition

> from, to are roles to hrf-number.
@ regex hrf-number from, to = /hrf-[0-9]{6}/
```

## <a name="roles"></a>Funções

Uma [função](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types#using-entity-role-to-define-context) é um alias nomeado para uma entidade com base no contexto dentro de um enunciado. Uma função pode ser usada com qualquer tipo de entidade predefinida ou personalizada (exceto listas de frases) e usada em padrões e exemplos de enunciados.

No exemplo abaixo, a entidade **Localização** tem duas funções, `origin` e `destination`:

|Entidade |Função   |Finalidade|
|-------|-------|-------|
|Location   |origin |de onde o avião parte|
|Location   |destino    |onde o avião pousa|

As funções no formato de arquivo .lu podem ser definidas explícita ou implicitamente. A definição de função explícita segue a notação:

```.lu
@ <entityType> <entityName> [hasRole[s]] role1, role2, ...
```

Abaixo, são mostradas as diversas maneiras pelas quais você pode definir explicitamente as entidades e as respectivas funções:

```.lu
> # ml entity definition with roles

@ ml name role1, role2

> this is the same as

@ ml name hasRoles role1, role2

> this is also the same as

@ ml name
@ name hasRoles role1, role2

> Also same as

@ ml name
@ name hasRole role1
@ name hasRole role2
```

Veja as funções definidas de maneira implícita diretamente em padrões e enunciados rotulados com o seguinte formato:

```.lu
{@\<entityName\>:\<roleName\>}
```

Veja no exemplo abaixo como as funções `userName:firstName` e `userName:lastName` são definidas implicitamente:

```.lu
# AskForUserName
- {userName:firstName=vishwac} {userName:lastName=kannan}
- I'm {userName:firstName=vishwac}
- my first name is {userName:firstName=vishwac}
- {userName=vishwac} is my name

> This definition is same as including an explicit definition for userName with 'lastName', 'firstName' as roles

> @ ml userName hasRoles lastName, firstName
```

Em padrões, você pode usar funções com a notação `{\<entityName\>:\<roleName\>}`. Aqui está um exemplo:

```.lu
# getUserName
- call me {name:userName}
- I'm {name:userName}
- my name is {name:userName}
```

Você também pode definir várias funções para uma entidade em padrões:

```.lu
> roles can be specified for list entity types as well - in this case fromCity and toCity are added as roles to the 'city' list entity defined further below

# BookFlight
- book flight from {city:fromCity} to {city:toCity}
- [can you] get me a flight from {city:fromCity} to {city:toCity}
- get me a flight to {city:toCity}
- I need to fly from {city:fromCity}

$city:Seattle=
- Seattle
- Tacoma
- SeaTac
- SEA

$city:Portland=
- Portland
- PDX
```

## <a name="patterns"></a>Padrões

Os [padrões][10] permitem definir um conjunto de regras que aumentam o modelo de machine learning. Defina padrões no arquivo .lu definindo uma entidade em um enunciado sem um valor rotulado.

Por exemplo, a seguinte definição será tratada como um padrão com `alarmTime` definido como um padrão:

```.lu
# DeleteAlarm
- delete the {alarmTime} alarm
```
Este exemplo será tratado como um enunciado, pois tem um valor rotulado _7h_:
```.lu
# DeleteAlarm
- delete the {alarmTime=7AM} alarm
```

>[!NOTE]
> Qualquer enunciado sem, pelo menos, um valor rotulado será tratado como um padrão. Qualquer entidade sem um valor explícito rotulado usará um padrão.

## <a name="phrase-list-definition"></a>Definição de lista de frases

Uma [lista de frases][11] é uma lista de palavras, frases, números ou outros caracteres que ajudam a encontrar o conceito que você está tentando identificar. A lista não diferencia maiúsculas e minúsculas.

Descreva as entidades da lista de frases usando a seguinte notação:

```.lu
@ phraselist \<Name\>
    - \<synonym1\>
    - \<synonym2\>
```

Este é um exemplo de uma definição de lista de frases:

```.lu
@ phraseList Want
@ phraseList Want =
    - require, need, desire, know

> You can also break up the phrase list values into an actual list

@ phraseList Want =
    - require
    - need
    - desire
    - know
```

Por padrão, os sinônimos são definidos como _não intercambiáveis_. Opcionalmente, você pode definir os sinônimos para que sejam _intercambiáveis_ como parte da definição. Aqui está um exemplo:

```.lu
@ phraselist question(interchangeable) =
    - are you
    - you are
```

As listas de frases podem ser marcadas como `disabled` por meio da seguinte notação:

```.lu
@ phraselist abc disabled

> also same as this
@ phraselist question(interchangeable) =
    - are you
    - you are

@ question disabled
```

Por padrão, as listas de frases são habilitadas em todos os modelos. No entanto, quando você inicia explicitamente a atribuição de listas de frases como um recurso (descritor) a outros modelos, as listas de frases específicas não são habilitadas para todos os modelos. Para sempre disponibilizar explicitamente uma lista de frases para todos os modelos, use o seguinte:

```.lu
@ phraselist abc enabledForAllModels
```

## <a name="tie-features-to-a-specific-model"></a>Vincular recursos a um modelo específico

As listas de frases podem ser adicionadas como um recurso a:

- outra intenção
- outra entidade
- um filho em uma entidade de n camadas

Este é um exemplo de como definir uma lista de frases como um recurso para outro modelo:

```.lu
> phrase list definition

@ phraseList PLCity(interchangeable) =
    - seattle
    - space needle
    - SEATAC
    - SEA

> phrase list as feature to intent (also applicable to entities)

@ intent getUserProfileIntent usesFeature PLCity

> phrase list as a feature to an ml entity.

@ ml myCity usesFeature PLCity

@ regex regexZipcode = /[0-9]{5}/

> phrase list as feature to n-depth entity with phrase list as a feature

@ ml address fromAddress, toAddress
@ address =
    - @ number 'door number'
    - @ ml streetName
    - @ ml location
        - @ ml city usesFeture PLCity
        - @ regexZipcode zipcode
```


## <a name="add-an-entity-or-intent-as-a-feature"></a>Adicionar uma entidade ou uma intenção como um recurso

Veja abaixo exemplos de como adicionar intenções e entidades como um recurso com `usesFeature`:

```.lu
> entity definition - @ <entityType> <entityName> [<roles>]

@ prebuilt personName
@ prebuilt age

> entity definition with roles

@ ml userName hasRoles fistName, lastName

> add entity as a feature to another entity

@ userName usesFeature personName

> add entity as feature to an intent

@ intent getUserNameIntent usesFeature personName

> Intent definition

# getUserNameIntent
- utterances

> multiple entities as a feature to a model

@ intent getUserNameIntent usesFeature age, personName

> intent as a feature to another intent

@ intent getUserProfileIntent usesFeature getUserNameIntent

# getUserProfileIntent
- utterances
```

## <a name="machine-learned-entity-with-children"></a>Entidade de machine learning com filhos

Esta é uma definição de uma entidade ml `address` com `fromAddress` e `toAddress` como duas funções, bem como filhos.

```.lu
@ list listCity
@ prebuilt number
@ prebuilt geographyV2
@ regex regexZipcode = /[0-9]{5}/
@ ml address hasRoles fromAddress, toAddress
@ address =
    - @ number 'door number'
    - @ ml streetName
    - @ ml location usesFeature geographyV2
        - @ listCity city
        - @ regexZipcode zipcode
```

## <a name="utterances"></a>Declarações

[Enunciados][12] são entrada do usuário que seu aplicativo precisa interpretar. Aprendizado ativo, ou o processo de continuar a treinar em novos enunciados, é essencial para a inteligência de aprendizado de máquina que o LUIS fornece. Para treinar o LUIS a extrair intenções e entidades, é importante capturar uma variedade de diferentes exemplos de enunciados para cada intenção.

Colete enunciados que você acredita que os usuários irão inserir. Inclua enunciados com o mesmo significado e uma variedade de construções, incluindo, entre outros:

- Duração do enunciado - curta, média e longa para o aplicativo cliente
- Palavra e comprimento de frase
- Posicionamento de palavras: entidades no início, no meio e no fim dos enunciados
- Gramática
- Pluralização
- Lematização
- Escolher substantivo e verbo
- Pontuação: uma variedade usando pontuação correta e incorreta e sem gramática

Rotule as entidades em enunciados usando a seguinte notação:

```.lu
# getUserProfile
- my name is {@userName = vishwac}

@ ml userName
```

Rotule também as funções diretamente nos enunciados:

```.lu
# getUserProfile
- my name is {@firstName = vishwac}

@ ml userName hasRoles firstName
```

As entidades de machine learning com filhos também podem ser rotuladas:

```.lu
# getUserProfile
- my name is {@userProfile = {@firstName = vishwac}}

@ prebuilt personName

@ ml userProfile
    - @ personName firstName
    - @ personName lastName
```

Para ajudar a rotular as entidades filho com facilidade para os tipos de entidades compostas e de machine learning, você pode dividir os rótulos:

```.lu
# getUserProfile
- my name is vishwac and I'm 36 years old
    - my name is {@userProfile = vishwac and I'm 36 years old}
    - my name is {@firstName = vishwac} and I'm 36 years old
    - my name is vishwac and I'm {@userAge = 36} years old
- i'm {@userProfile = {@firstName = vishwac}}

@ ml userProfile
    - @personName firstName
    - @personName lastName

@ prebuilt personName
```

## <a name="model-description"></a>Descrição do modelo

Inclua informações de configuração do aplicativo LUIS ou da base de dados de conhecimento do QnA Maker no arquivo .lu. Isso ajudará a orientar o analisador a processar o conteúdo do LU corretamente.

Veja como definir as informações de configuração:

```.lu
> !# @<property> = <value>
> !# @<scope>-<property> = <value>
> !# @<scope>-<property> = <semicolon-delimited-key-value-pairs>
```

Observe que todas as informações passadas explicitamente por meio de argumentos da CLI substituirão as informações do arquivo .lu.

```.lu
> Parser instruction - this is optional; unless specified, parser will default to the latest version.
> !# @version = 1.0

> LUIS application description
> !# @app.name = my luis application
> !# @app.desc = description of my luis application
> !# @app.versionId = 0.5
> !# @app.culture = en-us
> !# @app.luis_schema_version = 3.2.0
```

## <a name="external-references"></a>Referências externas

Referencia o arquivo .lu. siga a sintaxe de link Markdown. As referências com suporte incluem:

- Referência a outro arquivo .lu por meio de `[link name](<.lu file name>)`. A referência pode ser um caminho absoluto ou um caminho relativo do arquivo .lu contido.
- Há suporte para a referência a uma pasta com outros arquivos .lu por meio do seguinte:
    - `[link name](<.lu file path>/*)` procura os arquivos .lu no caminho absoluto ou relativo especificado
    - `[link name](<.lu file path>/**)` procura recursivamente os arquivos .lu no caminho absoluto ou relativo especificado, incluindo as subpastas.
- Adicione também referências aos enunciados definidos em um arquivo específico em uma seção de intenção ou como pares de QnA.
    - `[link name](<.lu file path>#<INTENT-NAME>)` localiza todos os enunciados em <NOME-DA-INTENÇÃO> no arquivo .lu e os adiciona à lista de enunciados em que a referência é especificada.
    - `[link name](<.lu file path>#<INTENT-NAME>*utterances*)` localiza todos os enunciados (não padrões) em <NOME-DA-INTENÇÃO> no arquivo .lu e os adiciona à lista de enunciados em que a referência é especificada.
    - `[link name](<.lu file path>#<INTENT-NAME>*patterns*)` localiza todos os padrões (não enunciados) em <NOME-DA-INTENÇÃO> no arquivo .lu e os adiciona à lista de padrões em que a referência é especificada.
    - `[link name](\<.lu file path>#*utterances*)` localiza todos os enunciados no arquivo .lu e os adiciona à lista de enunciados em que a referência é especificada.
    - `[link name](\<.lu file path>#*patterns*)` localiza todos os padrões no arquivo .lu e os adiciona à lista de enunciados em que a referência é especificada.
    - `[link name](\<.lu file path>#*utterancesAndPatterns*)` localiza todos os enunciados e padrões no arquivo .lu e os adiciona à lista de enunciados em que a referência é especificada.
    - `[link name](\<.qna file path>#$name?)` localiza todas as alterações da definição de alteração específica no conteúdo do .qna e as adiciona à lista de enunciados em que a referência é especificada.
    - `[link name](\<.qna file path>#*alterations*?)` localiza todas as alterações do conteúdo do .qna e as adiciona à lista de enunciados em que a referência é especificada.
    - `[link name](\<.qna file path>#?question-to-find?)` localiza todas as perguntas da variação de pergunta específica e as adiciona à lista de enunciados em que a referência é especificada. Observe que qualquer espaço na pergunta precisará ser substituído pelo caractere **-** .
    - `[link name](\<.qna file path>#*answers*?)` localiza todas as respostas e as adiciona à lista de enunciados em que a referência é especificada.

Este é um exemplo das referências mencionadas anteriormente:

```.lu
> You can include references to other .lu files

[All LU files](./all.lu)

> References to other files can have wildcards in them

[en-us](./en-us/*)

> References to other lu files can include subfolders as well.
> /** indicates to the parser to recursively look for .lu files in all subfolders as well.

[all LU files](../**)

> You can include deep references to intents defined in a .lu file in utterances

# None
- [None uttearnces](./all.lu#Help)

> With the above statement, the parser will parse all.lu and extract out all utterances associated with the 'Help' intent and add them under 'None' intent as defined in this file.

> NOTE: This **only** works for utterances as entities that are referenced by the uttearnces in the 'Help' intent will not be brought forward to this .lu file.

# All utterances
> you can use the *utterances* wild card to include all utterances from a lu file. This includes utterances across all intents defined in that .lu file.
- [all.lu](./all.lu#*utterances*)
> you can use the *patterns* wild card to include all patterns from a lu file.
> - [all.lu](./all.lu#*patterns*)
> you can use the *utterancesAndPatterns* wild card to include all utterances and patterns from a lu file.
> - [all.lu](./all.lu#*utterancesAndPatterns*)

> You can include wild cards with deep references to QnA maker questions defined in a .qna file in utterances

# None
- [QnA questions](./*#?)

> With the above statement, the parser will parse **all** .lu files under ./, extract out all questions from QnA pairs in those files and add them under 'None' intent as defined in this file.

> You can include deep references to QnA maker questions defined in a .qna file in utterances

# None
- [QnA questions](./qna1.qna#?)

> With the above statement, the parser will parse qna1.lu and extract out all questions from QnA pairs in that file and add them under 'None' intent as defined in this file.
```

## <a name="additional-information"></a>Informações adicionais:

- Leia [Formato de arquivo .qna](bot-builder-qna-file-format.md) para saber mais sobre os arquivos .qna.

[1]:https://luis.ai
[2]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[3]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types
[4]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types#simple-entity
[5]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types#prebuilt-entity
[6]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types#list-entity
[7]:https://docs.microsoft.com/azure/cognitive-services/luis/reference-entity-composite
[8]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types#regular-expression-entity
[9]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-roles
[10]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-patterns
[11]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature#what-is-a-phrase-list
[12]:https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
