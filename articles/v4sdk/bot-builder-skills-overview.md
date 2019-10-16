---
title: Visão geral das Habilidades do Bot Framework | Microsoft Docs
description: Saiba mais sobre as Habilidades do Bot Framework
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/09/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9bb149773fe1b2cdd09da0165a4fff4e647f419d
ms.sourcegitcommit: 5d81c5b25ea56e04f09d05916ee947d631009172
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72236430"
---
# <a name="virtual-assistant---skills-overview"></a>Assistente Virtual - Visão Geral das Habilidades

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

## <a name="overview"></a>Visão geral

Os desenvolvedores podem compor experiências de conversação combinando recursos de conversa reutilizáveis, conhecidos como Habilidades.

Dentro de uma empresa poderíamos ter a criação de um único bot pai reunindo vários sub-bots pertencentes a diferentes equipes ou o aproveitamento mais amplo dos recursos comuns fornecidos por outros desenvolvedores. Com essa versão prévia das Habilidades, os desenvolvedores podem criar um novo bot (normalmente por meio do modelo de Assistente Virtual) e adicionar/remover Habilidades com uma única operação de linha de comando incorporando todas as alterações na Expedição e Configuração.     

As próprias Habilidades são Bots, chamados remotamente, e um modelo de desenvolvedor (.NET, TS) está disponível para facilitar a criação de novas Habilidades.

Um objetivo-chave de design das Habilidades era manter o protocolo de Atividade consistente e garantir que a experiência de desenvolvimento fosse a mais próxima possível de qualquer bot comum do SDK V4. 

![Cenários de Habilidades](./media/enterprise-template/skills-scenarios.png)

## <a name="bot-framework-skills"></a>Habilidades do Bot Framework

Atualmente fornecemos as seguintes Habilidades do Bot Framework, suportadas pelo Microsoft Graph e disponível em vários idiomas.

![Cenários de Habilidades](./media/enterprise-template/skills-at-build.png)

| NOME | DESCRIÇÃO |
| ---- | ----------- |
|[Habilidade de Calendário](https://aka.ms/bf-calendar-skill)|Adicione recursos de calendário ao seu assistente. Da plataforma Microsoft Graph e Google.|
|[Habilidade de Email](https://aka.ms/bf-email-skill)|Adicione recursos de email ao seu assistente. Da plataforma Microsoft Graph e Google.|
|[Habilidade de tarefas](https://aka.ms/bf-todo-skill)|Adicione recursos de gerenciamento de tarefas ao seu assistente. Da plataforma Microsoft Graph.|
|[Habilidade de Ponto de Interesse](https://aka.ms/bf-poi-skill)|Localize pontos de interesse e direções. Da plataforma Azure Mapas e FourSquare.|
|[Habilidade Automotiva](https://aka.ms/bf-auto-skill)|Habilidade de demonstração para segmento vertical da indústria que permite o controle de recursos do carro.|
|[Habilidades Experimentais](https://aka.ms/bf-experimental-skills)|Notícias, Reservas em Restaurantes e Previsão do Tempo.|

## <a name="getting-started"></a>Introdução

Consulte os [tutoriais](https://aka.ms/bfs-tutorials) para aprender como se beneficiar das habilidades existentes e você mesmo construir as suas.
