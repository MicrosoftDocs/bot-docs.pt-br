---
title: Como escrever um manifesto de habilidades v2.1 | Microsoft Docs
description: Saiba como escrever um manifesto de habilidades do Bot Framework.
keywords: skills, manifest
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/04/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 240ebc0ab1e12606d9eb9a194ec8cf486136f799
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566666"
---
# <a name="how-to-write-a-v21-skill-manifest"></a>Como escrever um manifesto de habilidades v2.1

Um _manifesto de habilidade_ é um arquivo JSON que descreve as ações que a habilidade pode executar, os respectivos parâmetros de entrada e saída e os pontos de extremidade da habilidade. O manifesto contém as informações de que um desenvolvedor precisa para acessar a habilidade por meio de outro bot. Com a v2.1 do esquema de manifesto de habilidades, o manifesto também pode descrever as atividades proativas que a habilidade pode enviar e expedir os modelos usados pela habilidade.

Este artigo descreve [a versão 2.1.0](https://schemas.botframework.com/schemas/skills/v2.1/skill-manifest.json) do esquema de manifesto de habilidades do Bot Framework.
Para obter uma descrição da versão 2.0.0, confira como [Escrever um manifesto de habilidades v2.0](skills-write-manifest-2-0.md).

O esquema de manifesto de habilidades do Bot Framework usa o rascunho 7 do vocabulário do esquema JSON.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento das [habilidades](skills-conceptual.md) e dos [bots de habilidades](skills-about-skill-bots.md).
- Alguma familiaridade com o [esquema JSON](http://json-schema.org/) e o formato JSON.

## <a name="the-skill-manifest"></a>O manifesto de habilidades

O manifesto de habilidades contém diferentes categorias de informações:

- Metadados que descrevem a habilidade em um nível geral.
- Uma lista dos pontos de extremidade fornecidos pela habilidade.
- Listas opcionais das atividades que a habilidade pode receber e enviar de maneira proativa.
- Um objeto de definições opcional que contém esquemas para os objetos referenciados por outras partes do documento.
- Uma lista opcional dos modelos de expedição aos quais a habilidade dá suporte.

Veja a seguir o esquema completo da v2.1 do manifesto de habilidades do Bot Framework.

| Categoria/Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| **Metadados**
| $id | string | Obrigatório | O identificador do manifesto de habilidades. |
| $schema | string | Obrigatório | O URI HTTPS de um recurso de esquema JSON que descreve o formato do manifesto. Para a versão 2.1.0, o URI é `https://schemas.botframework.com/schemas/skills/v2.1/skill-manifest.json`. |
| copyright | string | Opcional | A notificação de direitos autorais da habilidade. |
| descrição | string | Opcional | Uma descrição legível da habilidade. |
| iconUrl | string | Opcional | O URI do ícone a ser mostrado para a habilidade. |
| license | string | Opcional | O contrato de licença da habilidade. |
| name | string | Obrigatório | O nome da habilidade. |
| version | string | Obrigatório | A versão da habilidade descrita pelo manifesto. |
| privacyUrl | string | Opcional | O URI da descrição de privacidade da habilidade. |
| publisherName | string | Obrigatório | O nome do editor de habilidades. |
| marcas | Matriz de cadeia de caracteres | Opcional | Um conjunto de marcas para a habilidade. Se ele estiver presente, cada marca precisará ser exclusiva. |
| **Pontos de extremidade**
| pontos de extremidade | matriz de [ponto de extremidade](#endpoint-object) | Obrigatório | A lista de pontos de extremidade compatível com a habilidade. Pelo menos um ponto de extremidade precisa ser definido. Cada ponto de extremidade precisa ser exclusivo. |
| **Atividades**
| atividades | objeto que contém [objetos de atividade](#activities) nomeados | Obrigatório | O conjunto de atividades iniciais aceitas pela habilidade. |
| activitiesSent | objeto que contém [objetos de atividade](#activities) nomeados | Opcional | Descreve as atividades proativas que podem ser enviadas pela habilidade. |
| **Definições**
| definitions | objeto | Opcional | Um objeto que contém subesquemas para os objetos usados no manifesto. |
| **Modelos de expedição**
| dispatchModels | Objeto [dispatchModels](#dispatch-models) | Opcional | Descreve os modelos de linguagem e as intenções de nível superior compatíveis com a habilidade. Confira para obter o esquema desse objeto. |

## <a name="endpoints"></a>Pontos de extremidade

Cada objeto de ponto de extremidade descreve um ponto de extremidade compatível com a habilidade.

Este exemplo lista dois pontos de extremidade para uma habilidade.

[!code-json[sample endpoints](~/../botframework-sdk/schemas/skills/v2.1/samples/complex-skillmanifest.json?range=17-32)]

### <a name="endpoint-object"></a>Objeto endpoint

Descreve um ponto de extremidade compatível com a habilidade.

| Campo | Type | Obrigatório | Descrição
|:-|:-|:-|:-
| descrição | string | Opcional | Uma descrição do ponto de extremidade.
| endpointUrl | string | Obrigatório | O ponto de extremidade do URI da habilidade.
| msAppId | string | Obrigatório | A ID do Aplicativo (GUID) da Microsoft da habilidade, usada para autenticar solicitações.
| name | string | Obrigatório | O nome exclusivo do ponto de extremidade.
| protocolo | string | Opcional | O protocolo compatível. O padrão é "BotFrameworkV3".

## <a name="activities"></a>Atividades

Cada objeto de atividade descreve uma atividade aceita pela habilidade ou uma atividade que a habilidade pode enviar de maneira proativa. Para as atividades de entrada, a habilidade iniciará uma ação ou uma tarefa com base na atividade inicial recebida. O nome associado ao objeto de atividade indica a ação ou a tarefa que a habilidade executará.

Alguns tipos de atividades têm uma propriedade de valor que pode ser usada para fornecer entrada adicional para a habilidade. Quando a habilidade é encerrada (conclui a ação), ela pode fornecer um valor retornado na propriedade de valor da atividade de fim de conversa associada.

Os tipos de atividades permitidos no esquema de manifesto de habilidades v2.1.preview-1 são: atividades de mensagem, evento, invocação e _outras_. Uma habilidade pode receber uma atividade de invocação, mas não pode enviar uma.

Este é um exemplo de descrição da atividade.

[!code-json[sample activity](~/../botframework-sdk/schemas/skills/v2.1/samples/complex-skillmanifest.json?range=84-94)]

### <a name="eventactivity-object"></a>Objeto eventActivity

Descreve uma atividade de evento aceita ou enviada pela habilidade.

| Campo | Type | Obrigatório | Descrição
|:-|:-|:-|:-
| descrição | string | Opcional | Uma descrição da ação.
| name | string | Obrigatório | O valor da propriedade de nome da atividade de evento.
| resultValue | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que a ação associada pode retornar.
| type | string | Obrigatório | O tipo de atividade. Precisa ser "event".
| value | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que essa ação espera como entrada.

### <a name="invokeactivity-object"></a>Objeto invokeActivity

Descreve uma atividade de invocação aceita pela habilidade.

| Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| descrição | string | Opcional | Uma descrição da ação.
| name | string | Obrigatório | O valor da propriedade de nome da atividade de invocação.
| resultValue | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que a ação associada pode retornar.
| type | string | Obrigatório | O tipo de atividade. Precisa ser "invoke".
| value | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que essa ação espera como entrada.

### <a name="messageactivity-object"></a>Objeto messageActivity

Descreve uma atividade de mensagem aceita ou enviada pela habilidade. A propriedade de texto da atividade de mensagem contém o enunciado do usuário.

| Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| descrição | string | Opcional | Uma descrição da ação.
| resultValue | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que a ação associada pode retornar.
| type | string | Obrigatório | O tipo de atividade. Precisa ser "message".
| value | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que essa ação espera como entrada.

### <a name="otheractivities-object"></a>Objeto otherActivities

Descreve uma atividade aceita ou enviada pela habilidade.

| Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| type | string | Obrigatório | O tipo de atividade. Precisa ser um dos outros tipos de atividades do Bot Framework: "contactRelationUpdate", "conversationUpdate", "deleteUserData", "endOfConversation", "handoff", "installationUpdate", "messageDelete", "messageUpdate", "messageReaction", "suggestion", "trace" ou "typing".

O objeto otherActivities pode incluir outras propriedades, mas o esquema do manifesto de habilidades não define o significado delas.

## <a name="definitions"></a>Definições

Cada definição descreve um subesquema que pode ser consumido por outras partes do documento.

Este é um exemplo de subesquema para informações de reserva de voo.

[!code-json[sample definition](~/../botframework-sdk/schemas/skills/v2.1/samples/complex-skillmanifest.json?range=127-146)]

## <a name="dispatch-models"></a>Modelos de expedição

O modelo de expedição contém uma lista de modelos de linguagem e uma lista de intenções de nível superior compatível com a habilidade. Esse é um recurso avançado para permitir que um desenvolvedor de um consumidor de habilidades crie um modelo de linguagem que combine os recursos dos bots de consumidor e de habilidades.

Um nome de localidade é uma combinação de um código de cultura em letras minúsculas ISO 639 associado a um idioma e um código de subcultura em letras maiúsculas ISO 3166 opcional associado a um país ou uma região, por exemplo, "en" ou "en-US".

| Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| intenções | Matriz de cadeia de caracteres | Opcional | Uma lista das intenções de nível superior compatíveis com a habilidade. Cada intenção precisa ser exclusiva.
| idiomas | objeto que contém matrizes [languageModel](#languagemodel-object) nomeadas | Obrigatório | Uma lista dos modelos de linguagem compatíveis com a habilidade. Cada nome é a localidade à qual os modelos de linguagem se referem e a matriz contém os módulos de linguagem dessa localidade. Um modelo de expedição precisa dar suporte a, pelo menos, uma localidade. Cada localidade dentro do campo de linguagens precisa ser exclusiva.

Este é um exemplo de modelo de expedição que contém dois modelos de linguagens em três localidades. Ele também descreve duas intenções de nível superior que podem ser reconhecidas pela habilidade.

[!code-json[sample activity](~/../botframework-sdk/schemas/skills/v2.1/samples/complex-skillmanifest.json?range=33-82)]

### <a name="languagemodel-object"></a>Objeto languageModel

Descreve um modelo de linguagem para determinada cultura. O nome é um nome de localidade.

| Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| contentType | string | Obrigatório | Tipo do modelo de linguagem.
| descrição | string | Opcional | Uma descrição do modelo de linguagem.
| name | string | Obrigatório | Nome do modelo de linguagem.
| url | string | Obrigatório | A URL do modelo de linguagem.

## <a name="sample-manifest"></a>Exemplo de manifesto

Este é um exemplo de manifesto v2.1 completo para uma habilidade que expõe várias atividades.

[!code-json[sample 2.1 manifest](~/../botframework-sdk/schemas/skills/v2.1/samples/complex-skillmanifest.json)]
