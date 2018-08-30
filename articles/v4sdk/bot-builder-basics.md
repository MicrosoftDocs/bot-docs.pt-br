---
title: Noções básicas do Bot Builder | Microsoft Docs
description: Estrutura básica do SDK do Bot Builder.
keywords: contexto de turno, estrutura do bot, receber entrada
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 34564b411f911ae82197d5a34cb954a103abe70b
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905410"
---
# <a name="basic-bot-structure"></a>Noções básicas da estrutura do bot

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

O Serviço de Bot do Azure e o SDK do Bot Builder fornecem bibliotecas, exemplos e ferramentas para ajudá-lo a compilar e depurar bots. No entanto, antes de entrar em muitos detalhes sobre qualquer um deles, é importante compreender a estrutura básica de um bot e como tudo funciona em conjunto. Esses conceitos são aplicáveis, independentemente da linguagem de programação escolhida. Ao longo deste artigo serão fornecidos links para conteúdos mais detalhados e uma estrutura mental inicial sobre como um bot opera.

A partir do zero, vamos explorar a estrutura básica de um bot.

## <a name="creation-of-your-bot"></a>Criação do bot

Um bot pode ser criado de várias maneiras como pelo [portal do Azure](~/bot-service-quickstart.md), no [Visual Studio](~/dotnet/bot-builder-dotnet-sdk-quickstart.md) ou através de ferramentas de linha de comando para [JavaScript](~/javascript/bot-builder-javascript-quickstart.md), [Java](~/java/bot-builder-java-quickstart.md) ou [Python](~/python/bot-builder-python-quickstart.md). Após criados, os bots poderão ser executar em um computador local, no Azure ou em outro serviço de nuvem. Todos funcionam de forma muito semelhante, independentemente de onde são executados ou como são compilados.

## <a name="interaction-with-your-bot"></a>Interação com o bot

Um bot inerentemente não tem nenhuma interface do usuário visível como um site ou aplicativo faz, em vez disso, um usuário interage com o bot através de uma [conversa](~/v4sdk/bot-concepts.md#activities-and-conversations). Dependendo do aplicativo utilizado para conectar o bot (que chamamos de [canal](~/v4sdk/bot-concepts.md), mas não abordaremos isso neste artigo), certas informações são enviadas e retornadas entre o usuário e o bot.

Cada unidade de informação é chamada de **atividade** dentro do bot e uma atividade pode originar de várias formas. As atividades incluem tanto a comunicação do usuário, que é referida como uma **mensagem**, quanto as informações adicionais encapsuladas em um grupo de outros [tipos de atividades](~/bot-service-activities-entities.md). Essas informações adicionais podem incluir quando um novo participante ingressa ou sai da conversa, quando uma conversa termina, etc. Esses tipos de comunicação da conexão do usuário são enviados pelo sistema subjacente, sem necessidade de qualquer ação do usuário.

O bot recebe a comunicação e a encapsula em um objeto de atividade, com o tipo correto, para fornecer ao código bot. Todos os outros tipos de atividade fornecem informações úteis, no entanto, a atividade mais interessante e mais comum é a atividade de **mensagem** do usuário.

Cada atividade que o bot recebe inicia um turno, que descreveremos com mais detalhes em breve.

## <a name="receiving-user-input"></a>Receber entrada do usuário

Ao receber uma atividade de mensagem do usuário, é necessário compreender o que a mensagem está enviando. A maneira mais direta é simplesmente corresponder o texto da mensagem de entrada a uma cadeia de caracteres. Com base na cadeia de caracteres, é possível optar por fazer algo, dependendo do que o bot está tentando alcançar. Isso pode incluir responder ao usuário, atualizar alguma variável ou recurso, salvando-o em [armazenamento](~/v4sdk/bot-builder-storage-concept.md), ou processamento semelhante.

Há maneiras mais complexas de reconhecer a entrada do usuário como usar [LUIS](~/v4sdk/bot-builder-concept-luis.md) ou [QnA Maker](~/v4sdk/bot-builder-howto-qna.md), mas a correspondência de cadeia de caracteres é a mais simples.

## <a name="defining-a-turn"></a>Definir um turno

[!INCLUDE [Define a turn](~/includes/snippet-definition-turn.md)]

O processamento de atividade é gerenciado pelo **adaptador**, conforme descrito mais detalhadamente em [processamento de atividade](~/v4sdk/bot-builder-concept-activity-processing.md). Quando o adaptador recebe uma atividade, ele cria um **contexto de turno** para fornecer informações sobre a atividade e contextualizar o turno atual que está em processamento. Esse contexto de turno existirá para a duração do turno e depois será eliminado, marcando o final do turno.

O [contexto de turno](~/v4sdk/bot-builder-concept-activity-processing.md#turn-context) contém um grupo de informações e o mesmo objeto é usado em todas as camadas do bot. Isso é útil porque esse objeto de contexto de turno pode ser, e deve ser, utilizado para armazenar informações que posteriormente poderão ser necessárias no turno.

> [!IMPORTANT]
> Todos os **turnos** são independentes um do outro, executando por conta própria, e têm o potencial de sobreposição. O bot pode estar processando vários turnos de cada vez, de vários usuários em vários canais. Cada turno terá o próprio contexto de turno, mas vale a pena considerar a complexidade que é introduzida em algumas situações.

## <a name="where-to-go-from-here"></a>Para onde ir a partir de agora

Este artigo evitou descrever muitos detalhes, como a forma que as [atividades são processadas](~/v4sdk/bot-builder-concept-activity-processing.md), os diferentes [tipos de conversa](~/v4sdk/bot-builder-conversations.md), como controlar o [estado da conversa ](~/v4sdk/bot-builder-storage-concept.md) para conversas mais inteligentes, e assim por diante. O restante dos tópicos conceituais baseia-se nas noções básicas e abrange o restante dos conceitos necessários para compreender ambos os bots e o Serviço de Bot do Azure. Você pode prosseguir para as seções das próximas etapas e ampliar o conhecimento ou ir direto ao ponto de seu interesse, experimentar o [início rápido](~/bot-service-quickstart.md) para compilar seu primeiro bot ou aprofundar-se em como [desenvolver](~/v4sdk/bot-builder-howto-send-messages.md) bots.

## <a name="next-steps"></a>Próximas etapas

A seguir, o Serviço do Bot Connector permite que o bot comunique-se com usuários em diferentes plataformas.

> [!div class="nextstepaction"]
> [Canais e o Serviço do Bot Connector](~/v4sdk/bot-concepts.md)
