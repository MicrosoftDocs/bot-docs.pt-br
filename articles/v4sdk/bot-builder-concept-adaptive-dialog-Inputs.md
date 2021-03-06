---
title: Solicitar entrada do usuário em caixas de diálogo adaptáveis
description: Coleta de entrada de usuários usando caixas de diálogo adaptáveis
keywords: bot, usuário, ações, entrada, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 04/27/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8e8de204afc40170e9787eef423ae3d4ce5cc58b
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577268"
---
# <a name="asking-for-user-input-in-adaptive-dialogs"></a>Solicitar entrada do usuário em caixas de diálogo adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O SDK do Bot Framework define diversas caixas de diálogo de entrada para coletar e validar a entrada do usuário. Caixas de diálogo de entrada são um tipo de ação de caixa de diálogo adaptável.

> [!IMPORTANT]
> As ações (_ações de gatilho_) são caixas de diálogo e, como tal, têm todo o poder e a flexibilidade de que você precisa para criar um fluxo de conversa totalmente funcional e robusto. Embora as ações incluídas no SDK do Bot Framework sejam amplas, você também pode criar suas próprias ações personalizadas para executar praticamente qualquer tarefa ou processo especializado necessário.

## <a name="prerequisites"></a>Pré-requisitos

* [Introdução às caixas de diálogo adaptáveis][introduction]
* [Eventos e gatilhos em caixas de diálogo adaptáveis][triggers]
* [Ações em diálogos adaptáveis][actions]
* [Escopos de memória e gerenciamento de estado em caixas de diálogo adaptáveis][managing-state]
* Familiaridade com [modelos de geração de linguagem][lg-templates]
* Familiaridade com [expressões adaptáveis][adaptive-expressions]

> [!TIP]
> Essa sintaxe definida nos [modelos de Geração de Linguagem][lg-templates], que inclui [Expressões adaptáveis][adaptive-expressions], é usada no objeto `ActivityTemplate` que é exigido por vários parâmetros usados na maioria das ações de entrada fornecidas no SDK do Bot Framework.

## <a name="inputs"></a>Entradas

Assim como os [prompts][prompts], você pode usar _entradas_ em caixas de diálogo adaptáveis para solicitar e coletar entrada de um usuário, validá-la e aceitá-la na memória. Uma entrada:

* Associa o resultado do prompt a uma propriedade em um escopo de [gerenciamento de estado][managing-state].
* Faz a solicitação ao usuário somente se a propriedade de resultado ainda não tem um valor.
* Salva a entrada na propriedade especificada se a entrada do usuário corresponde ao tipo de entidade esperado.
* Aceita restrições de validação, como mínimo, máximo e assim por diante.
* Pode usar como entrada as intenções localmente relevantes dentro de uma caixa de diálogo, bem como usar a interrupção como uma técnica para emergir a resposta do usuário para uma caixa de diálogo pai apropriada que possa tratá-la.

<!-- TODO P0.5: For more information, see [about interruptions in adaptive dialogs](./ all-about-interruptions.md). -->

A biblioteca de diálogos adaptáveis define os seguintes tipos de entrada:

* [A classe base de entrada][inputdialog]. A classe base da qual derivam todas as classes de entrada.
* [Text][textinput]. Para solicitar qualquer ***texto com base na** entrada do usuário.
_ [Number][numberinput]. Para solicitar qualquer entrada ***numérica _ com base no** usuário.
_ [Confirmação][confirminput]. Para solicitar uma ***confirmação** _ do usuário.
_ [Múltipla escolha][multiple-choice]. Para solicitar uma seleção de um ***conjunto de opções** _.
_ [Arquivo ou anexo][attachmentinput]. Para solicitar/habilitar um usuário para **carregar um arquivo**.
* [Data ou hora][datetimeinput]. Para solicitar uma ***data e hora** _ de um usuário.
_ [Logon do OAuth][oauthinput]. Para permitir que os usuários **entrem em um site seguro**.

## <a name="additional-information"></a>Informações adicionais

* Para obter informações mais detalhadas sobre entradas, incluindo exemplos de código, consulte o artigo [entradas adaptáveis de caixas de diálogo predefinidas][prebuilt-inputs].
* Confira mais sobre expressões no artigo [Expressões adaptáveis][adaptive-expressions].

[introduction]:bot-builder-adaptive-dialog-introduction.md
[triggers]:bot-builder-concept-adaptive-dialog-triggers.md
[actions]:bot-builder-concept-adaptive-dialog-actions.md
[prompts]:bot-builder-concept-waterfall-dialogs.md#prompts
[authentication]:bot-builder-concept-authentication.md
[add-authentication]:bot-builder-authentication.md
[managing-state]:bot-builder-concept-adaptive-dialog-memory-states.md
[recognizers]:bot-builder-concept-adaptive-dialog-recognizers.md
[lg-templates]:bot-builder-concept-adaptive-dialog-generators.md
[adaptive-expressions]:bot-builder-concept-adaptive-expressions.md
[prebuilt-inputs]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md
[inputdialog]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#inputdialog
[textinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#textinput
[numberinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#numberinput
[confirminput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#confirminput
[multiple-choice]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#choiceinput
[attachmentinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#attachmentinput
[datetimeinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#datetimeinput
[oauthinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#oauthinput
