---
title: Sobre os bots de habilidades | Microsoft Docs
description: Descreve como a lógica de conversação de um bot pode ser usada por outro bot usando o SDK do Bot Framework.
keywords: bot skill, skill bot.
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 04/28/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5eb235d887d2aec82239bc6aaf40f9044767b478
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417371"
---
# <a name="about-skill-bots"></a>Sobre os bots de habilidades

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Um _skill_ é um bot que pode executar um conjunto de tarefas para outro bot.
Uma habilidade pode ser consumida por outro bot, facilitando a reutilização.
Dessa forma, você pode criar um bot voltado para o usuário e estendê-lo consumindo as próprias habilidades ou as habilidades de terceiros.
Com pequenas modificações, qualquer bot pode agir como um skill.

Bots de skills:

- Implemente restrições de acesso por meio de um validador de declarações.
- Conforme apropriado, verifique os parâmetros de inicialização na propriedade _valor_ da atividade inicial.
- Envie mensagens para o usuário, como de costume.
- Sinalize a conclusão ou o cancelamento do skill por meio de uma atividade `endOfConversation`.
  - Forneça o valor retornado, se houver, na propriedade _valor_ da atividade.
  - Forneça um código de erro, se houver, na propriedade _código_ da atividade.

Para obter mais informações, confira a [Visão geral de habilidades](skills-conceptual.md) e [Sobre os consumidores de habilidades](skills-about-skill-consumers.md).

## <a name="skill-actions"></a>Ações de habilidades

Algumas habilidades podem executar uma variedade de tarefas ou _ações_. Por exemplo, uma habilidade de tarefas pendentes pode permitir criar, atualizar, exibir e excluir atividades que podem ser acessadas como conversas discretas.

<!--TODO Flesh this out-->

- Confira como [Implementar uma habilidade](skill-implement-skill.md) para obter uma habilidade simples que implementa uma ação.
- Confira como [Usar diálogos em uma habilidade](skill-actions-in-dialogs.md) para obter uma habilidade que usa diálogos para implementar várias ações.

## <a name="skill-manifests"></a>Manifestos de habilidades

Um _manifesto de habilidade_ é um arquivo JSON que descreve as ações que a habilidade pode executar, os respectivos parâmetros de entrada e saída e os pontos de extremidade da habilidade. Com a v2.1 do esquema de manifesto de habilidades, o manifesto também pode descrever as atividades proativas que a habilidade pode enviar e expedir os modelos usados pela habilidade.

<!--TODO Flesh this out-->

Para obter informações sobre o esquema de manifesto de habilidades, confira como [Escrever um manifesto de habilidades v2.1](skills-write-manifest-2-1.md) ou como [Escrever um manifesto de habilidades v2.0](skills-write-manifest-2-0.md).
