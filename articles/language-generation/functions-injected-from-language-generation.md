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
ms.openlocfilehash: dad9b63bd55a29a5d24f565d55e41397c6150181
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566456"
---
# <a name="functions-injected-from-the-language-generation-library"></a>Funções injetadas da biblioteca de geração de linguagem

[!INCLUDE[applies-to](../includes/applies-to.md)]

O artigo a seguir fornece detalhes sobre como injetar funções da biblioteca [LG (geração de linguagem)](../v4sdk/bot-builder-concept-language-generation.md).

## <a name="activityattachment"></a>ActivityAttachment

Retorna um `activityAttachment` construído com base em um objeto e um tipo.

```.lg
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

```.lg
# externalHeroCardActivity(type, title, value)
[Activity
    attachments = ${ActivityAttachment(json(fromFile('.\\herocard.json')), 'herocard')}
]
```

e o seguinte `herocard.json`:

```.lg
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

```.lg
externalHeroCardActivity('signin', 'Signin Button', 'http://login.microsoft.com')
```

Ele retorna um `herocard`:

```.lg
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

## <a name="template"></a>template

Retorna o resultado avaliado de determinado nome de modelo e escopo.

```.lg
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

*Exemplo*:

Este exemplo avalia o resultado da chamada do modelo como uma função.

Suponha que você tenha o seguinte modelo:

```.lg
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

```.lg
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
```.lg
   `you have ${add(1,2)} alarms`

    fromFile('/home/user/test.txt')
```

A função `fromFile()` avaliará a expressão e o resultado substituirá a expressão original.

Chamar `fromFile('/home/user/test.txt')` resultara na cadeia de caracteres _você tem três alarmes_.

## <a name="istemplate"></a>isTemplate

Retorna se determinado nome de modelo está incluído no avaliador.

```.lg
isTemplate(<templateName>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*tempalteName*> | Sim | String  | Um nome de modelo a ser verificado |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| <*result*> | Boolean | Indica se o nome do modelo fornecido está incluído no avaliador  |
||||

*Exemplo*

Este exemplo usa a função `isTemplate()` para verificar se determinado nome de modelo está no avaliador. Por exemplo, veja estes três modelos:

```.lg
# welcome
- hi

# show-alarms
- 7:am and 8:pm

# add-to-do
- you add a task at 7:pm
```

A chamada de `isTemplate("welcome")` será avaliada como `true`. A chamada de `isTemplate("delete-to-do")` será avaliada como `false`.

<!--
## Additional Information

- For more information about .lg templates read the [.lg file format](../file-format/bot-builder-lg-file-format.md) and [structured response template](language-generation-structured-response-template.md) reference articles.-->
