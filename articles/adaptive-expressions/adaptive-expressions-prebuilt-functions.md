---
title: Funções predefinidas de expressões adaptáveis – Serviço de Bot
description: Referência de funções predefinidas de expressões adaptáveis
keywords: expressões adaptáveis, funções predefinidas, referência
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/19/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b2cb0ff374b7b0c6f0636bb72f71f5b1c19b17cd
ms.sourcegitcommit: 5add21ad3daf0ce894612a22b951b98350961720
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420657"
---
# <a name="adaptive-expressions-prebuilt-functions"></a>Funções predefinidas de expressões adaptáveis

[!INCLUDE[applies-to](../includes/applies-to.md)]

Este artigo lista as funções predefinidas disponíveis ordenadas por seu uso geral. As expressões predefinidas são divididas nos seguintes tipos de função:

- [Cadeia de caracteres](#string-functions)
- [Coleção](#collection-functions)
- [Comparação lógica](#logical-comparison-functions)
- [Conversão](#conversion-functions)
- [Matemática](#math-functions)
- [Data](#date-and-time-functions)
- [Timex](#timex-functions)
- [Análise de URI](#uri-parsing-functions)
- [Manipulação e construção de objetos](#object-manipulation-and-construction-functions)
- [Expressão regular](#regular-expression-functions)
- [Verificação de tipo](#type-checking-functions)

Você também pode exibir a lista em [ordem alfabética](#add).

### <a name="string-functions"></a>Funções de cadeia de caracteres

|Função   |Explicação|
|-----------|-----------|
|[length](#length)|Retornar o comprimento de uma cadeia de caracteres.|
|[substitui](#replace)|Substituir uma substring pela cadeia de caracteres especificada e retornar a cadeia de caracteres atualizada. Essa função diferencia maiúsculas de minúsculas.|
|[replaceIgnoreCase](#replaceIgnoreCase)|   Substituir uma subcadeia de caracteres pela cadeia de caracteres especificada e retornar a cadeia de caracteres atualizada. Essa função não diferencia maiúsculas de minúsculas.|
|[split](#split)    |Retornar uma matriz que contém substrings com base no delimitador especificado.|
|[substring](#substring)|Retornar caracteres de uma cadeia de caracteres. substring(sourceString, startPos, endPos). startPos não pode ser menor que 0. Um endPos maior que o comprimento da cadeia de caracteres de origem será obtido como o comprimento máximo da cadeia de caracteres.|
|[toLower](#toLower)|Converter uma cadeia de caracteres em minúsculas.|
|[toUpper](#toUpper)|Converter uma cadeia de caracteres em maiúsculas.|
|[cortar](#trim)|Remover os espaços em branco à esquerda e à direita de uma cadeia de caracteres.|
|[addOrdinal](#addOrdinal)|Retornar o número ordinal do número de entrada.|
|[endsWith](#endsWith)|Verificar se uma cadeia de caracteres termina com uma subcadeia de caracteres específica. Retornar `true` se a substring for encontrada ou retornar `false` se não for encontrada. Essa função não diferencia maiúsculas de minúsculas.|
|[startsWith](#startsWith)|Verificar se uma cadeia de caracteres começa com uma subcadeia de caracteres específica. Retornar `true` se a substring for encontrada ou retornar `false` se não for encontrada. Essa função não diferencia maiúsculas de minúsculas.|
|[countWord](#countWord)|Retornar o número de palavras na cadeia de caracteres fornecida.|
|[concat](#concat)|Combinar duas ou mais cadeias de caracteres e retornar a cadeia de caracteres resultante.|
|[newGuid](#newGuid)|Retornar uma nova cadeia de caracteres Guid.|
|[indexOf](#indexOf)|Retornar a posição inicial ou o valor de índice de uma substring **ou** procurar o objeto especificado e retornar o índice de base zero da primeira ocorrência na lista inteira. Essa função não diferencia maiúsculas de minúsculas, e os índices começam com o número 0.|
|[lastIndexOf](#lastIndexOf)|Retornar a posição inicial ou o valor de índice da última ocorrência de uma substring **ou** pesquisar o objeto especificado e retornar o índice de base zero da última ocorrência dentro do intervalo de elementos na lista. Essa função não diferencia maiúsculas de minúsculas, e os índices começam com o número 0.|

### <a name="collection-functions"></a>Funções de coleção

|Função   |Explicação|
|-----------|-----------|
|[contains](#contains)  |Funciona para localizar um item em uma cadeia de caracteres, localizar um item em uma matriz ou localizar um parâmetro em um objeto complexo. <br> **Exemplos**: <br> contains('hello world', 'hello')<br> contains(createArray('1','2'), '1')<br> contains(json("{'foo':'bar'}"), 'foo')|
|[first](#first)|Retornar o primeiro item da coleção.|
|[join](#join) |Retornar uma cadeia de caracteres que tem todos os itens de uma matriz e tem cada caractere separado por um delimitador. join(collection, delimiter). <br>**Exemplo**: <br> join(createArray('a','b'), '.') = "a.b"|
|[last](#last) |Retornar o último item da coleção.|
|[contagem](#count)|Retornar o número de itens na coleção.|
|[foreach](#foreach)|Operar em cada elemento e retornar a nova coleção.|
|[union](#union)|Retornar uma coleção que tem todos os itens das coleções especificadas.|
|[skip](#skip)|Remover itens do início de uma coleção e retornar os itens restantes.|
|[take](#take)|Retornar itens do início de uma coleção.|
|[intersection](#intersection)|Retornar uma coleção que tem somente os itens comuns entre as coleções especificadas.|
|[subArray](#subArray)|Retornar uma submatriz das posições inicial e final especificadas. Os valores de índice começam com o número 0.|
|[select](#select)|Operar em cada elemento e retornar a nova coleção de elementos transformados.|
|[where](#where)|Filtrar em cada elemento e retornar a nova coleção de elementos filtrados que correspondem à condição específica.|
|[sortBy](#sortBy)|Classificar elementos na coleção em ordem crescente e retornar a coleção classificada.|
|[sortByDescending](#sortByDescending)|Classificar elementos na coleção em ordem decrescente e retornar a coleção classificada.|
|[indicesAndValues](#indicesAndValues)|Transformar uma matriz ou objeto em uma matriz de objetos com a propriedade index e value.|
|[flatten](#flatten)|Nivelar matrizes em uma matriz com valores que não são da matriz.|
|[unique](#unique)|Remover todas as duplicatas de uma matriz.|

### <a name="logical-comparison-functions"></a>Funções de comparação lógica

|Função|Explicação|
|-----------|-----------|
|[and](#and)|And lógico. Retornar true se todas as expressões especificadas forem avaliadas como true.|
|[equals](#equals)|Comparação igual. Retornar true se os valores especificados forem iguais.|
|[empty](#empty)|Verifique se o destino está vazio.|
|[greater](#greater)|Comparação maior que. Retornar `true` se o primeiro valor for maior ou retornar `false` se for menor.|
|[greaterOrEquals](#greaterOrEquals)|Comparação maior que ou igual a. Retornar `true` se o primeiro valor for maior ou igual ou retornar `false` se o primeiro valor for menor.|
|[if](#if)|Verificar se uma expressão é verdadeira ou falsa. Com base no resultado, retornar um valor especificado.|
|[less](#less)| Operação comparativa "menor que". Retornar `true` se o primeiro valor for menor ou retornar `false` se o primeiro valor for maior.|
|[lessOrEquals](#lessOrEquals)| Operação comparativa "menor que ou igual a". Retornar `true` se o primeiro valor for menor ou igual ou retornar `false` se o primeiro valor for maior.|
|[not](#not)|   Operador not lógico. Retornar `true` se a expressão for false ou retornar `false` se for true.|
|[or](#or)|Operação or lógica. Retornar `true` se pelo menos uma expressão for true ou retornar `false` quando todas forem false.|
|[exists](#exists)|Avaliar a veracidade da expressão.|

### <a name="conversion-functions"></a>Funções de conversão

|Função|Explicação|
|-----------|-----------|
|[float](#float)|Retornar a representação de ponto flutuante da cadeia de caracteres especificada ou a própria cadeia de caracteres se a conversão não for possível.|
|[int](#int)|Retornar a representação de inteiro da cadeia de caracteres especificada ou a própria cadeia de caracteres se a conversão não for possível.|
|[cadeia de caracteres](#string)|Retornar a versão de cadeia de caracteres do valor especificado.|
|[bool](#bool)|Retornar uma representação booliana da cadeia de caracteres especificada.|
|[createArray](#createArray)|Criar uma matriz de várias entradas.|
|[json](#json) |Retornar o objeto ou valor de tipo JSON (JavaScript Object Notation) de uma cadeia de caracteres ou XML.|
|[base64](#base64)|Retornar a versão codificada em base64 de uma cadeia de caracteres ou matriz de bytes.|
|[base64ToBinary](#base64ToBinary)|Retornar a versão binária de uma cadeia de caracteres codificada em Base64.|
|[base64ToString](#base64ToString)|Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em base64.|
|[binary](#binary)|Retornar a versão binária de um valor de entrada.|
|[dataUri](#dataUri)|Retornar o URI de um valor de entrada.|
|[dataUriToBinary](#dataUriToBinary)|Retornar a versão binária de um URI de dados.|
|[dataUriToString](#dataUriToString)|Retornar a versão da cadeia de caracteres de um URI de dados.|
|[uriComponent](#uriComponent)|Retornar a versão codificada para URI correspondente a um valor de entrada substituindo caracteres não seguros para URL por caracteres de escape.|
|[uriComponentToString](#uriComponentToString)|Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em URI.|
|[xml](#xml)|Somente C#. Retornar a versão XML de uma cadeia de caracteres.|

### <a name="math-functions"></a>Funções matemáticas

|Função|Explicação|
|-----------|-----------|
|[adicionar](#add)|And matemático. Retornar o resultado de adicionar dois números (caso de número puro) ou concatenar duas ou mais cadeias de caracteres.|
|[div](#div)|Divisão matemática. Retornar o resultado inteiro da divisão de dois números.|
|[max](#max)|Retornar o maior valor de uma coleção.|
|[min](#min)|Retornar o menor valor de uma coleção.|
|[mod](#mod)|Retornar o resto da divisão de dois números.|
|[mul](#mul)|Multiplicação matemática. Retornar o produto da multiplicação de dois números.|
|[rand](#rand)|Retornar um número aleatório entre o valor mínimo e máximo especificado.|
|[sub](#sub)|Subtração matemática. Retornar o resultado da subtração do segundo número do primeiro.|
|[sum](#sum)|Retornar a soma dos números em uma matriz.|
|[range](#range)|Retornar uma matriz de inteiros que começa em um inteiro especificado.|
|[exp](#exp)|Retornar a exponenciação de um número para outro.|
|[average](#average)|Retornar o número médio de uma matriz numérica.|

### <a name="date-and-time-functions"></a>Funções de data e hora

|Função|Explicação|
|-----------|-----------|
|[addDays](#addDays)|Adicionar um número especificado de dias a um carimbo de data/hora fornecido.|
|[addHours](#addHours)|Adicionar um número especificado de horas a um carimbo de data/hora fornecido.|
|[addMinutes](#addMinutes)|Adicionar um número especificado de minutos a um carimbo de data/hora fornecido.|
|[addSeconds](#addSeconds)|Adicionar um número especificado de segundos a um carimbo de data/hora fornecido.|
|[dayOfMonth](#dayOfMonth)|Retornar o dia de um mês de um carimbo de data/hora ou expressão Timex fornecido.|
|[dayOfWeek](#dayOfWeek)|Retornar o dia da semana de um carimbo de data/hora fornecido.|
|[dayOfYear](#dayOfYear)|Retornar o dia do ano de um carimbo de data/hora fornecido.|
|[formatDateTime](#formatDateTime)|Retornar um carimbo de data/hora no formato especificado.|
|[formatEpoch](#formatEpoch)|Retornar um carimbo de data/hora da Época do UNIX (horário do UNIX, hora do POSIX).|
|[formatTicks](#formatTicks)|Retornar um carimbo de data/hora de tiques.|
|[subtractFromTime](#subtractFromTime)|Subtrair um número de unidades de tempo de um carimbo de data/hora.|
|[utcNow](#utcNow)|Retornar o carimbo de data/hora atual como cadeia de caracteres.|
|[dateReadBack](#dateReadBack)|Usar a biblioteca de data e hora para fornecer um readback de data.|
|[month](#month)|Retornar o mês de um carimbo de data/hora fornecido.|
|[date](#date)|Retornar a data de um carimbo de data/hora fornecido.|
|[year](#year)|Retornar o ano de um carimbo de data/hora fornecido.|
|[getTimeOfDay](#getTimeOfDay)|Retornar a hora do dia de um carimbo de data/hora fornecido. |
|[getFutureTime](#getFutureTime)|Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas.   |
|[getPastTime](#getPastTime)|Retornar o carimbo de data/hora atual menos as unidades de tempo especificadas.  |
|[addToTime](#addToTime)   |Adicionar um número de unidades de tempo a um carimbo de data/hora.   |
|[convertFromUTC](#convertFromUTC) |Converter um carimbo de data/hora de UTC (Tempo Universal Coordenado). |
|[convertToUTC](#convertToUTC) |Converter um carimbo de data/hora para UTC (Tempo Universal Coordenado).  |
|[startOfDay](#startOfDay) |Retornar o início do dia de um carimbo de data/hora.|
|[startOfHour](#startOfHour)   |Retornar o início da hora de um carimbo de data/hora. |
|[startOfMonth](#startOfMonth) |Retornar o início do mês de um carimbo de data/hora.|
|[ticks](#ticks)   |Retornar o valor de propriedade ticks de um carimbo de data/hora especificado.|

### <a name="timex-functions"></a>Funções timex

|Função   |Explicação|
|-----------|-----------|
|[isPresent](#isPresent)    | Retornar true se a expressão TimexProperty ou Timex se referir ao presente. |
|[isDuration](#isDuration)    | Retornar true se a expressão TimexProperty ou Timex se referir à duração. |
|[isTime](#isTime)    | Retornar true se a expressão TimexProperty ou Timex se referir ao tempo. |
|[isDate](#isDate)    | Retornar true se a expressão TimexProperty ou Timex se referir à data. |
|[isTimeRange](#isTimeRange)    | Retornar true se a expressão TimexProperty ou Timex se referir ao intervalo de tempo. |
|[isDateRange](#isDateRange)    | Retornar true se a expressão TimexProperty ou Timex se referir ao intervalo de datas. |
|[isDefinite](#isDefinite)    | Retornar true se a expressão TimexProperty ou Timex se referir ao dia definido. |

### <a name="uri-parsing-functions"></a>Funções de análise de URI

|Função|Explicação|
|-----------|-----------|
|[uriHost](#uriHost)   |Retornar o valor de host de um URI (Uniform Resource Identifier).|
|[uriPath](#uriPath)   |Retornar o valor de caminho de um URI (Uniform Resource Identifier). |
|[uriPathAndQuery](#uriPathAndQuery)  |Retornar os valores de caminho e consulta de um URI (Uniform Resource Identifier).  |
|[uriPort](#uriPort)   |Retornar o valor de porta de um URI (Uniform Resource Identifier).|
|[uriQuery](#uriQuery) |Retornar o valor de consulta de um URI (Uniform Resource Identifier).|
|[uriScheme](#uriScheme)|Retornar o valor de esquema de um URI (Uniform Resource Identifier).  |

### <a name="object-manipulation-and-construction-functions"></a>Funções de manipulação e construção de objetos

|Função|Explicação|
|-----------|-----------|
|[addProperty](#addProperty)   |Adicionar uma propriedade e seu valor, ou par nome-valor, a um objeto JSON e retornar o objeto atualizado.|
|[removeProperty](#removeProperty) |Remover uma propriedade de um objeto JSON e retornar o objeto atualizado.|
|[setProperty](#setProperty)   |Definir o valor da propriedade de um objeto JSON e retornar o objeto atualizado.|
|[getProperty](#getProperty)   |Retornar o valor da propriedade fornecida em um objeto JSON. |
|[coalesce](#coalesce) |Retornar o primeiro valor não nulo de um ou mais parâmetros. |
|[xPath](#xPath)   |Somente C#. Verificar o XML em busca de nós ou valores correspondentes a uma expressão XPath (Linguagem XPath) e retornar esses nós ou valores.|
|[jPath](#jPath)   |Marcar JSON ou uma cadeia de caracteres JSON para nós ou valores que correspondam a uma expressão de caminho e retornar os nós correspondentes.|
|[setPathToValue](#setPathToValue)   |Definir o valor de um caminho específico e retornar o valor.|

### <a name="regular-expression-functions"></a>Funções de expressão regular

|Função|Explicação|
|-----------|-----------|
|[isMatch](#isMatch)|Retornar true se uma cadeia de caracteres corresponder a um padrão regex comum.|

### <a name="type-checking-functions"></a>Funções de verificação de tipo

|Função|Explicação|
|-----------|-----------|
|[isInteger](#isInteger)|Retornar true se a entrada fornecida for um número inteiro|
|[isFloat](#isFloat)|Retornar true se a entrada fornecida for um número de ponto flutuante|
|[isBoolean](#isBoolean)|Retornar true se a entrada fornecida for um booliano.|
|[isArray](#isArray)|Retornar true se a entrada fornecida for uma matriz.|
|[isObject](#isObject)|Retornar true se a entrada fornecida for um objeto.|
|[isDateTime](#isDateTime)|Retornar true se a entrada fornecida for um carimbo de data/hora de formato UTC ISO.|
|[isString](#isString)|Retornar true se a entrada fornecida for uma cadeia de caracteres.|

<a name="add"></a>

### <a name="add"></a>add

Retornar o resultado de adicionar dois ou mais números (caso de número puro) ou concatenar duas ou mais cadeias de caracteres (outro caso).

```
add(<item1>, <item2>, ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*item1*>, <*item2*>,... | Sim | any | itens |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*result-sum*> | número ou cadeia de caracteres | O resultado de adicionar os números especificados ou do resultado de concatenar.|
||||

*Exemplo*

Esse exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E retorna o resultado **2,5**.

Este exemplo concatena os itens especificados:

```
add('hello',null)
add('hello','world')
```

E retorna os resultados
- **hello**
- **helloworld**

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicionar um número de dias a um carimbo de data/hora.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | A cadeia de caracteres que contém o carimbo de data/hora que deve estar no formato UTC ISO padrão<br>AAAA-MM-DDTHH:mm:ss.fffZ |
| <*days*> | Sim | inteiro | O número positivo ou negativo de dias a ser adicionado |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora mais o número de dias especificado  |
||||

*Exemplo 1*

Esse exemplo adiciona 10 dias ao carimbo de data/hora especificado:

```
addDays('2018-03-15T13:00:00.000Z', 10)
```

E retorna o resultado **2018-03-25T00:00:00.000Z**.

*Exemplo 2*

Esse exemplo subtrai cinco dias do carimbo de data/hora especificado:

```
addDays('2018-03-15T00:00:00.000Z', -5)
```

E retorna o resultado **2018-03-10T00:00:00.000Z**.

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicionar um número de horas a um carimbo de data/hora.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*hours*> | Sim | inteiro | O número positivo ou negativo de horas a ser adicionado |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora mais o número de horas especificado  |
||||

*Exemplo 1*

Esse exemplo adiciona 10 horas ao carimbo de data/hora especificado:

```
addHours('2018-03-15T00:00:00.000Z', 10)
```

E retorna o resultado **2018-03-15T10:00:00.000Z**.

*Exemplo 2*

Esse exemplo subtrai cinco horas do carimbo de data/hora especificado:

```
addHours('2018-03-15T15:00:00.000Z', -5)
```

E retorna o resultado **2018-03-15T10:00:00.000Z**.

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicionar um número de minutos a um carimbo de data/hora.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*minutes*> | Sim | inteiro | O número positivo ou negativo de minutos a ser adicionado |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora mais o número de minutos especificado |
||||

*Exemplo 1*

Esse exemplo adiciona 10 minutos ao carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:10:00.000Z', 10)
```

E retorna o resultado **2018-03-15T00:20:00.000Z**.

*Exemplo 2*

Esse exemplo subtrai cinco minutos do carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:20:00.000Z', -5)
```

E retorna o resultado **2018-03-15T00:15:00.000Z**.

<a name="addOrdinal"></a>

### <a name="addordinal"></a>addOrdinal

Retornar o número ordinal do número de entrada.

```
addOrdinal(<number>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*number*>| Sim | inteiro | Os números a serem convertidos em um número ordinal |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*result*> | string | O número ordinal convertido do número de entrada |
||||

*Exemplo*

```
addOrdinal(11)
addOrdinal(12)
addOrdinal(13)
addOrdinal(21)
addOrdinal(22)
addOrdinal(23)
```

E, respectivamente, retorna estes resultados:
* **11º**
* **12º**
* **13º**
* **21º**
* **22º**
* **23º**

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Adicionar uma propriedade e seu valor, ou par nome-valor, a um objeto JSON e retornar o objeto atualizado. Se o objeto já existir em runtime, a função gerará um erro.

```
addProperty('<object>', '<property>', value)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | objeto | O objeto JSON em que você deseja adicionar uma propriedade |
|<*property*>| Sim | string | O nome da propriedade a ser adicionada |
|<*value*>| Sim | any | O valor da propriedade |

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | objeto | O objeto JSON atualizado após a adição de uma nova propriedade |
||||

*Exemplo*<br>
Este exemplo adiciona a propriedade **accountNumber** ao objeto **customerProfile** convertido em JSON com a função [json()](#json). A função atribui um valor gerado pela função [newGuid()](#newGuid) e retorna o objeto atualizado:

```
addProperty(json('customerProfile'), 'accountNumber', newGuid())
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adicionar um número de segundos a um carimbo de data/hora.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*seconds*> | Sim | inteiro | O número positivo ou negativo de segundos a ser adicionado |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora mais o número de segundos especificado  |
||||

*Exemplo 1*

Esse exemplo adiciona 10 segundos ao carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:00.000Z', 10)
```

E retorna o resultado **2018-03-15T00:00:10.000Z**.

*Exemplo 2*

Esse exemplo subtrai cinco segundos ao carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:30.000Z', -5)
```

E retorna o resultado **2018-03-15T00:00:25.000Z**.

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Adicionar um número de unidades de tempo a um carimbo de data/hora. Consulte também [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', '<interval>', <timeUnit>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | inteiro | O número de unidades de tempo especificadas a ser adicionado |
| <*timeUnit*> | Sim | string | A unidade de tempo a ser usada com *interval*. As unidades possíveis são "Second", "Minute", "Hour", "Day", "Week", "Month" e "Year". |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora mais o número de unidades de tempo especificadas com o formato fornecido. |
||||

*Exemplo 1*<br>
Este exemplo adiciona um dia ao carimbo de data/hora especificado.

```
addToTime('2018-01-01T00:00:00.000Z', 1, 'Day')
```

E retorna o resultado **2018-01-02T00:00:00.000Z**.

*Exemplo 2*<br>
Este exemplo adiciona duas semanas ao carimbo de data/hora especificado.

```
addToTime('2018-01-01T00:00:00.000Z', 2, 'Week', 'MM-DD-YY')
```

E retorna o resultado no formato 'MM-DD-AA' como **01-15-18**.


<a name="and"></a>

### <a name="and"></a>e

Verificar se todas as expressões são verdadeiras. Retornar `true` quando todas as expressões forem true ou retornar `false` quando pelo menos uma expressão for false.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Sim | Boolean | As expressões a serem verificadas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| true ou false | Boolean | Retornar `true` se todas as expressões forem true. Retornar `false` se pelo menos uma expressão for false. |
||||

*Exemplo 1*

Esses exemplos verificam se os valores boolianos são todos true:

```
and(true, true)
and(false, true)
and(false, false)
```

E, respectivamente, retorna estes resultados:

* Ambas as expressões são true, então as funções retornam `true`.
* Uma expressão é false, então as funções retornam `false`.
* Ambas as expressões são false, então a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são todas true:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E, respectivamente, retorna estes resultados:

* Ambas as expressões são true, então as funções retornam `true`.
* Uma expressão é false, então as funções retornam `false`.
* Ambas as expressões são false, então as funções retornam `false`.

<a name="average"></a>

### <a name="average"></a>média

Retornar a média numérica de uma matriz numérica.

```
average(<numericArray>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*numericArray*> | Sim | matriz de números | A matriz de entrada para calcular a média |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*average-of-array*> | número | O valor médio da matriz fornecida |
||||

*Exemplo*

Este exemplo calcula a média da matriz em `createArray()`:

```
average(createArray(1,2,3))
```

E retorna o resultado **2**.

<a name="base64"></a>

### <a name="base64"></a>base64

Retornar a versão codificada em base64 de uma cadeia de caracteres ou matriz de bytes.

```
base64('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres ou matriz de bytes | A cadeia de caracteres de entrada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | string | A versão codificada em base64 da cadeia de caracteres de entrada |
||||

*Exemplo 1*

Este exemplo converte a cadeia de caracteres **hello** em uma cadeia de caracteres codificada em base64:

```
base64('hello')
```

E retorna o resultado **"aGVsbG8="** .

*Exemplo 2*

Este exemplo usa byteArr, que é igual a **new byte[] { 3, 5, 1, 12 }** :

```
base64('byteArr')
```

E retorna o resultado **"AwUBDA=="** .

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retornar a matriz binária de uma cadeia de caracteres codificada em base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres codificada em Base64 a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | matriz de bytes | A versão binária da cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres codificada em base64 **AwUBDA==** em uma cadeia de caracteres binária:

```
base64ToBinary('AwUBDA==')
```

E retorna o resultado **new byte[] { 3, 5, 1, 12 }** .

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retornar a versão da cadeia de caracteres para uma cadeia de caracteres codificada em base64, decodificando com eficiência a cadeia de caracteres base64.

```
base64ToString('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres codificada em Base64 a ser decodificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | string | A versão de cadeia de caracteres de uma cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres codificada em base64 **aGVsbG8=** em uma cadeia de caracteres decodificada:

```
base64ToString('aGVsbG8=')
```

E retorna o resultado **hello**.

<a name="binary"></a>

### <a name="binary"></a>binary

Retornar a versão binária de uma cadeia de caracteres.

```
binary('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | matriz de bytes | A versão binária para a cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres **hello** em uma cadeia de caracteres binária:

```
binary('hello')
```

E retorna o resultado **new byte[] { 104, 101, 108, 108, 111 }** .

<a name="bool"></a>

### <a name="bool"></a>bool

Retornar a versão booliana de um valor.

```
bool(<value>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | any | O valor a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | A versão booliana do valor especificado |
||||

*Exemplo*

Esses exemplos convertem os valores especificados em valores boolianos:

```
bool(1)
bool(0)
```

E, respectivamente, retorna estes resultados:

* `true`
* `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Retornar o primeiro valor não nulo de um ou mais parâmetros. Cadeias de caracteres vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object**1>, <object**2>, ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object**1*>, <*object**2*>, ... | Sim | Qualquer um (tipos mistos aceitáveis) | Um ou mais itens para verificar se são nulos|
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | any | O primeiro item ou valor não nulo. Se todos os parâmetros forem nulos, essa função retornará null. |
||||

*Exemplo*<br>
Estes exemplos retornarão o primeiro valor não nulo dos valores especificados ou null quando todos os valores forem nulos.

Estes são alguns exemplos:
```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Eles, respectivamente, retornam estes resultados:

- `true`
- **hello**
- nulo

<a name="concat"></a>

### <a name="concat"></a>concat

Combinar duas ou mais cadeias de caracteres e retornar a cadeia de caracteres combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Sim | string | Pelo menos duas cadeias de caracteres a serem combinadas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*text1text2...* > | string | A cadeia de caracteres criada das cadeias de caracteres de entrada combinadas |
||||

*Exemplo*

Este exemplo combina as cadeias de caracteres **Hello** e **World**:

```
concat('Hello', 'World')
```

E retorna o resultado **HelloWorld**.

<a name="contains"></a>

### <a name="contains"></a>contains

Verificar se uma coleção tem um item específico. Retornar `true` se o item for encontrado ou retornar `false` se não for encontrado. Essa função diferencia maiúsculas de minúsculas.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Essa função funciona nos seguintes tipos de coleção:

* Uma cadeia de caracteres para localizar uma substring
* Uma matriz para localizar um valor
* Um dicionário para localizar uma chave

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | Cadeia de caracteres, matriz ou dicionário | A coleção a ser verificada |
| <*value*> | Sim | Cadeia de caracteres, matriz ou dicionário, respectivamente | O item a ser localizado |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` se o item for encontrado. Retornar `false` se não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se há a substring **world** na cadeia de caracteres **hello world**:

```
contains('hello world', 'world')
```

E retorna o resultado `true`.

*Exemplo 2*

Este exemplo verifica se há a substring **universe** na substring **hello world**:

```
contains('hello world', 'universe')
```

E retorna o resultado `false`.

<a name="count"></a>

### <a name="count"></a>count

Retornar o número de itens em uma coleção.

```
count('<collection>')
count([<collection>])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | cadeia de caracteres ou matriz | A coleção com os itens a serem contados |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*length-or-count*> | inteiro | O número de itens na coleção |
||||

*Exemplos*:

Esses exemplos contam o número de itens nestas coleções:

```
count('abcd')
count(createArray(0, 1, 2, 3))
```

E ambas retornam o resultado **4**.

<a name="countWord"></a>

### <a name="countword"></a>countWord

Retornar o número de palavras na cadeia de caracteres

```
countWord('<text>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres a ser contada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*count*> | inteiro | O número de palavras na cadeia de caracteres |
||||

*Exemplo*

Este exemplo conta o número de palavras na cadeia de caracteres **hello world**:

```
countWord("hello word")
```

E retorna o resultado **2**.

<a name="convertFromUTC"></a>

### <a name="convertfromutc"></a>convertFromUTC

Converter um carimbo de data/hora de UTC (Tempo Universal Coordenado) para o fuso horário de destino.

```
convertFromUTC('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*destinationTimeZone*> | Sim | string | O nome do fuso horário de destino. Compatível com fusos horários Windows e Iana. |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | O carimbo de data/hora convertido na região do fuso horário |
||||

*Exemplos*:

Estes exemplos convertem de UTC para Horário Padrão do Pacífico:

```
convertFromUTC("convertFromUTC('2018-02-02T02:00:00.000Z', 'Pacific Standard Time', 'MM-DD-YY')"
convertFromUTC('2018-02-02T02:00:00.000Z', 'Pacific Standard Time')
```

E, respectivamente, retornam estes resultados:

* **02-01-18**
* **2018-02-01T18:00:00.000-08:00**

<a name="convertToUTC"></a>

### <a name="converttoutc"></a>convertToUTC

Converter um carimbo de data/hora do fuso horário de origem para UTC (Tempo Universal Coordenado).

```
convertToUTC('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | string | O nome do fuso horário de destino. Compatível com fusos horários Windows e Iana. |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | O carimbo de data/hora convertido na região do fuso horário |
||||

*Exemplo*

```
convertToUTC('01/01/2018 00:00:00',', 'Pacific Standard Time')
```

Retorna o resultado **2018-01-01T08:00:00.000 Z**.

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retornar uma matriz de várias entradas.

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Sim | Qualquer um, mas não misto | Pelo menos dois itens para criar a matriz |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | matriz | A matriz criada com base em todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz com base nestas entradas:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E retorna o resultado **[h, e, l, l, o]** .

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Retornar um URI (Uniform Resource Identifier) de dados de uma cadeia de caracteres.

```
dataUri('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*>| Sim | string | A cadeia de caracteres a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*date-uri*>] | string | O URI dos dados da cadeia de caracteres de entrada |
||||

*Exemplo*

```
dataUri('hello')
```

Retorna o resultado **data:text/plain;charset=utf-8;base64,aGVsbG8=** .

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Retornar a versão binária de um URI (Uniform Resource Identifier) de dados.

```
dataUriToBinary('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*>| Sim | string | O URI de dados a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*binary-for-data-uri*>] | matriz de bytes | A versão binária do URI de dados |
||||

*Exemplo*

Este exemplo cria uma versão binária para o seguinte URI de dados:

```
dataUriToBinary('aGVsbG8=')
```

E retorna o resultado **new byte[] { 97, 71, 86, 115, 98, 71, 56, 61 }** .

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Retornar a versão de cadeia de caracteres de um URI (Uniform Resource Identifier) de dados.

```
dataUriToString('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*>| Sim | string | O URI de dados a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*string-for-data-uri*>] | string | A versão de cadeia de caracteres do URI de dados |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres para o seguinte URI de dados:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna o resultado **hello**.

<a name="date"></a>

### <a name="date"></a>date

Retornar a data de um carimbo de data/hora especificado no formato **m/dd/aaaa**.

```
date('<timestramp>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*date*> | string | A data do carimbo de data/hora especificado |
||||

```
date('2018-03-15T13:00:00.000Z')
```

Retorna o resultado **3-15-2018**.

<a name="dateReadBack"></a>

### <a name="datereadback"></a>dateReadBack

Usar a biblioteca de data e hora para fornecer um readback de data.

```
dateReadBack('<currentDate>', '<targetDate>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*currentDate*> | Sim | string | Uma cadeia de caracteres que contém a data atual |
| <*targetDate*> | Sim | string | Uma cadeia de caracteres que contém a data de destino |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*date-readback*> | string | O readback entre a data atual e a data de destino  |
||||

*Exemplo 1*

```
dateReadBack('2018-03-15T13:00:00.000Z', '2018-03-16T13:00:00.000Z')
```

Retorna o resultado em **tomorrow**.

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retorna o dia do mês de um carimbo de data/hora.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-month*> | inteiro | O dia do mês do carimbo de data/hora especificado |
||||

*Exemplo*

Este exemplo retorna o número do dia do mês do seguinte carimbo de data/hora:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E retorna o resultado **15**.

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retornar o dia da semana de um carimbo de data/hora.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-week*> | inteiro | O dia da semana do carimbo de data/hora especificado. O domingo é 0, a segunda-feira é 1 e assim por diante.  |
||||

*Exemplo*

Este exemplo retorna o número do dia da semana do seguinte carimbo de data/hora:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E retorna o resultado **3**.

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retorna o dia do ano de um carimbo de data/hora.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-year*> | inteiro | O dia do ano do carimbo de data/hora especificado |
||||

*Exemplo*

Este exemplo retorna o número do dia do ano do seguinte carimbo de data/hora:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E retorna o resultado **74**.

<a name="div"></a>

### <a name="div"></a>div

Retornar o resultado inteiro da divisão de dois números. Para retornar o restante, confira [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | número | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | número | O número que divide o *dividendo*. Não pode ser 0. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*quotient-result*> | número | O resultado da divisão do primeiro número pelo segundo |
||||

*Exemplo*

Os dois exemplos dividem o primeiro número pelo segundo:

```
div(10, 5)
div(11, 5)
```

E retorna o resultado **2**.

Se um dos parâmetros for um float, o resultado também será um float.

*Exemplo*

```
div(11.2, 3)
```

Retorna o resultado **5,5**.

<a name="empty"></a>

### <a name="empty"></a>empty

Verifique se uma instância está vazia. Retorne `true` se a entrada estiver vazia.
Vazio significa:

- a entrada é nula ou indefinida
- a entrada é uma cadeia de caracteres nula ou vazia
- a entrada tem uma coleção de tamanho zero
- a entrada é um objeto sem propriedade.

```
empty('<instance>')
empty([<instance>])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*instância*> | Sim | any | A instância a ser verificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retorne `true` quando a instância estiver vazia.|
||||

*Exemplo*

Esses exemplos verificam se a instância especificada está vazia:

```
empty('')
empty('abc')
empty([1])
empty(null)
```

E retornam estes resultados, respectivamente:

* Passa uma cadeia de caracteres vazia para que a função retorne `true`.
* Passa uma cadeia de caracteres **abc**, para que a função retorne `false`.
* Passa a coleção com um item, de modo que a função retorna `false`.
* Passa o objeto nulo, de modo que a função retorna `true`.

<a name="endsWith"></a>

### <a name="endswith"></a>endsWith

Verificar se uma cadeia de caracteres termina com uma subcadeia de caracteres específica. Retornar `true` se a substring for encontrada ou retornar `false` se não for encontrada. Essa função não diferencia maiúsculas de minúsculas.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | string | A subcadeia de caracteres final a ser localizada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` quando a substring final for localizada. Retornar `false` se não for encontrado |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia de caracteres **hello world** termina com a cadeia de caracteres **world**:

```
endsWith('hello world', 'world')
```

E retorna o resultado `true`.

*Exemplo 2*

Este exemplo verifica se a cadeia de caracteres **hello world** termina com a cadeia de caracteres **universe**:

```
endsWith('hello world', 'universe')
```

E retorna o resultado `false`.

<a name="equals"></a>

### <a name="equals"></a>equals

Verificar se os valores, expressões ou objetos são equivalentes. Retornar `true` quando ambos forem equivalentes ou retornar `false` quando não forem equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Sim | any | Os valores, expressões ou objetos a serem comparados |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` quando ambos forem equivalentes. Retornar `false` se não for equivalente. |
||||

*Exemplo*

Estes exemplos verificam se as entradas especificadas são equivalentes:

```
equals(true, 1)
equals('abc', 'abcd')
```

E retornam estes resultados, respectivamente:

* Ambos os valores são equivalentes, então a função retorna `true`.
* Ambos os valores não são equivalentes, então a função retorna `false`.

<a name="exists"></a>

### <a name="exists"></a>exists

Avaliar a veracidade da expressão.

```
exists(expression)
```

| Parâmetro | Obrigatório | Type | Descrição |
|-----------|----------|------|-------------|
| expressão | Sim | expressão | Expressão da qual a veracidade será avaliada |
|||||

| Valor retornado | Type | Descrição |
|--------------|------|-------------|
| <*true ou false*> | Boolean | Resultado da avaliação da expressão |

*Exemplo*

Estes exemplos avaliam a veracidade de `foo = {"bar":"value"}`:

```
exists(foo.bar)
exists(foo.bar2)
```

E retornam estes resultados, respectivamente:
- `true`
- `false`

<a name="exp"></a>

### <a name="exp"></a>exp

Retornar a exponenciação de um número para outro.

```
exp(realNumber, exponentNumber)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| realNumber | Sim | número | Número real do qual o expoente será calculado |
| exponentNumber | Sim | número | Número do expoente |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*result-exp*> | número | O resultado do cálculo do expoente de `realNumber` |
||||

*Exemplo*

Este exemplo calcula o expoente:

```
exp(2, 2)
```

E retorna o resultado **4**.

<a name="first"></a>

### <a name="first"></a>first

Retornar o primeiro item de uma cadeia de caracteres ou de uma matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | cadeia de caracteres ou matriz | A coleção na qual localizar o primeiro item |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | any | O primeiro item na coleção |
||||

*Exemplo*

Estes exemplos localizam o primeiro item nas seguintes coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E retornam estes resultados, respectivamente:
* **h**
* **0**

<a name="flatten"/>

### <a name="flatten"></a>mesclar

Nivelar uma matriz em valores que não são da matriz.  Opcionalmente, defina a profundidade máxima de nivelamento.

```
flatten([<collection>], '<depth>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | matriz | Coleção a ser nivelada |
| <*depth*> | Não | número | Profundidade máxima de nivelamento. O padrão é infinito. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*new-collection*> | matriz | Nova coleção cujos elementos foram nivelados para uma não matriz na profundidade especificada |
||||

*Exemplo 1*

Este exemplo nivela a seguinte matriz:

```
flatten(createArray(1, createArray(2), createArray(createArray(3, 4), createArray(5, 6)))
```

E retorna o resultado **[1, 2, 3, 4, 5, 6]** .

*Exemplo 2*

Este exemplo nivela a matriz com uma profundidade de **1**:

```
flatten(createArray(1, createArray(2), createArray(createArray(3, 4), createArray(5, 6)), 1)
```

E retorna o resultado **[1, 2, [3, 4], [5, 6]]** .

<a name="float"></a>

### <a name="float"></a>FLOAT

Converter uma versão de cadeia de caracteres de um número de ponto flutuante em um número de ponto flutuante. É possível usar essa função apenas ao passar parâmetros personalizadas para um aplicativo, como um aplicativo lógico.

```
float('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres que tem um número de ponto flutuante válido a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*float-value*> | FLOAT | O número de ponto flutuante da cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo cria uma versão de cadeia de caracteres do seguinte número de ponto flutuante:

```
float('10.333')
```

E retorna a cadeia de caracteres resultante **10,333**.

<a name="foreach"></a>

### <a name="foreach"></a>foreach

Operar em cada elemento e retornar a nova coleção.

```
foreach([<collection/instance>], <iteratorName>, <function>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection/instance*> | Sim | objeto ou matriz | A coleção com os itens |
| <*iteratorName*> | Sim | Nome do iterador | O item principal da função de seta |
| <*function*> | Sim | expressão | Função que contém `iteratorName` |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*new-collection*> | matriz | A nova coleção na qual cada elemento foi avaliado pela função  |
||||

*Exemplo 1*

Este exemplo gera uma nova coleção:

```
foreach(createArray(0, 1, 2, 3), x, x + 1)
```

E retorna o resultado **[1, 2, 3, 4]** .

*Exemplo 2*

Este exemplo gera uma nova coleção:

```
foreach(json("{'name': 'jack', 'age': '15'}"), x, concat(x.key, ':', x.value))
```

E retorna o resultado **['name:jack', 'age:15']** .

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Retornar um carimbo de data/hora no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | string | O carimbo de data/hora atualizado no formato especificado |
||||

*Exemplos*

Estes exemplos convertem um carimbo de data/hora ou um carimbo de data/hora Unix para o formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E retorna os seguintes resultados:

- **2018-03-15T12:00:00**

<a name="formatEpoch"></a>

### <a name="formatepoch"></a>formatEpoch

Retorna um carimbo de data/hora no formato especificado da hora do UNIX (também conhecida como hora da Época, hora do POSIX, hora da Época do UNIX).

```
formatEpoch('<epoch>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*epoch*> | Sim | número | O número de época |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | string | O carimbo de data/hora atualizado no formato especificado |
||||

*Exemplos*

Este exemplo converte um carimbo de data/hora do Unix no formato especificado:

```
formatEpoch(1521118800, 'yyyy-MM-ddTHH:mm:ss.fffZ)'
```

E retorna o resultado **2018-03-15T12:00:00.000Z**.


<a name="formatTicks"></a>

### <a name="formatticks"></a>formatTicks

Retornar um carimbo de data/hora no formato especificado de tiques.

```
formatTicks('<ticks>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*epoch*> | Sim | número (ou bigint em JavaScript)| O número de tiques |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | string | O carimbo de data/hora atualizado no formato especificado |
||||

*Exemplos*

Este exemplo converte tiques no formato especificado:

```
formatTicks(637243624200000000, 'yyyy-MM-ddTHH:mm:ss.fffZ)'
```

E retorna o resultado **2020-05-06T11:47:00.000Z**.

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | inteiro | O número de unidades de tempo especificadas a serem adicionadas |
| <*timeUnit*> | Sim | string | A unidade de tempo a ser usada com *interval*. As unidades possíveis são "Second", "Minute", "Hour", "Day", "Week", "Month" e "Year".|
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora atual mais o número de unidades de tempo especificado |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora seja **2019-03-01T00:00:00.000Z**. O exemplo abaixo adiciona cinco dias a esse carimbo de data/hora:

```
getFutureTime(2, 'Week')
```

E retorna o resultado **2019-03-15T00:00:00.000Z**.

*Exemplo 2*

Suponha que o carimbo de data/hora seja **2018-03-01T00:00:00.000Z**. O exemplo abaixo adiciona cinco dias ao carimbo de data/hora e converte o resultado para o formato **MM-DD-AA**:

```
getFutureTime(5, 'Day', 'MM-DD-YY')
```

E retorna o resultado **03-06-18**.

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retornar o carimbo de data/hora atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | inteiro | O número de unidades de tempo especificadas a ser subtraído |
| <*timeUnit*> | Sim | string | A unidade de tempo a ser usada com *interval*. As unidades possíveis são "Second", "Minute", "Hour", "Day", "Week", "Month" e "Year". |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora atual menos o número de unidades de tempo especificado |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora seja **2018-02-01T00:00:00.000Z**. Esse exemplo adiciona cinco dias a esse carimbo de data/hora:

```
getPastTime(5, 'Day')
```

E retorna o resultado **2019-01-27T00:00:00.000Z**.

*Exemplo 2*

Suponha que o carimbo de data/hora seja **2018-03-01T00:00:00.000Z**. Este exemplo adiciona cinco dias ao carimbo de data/hora e converte o resultado para o formato **MM-DD-AA**:

```
getPastTime(5, 'Day', 'MM-DD-YY')
```

E retorna o resultado **02-26-18**.

<a name="getProperty"></a>

### <a name="getproperty"></a>getProperty

Recuperar o valor da propriedade especificada do objeto JSON.

```
getProperty(<JSONObject>, '<Property>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*JSONObject*> | Sim | objeto | O objeto JSON contém a propriedade e o valor que você deseja obter |
| <*property*> | Sim | string | A propriedade especificada que você deseja obter do objeto JSON |

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| value | objeto | O valor da propriedade especificada que você deseja obter do objeto JSON|
||||

*Exemplo*

Este exemplo obtém as propriedades de **item = {'name': 'myName', 'age': 18, 'state': ['single', 'junior', 'Grade A']}** :

```
getProperty(item, 'state')
```

E retorna o resultado **['single', 'junior', 'Grade A']** .

<a name="getTimeOfDay"></a>

### <a name="gettimeofday"></a>getTimeOfDay

Retornar a hora do dia de um carimbo de data/hora fornecido.

```
getTimeOfDay('<timestamp>')
```

A hora retornada é uma das cadeias de caracteres a seguir:


| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora especificado |

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*time-of-day*> | string | A hora do dia do carimbo de data/hora especificado|
||||

Abaixo estão listadas as cadeias de caracteres associadas à hora do dia:

|Hora do dia | Timestamp |
|---|---|
| meia-noite | 00:00 |
| manhã | 00:01 – 11:59 |
| tarde | 12:00 |
| noite | 18:00 – 22:00 |
| noite | 22:01 – 23:59 |


*Exemplo*

```
getTimeOfDay('2018-03-15T08:00:00.000Z')
```

Retorna o resultado **manhã**.

<a name="greater"></a>

### <a name="greater"></a>greater

Verificar se o primeiro valor é maior que o segundo valor. Retornar `true` se o primeiro valor for maior ou retornar `false` se for menor.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é maior do que o segundo |
| <*compareTo*> | Sim | inteiro, float ou cadeia de caracteres, respectivamente  | O valor de comparação |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` se o primeiro valor for maior que o segundo. Retornar `false` se o primeiro valor for igual ou menor que o segundo. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é maior que o segundo:

```
greater(10, 5)
greater('apple', 'banana')
```

E retornam os seguintes resultados, respectivamente:

* `true`
* `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verificar se o primeiro valor é superior ou igual ao segundo. Retornar `true` quando o primeiro valor for maior ou igual ou retornar `false` se o primeiro valor for inferior.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é superior ou igual ao segundo |
| <*compareTo*> | Sim | inteiro, float ou cadeia de caracteres, respectivamente | O valor de comparação |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` se o primeiro valor for maior ou igual ao segundo. Retornar `false` se o primeiro valor for menor que o segundo. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é superior ou igual ao segundo:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E retornam os seguintes resultados, respectivamente:

* `true`
* `false`

<a name="if"></a>

### <a name="if"></a>if

Verificar se uma expressão é verdadeira ou falsa. Com base no resultado, retornar um valor especificado.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Boolean | A expressão a ser verificada |
| <*valueIfTrue*> | Sim | any | O valor a ser retornado se a expressão for true |
| <*valueIfFalse*> | Sim | any | O valor a ser retornado se a expressão for false |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | any | O valor especificado retornado dependendo se a expressão for `true` ou `false` |
||||

*Exemplo*

Este exemplo avalia se `equals(1,1)` é true:

```
if(equals(1, 1), 'yes', 'no')
```

E retorna **yes**, porque a expressão especificada retorna `true`. Caso contrário, o exemplo retorna **no**.


<a name="indexOf"></a>

### <a name="indexof"></a>indexOf

Retornar o valor de índice ou da posição inicial de uma substring. Essa função não diferencia maiúsculas de minúsculas, e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | cadeia de caracteres ou matriz | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | string | A subcadeia de caracteres a ser localizada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*index-value*> | inteiro | O valor de índice ou da posição inicial da substring especificada.
Se a cadeia de caracteres não for localizada, retornará o número -1. |
||||

*Exemplo 1*

Este exemplo localiza o valor de índice inicial da substring **world** na cadeia de caracteres **hello world**:

```
indexOf('hello world', 'world')
```

E retorna o resultado **6**.

*Exemplo 2*

Este exemplo localiza o valor de índice inicial da substring **def** na matriz **['abc', 'def', 'ghi']** :
```
indexOf(createArray('abc', 'def', 'ghi'), 'def')
```

E retorna o resultado **1**.

<a name="indicesAndValues"></a>

### <a name="indicesandvalues"></a>indicesAndValues

Transformar uma matriz ou objeto em uma matriz de objetos com a propriedade index (index atual) e value. Para matrizes, o índice é a posição na matriz.  Para objetos, é a chave do valor.

```
indicesAndValues('<collection or object>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection ou object*> | Sim | objeto ou matriz | Objeto ou matriz original |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*collection*> | matriz | Nova matriz. Cada item tem duas propriedades: o índice com a posição em uma matriz ou a chave para um objeto e o valor correspondente. |
||||

*Exemplo 1*

Digamos que você tenha uma lista **{ items: ["zero", "one", "two"] }** . A seguinte função usa essa lista:

```
indicesAndValues(items)
```

E retorna uma nova lista:

```
[
  {
    index: 0,
    value: 'zero'
  },
  {
    index: 1,
    value: 'one'
  },
  {
    index: 2,
    value: 'two'
  }
]
```

*Exemplo 2*

Digamos que você tenha uma lista **{ items: ["zero", "one", "two"] }** . A seguinte função usa essa lista:

```
where(indicesAndValues(items), elt, elt.index >= 1)
```

E retorna uma nova lista:
```
[
  {
    index: 1,
    value: 'one'
  },
  {
    index: 2,
    value: 'two'
  }
]
```

*Exemplo 3*

Digamos que você tenha uma lista **{ items: ["zero", "one", "two"] }** . A seguinte função usa essa lista:

```
join(foreach(indicesAndValues(items), item, item.value), ',')
```

E retorna o resultado **zero,one,two**. Essa expressão tem o mesmo efeito que [join(items, ',')](#join).

*Exemplo 4*

Digamos que você tenha um objeto **{ user: {name: 'jack', age: 20} }** . A seguinte função usa esse objeto:

```
indicesAndValues(user)
```

E retorna um novo objeto:
```
[
  {
    index: 'name',
    value: 'jack'
  },
  {
    index: 'age',
    value: 20
  }
]
```

<a name="int"></a>

### <a name="int"></a>INT

Retornar a versão em inteiros de uma cadeia de caracteres.

```
int('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*integer-result*> | inteiro | A versão em inteiros da cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo cria uma versão em inteiros da cadeia de caracteres **10**:

```
int('10')
```

E retorna o resultado como o inteiro **10**.

<a name="intersection"></a>

### <a name="intersection"></a>interseção

Retornar uma coleção que tem somente os itens comuns entre as coleções especificadas. Para aparecer no resultado, um item deve ser exibido em todas as coleções passadas para essa função. Se um ou mais itens tiverem o mesmo nome, o último item com o nome será exibido neste resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>  | Sim | matriz ou objeto, mas não ambos | As coleções das quais você deseja somente os itens comuns |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*common-items*> | matriz ou objeto, respectivamente | Uma coleção que tem somente os itens comuns entre as coleções especificadas |
||||

*Exemplo*

Este exemplo localiza os itens comuns entre as seguintes matrizes:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E retorna uma matriz apenas com os itens **[1, 2]** .

<a name="isArray"></a>

### <a name="isarray"></a>isArray

Retornar `true` se uma entrada fornecida for uma matriz.

```
isArray('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | any | A entrada a ser testada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | Retornar `true` se uma entrada fornecida for uma matriz ou retornar `false` se não for uma matriz. |
||||

*Exemplos*

Os seguintes exemplos verificam se a entrada é uma matriz:

```
isArray('hello')
isArray(createArray('hello', 'world'))
```

E retornam os seguintes resultados, respectivamente:

- A entrada é uma cadeia de caracteres, portanto, a função retorna `false`.
- A entrada é uma matriz, portanto, a função retorna `true`.

<a name="isBoolean"></a>

### <a name="isboolean"></a>isBoolean

Retornar `true` se uma entrada fornecida for um booliano.

```
isBoolean('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | any | A entrada a ser testada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | Retornar `true` se uma entrada fornecida for um booliano ou retornar `false` se não for um booliano. |
||||

*Exemplos*

Os seguintes exemplos verificam se a entrada é um booliano:

```
isBoolean('hello')
isBoolean(32 > 16)
```

E retornam os seguintes resultados, respectivamente:

- A entrada é uma cadeia de caracteres, portanto, a função retorna `false`.
- A entrada é um booliano, portanto, a função retorna `true`.
<a name="isDate"></a>

### <a name="isdate"></a>isDate

Retornar `true` se a expressão TimexProperty ou Timex fornecida se referir a uma data válida. As datas válidas contêm o month e o dayOfMonth ou contêm o dayOfWeek.

```
isDate('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | objeto ou cadeia de caracteres | O objeto Timexproperty de entrada ou uma cadeia de caracteres de expressão Timex. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*boolean-result*> | Boolean | Retornar `true` se a entrada se referir a uma data válida ou retornar `false` se a data for inválida. |
||||

*Exemplos*

Estes exemplos verificam se as seguintes entradas são datas válidas:

```
isDate('2020-12')
isDate('xxxx-12-21')
```

E retornam os seguintes resultados:
- `false`
- `true`

<a name="isDateRange"></a>

### <a name="isdaterange"></a>isDateRange

Retornar `true` se a expressão TimexProperty ou Timex fornecida se referir a um intervalo de datas válido.

```
isDateRange('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | objeto ou cadeia de caracteres | O objeto Timexproperty de entrada ou uma cadeia de caracteres de expressão Timex. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*boolean-result*> | Boolean | Retornar `true` se a entrada fornecida se referir a um intervalo de datas válido ou retornar `false` se não for um intervalo de datas válido. |
||||

*Exemplos*

Estes exemplos verificam se as entradas a seguir são intervalos de datas válidos:

```
isDateRange('PT30M')
isDateRange('2012-02')
```

E retornam os seguintes resultados:

- `false`
- `true`

<a name="isDateTime"></a>

### <a name="isdatetime"></a>isDateTime

Retornar `true` se uma entrada fornecida for uma cadeia de caracteres de carimbo de data/hora de formato ISO UTC (**AAAA-MM-DDTHH:mm:ss.fffZ**).

```
isDateTime('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | any | A entrada a ser testada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | Retornar `true` se uma entrada fornecida for uma cadeia de caracteres de carimbo de data/hora de formato UTC ISO ou retornar `false` se não for uma cadeia de caracteres de carimbo de data/hora de formato UTC ISO. |
||||

*Exemplos*

Os seguintes exemplos verificam se a entrada é uma cadeia de caracteres de formato UTC ISO:

```
isDateTime('hello world!')
isDateTime('2019-03-01T00:00:00.000Z')
```

E retornam os seguintes resultados, respectivamente:

- A entrada é uma cadeia de caracteres, portanto, a função retorna `false`.
- A entrada é uma cadeia de caracteres de formato UTC ISO, portanto, a função retorna `true`.

<a name="isDefinite"></a>

### <a name="isdefinite"></a>isDefinite

Retornar `true` se a expressão TimexProperty ou Timex fornecida se referir a uma data válida. As datas válidas contêm year, month e dayOfMonth.

```
isDefinite('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | objeto ou cadeia de caracteres | O objeto Timexproperty de entrada ou uma cadeia de caracteres de expressão Timex. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*boolean-result*> | Boolean | Retornar `true` se a entrada fornecida se referir a uma data completa válida ou retornar `false` se não fizer referência a uma data completa válida. |
||||

*Exemplos*

Suponha que haja um objeto Timexproperty **validFullDate = new TimexProperty("2020-02-20")** e a propriedade `Now` esteja definida como `true`. Os seguintes exemplos verificam se o objeto refere-se a uma data completa válida:

```
isDefinite('xxxx-12-21')
isDefinite(validFullDate)
```

E retornam os seguintes resultados, respectivamente:

- `false`
- `true`

<a name="isDuration"></a>

### <a name="isduration"></a>isDuration

Retornar `true` se a expressão TimexProperty ou Timex fornecida se referir a uma duração válida.

```
isDuration('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | objeto ou cadeia de caracteres | O objeto Timexproperty de entrada ou uma cadeia de caracteres de expressão Timex. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*boolean-result*> | Boolean | Retornar `true` se a entrada se referir a uma duração válida ou retornar `false` se a entrada não se referir a uma duração válida. |
||||

*Exemplos*

Os exemplos abaixo verificam se a seguinte entrada se refere a uma duração válida:

```
isDuration('PT30M')
isDuration('2012-02')
```

E retornam os seguintes resultados, respectivamente:

- `true`
- `false`

<a name="isFloat"></a>

### <a name="isfloat"></a>isFloat

Retornar `true` se uma entrada fornecida for um número de ponto flutuante. Devido ao alinhamento entre C# e JavaScript, um número com um resto diferente de zero de seu módulo 1 será tratado como um número de ponto flutuante.

```
isFloat('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | any | A entrada a ser testada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | Retornar `true` se uma entrada fornecida for um número de ponto flutuante ou retornar `false` se a entrada não for um número de ponto flutuante. |
||||

*Exemplos*

Os exemplos a seguir verificam se a entrada é um número de ponto flutuante:

```
isFloat('hello world!')
isFloat(1.0)
isFloat(12.01)
```

E retornam os seguintes resultados, respectivamente:

- A entrada é uma cadeia de caracteres, portanto, a função retorna `false`.
- A entrada tem um módulo que é igual a 0, portanto, a função retorna `false`.
- A entrada é um número de ponto flutuante, portanto, a função retorna `true`.

<a name="isInteger"></a>

### <a name="isinteger"></a>isInteger

Retornar `true` se a entrada fornecida for um número inteiro. Devido ao alinhamento entre C# e JavaScript, um número com um resto diferente de zero de seu módulo 1 será tratado como um número de ponto flutuante.

```
isInteger('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | any | A entrada a ser testada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | A entrada é um número inteiro |
||||

*Exemplos*

Os seguintes exemplos verificam se a entrada é um inteiro:

```
isInteger('hello world!')
isInteger(1.0)
isInteger(12)
```

E retornam os seguintes resultados, respectivamente:

- A entrada é uma cadeia de caracteres, portanto, a função retorna `false`.
- A entrada tem um módulo que é igual a 0, portanto, a função retorna `true`.
- A entrada é um inteiro, portanto, a função retorna `true`.

<a name="isMatch"></a>

### <a name="ismatch"></a>isMatch

Retornar `true` se uma cadeia de caracteres fornecida corresponder a um padrão de expressão regular especificado.

```
isMatch('<target**string>', '<pattern>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*target**string*> | Sim | string | A cadeia de caracteres a ser correspondida |
| <*pattern*> | Sim | string | Um padrão de expressão regular |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | Retornar `true` se uma cadeia de caracteres fornecida corresponder a um padrão comum de expressão regular ou retornar `false` se não corresponder ao padrão. |
||||

*Exemplos*

O seguinte exemplo verifica se a entrada corresponde ao padrão de expressão regular especificado:

```
isMatch('ab', '^[a-z]{1,2}$')
isMatch('FUTURE', '(?i)fortune|future')
isMatch('12abc34', '([0-9]+)([a-z]+)([0-9]+)')
isMatch('abacaxc', 'ab.*?c')
```

E retorna o mesmo resultado `true`.

<a name="isObject"></a>

### <a name="isobject"></a>isObject

Retornar `true` se uma entrada fornecida for um objeto complexo ou retornar `false` se for um objeto primitivo. Os objetos primitivos incluem cadeias de caracteres, números e boolianos; os tipos complexos, como classes, contêm propriedades. <!--In C#, the input is neither a value type nor a string. In JavaScript, it reflects to the input is not a primitive data types.-->

```
isObject('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | any | A entrada a ser testada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | Retornar `true` se uma entrada fornecida for um objeto complexo ou retornar `false` se for um objeto primitivo. |
||||

*Exemplos*

Os seguintes exemplos verificam se a entrada fornecida é um objeto:

```
isObject('hello world!')
isObject({userName: "Sam"})
```

E retornam os seguintes resultados, respectivamente:

- A entrada é uma cadeia de caracteres, portanto, a função retorna `false`.
- A entrada é um objeto, portanto, a função retorna `true`.

<a name="isPresent"></a>

### <a name="ispresent"></a>isPresent

Retornar `true` se a expressão TimexProperty ou Timex fornecida se referir ao presente.

```
isPresent('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | objeto ou cadeia de caracteres | O objeto Timexproperty de entrada ou uma cadeia de caracteres de expressão Timex |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*boolean-result*> | Boolean | Retornar `true` se a entrada se referir ao presente ou retornar false se ela não se referir ao presente. |
||||

*Exemplos* suponha que tenhamos um objeto Timexproperty **validNow = new TimexProperty() { Now = true }** e definimos a propriedade `Now` como `true`. Os exemplos abaixo verificam se a seguinte entrada se refere ao presente:

```
isPresent('PT30M')
isPresent(validNow)
```

E retornam os seguintes resultados, respectivamente:

- `false`
- `true`

<a name="isString"></a>

### <a name="isstring"></a>isString

Retornar `true` se uma entrada fornecida for uma cadeia de caracteres.

```
isString('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | any | A entrada a ser testada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Boolean-result*> | Boolean | Retornar `true` se uma entrada fornecida for uma cadeia de caracteres ou retornar `false` se não for uma cadeia de caracteres. |
||||

*Exemplos*

Os seguintes exemplos verificam se a entrada fornecida é uma cadeia de caracteres:

```
isString('hello world!')
isString(3.14)
```

E retornam os seguintes resultados, respectivamente:

- A entrada é uma cadeia de caracteres, portanto, a função retorna `true`.
- A entrada é um float, portanto, a função retorna `false`.

<a name="isTime"></a>

### <a name="istime"></a>isTime

Retornar `true` se a expressão TimexProperty ou Timex fornecida se referir a um horário válido. O horário válido contém horas, minutos e segundos.

```
isTime('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | objeto ou cadeia de caracteres | O objeto Timexproperty de entrada ou uma cadeia de caracteres de expressão Timex |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*boolean-result*> | Boolean | Retornar `true` se a entrada se referir a um horário válido ou retornar `false` se não se referir a um horário válido. |
||||

*Exemplos*

Estes exemplos verificam se a seguinte entrada se refere a um horário válido:

```
isTime('PT30M')
isTime('2012-02-21T12:30:45')
```

E retornam os seguintes resultados, respectivamente:

- `false`
- `true`

<a name="isTimeRange"></a>

### <a name="istimerange"></a>isTimeRange

Retornar `true` se a expressão TimexProperty ou Timex fornecida se referir a um intervalo de tempo válido que contêm partOfDay.

```
isTime('<input>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*input*> | Sim | objeto ou cadeia de caracteres | O objeto Timexproperty de entrada ou uma cadeia de caracteres de expressão Timex. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*boolean-result*> | Boolean | Retornar `true` se a entrada se referir a um intervalo de tempo válido ou retornar `false` se não se referir a um intervalo de tempo válido. |
||||

*Exemplos*

Suponha que tenhamos um objeto Timexproperty **validTimeRange = new TimexProperty() { PartOfDay = "morning" }** e definimos a propriedade `Now` como `true`. Estes exemplos verificam se as seguintes entradas são intervalos de tempo válidos:

```
isTimeRange('PT30M')
isTimeRange(validTimeRange)
```

E retornam os seguintes resultados, respectivamente:

- `false`
- `true`

<a name="join"></a>

### <a name="join"></a>join

Retornar uma cadeia de caracteres que tem todos os itens de uma matriz com cada caractere separado por um *delimitador*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | matriz | A matriz que tem os itens a serem ingressados |
| <*delimiter*> | Sim | string | O separador exibido entre cada caractere na cadeia de caracteres resultante |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | string | A cadeia de caracteres resultante criada com base em todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres com base em todos os itens nessa matriz com o caractere especificado **.** como o delimitador:

```
join(createArray('a', 'b', 'c'), '.')
```

E retorna o resultado **a.b.c**.


<a name="jPath"></a>

### <a name="jpath"></a>jPath

Marcar JSON ou uma cadeia de caracteres JSON para nós ou valores que correspondam a uma expressão de caminho e retornar os nós correspondentes.

```
jPath(<json>, '<path>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*json*> | Sim | any | A cadeia de caracteres ou objeto JSON para pesquisar nós ou valores que correspondem a um valor de expressão path |
| <*path*> | Sim | any | A expressão path usada para localizar valores ou nós JSON correspondentes |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
|[ <*json-node*>] | matriz | Uma lista de nós ou valor JSON que corresponde à expressão de caminho especificada |
||||

*Exemplo de C#*

Digamos que você tenha o seguinte JSON:

```json
{
    "Stores": [
        "Lambton Quay",
        "Willis Street"
    ],
    "Manufacturers": [
        {
            "Name": "Acme Co",
            "Products": [
                {
                    "Name": "Anvil",
                    "Price": 50
                }
            ]
        },
        {
            "Name": "Contoso",
            "Products": [
                {
                    "Name": "Elbow Grease",
                    "Price": 99.95
                },
                {
                    "Name": "Headlight Fluid",
                    "Price": 4
                }
            ]
        }
    ]
}
```

A expressão de caminho é **$..Products[?(@.Price >= 50)].Name**

```
jPath(jsonStr, path)
```

E ele retorna o resultado **["Anvil", "Elbow Grease"]** .

*Exemplo de JavaScript*

Digamos que você tenha o seguinte JSON:

```json
{
    "automobiles": [
        {
            "maker": "Nissan",
            "model": "Teana",
            "year": 2011
        },
        {
            "maker": "Honda",
            "model": "Jazz",
            "year": 2010
        },
        {
            "maker": "Honda",
            "model": "Civic",
            "year": 2007
        },
        {
            "maker": "Toyota",
            "model": "Yaris",
            "year": 2008
        },
        {
            "maker": "Honda",
            "model": "Accord",
            "year": 2011
        }
    ],
    "motorcycles": [
        {
            "maker": "Honda",
            "model": "ST1300",
            "year": 2012
        }
    ]
}
```

A expressão de caminho é **.automobiles{.maker === "Honda" && .year > 2009}.model**.

```
jPath(jsonStr, path)
```

E ela retorna o resultado **['Jazz', 'Accord']** .

<a name="json"></a>

### <a name="json"></a>json

Retornar o objeto ou valor de tipo JSON (JavaScript Object Notation) de uma cadeia de caracteres ou XML.

```
json('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | cadeia de caracteres ou XML | A cadeia de caracteres ou XML a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*JSON-result*> | string | A cadeia de caracteres resultante criada com base em todos os itens na matriz especificada |
||||

*Exemplo 1*

Este exemplo converte uma cadeia de caracteres em JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E retorna o resultado:

```
{
  "fullName": "Sophia Owen"
}
```

*Exemplo 2*

Este exemplo converte o XML em JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E retorna o resultado:

```
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="last"></a>

### <a name="last"></a>last

Retornar o último item de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | cadeia de caracteres ou matriz | A coleção na qual localizar o último item |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Cadeia de caracteres ou matriz, respectivamente | O último item na coleção |
||||

*Exemplo*

Esses exemplos localizam o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E retornam os seguintes resultados, respectivamente:

* **d**
* **3**

<a name="lastIndexOf"></a>

### <a name="lastindexof"></a>lastIndexOf

Retorna a posição inicial ou valor de índice da última ocorrência de uma substring. Essa função não diferencia maiúsculas de minúsculas, e os índices começam com o número 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | cadeia de caracteres ou matriz | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | string | A subcadeia de caracteres a ser localizada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | inteiro | A posição inicial ou valor de índice da última ocorrência da substring especificada. Se a cadeia de caracteres não for localizada, retornará o número **-1**. |
||||

*Exemplo 1*

Este exemplo encontra o valor de índice inicial da última ocorrência da substring **world** na cadeia de caracteres **hello world**:

```
lastIndexOf('hello world', 'world')
```

E retorna o resultado **6**.

*Exemplo 2*

Este exemplo localiza o valor de índice inicial da última ocorrência da substring **def** na matriz **['abc', 'def', 'ghi', 'def']** .

```
lastIndexOf(createArray('abc', 'def', 'ghi', 'def'), 'def')
```

E retorna o resultado **3**.

### <a name="length"></a>comprimento

Retornar o comprimento de uma cadeia de caracteres.

```
length('<str>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*str*> | Sim | string | A cadeia de caracteres a ser calculada para o comprimento |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*length*> | inteiro | O comprimento dessa cadeia de caracteres |
||||

*Exemplos*

Estes exemplos obtêm o comprimento das cadeias de caracteres:

```
length('hello')
length('hello world')
```

E retornam os seguintes resultados, respectivamente:

* **5**
* **11**

<a name="less"></a>

### <a name="less"></a>less

Verificar se o primeiro valor é menor que o segundo valor. Retornar `true` se o primeiro valor for menor ou retornar `false` se o primeiro valor for maior.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é menor do que o segundo |
| <*compareTo*> | Sim | inteiro, float ou cadeia de caracteres, respectivamente  | O item de comparação |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` se o primeiro valor for menor que o segundo. Retornar `false` se o primeiro valor for igual ou maior que o segundo. |
||||

*Exemplos*

Esses exemplos verificam se o primeiro valor é menor que o segundo.

```
less(5, 10)
less('banana', 'apple')
```

E retornam os seguintes resultados, respectivamente:

* `true`
* `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verificar se o primeiro valor é inferior ou igual ao segundo valor. Retornar `true` se o primeiro valor for menor ou igual ou retornar `false` se o primeiro valor for maior.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é inferior ou igual ao segundo |
| <*compareTo*> | Sim | inteiro, float ou cadeia de caracteres, respectivamente  | O item de comparação |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Boolean | Retornar `true` se o primeiro valor for igual ou menor que o segundo. Retornar `false` se o primeiro valor for maior que o segundo. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é menor ou igual ao segundo.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E retornam os seguintes resultados, respectivamente:

* `true`
* `false`

<a name="max"></a>

### <a name="max"></a>max

Retornar o maior valor de uma lista ou matriz. A lista ou matriz é inclusiva em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | número | O conjunto de números do qual você deseja o valor mais alto |
| [<*number1*>, <*number2*>, ...] | Sim | matriz de números | A matriz de números da qual você deseja o valor mais alto |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*max-value*> | número | O valor mais alto na matriz ou conjunto de números especificado |
||||

*Exemplos*

Esses exemplos obtêm o valor mais alto do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E retornam o resultado **3**.

<a name="min"></a>

### <a name="min"></a>Min

Retornar o valor mais baixo de um conjunto de números ou de uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | número | O conjunto de números do qual você deseja o valor mais baixo |
| [<*number1*>, <*number2*>, ...] | Sim | matriz de números | A matriz de números da qual você deseja o valor mais baixo |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*min-value*> | número | O valor mais baixo na matriz ou conjunto de números especificado |
||||

*Exemplos*

Esses exemplos obtêm o valor mais baixo no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E retornam o resultado **1**.

<a name="mod"></a>

### <a name="mod"></a>mod

Retornar o resto da divisão de dois números. Para obter o resultado inteiro, consulte [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | número | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | número | O número que divide o *dividendo*. Não pode ser 0. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*modulo-result*> | número | O resto da divisão do primeiro número pelo segundo |
||||

*Exemplo*

O exemplo divide o primeiro número pelo segundo:

```
mod(3, 2)
```

E retorna o resultado **1**.

<a name="month"></a>

### <a name="month"></a>month

Retornar o mês do carimbo de data/hora especificado.

```
month('<timestamp>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*number-of-month*> | inteiro | O número do mês no carimbo de data/hora especificado |
||||

*Exemplo*

```
month('2018-03-15T13:01:00.000Z')
```

E ele retorna o resultado **3**.

<a name="mul"></a>

### <a name="mul"></a>mul

Retornar o produto da multiplicação de dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | inteiro ou float | O número a ser multiplicado por *multiplicand2* |
| <*multiplicand2*> | Sim | inteiro ou float | O número que multiplica *multiplicand1* |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*product-result*> | inteiro ou float | O produto da multiplicação do primeiro número pelo segundo |
||||

*Exemplos*

Esses exemplos multiplicam o primeiro número pelo segundo:

```
mul(1, 2)
mul(1.5, 2)
```

E retornam os seguintes resultados, respectivamente:

* **2**
* **3**

<a name="newGuid"></a>

### <a name="newguid"></a>newGuid

Retornar uma nova cadeia de caracteres Guid.

```
newGuid()
```

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*Guid-string*> | string | Uma nova cadeia de caracteres Guid, o comprimento é 36 e é semelhante a *xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx*|
||||

*Exemplo*

```
newGuid()
```

E retorna um resultado que segue o formato **xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx**.

<a name="not"></a>

### <a name="not"></a>não

Verificar se uma expressão é falsa. Retornar `true` se a expressão for false ou retornar `false` se for true.

```
not(<expression>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Boolean | A expressão a ser verificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` se a expressão for falsa. Retornar `false` se a expressão for true. |
||||

*Exemplo 1*

Esses exemplos verificam se as expressões especificadas são false:

```
not(false)
not(true)
```

E retornam os seguintes resultados, respectivamente:

* A expressão é false, então a função retorna `true`.
* A expressão é true, então a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são false:

```
not(equals(1, 2))
not(equals(1, 1))
```

E retornam os seguintes resultados, respectivamente:

* A expressão é false, então a função retorna `true`.
* A expressão é true, então a função retorna `false`.

<a name="or"></a>

### <a name="or"></a>ou

Verificar se pelo menos uma expressão é verdadeira. Retornar `true` se pelo menos uma expressão for true ou retornar `false` quando todas forem false.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Sim | Boolean | As expressões a serem verificadas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` se pelo menos uma expressão for true. Retornar `false` se todas as expressões forem false. |
||||

*Exemplo 1*

Esses exemplos verificam se pelo menos uma expressão é true:

```
or(true, false)
or(false, false)
```

E retornam os seguintes resultados, respectivamente:

* No mínimo, uma expressão é true, então a função retorna `true`.
* Ambas as expressões são false, então a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se pelo menos uma expressão é true:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E retornam os seguintes resultados, respectivamente:

* No mínimo, uma expressão é true, então a função retorna `true`.
* Ambas as expressões são false, então a função retorna `false`.

<a name="rand"></a>

### <a name="rand"></a>rand

Retornar um inteiro aleatório de um intervalo especificado, inclusivo apenas na extremidade inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | inteiro | O menor inteiro no intervalo |
| <*maxValue*> | Sim | inteiro | O inteiro que segue o inteiro mais alto no intervalo que a função pode retornar |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*random-result*> | inteiro | O inteiro aleatório retornado do intervalo especificado |
||||

*Exemplo*

Esse exemplo obtém um inteiro aleatório do intervalo especificado, excluindo o valor máximo:

```
rand(1, 5)
```

E retorna **1**, **2**, **3** ou **4** como resultado.

<a name="range"></a>

### <a name="range"></a>range

Retornar uma matriz de inteiros que começa em um inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | inteiro | Um valor inteiro que inicia a matriz como o primeiro item |
| <*count*> | Sim | inteiro | O número de inteiros na matriz |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*range-result*> | inteiro | A matriz com inteiros que começa no índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz de inteiros que começa no índice especificado **1** e tem o número de inteiros especificado como **4**:

```
range(1, 4)
```

E retorna o resultado **[1, 2, 3, 4]** .

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Remover uma propriedade de um objeto e retornar o objeto atualizado.

```
removeProperty(<object>, '<property>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | objeto | O objeto JSON no qual você deseja remover uma propriedade |
| <*property*> | Sim | string | O nome da propriedade a remover |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | objeto | O objeto JSON atualizado sem a propriedade especificada |
||||

*Exemplo*

Este exemplo remove a propriedade `accountLocation` de um objeto `customerProfile`, convertido em JSON com a função [json()](#json) e retorna o objeto atualizado:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="replace"></a>

### <a name="replace"></a>substituir

Substituir uma subcadeia de caracteres pela cadeia de caracteres especificada e retornar a cadeia de caracteres resultante. Essa função diferencia maiúsculas de minúsculas.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres que tem a subcadeia de caracteres a ser substituída |
| <*oldText*> | Sim | string | A subcadeia de caracteres a ser substituída |
| <*newText*> | Sim | string | A cadeia de caracteres de substituição |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-text*> | string | A cadeia de caracteres atualizada após substituir a substring. Se a substring não for localizada, a função retornará a cadeia de caracteres original. |
||||

*Exemplo 1*

Este exemplo localiza a substring **old** em **the old string** e substitui **old** por **new**:

```
replace('the old string', 'old', 'new')
```

O resultado é a cadeia de caracteres **the new string**.

*Exemplo 2*

Ao lidar com caracteres de escape, o mecanismo de expressão lida com o unescape para você. Essa função substitui as cadeias de caracteres por caracteres de escape.

```
replace('hello\"', '\"', '\n')
replace('hello\n', '\n', '\\\\')
@"replace('hello\\', '\\', '\\\\')"
@"replace('hello\n', '\n', '\\\\')"
```

E retornam os seguintes resultados, respectivamente:

- **hello\n**
- **hello\\\\**
- **@"hello\\\\"**
- **@"hello\\\\"**

<a name="replaceIgnoreCase"></a>

### <a name="replaceignorecase"></a>replaceIgnoreCase

Substituir uma subcadeia de caracteres pela cadeia de caracteres especificada e retornar a cadeia de caracteres resultante. Essa função não diferencia maiúsculas de minúsculas.

```
replaceIgnoreCase('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres que tem a subcadeia de caracteres a ser substituída |
| <*oldText*> | Sim | string | A subcadeia de caracteres a ser substituída |
| <*newText*> | Sim | string | A cadeia de caracteres de substituição |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-text*> | string | A cadeia de caracteres atualizada após substituir a substring. Se a subcadeia de caracteres não for localizada, retorne a cadeia de caracteres original. |
||||

*Exemplo*

Este exemplo localiza a substring **old** na cadeia de caracteres **the old string** e substitui **old** por **new**:

```
replace('the old string', 'old', 'new')
```

E retorna o resultado **the new string**.

<a name="select"></a>

### <a name="select"></a>select

Operar em cada elemento e retornar a nova coleção de elementos transformados.

```
select([<collection/instance>], <iteratorName>, <function>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection/instance*> | Sim | matriz | A coleção com os itens |
| <*iteratorName*> | Sim | Nome do iterador | O item principal da função de seta |
| <*function*> | Sim | expressão | Função que pode conter `iteratorName` |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*new-collection*> | matriz | A nova coleção na qual cada elemento foi avaliado com a função  |
||||

*Exemplo 1*

Este exemplo gera novas coleções:

```
select(createArray(0, 1, 2, 3), x, x + 1)
```

E retorna o resultado **[1, 2, 3, 4]** .

*Exemplo 2*

Este exemplo gera uma nova coleção para uma instância:

```
select(json("{'name': 'jack', 'age': '15'}"), x, concat(x.key, ':', x.value))
```

E retorna o resultado **['name:jack', 'age:15']** .

<a name="setPathToValue"></a>

### <a name="setpathtovalue"></a>setPathToValue

Recuperar o valor da propriedade especificada do objeto JSON.

```
setPathToValue(<path>, <value>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Path*> | Sim | objeto | O caminho que você deseja definir |
| <*value*> | Sim | objeto | O valor que você deseja definir para o caminho |

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| value | objeto | O valor a ser definido |
||||

*Exemplo 1*

O seguinte exemplo define o valor **1** para o caminho:

```
setPathToValue(path.x, 1)
```

E retorna o resultado **1**. `path.x` está definido como **1**.

*Exemplo 2*

Este exemplo abaixo define o valor:

```
setPathToValue(path.array[0], 7) + path.array[0]
```

E retorna o resultado **14**.

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Definir o valor da propriedade do objeto e retornar o objeto atualizado. Para adicionar uma nova propriedade, use essa função ou a função [addProperty()](#addProperty).

```
setProperty(<object>, '<property>', <value>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | objeto | O objeto JSON no qual você deseja definir uma propriedade |
| <*property*> | Sim | string | O nome da propriedade a ser definida |
| <*value*> | Sim | any | O valor a ser definido para a propriedade especificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | objeto | O objeto JSON atualizado cuja propriedade você define |
||||

*Exemplo*

Este exemplo define a propriedade `accountNumber` em um objeto `customerProfile`, convertido em JSON com a função [json()](#json). A função atribui um valor gerado pela função [newGuid()](#newGuid) e retorna o objeto JSON atualizado:

```
setProperty(json('customerProfile'), 'accountNumber', newGuid())
```

<a name="skip"></a>

### <a name="skip"></a>skip

Remover itens do início de uma coleção e retornar todos os outros itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | matriz | A coleção cujos itens você deseja remover |
| <*count*> | Sim | inteiro | Um inteiro positivo para o número de itens a ser removido no início |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | matriz | A coleção atualizada depois de remover os itens especificados |
||||

*Exemplo*

Este exemplo remove um item, o número *1*, do início da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E retorna uma matriz com os itens restantes: **[1,2,3]** .

<a name="sortBy"></a>

### <a name="sortby"></a>sortBy

Classificar elementos na coleção em ordem crescente e retornar a coleção classificada.

```
sortBy([<collection>], '<property>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | cadeia de caracteres ou matriz | A coleção a ser classificada |
| <*property*> | Não | string | Classificar por essa propriedade específica do elemento object na coleção, se definido|
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*new-collection*> | matriz | A nova coleção cujos elementos foram classificados |
||||

*Exemplo 1*

Este exemplo gera classificações na seguinte coleção:

```
sortBy(createArray(1, 2, 0, 3))
```

E retornam o resultado **[0, 1, 2, 3]** .

*Exemplo 2*

Suponha que você tenha a seguinte coleção:

```
{
  'nestedItems': [
    {'x': 2},
    {'x': 1},
    {'x': 3}
  ]
}
```

Este exemplo gera uma nova coleção classificada com base na propriedade object **x**

```
sortBy(nestedItems, 'x')
```

E retorna o resultado:

```
{
  'nestedItems': [
    {'x': 1},
    {'x': 2},
    {'x': 3}
  ]
}
```

<a name="sortByDescending"></a>

### <a name="sortbydescending"></a>sortByDescending

Classificar elementos na coleção em ordem decrescente e retornar a coleção classificada.

```
sortBy([<collection>], '<property>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | cadeia de caracteres ou matriz | A coleção a ser classificada |
| <*property*> | Não | string | Classificar por essa propriedade específica do elemento object na coleção, se definido|
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*new-collection*> | matriz | A nova coleção cujos elementos foram classificados |
||||

*Exemplo 1*

Este exemplo gera uma nova coleção classificada:

```
sortByDescending(createArray(1, 2, 0, 3))
```

E retorna o resultado **[3, 2, 1, 0]** .

*Exemplo 2*

Suponha que você tenha a seguinte coleção:

```
{
  'nestedItems': [
    {'x': 2},
    {'x': 1},
    {'x': 3}
  ]
}
```

Este exemplo gera uma nova coleção classificada com base na propriedade object **x**:

```
sortByDescending(nestedItems, 'x')
```

E retorna este resultado:

```
{
  'nestedItems': [
    {'x': 3},
    {'x': 2},
    {'x': 1}
  ]
}
```

<a name="split"></a>

### <a name="split"></a>split

Retorna uma matriz que contém substrings separadas por vírgulas, com base no caractere delimitador especificado na cadeia de caracteres original.

```
split('<text>', '<delimiter>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres para separar em substrings com base no delimitador especificado na cadeia de caracteres original. Se o texto for um valor nulo, ele será considerado uma cadeia de caracteres vazia. |
| <*delimiter*> | Não | string | O caractere na cadeia de caracteres original para usar como delimitador. Se o delimitador não for fornecido ou se ele for um valor nulo, o valor padrão será uma cadeia de caracteres vazia. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | matriz | Uma matriz contendo subcadeias de caracteres da cadeia de caracteres original, separadas por vírgulas |
||||

*Exemplos*

Estes exemplos criam uma matriz com substrings da cadeia de caracteres especificada com base no caractere delimitador especificado:

```
split('a**b**c', '**')
split('hello', '')
split('', 'e')
split('', '')
split('hello')
```

E retorna as seguintes matrizes como o resultado, respectivamente:
- **["a", "b", "c"]**
- **["h", "e", "l", "l", "o"]**
- **[""]** , **[ ]**
- **["h", "e", "l", "l", "o"]**

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retornar o início do dia de um carimbo de data/hora.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| '<*updated-timestamp*>'| string | O carimbo de data/hora especificado começando na marca de zero hora para o dia |
||||

*Exemplo*

Este exemplo localiza o início do dia:

```
startOfDay('2018-03-15T13:30:30.000Z')
```

E retorna o resultado **2018-03-15T00:00:00.000Z**.

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Retornar o início da hora de um carimbo de data/hora.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| '<*updated-timestamp*>'| string | O carimbo de data/hora especificado começando na marca de zero minuto para o dia |
||||

*Exemplo*

Este exemplo localiza o início da hora:

```
startOfHour('2018-03-15T13:30:30.000Z')
```

E retorna o resultado **2018-03-15T13:00:00.000Z**.

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retornar o início do mês de um carimbo de data/hora.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| '<*updated-timestamp*>'| string | O carimbo de data/hora especificado começando no primeiro dia do mês na marca de zero hora |
||||

*Exemplo*

Este exemplo localiza o início do mês:

```
startOfDay('2018-03-15T13:30:30.000Z')
```

E retorna o resultado **2018-03-01T00:00:00.000Z**.

<a name="startsWith"></a>

### <a name="startswith"></a>startsWith

Verificar se uma cadeia de caracteres começa com uma subcadeia de caracteres específica. Retornar `true` se a substring for encontrada ou retornar `false` se não for encontrada. Essa função não diferencia maiúsculas de minúsculas.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | string | A substring inicial a ser localizada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornar `true` quando a substring inicial for localizada. Retornar `false` se não for encontrada |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia de caracteres **hello world** começa com a cadeia de caracteres **hello**:

```
startsWith('hello world', 'hello')
```

E retorna o resultado `true`.

*Exemplo 2*

Este exemplo verifica se a cadeia de caracteres **hello world** começa com a cadeia de caracteres **greeting**:

```
startsWith('hello world', 'greeting')
```

E retorna o resultado `false`.

<a name="string"></a>

### <a name="string"></a>string

Retornar a versão de cadeia de caracteres de um valor.

```
string(<value>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | any | O valor a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*string-value*> | string | A versão de cadeia de caracteres do valor especificado |
||||

*Exemplo 1*

Este exemplo cria uma versão de cadeia de caracteres para o número **10**:

```
string(10)
```

E retorna o resultado da cadeia de caracteres **10**.

*Exemplo 2*

Este exemplo cria uma cadeia de caracteres para o objeto JSON especificado e usa o caractere de barra invertida, **\\\\** , como o caractere de escape para o caractere de aspas duplas **"** .

```
string( { "name": "Sophie Owen" } )
```

E retorna o resultado **{ \\"name\\": \\"Sophie Owen\\" }**

<a name="sub"></a>

### <a name="sub"></a>sub

Retornar o resultado da subtração do segundo número do primeiro.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | número | O número do qual subtrair o *subtraendo* |
| <*subtrahend*> | Sim | número | O número a ser subtraído do *minuendo* |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*result*> | número | O resultado da subtração do segundo número do primeiro |
||||

*Exemplo*

Esse exemplo subtrai o segundo número do primeiro:

```
sub(10.3, .3)
```

E retorna o resultado **10**.

<a name="subArray"></a>

### <a name="subarray"></a>subArray

Retornar uma submatriz das posições inicial e final especificadas. Os valores de índice começam com o número 0.

```
subArray(<Array>, <startIndex>, <endIndex>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*array*> | Sim | matriz | A matriz da qual criar a submatriz |
| <*startIndex*> | Sim | inteiro | Um número positivo igual ou maior que 0 a ser usado como a posição inicial ou o valor do índice |
| <*endIndex*> | Sim | inteiro |  Um número positivo igual ou maior que 0 a ser usado como a posição final ou o valor do índice|
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*subarray-result*> | matriz | Uma submatriz com o número especificado de itens, começando na posição de índice especificada na cadeia de caracteres de origem |
||||

*Exemplo*

Este exemplo cria uma submatriz a partir da matriz especificada:

```
subArray(createArray('H','e','l','l','o'), 2, 5)
```

E retorna o resultado **["l", "l", "o"]** .

<a name="substring"></a>

### <a name="substring"></a>substring

Retornar caracteres de uma cadeia de caracteres, começando na posição especificada ou índice. Os valores de índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres da qual criar a substring |
| <*startIndex*> | Sim | inteiro | Um número positivo igual ou maior que 0 da submatriz a ser usado como a posição inicial ou o valor do índice |
| <*length*> | Sim | inteiro | Um número positivo de caracteres da submatriz na substring |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*substring-result*> | string | Uma subcadeia de caracteres com o número de caracteres especificado, começando na posição de índice especificada na cadeia de caracteres de origem |
||||

*Exemplo*

Esse exemplo cria uma subcadeia de caracteres de cinco caracteres com base na cadeia de caracteres especificada, começando no valor de índice 6:

```
substring('hello world', 6, 5)
```

E retorna o resultado **world**.

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtrair um número de unidades de tempo de um carimbo de data/hora. Confira também [getPastTime()](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | inteiro | O número de unidades de tempo especificadas a serem subtraídas |
| <*timeUnit*> | Sim | string | A unidade de tempo a ser usada com *interval*. As unidades possíveis são "Second", "Minute", "Hour", "Day", "Week", "Month" e "Year". |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | O carimbo de data/hora menos o número de unidades de tempo especificado |
||||

*Exemplo 1*

Este exemplo subtrai um dia do seguinte carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00.000Z', 1, 'Day')
```

E retorna o resultado **2018-01-01T00:00:00.000Z**.

*Exemplo 2*

Este exemplo subtrai um dia do seguinte carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00.000Z', 1, 'Day', 'D')
```

E retorna o resultado usando o formato *D* opcional: **Monday, January, 1, 2018**.

<a name="sum"></a>

### <a name="sum"></a>Sum

Retornar o resultado da adição de números em uma lista.

```
add([<list of numbers>])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| [\<list of numbers\>] | Sim | matriz de números | Os números a serem adicionados |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*result-sum*> | número | O resultado da adição dos números especificados |
||||

*Exemplo*

Esse exemplo adiciona os números especificados:

```
add(createArray(1, 1.5))
```

E retorna o resultado **2,5**.

<a name="take"></a>

### <a name="take"></a>take

Retornar itens do início de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | cadeia de caracteres ou matriz | A coleção cujos itens você deseja |
| <*count*> | Sim | inteiro | Um inteiro positivo para o número de itens que você deseja no início |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*subset*> ou [<*subset*>]| String ou Array, respectivamente | Uma cadeia de caracteres ou matriz que tem o número de itens especificado extraído do início da coleção original |
||||

*Exemplo*

Esses exemplos obtêm o número de itens especificado no início destas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E retornam os seguintes resultados, respectivamente:

- **abc**
- **[0, 1, 2]**

<a name='ticks'></a>

### <a name="ticks"></a>ticks

Retornar o valor de propriedade ticks de um carimbo de data/hora especificado. Um tique é um intervalo de 100 nanossegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*>| Sim | string | A cadeia de caracteres de um carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*ticks-number*> | inteiro (bigint em JavaScript)| O número de tiques desde o carimbo de data/hora especificado |
||||

*Exemplo*

O exemplo converte um carimbo de data/hora em sua propriedade ticks

```
ticks('2018-01-01T08:00:00.000Z')
```

E retorna o resultado **636503904000000000**.

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retornar uma cadeia de caracteres em letras minúsculas. Se um caractere na cadeia de caracteres não tiver uma versão em minúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toLower('<text>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres a ser retornada no formato em minúsculas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | string | A cadeia de caracteres original no formato em minúsculas |
||||

*Exemplo*

Este exemplo converte uma cadeia de caracteres em minúsculas:

```
toLower('Hello World')
```

E retorna o resultado **hello world**.

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retornar uma cadeia de caracteres em letras maiúsculas. Se um caractere na cadeia de caracteres não tiver uma versão em maiúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toUpper('<text>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres a ser retornada no formato em maiúsculas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | string | A cadeia de caracteres original no formato em maiúsculas |
||||

*Exemplo*

Este exemplo converte uma cadeia de caracteres em maiúsculas:

```
toUpper('Hello World')
```

E retorna o resultado **HELLO WORLD**.

<a name="trim"></a>

### <a name="trim"></a>cortar

Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada.

```
trim('<text>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | string | A cadeia de caracteres que tem o espaço em branco à esquerda e à direita a ser removida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updatedText*> | string | Uma versão atualizada da cadeia de caracteres original sem espaço em branco à esquerda ou à direita |
||||

*Exemplo*

Este exemplo remove o espaço em branco à esquerda e à direita da cadeia de caracteres **" Hello World  "** :

```
trim(' Hello World  ')
```

E retorna o resultado aparado **Hello World**.

<a name="union"></a>

### <a name="union"></a>union

Retornar uma coleção que tem todos os itens das coleções especificadas. Para aparecer no resultado, um item pode ser exibido em qualquer coleção passada para essa função. Se um ou mais itens tiverem o mesmo nome, o último item com o nome será exibido neste resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...| Sim | matriz ou objeto, mas não ambos | As coleções das quais você deseja todos os itens |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | matriz ou objeto, respectivamente | Uma coleção com todos os itens das coleções especificadas. Nenhuma duplicata adicionada. |
||||

*Exemplo*

Este exemplo obtém todos os itens das seguintes coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E retorna o resultado **[1, 2, 3, 10, 101]** .

<a name="unique"/>

### <a name="unique"></a>exclusivo

Remover todas as duplicatas de uma matriz.

```
unique([<collection>])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | matriz | A coleção a ser modificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*new-collection*> | matriz | Nova coleção com elementos duplicados removidos |
||||

*Exemplo 1*

Este exemplo remove os elementos duplicados da seguinte matriz:

```
unique(createArray(1, 2, 1))
```

E retorna o resultado **[1, 2]** .

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Retornar a versão binária de um componente URI (Uniform Resource Identifier).

```
uriComponent('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres a ser convertida em um formato codificado para URI |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | string | A cadeia de caracteres codificada para URI com caracteres de escape |
||||

*Exemplo*

Este exemplo cria uma versão codificada para URI de uma cadeia de caracteres:

```
uriComponent('https://contoso.com')
```

E retorna o resultado **http%3A%2F%2Fcontoso.com**.

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada para URI (Uniform Resource Identifier), decodificando com eficácia a cadeia de caracteres codificada para URI.

```
uriComponentToString('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres codificada para URI a ser decodificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | string | A versão decodificada para a cadeia de caracteres codificada para URI |
||||

*Exemplo*

Este exemplo cria a versão de cadeia de caracteres decodificada de uma cadeia de caracteres codificada para URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E retorna o resultado **https://contoso.com** .

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Retornar o valor de host de um URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | string | O URI cujo valor de host você deseja |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*host-value*> | string | O valor de host do URI especificado |
||||

*Exemplo*

Este exemplo localiza o valor de host do seguinte URI:

```
uriHost('https://www.localhost.com:8080')
```

E retorna o resultado **www.localhost.com**.

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Retornar o valor de caminho de um URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | string | O URI cujo valor de caminho você deseja |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*path-value*> | string | O valor de caminho do URI especificado |
||||

*Exemplo*

Este exemplo localiza o valor de caminho do seguinte URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna o resultado **/catalog/shownew.htm**.

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Retornar o valor de caminho e consulta de um URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | string | O URI cujo valor de caminho e consulta você deseja |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*path-query-value*> | string | O valor de caminho e consulta do URI especificado |
||||

*Exemplo*

Este exemplo localiza o valor de caminho e consulta do seguinte URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna o resultado **/catalog/shownew.htm?date=today**.

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Retornar o valor de porta de um URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | string | O URI cujo valor de caminho você deseja |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*port-value*> | string | O valor de porta do URI especificado |
||||

*Exemplo*

Este exemplo localiza o valor de porta do seguinte URI:

```
uriPort('http://www.localhost:8080')
```

E retorna o resultado **8080**.

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Retornar o valor de consulta de um URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | string | O URI cujo valor de consulta você deseja |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*query-value*> | string | O valor de consulta do URI especificado |
||||

*Exemplo*

Este exemplo localiza o valor de consulta do seguinte URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna o resultado **?date=today**.

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Retornar o valor de esquema de um URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | string | O URI cujo valor de consulta você deseja |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*scheme-value*> | string | O valor de esquema do URI especificado |
||||

*Exemplo*

Este exemplo localiza o valor de esquema do seguinte URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna o resultado **http**.

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Retornar o carimbo de data/hora atual.

```
utcNow('<format>')
```

Ou é possível especificar um formato diferente com o parâmetro <*formato*>.


| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | string | Um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é o UTC ISO, AAAA-MM-DDTHH:mm:ss.fffZ, compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO**8601). |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | string | A data e hora atual |
||||

*Exemplo 1*

Suponha que a data seja **April 15, 2018** às **1:00:00 PM**. Este exemplo obtém o carimbo de data/hora:

```
utcNow()
```

E retorna o resultado **2018-04-15T13:00:00.000Z**.

*Exemplo 2*

Suponha que a data seja **April 15, 2018** às **1:00:00 PM**. Este exemplo obtém o carimbo de data/hora atual usando o formato *D* opcional:

```
utcNow('D')
```

E retorna o resultado **Sunday, April 15, 2018**.

<a name="where"></a>

### <a name="where"></a>onde

Filtrar em cada elemento e retornar a nova coleção de elementos filtrados que correspondem a uma condição específica.

```
where([<collection/instance>], <iteratorName>, <function>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection/instance*> | Sim | matriz | A coleção com os itens |
| <*iteratorName*> | Sim | nome do iterador | O item principal da função de seta |
| <*function*> | Sim | expressão | Função Condition usada para filtrar itens|
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*new-collection/new-object*> | matriz/objeto | A nova coleção na qual cada elemento foi filtrado com a função  |
||||

*Exemplo 1*

Este exemplo gera novas coleções:

```
where(createArray(0, 1, 2, 3), x, x > 1)
```

E retorna o resultado **[2, 3]** .

*Exemplo 2*

Este exemplo gera um novo objeto:

```
where(json("{'name': 'jack', 'age': '15'}"), x, x.value == 'jack')
```

E retorna o resultado **{'name': 'jack'}** .

<a name="xml"></a>

### <a name="xml"></a>Xml

**Somente C#** . Retornar a versão XML de uma cadeia de caracteres que contém um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | string | A cadeia de caracteres com o objeto JSON para converter o objeto JSON deve ter apenas uma propriedade raiz, que não pode ser uma matriz. Use **\\** como um caractere de escape para aspas duplas (").|
||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*xml-version*> | objeto | O XML codificado para a cadeia de caracteres ou objeto JSON especificado |
||||

*Exemplo 1*

Este exemplo cria a versão XML para uma cadeia de caracteres, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E retorna o resultado XML:

```
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que você tenha um objeto JSON `person`, visto abaixo:

```
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Este exemplo cria o XML para uma cadeia de caracteres que contém esse objeto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E retorna o resultado XML:

```
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person
```

<a name="xPath"></a>

### <a name="xpath"></a>xPath

**Somente C#** . Verificar o XML em busca de nós ou valores correspondentes a uma expressão XPath (Linguagem XPath) e retornar esses nós ou valores. Uma expressão XPath (conhecida como XPath), ajuda você a navegar por uma estrutura de documento XML para poder selecionar nós ou calcular valores no conteúdo XML.

```
xPath('<xml>', '<xpath>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | any | A cadeia de caracteres XML na qual pesquisar nós ou valores correspondentes a um valor de expressão XPath |
| <*xPath*> | Sim | any | A expressão XPath usada para localizar valores ou nós XML correspondentes |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Um nó XML quando um único nó corresponde à expressão XPath especificada |
| <*value*> | any | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
< *[<xml-node1>, <xml-node2>, ...] -ou- [<value1>, <value2>, ...]* > | matriz | Uma matriz com valores ou nós XML correspondentes à expressão XPath especificada |
||||

*Exemplo 1*

Esse exemplo localiza nós correspondentes ao nó `<name></name>` nos argumentos especificados e retorna uma matriz com estes valores de nós:

```
xpath(items, '/produce/item/name')
```

Os argumentos incluem a cadeia de caracteres **items**, que contém esse XML:

```
"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"
```

Confira a matriz de resultado com os nós correspondentes a `<name></name>`:

```
[ <name>Gala</name>, <name>Honeycrisp</name> ]
```

*Exemplo 2*

Seguindo o exemplo 1, este exemplo localiza nós correspondentes ao nó `<count></count>` e adiciona esses valores de nó com a função [sum()](#sum):

```
xpath(xml(parameters('items')), 'sum(/produce/item/count)')
```

E retorna o resultado **30**.

<a name="year"></a>

### <a name="year"></a>year

Retornar o ano do carimbo de data/hora especificado.

```
year('<timestamp>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | string | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*year*> | inteiro | O ano no carimbo de data/hora especificado |
||||

*Exemplo*

Este exemplo avalia o carimbo de data/hora do ano:

```
year('2018-03-15T00:00:00.000Z')
```

E retorna o resultado **2018**.
