---
title: Funções injetadas da biblioteca de geração de linguagem – Serviço de Bot
description: Descreve como injetar funções da LG em modelos.
keywords: functions from lg, reference, language generation
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: df825658e6cdd992f8e5c8fed1146bc42d21e2a4
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98575797"
---
# <a name="functions-injected-from-the-language-generation-library"></a>Funções injetadas da biblioteca de geração de linguagem

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O artigo a seguir fornece detalhes sobre como injetar funções da biblioteca [LG (geração de linguagem)](../v4sdk/bot-builder-concept-language-generation.md).

## <a name="activityattachment"></a>ActivityAttachment

Retorna um `activityAttachment` construído com base em um objeto e um tipo.

```lg
ActivityAttachment(<collection-of-objects>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*content*> | Sim | objeto | Objeto que contém as informações do anexo |
| <*type*> | Sim | string  | Uma cadeia de caracteres que representa o tipo de anexo |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*activityAttachment*> | objeto | Um `activityAttachment` formado com base nas entradas |
||||

*Exemplo*:

Este exemplo converte uma coleção de objetos em um `activityAttachment`.
<!--
Using the `ActivityAttachment()` function in the template body, type, title, value are parameters in the template name.-->

Suponha que você tenha o seguinte modelo:

```lg
# externalHeroCardActivity(type, title, value)
[Activity
    attachments = ${ActivityAttachment(json(fromFile('.\\herocard.json')), 'herocard')}
]
```

e o seguinte `herocard.json`:

```json
{
  "title": "titleContent",
  "text": "textContent",
  "Buttons": [
    {
      "type": "imBack",
      "Title": "titleContent",
      "Value": "textContent",
      "Text": "textContent"
    }
  ],
  "tap": {
    "type": "${type}",
    "title": "${title}",
    "text": "${title}",
    "value": "${value}"
  }
}
```

Chamando `externalHeroCardActivity()` como uma função:

```lg
externalHeroCardActivity('signin', 'Signin Button', 'http://login.microsoft.com')
```

Ele retorna um `herocard`:

```lg
{
    "lgType" = "attachment",
    "contenttype" = "herocard",
    "content" = {
        "title": "titleContent",
        "text": "textContent",
        "Buttons": [
            {
            "type": "imBack",
            "Title": "titleContent",
            "Value": "textContent",
            "Text": "textContent"
            }
        ],
        "tap": {
            "type": "signin",
            "title": "Signin Button",
            "text": "Signin Button",
            "value": "http://login.microsoft.com"
        }
    }
}
```

## <a name="expandtext"></a>expandText

Avaliar o texto sem formatação em um objeto e retornar os dados de texto expandido.

```lg
expandText(<object>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | objeto | O objeto com texto a ser expandido. |
||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*evaluated-result*> | objeto | Os dados de texto expandidos. |
||||

*Exemplo*

Este exemplo avalia o texto sem formatação em um objeto JSON e retorna o resultado do texto expandido.

Digamos que você tenha o seguinte objeto:

```json
{
  "@answer": "hello ${user.name}",
  "user": {
    "name": "vivian"
  }
}
```

`expandText(@answer)`A chamada resultará no objeto **Hello Vivian**.

## <a name="template"></a>template

Retorna o resultado avaliado de determinado nome de modelo e escopo.

```lg
template(<templateName>, '<param1>', '<param2>', ...)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*templateName*> | Sim | string  | Uma cadeia de caracteres que representa o nome do modelo |
| <*param1*>,<*param2*>, ... | Sim | Objeto  | Os parâmetros passados para o modelo |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*evaluated-result*> | objeto | O resultado foi avaliado com base no modelo como uma função  |
||||

*Exemplo*

Este exemplo avalia o resultado da chamada do modelo como uma função.

Suponha que você tenha o seguinte modelo:

```lg
# welcome(userName)
- Hi ${userName}
- Hello ${userName}
- Hey ${userName}
```

A chamada de `template("welcome", "DL")` resultará em um dos seguintes:

- _Oi, DL_
- _Olá, DL_
- _Ei, DL_

## <a name="fromfile"></a>fromFile

Retorna o resultado avaliado da expressão no arquivo fornecido.

```lg
fromFile(<filePath>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*filePath*> | Sim | string  | o caminho relativo ou absoluto de um arquivo contém expressões |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*result*> | string | A representação de cadeia de caracteres do resultado avaliado |
||||

*Exemplo*

Este exemplo avalia o resultado do arquivo fornecido.

Suponha que você tenha um arquivo chamado `/home/user/test.txt`. Dentro do arquivo, há o seguinte:

```lg
`you have ${add(1,2)} alarms`

fromFile('/home/user/test.txt')
```

A função `fromFile()` avaliará a expressão e o resultado substituirá a expressão original.

Chamar `fromFile('/home/user/test.txt')` resultara na cadeia de caracteres _você tem três alarmes_.

## <a name="istemplate"></a>isTemplate

Retorna se determinado nome de modelo está incluído no avaliador.

```lg
isTemplate(<templateName>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*templateName*> | Sim | String  | Um nome de modelo a ser verificado |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*result*> | Boolean | Indica se o nome do modelo fornecido está incluído no avaliador  |
||||

*Exemplo*

Este exemplo usa a função `isTemplate()` para verificar se determinado nome de modelo está no avaliador. Por exemplo, veja estes três modelos:

```lg
# welcome
- hi

# show-alarms
- 7:am and 8:pm

# add-to-do
- you add a task at 7:pm
```

`isTemplate("welcome")`A chamada será avaliada como `true` . `isTemplate("delete-to-do")`A chamada será avaliada como `false` .

## <a name="additional-information"></a>Informações adicionais

- [Formato de arquivo .lg](../file-format/bot-builder-lg-file-format.md)
- [Modelo de resposta estruturado](language-generation-structured-response-template.md)
