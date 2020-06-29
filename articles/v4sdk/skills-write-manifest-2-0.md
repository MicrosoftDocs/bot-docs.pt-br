---
title: Como escrever um manifesto de habilidades v2.0 | Microsoft Docs
description: Saiba como escrever um manifesto de habilidades do Bot Framework.
keywords: habilidades, manifesto
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/04/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1491cfe40135b04f94481d5792c14886abd4fe5c
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566656"
---
# <a name="how-to-write-a-v20-skill-manifest"></a>Como escrever um manifesto de habilidades v2.0

Um _manifesto de habilidade_ é um arquivo JSON que descreve as ações que a habilidade pode executar, os respectivos parâmetros de entrada e saída e os pontos de extremidade da habilidade. O manifesto contém as informações de que um desenvolvedor precisa para acessar a habilidade por meio de outro bot.

Este artigo descreve [a versão 2.0.0](https://schemas.botframework.com/schemas/skills/v2.0/skill-manifest.json) do esquema de manifesto de habilidades do Bot Framework.
Para obter uma descrição da versão 2.1.preview-1, confira como [Escrever um manifesto de habilidades v2.1](skills-write-manifest-2-1.md).

O esquema de manifesto de habilidades do Bot Framework usa o rascunho 7 do vocabulário do esquema JSON.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento das [habilidades](skills-conceptual.md) e dos [bots de habilidades](skills-about-skill-bots.md).
- Alguma familiaridade com o [esquema JSON](http://json-schema.org/) e o formato JSON.

## <a name="the-skill-manifest"></a>O manifesto de habilidades

O manifesto de habilidades contém diferentes categorias de informações:

- Metadados que descrevem a habilidade em um nível geral.
- Uma lista dos pontos de extremidade fornecidos pela habilidade.
- Uma lista opcional das atividades que a habilidade pode receber.
- Um objeto de definições opcional que contém esquemas para os objetos referenciados por outras partes do documento.

Veja a seguir o esquema completo da v2.0 do manifesto de habilidades do Bot Framework.

| Categoria/Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| **Metadados**
| $id | string | Obrigatório | O identificador do manifesto de habilidades. |
| $schema | string | Obrigatório | O URI HTTPS de um recurso de esquema JSON que descreve o formato do manifesto. Para a versão 2.0.0, o URI é `https://schemas.botframework.com/schemas/skills/v2.0/skill-manifest.json`. |
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
| **Definições**
| definitions | objeto | Opcional | Um objeto que contém subesquemas para os objetos usados no manifesto. |

## <a name="endpoints"></a>Pontos de extremidade

Cada objeto de ponto de extremidade descreve um ponto de extremidade compatível com a habilidade.

Este exemplo lista dois pontos de extremidade para uma habilidade.

[!code-json[sample endpoints](~/../botframework-sdk/schemas/skills/v2.0/samples/complex-skillmanifest.json?range=17-32)]

### <a name="endpoint-object"></a>Objeto endpoint

| Campo | Type | Obrigatório | Descrição
|:-|:-|:-|:-
| descrição | string | Opcional | Uma descrição do ponto de extremidade.
| endpointUrl | string | Obrigatório | O ponto de extremidade do URI da habilidade.
| msAppId | string | Obrigatório | A ID do Aplicativo (GUID) da Microsoft da habilidade, usada para autenticar solicitações.
| name | string | Obrigatório | O nome exclusivo do ponto de extremidade.
| protocolo | string | Opcional | O protocolo compatível. O padrão é "BotFrameworkV3".

## <a name="activities"></a>Atividades

Cada objeto de atividade descreve uma atividade aceita pela habilidade. A habilidade iniciará uma ação ou uma tarefa com base na atividade inicial recebida. O nome associado ao objeto de atividade indica a ação ou a tarefa que a habilidade executará.

Alguns tipos de atividades têm uma propriedade de valor que pode ser usada para fornecer entrada adicional para a habilidade. Quando a habilidade é encerrada (conclui a ação), ela pode fornecer um valor retornado na propriedade de valor da atividade de fim de conversa associada.

Os tipos de atividades permitidos no esquema de manifesto da habilidade v2.0.0 são: atividades de mensagem, evento e invocação.

Este é um exemplo de descrição da atividade.

[!code-json[sample activity](~/../botframework-sdk/schemas/skills/v2.0/samples/complex-skillmanifest.json?range=34-44)]

### <a name="eventactivity-object"></a>Objeto eventActivity

Descreve uma atividade de evento aceita pela habilidade.

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

Descreve uma atividade de mensagem aceita pela habilidade. A propriedade de texto da atividade de mensagem contém o enunciado do usuário.

| Campo | Type | Obrigatório | Descrição |
|:-|:-|:-|:-|
| descrição | string | Opcional | Uma descrição da ação.
| resultValue | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que a ação associada pode retornar.
| type | string | Obrigatório | O tipo de atividade. Precisa ser "message".
| value | objeto | Opcional | Uma definição de esquema JSON do tipo de objeto que essa ação espera como entrada.

## <a name="definitions"></a>Definições

Cada definição descreve um subesquema que pode ser consumido por outras partes do documento.

Este é um exemplo de subesquema para informações de reserva de voo.

[!code-json[sample definition](~/../botframework-sdk/schemas/skills/v2.0/samples/complex-skillmanifest.json?range=71-90)]

## <a name="sample-manifest"></a>Exemplo de manifesto

Este é um exemplo de manifesto v2.0 completo para uma habilidade que expõe várias atividades.

[!code-json[sample 2.0 manifest](~/../botframework-sdk/schemas/skills/v2.0/samples/complex-skillmanifest.json)]
