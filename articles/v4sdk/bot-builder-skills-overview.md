---
title: Visão geral das Habilidades do Assistente Virtual – Serviço de Bot
description: Familiarize-se com as habilidades do assistente virtual. Saiba mais sobre as habilidades do bot Framework que estão disponíveis para uso em bots, como o calendário e as habilidades de email.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/09/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: cfa55a6aa26f4909112aef058f6633d6ced16a48
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717562"
---
# <a name="virtual-assistant---skills-overview"></a>Assistente Virtual - Visão Geral das Habilidades

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

## <a name="overview"></a>Visão geral

Os desenvolvedores podem compor experiências de conversa reunindo recursos de conversa reutilizáveis, conhecidos como habilidades.

Dentro de uma empresa poderíamos ter a criação de um único bot pai reunindo vários sub-bots pertencentes a diferentes equipes ou o aproveitamento mais amplo dos recursos comuns fornecidos por outros desenvolvedores. Com essa versão prévia das Habilidades, os desenvolvedores podem criar um novo bot (normalmente por meio do modelo de Assistente Virtual) e adicionar/remover Habilidades com uma única operação de linha de comando incorporando todas as alterações na Expedição e Configuração.

As habilidades são bots, chamadas remotamente e um modelo de desenvolvedor de habilidades (.NET, TS) está disponível para facilitar a criação de novas habilidades.

Um objetivo-chave de design das Habilidades era manter o protocolo de Atividade consistente e garantir que a experiência de desenvolvimento fosse a mais próxima possível de qualquer bot comum do SDK V4.

![Cenários de Habilidades](./media/enterprise-template/skills-scenarios.png)

## <a name="bot-framework-skills"></a>Habilidades do Bot Framework

Neste momento, você disponibilizou as seguintes habilidades do bot Framework, alimentadas pela Microsoft Graph e disponíveis em vários idiomas.

![Compilação de cenários de habilidades](./media/enterprise-template/skills-at-build.png)

| Nome | Descrição |
| ---- | ----------- |
|[Habilidade de Calendário](https://aka.ms/bf-calendar-skill)|Adicione recursos de calendário ao seu assistente. Da plataforma Microsoft Graph e Google.|
|[Habilidade de Email](https://aka.ms/bf-email-skill)|Adicione recursos de email ao seu assistente. Da plataforma Microsoft Graph e Google.|
|[Habilidade de tarefas](https://aka.ms/bf-todo-skill)|Adicione recursos de gerenciamento de tarefas ao seu assistente. Da plataforma Microsoft Graph.|
|[Habilidade de Ponto de Interesse](https://aka.ms/bf-poi-skill)|Localize pontos de interesse e direções. Da plataforma Azure Mapas e FourSquare.|
|[Habilidade Automotiva](https://aka.ms/bf-auto-skill)|Habilidade de demonstração para segmento vertical da indústria que permite o controle de recursos do carro.|
|[Habilidades Experimentais](https://aka.ms/bf-experimental-skills)|Notícias, Reservas em Restaurantes e Previsão do Tempo.|

## <a name="getting-started"></a>Introdução

Consulte os [tutoriais](https://aka.ms/bfs-tutorials) para aprender como se beneficiar das habilidades existentes e você mesmo construir as suas.
